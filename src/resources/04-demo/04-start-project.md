# Start a Project


[![img](http://img.youtube.com/vi/9fpLYjMkXik/0.jpg)](https://www.youtube.com/watch?v=9fpLYjMkXik)



To get started with our prebaked heavily opinonated template run the following commands

* `npm i -g bcw` - skip if you already have it
* `bcw-create`
* select fullstack-vue and follow the prompts

> be sure to open the vscode workspace to take full advantage of `.eslintrc` across the entire project

## Auth0 Server Setup

This template is designed to help kickstart a project that utilizes <a href="https://auth0.com/" target="_blank">Auth0.</a> The bulk of the structure has been setup and requires a few pieces of configuration.

The first thing you will need to provide is in the `.env` file. You will need to supply the port, Auth0 credentials, and mongoDb connectionstring. These environment variables are used throughout the template, so be sure to add them in when moving into production as well.

**_.env_**

``` 

NODE_ENV=dev
CONNECTION_STRING=
AUTH_DOMAIN=
AUTH_AUDIENCE=
AUTH_CLIENT_ID=
```

## Logger

Logging is always tricky and many people use any random flavor of external logging tools pick one and tie it into the `Logger.js` utility, do not use `console.log` you will thank us in the long run

## MVC - Controllers

This template will automatically register all of the controllers found in the controllers folder of the server. This opinionated workflow should help provide a structure on how to build your api. Generally speaking every controller method should start with a `try catch block` and utilize the default error handler setup in Startup.js This means if a request ever fails the controller should call the next function with the error provided.

``` javascript
import {
    Auth0Provider
} from '@bcwdev/auth0provider'
import {
    profilesService
} from '../services/ProfilesService'
import BaseController from '../utils/BaseController'

export class ProfilesController extends BaseController {
    constructor() {
        super('profile') // sets the endpoint
        this.router
            .use(Auth0Provider.getAuthorizedUserInfo) // Authorization Gate
            .get('', this.getUserProfile)
    }

    async getUserProfile(req, res, next) {
        // Always try in the controller and throw from a service
        try {
            const profile = await profilesService.getProfile(req.userInfo)
            res.send(profile)
        } catch (error) {
            next(error) // sent to global error handler
        }
    }
}
```

## MVC - Services

Services are responsible for implementing and enforcing your business rules. Be sure to use them wisely and do not put your business logic in controllers. Services should be usable by both controllers and sockets and potentally other services. Never directly access the `DbContext` outside of a service.

## MVC - (Models, Collections & DbContext)

Models are defined as mongoose schemas and then imported into a central location called the DbContext. All access to the database should be limited to the DbContext. `Collections.js` is a file purely designed to avoid the common problem of magic strings. This means when you register your models and have dependencies or relationships between one or more models you should import from Collections so you know the naming is always the same.

## Working with Auth0

This library provides easily configured middleware that will validate user auth tokens, roles, permissions and provides a simple approach to get userInfo associted with a user account. Each middleware will call next with an error on any failure so be sure to setup a default error handler. Also note that we extend the express request object with

* req.user: `{ UserIdentity }`
* req.userInfo: `{ UserInfo }`

## Enable RBAC or Extended Rules (required)

In your auth0 dashboard be sure to enable RBAC or add in this custom rule

``` javascript
//AUTH0 RULE
/**
 * Add common namespaced properties to userInfo, 
 * note auth0 will strip any non namespaced properties
 */
function extendUserInfo(user, context, callback) {
    const uuid = require('uuid@3.3.2');
    const namespace = 'https://YOURDOMAINHERE.auth0.com';
    context.idToken = context.idToken || {};
    context.authorization = context.authorization || {};
    user.app_metadata = user.app_metadata || {
        new: true
    };
    user.app_metadata.id = user.app_metadata.id || uuid();

    for (const key in user.app_metadata) {
        context.idToken[`${namespace}/${key}`] = user.app_metadata[key];
    }
    context.idToken[`${namespace}/roles`] = context.authorization.roles;
    context.idToken[`${namespace}/permissions`] = context.authorization.permissions;
    context.idToken[`${namespace}/user_metadata`] = user.user_metadata;

    if (!user.app_metadata.new) {
        return callback(null, user, context);
    }
    delete user.app_metadata.new;
    auth0.users.updateAppMetadata(user.user_id, user.app_metadata)
        .then(function() {
            callback(null, user, context);
        })
        .catch(function(err) {
            callback(err);
        });
}
```

## Mongoose Helper

This is a small utility designed to help with common db queries & tasks that I never remember in mongoose alone

``` javascript
import mongoose from "mongoose";
import slug from "mongoose-slug-plugin";
import {
    PROFILE
} from "../db/Collections";

export function Schemator(schema, {
    addSlug = false,
    slugField = "name"
} = {}, overrides = {}) {

    let scheme = new mongoose.Schema({
        ...schema,
        _deleted: {
            type: Boolean,
            default: false
        },
        _creatorId: string(true)
    }, {
        timestamps: true,
        id: true,
        toObject: {
            virtuals: true
        },
        toJSON: {
            virtuals: true
        },
        ...overrides
    });
    if (addSlug) {
        scheme.plugin(slug, {
            tmpl: `<%=${slugField}%>`
        });
    }

    // Formats error when bad Id
    scheme.post('save', function(err, doc, next) {
        if (err) {
            if (err.message.includes("Cast to ObjectID")) {
                err.message = err.message.slice(0, err.message.lastIndexOf(":")) + " is required or malformed";
            }
            return next(err);
        }
        next();
    });

    // NOTE fields starting with an _underscore will not be sent from the server 
    scheme.methods.toJSON = function() {
        let obj = this.toObject();
        obj.id = obj._id;
        Object.keys(obj).forEach(k => {
            if (k === "_id") {
                return
            }
            if (k.startsWith("_")) {
                delete obj[k];
            }
        });
        return obj;
    };
    // NOTE extends all object to include the Collection Name
    scheme.virtual("collectionType").get(function() {
        return this.collection.collectionName
    })
    // NOTE extends all objects to add their creator
    scheme.virtual("creator", {
        localField: "_creatorId",
        ref: PROFILE,
        foreignField: "_id",
        justOne: true,
        options: {
            select: "name picture -email"
        }
    });

    return scheme;
}

// [!] IMPORTANT never allow a string to be set to the databse without a max length
export function string(required = false, maxlength = 255) {
    return {
        type: String,
        trim: true,
        maxlength,
        required,
        default: ""
    };
}
export function url(required = false, maxlength = 5000) {
    return {
        ...string(required, maxlength),
        match: /(http(s)?:\/\/.)?(www\.)?[-a-zA-Z0-9@:%._\+~#=]{2,256}\.[a-z]{2,6}\b([-a-zA-Z0-9@:%_\+.~#?&//=]*)/
    };
}

/**
 * Creates a relationship between models (Model Name Required)
 * @param {string} ref
 * @param {string} [type]
 */
export function rel(ref, type = mongoose.Schema.Types.ObjectId) {
    if (!ref) {
        throw new Error("You must declare a ref when establishing a relationship");
    }
    return {
        type,
        required: true,
        ref,
        trim: true
    };
}

/**
 * Adds a profile to schema via _creatorId relationship
 * @type {mongoose.Schema<any>} schema
 * @type {string} [field]
 * @type {string} [localField]
 * @type {boolean} [justOne]
 * @type {string} [foreignField]
 */
export function virtualProfile(schema, field = 'profile', localField = '_creatorId', justOne = true, foreignField = '_id', select = 'name email picture') {
    schema.virtual(field, {
        localField,
        ref: PROFILE,
        foreignField,
        justOne
    });
    autoPopulate(schema, field, select);
};

/**
 * AutoPopulates on every find update and create
 * @param {mongoose.Schema<any>} schema
 * @param {string} populate
 * @param {string} [select]
 */
export function autoPopulate(schema, populate, select = '') {
    function include(next) {
        this.populate(populate, select);
        next();
    }
    schema.pre("findOne", include);
    schema.pre("find", include);
    schema.pre("findOneAndUpdate", include)
    schema.post("create", include)
};

export function cascadeDelete(schema, otherModels, localKey = "_id") {
    schema.pre('remove', function(next) {
        otherModels.forEach(m => {
            m.remove({
                [m.foreignKey]: this[localKey]
            }).exec();
        })
        next();
    });
}
```

## Control Access

Conditionally render components based upon permissions and roles

``` vue
<template>
  <section class="experimental" title="experimental feature" v-if="hasPermissions('view:experimental')">
    <small class="experimental-badge badge text-uppercase danger stroke p-1">experimental feature</small>
    <slot />
  </section>
</template>

<script setup>
export { hasPermissions } from '@bcwdev/auth0provider-client'
</script>
```

``` js
import {
    authGuard,
    hasRoles
} from '@bcwdev/auth0provider-client'

function loadPage(page) {
    return () => import(`./pages/${page}`)
}

let routes = [{
        path: '/profile',
        name: 'Profile',
        beforeEnter: authGuard,
        component: loadPage('ProfilePage.vue')
    },
    {
        path: '/admins',
        name: 'Admins',
        beforeEnter(to, from, next) {
            authGuard(to, from, () => {
                if (!hasRoles('admin')) {
                    return next('/profile')
                }
                next()
            })
        },
        component: loadPage('AdminsPage.vue')
    },
]
```
