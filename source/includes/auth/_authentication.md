# Authentication

In order to play on a MUD, the server will need to know who you are. This is achieved by the end user authenticating against the MUD server in some form.

There are two standard mechanisms that are supported for authentication, and all servers will use exactly one of these two.

* simple - Simple Authentication is the most straightforward, and simply means that the client will collect a username and password from the player, and send them to the server.
* oauth2 - OAuth2 Authentication is more complicated to implement, both for the Server and the Client, but gives advantages in terms of making it more secure regarding how the authentication occurs, making it more flexible what authentication mechanisms can be supported, and making it possible to support federated authentication - i.e. authenticating against a third-party provider.

Note that OpenID Connect as an extension to OAuth2 is both supported and recommended, since this adds some extra details to make the OAuth2 workflow more secure.

## Authenticating the user
All of the REST requests that exist as part of this specification are able to be called without any authentication required at all. It is possible that end servers may define additional endpoints beyond this specification that do require authentication, but that is beyond the scope of this document.

The WebSockets connection does require authentication, and until this is successful no other commands on this connection will be successful. If multiple WebSockets connections are used for any reason then each will need to be individually authenticated.

Authentication on the WebSockets connection is achieved by sending a command message that contains the appropriate details, and waiting for a success or failure response. If an authentication response message is not received within a reasonable timeframe then the connection should be closed and re-established before trying again, as this may mean that messages are not being received correctly.

## Simple Authentication
Simple Authentication assumes that all users will be identified by a Username and Password pair. This specification makes no requirements on the validity of either Username or Password, but the actual end server may be different.

This is achieved by sending an *authenticate* command, using a mode of *simple* to indicate that Simple Authentication is being used.

```json
{
  "type": "authenticate",
  "mode": "simple",
  "username": "mudadmin",
  "password": "Super5ecretPa55word"
}
```
Field | Required | Description
------|----------|------------
type | Yes | Indicates that this is an "authenticate" command
mode | Yes | Set to "simple" for Simple Authentication
username | Yes | The username of the user
password | Yes | The password of the user

## OAuth2 Authentication
OAuth2 Authentication assumes that users will be externally authenticated, and that as a result of this a valid OAuth2 Bearer Token will be available. The mechanisms by which this token is obtained are beyond the scope of this document.

TODO: Describe introspection of OAuth2 Authentication
TODO: Describe scopes of OAuth2 Tokens

```json
{
  "type": "authenticate",
  "mode": "bearer",
  "token": "abc123"
}
```
Field | Required | Description
------|----------|------------
type | Yes | Indicates that this is an "authenticate" command
mode | Yes | Set to "bearer" for Bearer Token Authentication
token | Yes | The Bearer Token to use for Authentication

## Success response
On a successful Authentication, a message will be sent back to the client indicating that the session is now authenticated and other commands can be sent.

```json
{
  "type": "authenticated",
  "state": true
}
```
Field | Required | Description
------|----------|------------
type | Yes | Indicates that this is an indication of the authentated state of the client
state | Yes | True indicates that the client is now authenticated

## Error response
On a failed Authentication, a message will be sent back to the client indicating that the session is not authenticated, and with additional information describing why the authentication failed

```json
{
  "type": "authenticated",
  "state": false,
  "reason": "INVALID_USER"
}
```

Field | Required | Description
------|----------|------------
type | Yes | Indicates that this is an indication of the authentated state of the client
state | Yes | False indicates to the client that authentication failed
reason | Yes | An error code describing why authentication failed

### Error codes
Reason | Description
-------|------------
INVALID_USER | The user credentials - either Username and Password for Simple Authentication, or the Token for OAuth2 Authentication - were not valid. This could indicate that the username or password was incorrect, or the token was malformed or not issued by a trusted provider, or many other reasons that indicate that the credentials are not valid
UNSUPPORTED_MODE | The *mode* parameter was not one of the supported values
INVALID_REQUEST | One of the required fields was not present
