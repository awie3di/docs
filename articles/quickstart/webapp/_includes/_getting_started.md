## Before you start

This guide walks you through setting up authentication and authorization in your ${library} apps with Auth0. I

f you are new to Auth0, we suggest you read our [Overview](/overview) first. For a complete picture of authentication and authorization for regular web apps, check read the [Single Sign-On for Regular Web Apps](/architecture-scenarios/application/web-app-sso#authentication-flow) documentation.

Auth0 uses OAuth. If you want to learn more about the OAuth flows used by regular web apps, read about [Authentication for Server-side Web Apps](/client-auth/current/server-side-web).

<%= include('../../../_includes/_new_app') %>

<%= include('../../../_includes/_callback_url') %>

::: note
If you are following along with the sample project you downloaded from the top of this page, set **Callback URL** to
`${callback}`.
:::
