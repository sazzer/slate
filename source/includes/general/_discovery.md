# Discovery

## WebFinger

```http
GET /.well-known/webfinger?resource=acct%3Amudadmin%40mud.example.com&rel=urn%3Amud%3Awebsocket HTTP/1.1
Host: mud.example.com
Accept: application/jrd+json
```

Discovery of the WebSockets MUD API starts with a [WebFinger](https://webfinger.net/) request to the domain running the MUD. In the WebFinger response, a *rel* of *urn:mud:websocket*. The *resource* parameter is not important, but might be important for other WebFinger resources on the same domain, such as OpenID Connect.

```http
HTTP/1.1 200 OK
Content-Type: application/jrd+json
```
```json
{
  "subject": "mudadmin@mud.example.com",
  "links": [
    {
      "rel": "urn:mud:websocket",
      "href": "https://mud.example.com"
    }
  ]
}
```

A successful response to this will return a JSON Resource Descriptor including the base URL for the actual MUD server, under which the MUD Introspection Document can be loaded.

## Introspection

The MUD Introspection Document is a JSON Document that describes all of the details of the MUD that any particular client might need to know in order to work with it. These are not strictly aimed at interactive clients, though obviously they are a major use case, but can also be aimed at other forms of client that might aggregate data together or display semi-static data - e.g. the MUD web site might make use of it.

```http
GET /.well-known/mud-configuration HTTP/1.1
Host: mud.example.com
Accept: application/json
```

The Introspection document is always available at a path of */.well-known/mud-configuration* underneath the HREF returned by the WebFinger request. Often this is simply going to be a URL directly under the domain that the MUD runs on, but this is not a requirement.

```http
HTTP/1.1 200 OK
Content-Type: application/json
```
```json
{
  "name": "Example MUD",
  "contact": [
    "Mud Admin <mudadmin@mud.example.com>"
  ],
  "authentication_type": "simple",
  "websocket_uri": "wss://mud.example.com/ws",
  "registration_uri": "https://mud.example.com/register",
  "tos_uri"

}
```
Field | Required | Description
------|----------|------------
name | Yes | The name of the MUD
contact | Yes | List of contact people for administration concerns
authentication_type | Yes | The mechanism to use to authenticate users
websocket_uri | Yes | The URI of the WebSocket endpoint to use for the MUD
registration_uri | No | The URI to use to register new accounts, if supported.
tos_uri | No | The URI to the Terms of Service for using the MUD
