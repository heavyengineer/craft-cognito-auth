# Craft Cognito Auth plugin

Enable authentication to Craft with Amazon Cognito using [JSON Web Tokens](https://jwt.io/) (JWT).

![Screenshot](resources/img/cognito+jwt.png)

## Requirements

This plugin requires Craft CMS 3.3 or later.

## Installation

To install the plugin, follow these instructions.

1. Open your terminal and go to your Craft project:

    cd /path/to/project

2. Then tell Composer to load the plugin:

    composer require structureit/craft-cognito-auth

3. In the Control Panel, go to Settings → Plugins and click the “Install” button for Craft Cognito Auth.

## Craft Cognito Auth Overview

From the [official JWT website](https://jwt.io/):

    JSON Web Tokens are an open, industry standard RFC 7519 method for representing claims securely between two parties.

This plugin enables logging into Craft using an Amazon Cognito User Pool. Uses Cognito JWT "Implicit Grant" (`type=token`) requests.

## Configuring Craft Cognito Auth

Once installed, naviate to the settings page of the plugin and enter required settings to activate the plugin:

| Setting                                                  | Description                                                                                            |
| -------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| `Auto create users`                                      | Optional. Activate to enable auto-creation of a public user when provided a verifiable JWT.            |
| `Auto create users when Public Registration is disabled` | Optional. Activate to enable auto-creation of a user even when Public Registration is disabled.        |
| `Default user group`                                     | Optional. Set to a group to add newly-created users to.                                                |
| `Enable CP login button`                                 | Optional. Enable to add a button to the CP login screen to login using Cognito.                        |
| `Customize login button text`                            | Optional. Used to override the default text on the Login with Cognito button.                          |
| `Redirect URL`                                           | Optional. Used to redirect the user after they have logged in, default is the current site's base url. |
| **AWS Settings**                                         |
| `User Pool Region`                                       | Required. The AWS Region where the User Pool is hosted.                                                |
| `App Domain`                                             | Optional. Used to generate the login link for the Login with Cognito button.                           |
| `App Client ID`                                          | Required. Used to generate the login link, and to verify the JWT was created for the correct pool.     |
| `User Pool ID`                                           | Required. Used to get the key Cognito used to sign the JWT.                                            |

## Using Craft Cognito Auth

The plugin will attempt to verify incoming requests to the control panel URL (usually `/admin`) `/cognitologin` with a JWT present in the parameters.

The plugin will get the Json Web Token Set (JWKS) from Cognito,
and use it to attempt to verify the token using the [lcobucci/jwt](https://github.com/lcobucci/jwt) package for PHP.
The package adheres to the [IANA specifications](https://www.iana.org/assignments/jwt/jwt.xhtml) for JWTs.

If a provided token can be verified AND
can be matched to a user account with an email address matching the provided `email` key
or a username matching the provided `cognito:username` key,
the user will be authenticated and the request allowed to continue.

If the token is verifiable but a matching user account does NOT exist, but:

- the `Auto create user` setting is enabled AND public registration is enabled
- OR the `Auto create user` setting is enabled, public registration is disabled, but the `Auto create users when Public Registration is disabled` setting is enabled

in the Craft settings, a new user account will be created on-the-fly and the new user then logged in.

## Craft Cognito Auth Roadmap

### Features

The plugin does or will offer the following features:

- [x] Validate incoming requests with a JWT present to a specific route (`cognitologin` under the CP url).
- [x] Inject Javascript to get the Cognito-provided JWT from the SearchParameters (`#id_token=`) into the QueryParameters (`?jwt=`).
- [x] Check the signature against Amazon's Certificate for the configured User Pool.
- [x] Match a validated JWT to a user account in Craft CMS and login as that user.
- [x] Optionally create a new account if no existing account can be found.
- [x] Optionally add Login with Cognito button to the CP Login screen.
- [x] Add `logout-redirect` route to the control panel to redirect back to the page the request came from.
- [x] Allow using environment variables for text-input settings.

Original JWT auth written by [Mike Pierce](https://michaelpierce.trade) and made possible by [Edenspiekermann](https://edenspiekermann.com).
Converted to use Cognito JWTs and verification flow by [Matthew Carter](https://github.com/Walkman100).
Maintained by [StructureIT devs](https://github.com/StructureIt).
