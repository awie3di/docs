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

<<<<<<< HEAD
Be sure that you have completed the [Login](/quickstart/native/android/00-login) and the [Session Handling](/quickstart/native/android/03-session-handling) Quickstarts. You'll need a valid `access_token` and `id_token` to call the API clients.

Before you launch the login process, make sure you will get a valid profile from the authorization server. To do that, ask for the `openid profile email` scope. Find the snippet in which you initialize the `WebAuthProvider` class. To that snippet, add the line `withScope("openid profile email")`.
=======
::: note
Before you continue with this tutorial, make sure that you have completed the [Login](/quickstart/native/android/00-login) and [Session Handling](/quickstart/native/android/03-session-handling) tutorials. To call the API clients, you need a valid access token and ID token.
:::

Before launching the login process, you need to make sure you get a valid profile from the authorization server. To do that, ask for the `openid profile email` scope. Find the snippet in which you initialize the `WebAuthProvider` class. To that snippet, add the line `withScope("openid profile email")`.
>>>>>>> ad35149da0903801caee82cf308defe2e0254ae2

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

<<<<<<< HEAD

Use the access token and the `AuthenticationAPIClient` client to obtain the user's ID. 

When you call the `AuthenticationAPIClient` client, it returns an instance of the `UserProfile` profile. This profile is a basic, OIDC-conformant profile, which guarantees only the `sub` claim. The `sub` claim contains the user's ID. Depending on the scope you requested, the remaining claims returned by the `UserProfile` are different. 

=======
To get the user's information:
1. Use the user's access token to call the `userInfo` method in the `AuthenticationAPIClient` client instance.
You get an instance of the `UserProfile` profile. The profile is OIDC-conformant. Depending on the on the [scopes](/scopes/current) you requested, the profile contains different information. 
2. To get the user's full profile, use the [Management API](/api/management/v2#!/Users).
>>>>>>> ad35149da0903801caee82cf308defe2e0254ae2

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

When you get the `sub` value, call the [Management API](https://auth0.com/docs/api/management/v2#!/Users).

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

##### I. DEFAULT INFO

At this point, you already have access to the `UserProfile` instance.
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

The `userMetadata` map contains user profile fields that can be added from the client-side (e.g. when a user edits their profile). You can access this information in the following way:

```java
String country = (String) profile.getUserMetadata().get("country");
```

::: note
You can choose the key names and value types for subscripting the `user_metadata` map.
:::

##### B. APP METADATA

The `appMetadata` map contains fields that are usually added via a Rule or Hook, which is read-only for the native platform.

::: note
For further information on metadata, see the full documentation.
:::

#### C. Extra information

The `extraInfo` map contains additional values that are stored in Auth0 but not mapped to a `UserProfile` getter method. For native platforms, this information is read-only.

## Update the User's Profile

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
