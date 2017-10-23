## Before you start

This guide walks you through setting up authentication and authorization in your ${library} apps with Auth0. If you are new to Auth0, check our [Overview](https://auth0.com/docs/overview). For a complete picture of authentication and authorization for all Single Page Applications, check our [SPA + API documentation](https://auth0.com/docs/architecture-scenarios/application/spa-api).

Auth0 uses OAuth. If you want to learn more about the OAuth flows used by Single Page Applications, read about the [Implicit Grant Flow](https://auth0.com/docs/api-auth/tutorials/implicit-grant).

<%= include('../../../_includes/_new_app') %>

<%= include('../../../_includes/_callback_url') %>

::: note
If you are following along with the sample project,  **Allowed Callback URLs** should be set to `${callback}`.
:::

<%= include('../_includes/_install_auth0js') %>