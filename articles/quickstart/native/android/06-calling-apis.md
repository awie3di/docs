---
title: Calling APIs
description: This tutorial will show you how to use the Auth0 tokens to make authenticated API calls.
seo_alias: android
budicon: 546
---

This tutorial demonstrates how to use a previously saved token to authenticate your API calls.

<%= include('../../../_includes/_package', {
  org: 'auth0-samples',
  repo: 'auth0-android-sample',
  path: '06-Calling-APIs',
  requirements: [
    'Android Studio 2.3',
    'Android SDK 25',
    'Emulator - Nexus 5X - Android 6.0'
  ]
}) %>__

## Before Starting

Before you continue with this tutorial, make sure that you have completed the previous tutorials. This tutorial assumes that:
* You have completed the [Session Handling](/quickstart/native/android/03-session-handling) tutorial and you know how to handle the `Credentials` object.
* You have set up a backend application as API. To learn how to do it, follow one of the [backend tutorials](https://auth0.com/docs/quickstart/backend). 

After you set up an API, declare the endpoint you need to call as a constant in the current class:

```java
// app/src/main/java/com/auth0/samples/LoginActivity.java

private static final String API_URL = "localhost:8080/secure";
```

## Get the User's Access Token

Your first step is to get an `access_token`. Use the basic [Login](/quickstart/native/android/00-login) tutorial if you need some guidance.

Create an instance of the `OkHttpClient` client and a new `Request`. Use the provided builder to customize the `Http` method, the URL and the headers in the request. Set the **Authorization** header with the token type and the user's access token.

::: note
Depending on the standards in your API, you configure the authorization header differently. The code below is just an example.
:::

To prepare the request in this example we use the [OkHttp](https://github.com/square/okhttp) library. Create the `OkHttpClient` instance and a new `Request`. We use the provided builder to customize the request Http method, URL and headers. Here we set the **Authorization Header** with the token type and the `access_token` that identifies the logged-in user.

```java
// app/src/main/java/com/auth0/samples/LoginActivity.java

OkHttpClient client = new OkHttpClient();
Request request = new Request.Builder()
    .get()
    .url(API_URL)
    .addHeader("Authorization", "Bearer " + accessToken);
    .build();
```

::: note
Notice that how you configure your authorization header should match the standards that you're using in your API, this is just an example of what it could look like.
:::

## Send the Request

Tell the client to create a new `Call` with the request you created. Call the `enqueue` function to execute the request asynchronously.

```java
// app/src/main/java/com/auth0/samples/LoginActivity.java

client.newCall(request).enqueue(new Callback() {
    @Override
    public void onFailure(Request request, IOException e) {
        //show error
    }

    @Override
    public void onResponse(final Response response) throws IOException {
        if (response.isSuccessful()) {
            //API call success
        } else {
            //API call failed. Check http error code and message
        }
    }
});
```

Check if the request was made and if the response that came back was what you expected. 

You need to configure your server side to protect your API endpoints with the key for your Auth0 client. In this example, you can use the user's access token issued by Auth0 to call Auth0 APIs.
