# Using the Metadata

Auth0 Provides each user with two fields customizable fields for storing various types of information. These fields require users to have the correct claims to manipulate.

## User Metadata
The `user_metadata` property is designed to store trivial user customizable preferences. Data stored here should not impact a user's core functionality. Logged in users can edit their own user_metadata if they have the `update:current_user_metadata` permission in their token. As a developer always be cautious of using or assuming some piece of data will exist here.

```json
{
    "user_metadata": {
        "theme": "dark",
        "displayName": "Jimmy117",
        "language": "EN-us"
    }
}
```

## App Metadata
The `app_metadata` stores information such as subscriptions, security groups, or access control modifications. Data stored in the app_metadata can be used to directly impact a user's core functionality. Data stored in app_metadata cannot be edited by users as it requires a Manager access token to manipulate.

> 💀 IMPORTANT: Data unrelated to user authentication / authorization should always be stored in an external database and not in the user profile metadata.