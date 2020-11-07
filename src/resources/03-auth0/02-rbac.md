# Access Control

![rbac](https://miro.medium.com/max/601/1*ub3g0nUC6NCkYPHoNB6rFw.png)

## Role Access Control (RBAC)

Role-based access control (RBAC) is a method of restricting access based on the roles of individual users within an organization. RBAC facilitates controlling user access rights only to the information they need to do their jobs and prevents them from accessing information that doesn't pertain to them. 

Naming things is hard... and depending on the granularity of our application RBAC alone can be incredibly difficult to manage. Often times within an organization we end up with a simple rule set which states something along the lines of only an admin can do x

``` js
if (user.role !== 'ADMIN') {
    throw new Error('Invalid User Access')
}
```

This approach only works up until we introduce something like a `super admin` . Then our we once again have to update our code to enforce that a super admin can surely do what an admin can do... Problem is we may have this conditional statement in several locations throughout our server.... Then we further complicate things when we have a system or scope that says I need a certain set of users to be able to perform some admin actions but they should not have access to all the same things as my admins so we end up creating another role... `moderator` , But of course there is an outlier where one user in paticular needs a bit more access but still less than the `admin` and more than our `moderator` . Soon we are facing hundreds of roles and no one can remember what each one of them is for. 

![permission](https://www.elastic.co/guide/en/elasticsearch/reference/current/security/authorization/images/authorization.png)

## Permission Based Control (PBAC)

Along comes Permission Based Control. This follows the same concepts of role based control where a user must have permission to access a system... Generally in Permission based control we create roles to group a various set of permissions we can then assign a role to a user which will grant them the set of predefined permissions but then each user can have their permissions individually altered. This approach saves us from over generating roles and applies a more granular approach to enforcing access control. 

![pbac](https://tasdikrahman.me/content/images/2017/06/rbac_header.jpg)

## Rule Extend UserInfo

Once we have figured out the Access Control strategies we want to use to build an application it becomes helpful to extend our `user_info` and `user_access` tokens to include a users roles and permissions. When adding this type of information to a token we must follow an OIDC namespacing strategy that is designed to help distinguish the claims based upon the correct namesapce. 

Here is an Auth0 Rule that can be written to ensure all users have a unique id and a way to extend their tokens with the users `meta_data` `roles` and `permissions`

``` javascript
function extendUserInfo(user, context, callback) {
    const uuid = require('uuid@3.3.2');
    const namespace = 'https://<YOUR_DOMAIN>.auth0.com';
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
