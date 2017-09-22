---
title: User Profile
description: This tutorial will show you how to get and modify the user's profile data.
seo_alias: android
budicon: 292
---

This tutorial will show you how to get and modify the user's profile data in your Android apps with Auth0.

<%= include('../../../_includes/_package', {
  org: 'auth0-samples',
  repo: 'auth0-android-sample',
  path: '04-User-Profile',
  requirements: [
    'Android Studio 2.3',
    'Android SDK 25',
    'Emulator - Nexus 5X - Android 6.0'
  ]
}) %>__

## Before Starting

Be sure that you have completed the [Login](/quickstart/native/android/00-login) and the [Session Handling](/quickstart/native/android/03-session-handling) Quickstarts. You'll need a valid `access_token` and `id_token` to call the API clients.

Before you launch the login process, make sure you will get a valid profile from the authorization server. To do that, ask for the `openid profile email` scope. Find the snippet in which you initialize the `WebAuthProvider` class. To that snippet, add the line `withScope("openid profile email")`.

```java
Auth0 auth0 = new Auth0(this);
auth0.setOIDCConformant(true);
WebAuthProvider.init(auth0)
                .withScheme("demo")
                .withScope("openid profile email")
                .start(this, callback);
```

## Request User Data

Create instances of the API clients. You will use them to request the users' profile data.

```java
// app/src/main/java/com/auth0/samples/activities/MainActivity.java

Auth0 auth0 = new Auth0(this);
auth0.setOIDCConformant(true);

String idToken = CredentialsManager.getCredentials(this).getIdToken();
UsersAPIClient usersClient = new UsersAPIClient(auth0, idToken);
AuthenticationAPIClient authClient = new AuthenticationAPIClient(auth0);
```

::: note
It's suggested that you add both the Auth0 `domain` and `clientId` to the `strings.xml` file rather than hardcode them.
:::

To get the user's information:

1. Create an instance of the `AuhenticationAPIClient` client.
2. Use the user's access token to call the `userInfo` method in the client. 
You get an instance of the `UserProfile` profile. The profile is OIDC-conformant. Depending on the [scopes](https://auth0.com/docs/scopes/current) you requested, the profile contains different information.
3. To get the user's ID, use the [Management API](https://auth0.com/docs/api/management/v2#!/Users).

```java
// app/src/main/java/com/auth0/samples/activities/MainActivity.java

String accessToken = CredentialsManager.getCredentials(this).getAccessToken();
authenticationClient.userInfo(accessToken)
    .start(new BaseCallback<UserProfile, AuthenticationException>() {

        @Override
        public void onSuccess(final UserProfile userInfo) {
            String userId = userInfo.getId();
            // fetch the full user profile
        }

        @Override
        public void onFailure(AuthenticationException error) {
            //show error
        }
    });
```

Use the `UsersAPIClient` client and the user's ID to get the full user profile.

```java
// app/src/main/java/com/auth0/samples/activities/MainActivity.java

usersClient.getProfile(userId)
        .start(new BaseCallback<UserProfile, ManagementException>() {
            @Override
            public void onSuccess(UserProfile profile) {
                // Display the user profile
            }

            @Override
            public void onFailure(ManagementException error) {
                //show error
            }
        });
```


## Access the Data Inside the Received Profile

## Access The Data Inside The UserProfile

##### I. DEFAULT INFO

At this point, you already have access to the `UserProfile`.
You can use this data wherever you need it.

Some examples are:

```java
profile.getName();
profile.getEmail();
profile.getPictureURL();
```

::: panel Modifying the UI
You cannot modify the UI inside the `onSuccess()` method because the method works in a second thread. To solve this issue, you can choose between three options:
* Persist the data
* Create a task in the UI thread
* Create a handler to receive the information
:::

#### I. ADDITIONAL INFO

Besides the defaults, you can handle more information that is contained within any of the following `map`:

##### A. USER METADATA

The `userMetadata` map contains fields related to the user profile that can be added from the client-side (e.g. when editing the profile). This tutorial explains how to achieve this. You can access its fields as follows:

```java
String country = (String) profile.getUserMetadata().get("country");
```

::: note
You can choose the names and value types of the keys you use for subscripting the `user_metadata` map.
:::

##### B. APP METADATA

The `appMetadata` map contains fields that are usually added via a Rule or Hook, which is read-only for the native platform.

##### C. EXTRA INFO

The `extraInfo` map contains additional values that are stored in Auth0 but not mapped to any getter method in the `UserProfile` class. For native platforms, this information is read-only.

::: note
For further information on metadata, see the full documentation.
:::

## Update the User Profile

You can only update the user metadata. In order to do so you must create a `Map<String, Object>` and add the new metadata:

```java
Map<String, Object> userMetadata = new HashMap<>();
userMetadata.put("country", "USA");
```

And then with the `UsersAPIClient`, perform the update:

```java
// app/src/main/java/com/auth0/samples/activities/MainActivity.java

String idToken = CredentialsManager.getCredentials(this).getIdToken();
UsersAPIClient usersClient = new UsersAPIClient(auth0, idToken);
usersClient.updateMetadata(userInfo.getId(), userMetadata).start(new BaseCallback<UserProfile, ManagementException>() {
  @Override
  public void onSuccess(final UserProfile profile) {
    // As receive the updated profile here
    // You can react to this, and show the information to the user.
  }

  @Override
  public void onFailure(ManagementException error) {
    //show error
  }
});
```
