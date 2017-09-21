---
title: Authorization
description: This tutorial will show you how to use the Auth0 authentication API in your Android project to create a custom login screen.
seo_alias: android
budicon: 500
---

This step demonstrates how to use Auth0 to create access roles for your users. With access roles, you can authorize or deny content to different users based on the level of access they have.

<%= include('../../../_includes/_package', {
  org: 'auth0-samples',
  repo: 'auth0-android-sample',
  path: '07-Authorization',
  requirements: [
    'Android Studio 2.3',
    'Android SDK 25',
    'Emulator - Nexus 5X - Android 6.0'
  ]
}) %>__

## Before Starting

Be sure that you have completed the [user profile](04-user-profile) quickstart.

Create a rule that assigns the following access roles to your user: 
* An admin role
* A regular user role

To assign roles, go to the [New rule](${manage_url}/#/rules/new) page. In the **Access Control** section, select the **Set roles to a user** template. 

Edit the following line from the default script to match the conditions that fit your needs:

```java
if (user.email.indexOf('@example.com') > -1)
```
to match the condition that fits your needs.

The default rules for assigning access roles are:
* If the user's email contains `@example.com`, the user gets the admin role. 
* If the user's email contains anything else, the user gets the regular user role.

::: note
You can define more roles other than `admin` and `user`, depending on your product requirements.
:::

::: note
In the demo app, we use `@admin.com` to validate, like the next rule:
:::

```js
  var addRolesToUser = function(user, cb) {
    if (user.email.indexOf('@admin.com') > -1) {
      cb(null, ['admin']);
    } else {
      cb(null, ['user']);
    }
  };
```

## Test the Rule in Your Project

Once you have the user profile, you can save the profile and access it at any point. To learn more about user profiles, see the [User Profile](04-user-profile) tutorial. 

Inside it, you will have the role, and you will be ready to perform the access control.

```java
// app/src/main/java/com/auth0/samples/activities/MainActivity.java
List<String> roles = (List<String>) mUserProfile.getAppMetadata().get("roles");

if (roles.contains("admin")) {
 // perform any action
};
```

::: note
Application metadata cannot be modified by users but the user metadata can be modified. Because of that, the information about user roles is stored in the `appMetadata` HashMap, not in the `userMetadata` HashMap.
:::

## Restrict Content Based On Access Level

Now you can recognize the users with different roles in your app. You can use this information to give and restrict access to selected features in your app to users with different roles.