---
layout: post
title: 'Roles in Auth0'
date: '2023-09-19T06:00:00.000-04:00'
author: Tom
tags:
- Auth0
- OAuth2
---
[Auth0][auth0] is a service that provides authentication and authorization services for web applications. It supports
[OAuth2][oauth2] and [OpenID Connect][openid-connect] protocols. I have been using it for a couple of years at work and
have run into idiosyncrasies that have taken me a while to figure out. This post will cover one of those idiosyncrasies.

I was helping to add Auth0 to an existing web application. The application already had a concept of roles, but we wanted
to administer those roles in Auth0. Auth0 has a concept of roles, but it requires the use of [Auth0 organizations][organizations].
The team did not want to bring in the concept of organizations. We were able to assign roles to users in Auth0, but we
needed the roles to be available in the application.

Auth0 provides a concept called [actions][actions] for hooking into the authentication process at various extension
points and running custom code. I created an action that would run after a user was authenticated, at the
[post-login][post-login] event, and would add the user's roles as custom claims in the access token. The application
could then read the roles from the access token.

The action code looked something like the following:

```javascript
exports.onExecutePostLogin = async (event, api) => {
  // if you support multiple clients, but only want to add roles for the ones with names that start with a certain prefix
  const expectedClientPrefixRegex = /^client-prefix/;
  if (!expectedClientPrefixRegex.test(event.client.name)) return;
  
  // any claims that are added to the access token must be namespaced with a URI
  const namespace = 'https://example.com';
  const roles = event.authorization?.roles ?? [];
  api.accessToken.setCustomClaim(`${namespace}/roles`, roles);
};
```

We did not have to introduce the concept of organizations, and we were able to get the roles into the application.

[auth0]: https://auth0.com/
[oauth2]: https://oauth.net/2/
[openid-connect]: https://openid.net/connect/
[organizations]: https://auth0.com/docs/manage-users/organizations
[actions]: https://auth0.com/docs/customize/actions
[post-login]: https://auth0.com/docs/customize/actions/flows-and-triggers/login-flow