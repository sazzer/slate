# Discovery

## WebFinger

Discovery of the WebSockets MUD API starts with a [WebFinger](https://webfinger.net/) request to the domain running the MUD. In the WebFinger response, a *rel* of *urn:mud:websocket*. The *resource* parameter is not important, but might be important for other WebFinger resources on the same domain, such as OpenID Connect.

```http
GET /.well-known/webfinger?resource=acct%3Amudadmin%40mud.example.com&rel=urn%3Amud%3Awebsocket HTTP/1.1
Host: mud.example.com
Accept: application/jrd+json
```

A successful response to this will return a JSON Resource Descriptor including the base URL for the actual MUD server, under which the MUD Introspection Document can be loaded.

```http
HTTP/1.1 200 OK
Content-Type: application/jrd+json

  {
   "subject": "mudadmin@mud.example.com",
   "links":
    [
     {
      "rel": "urn:mud:websocket",
      "href": "https://mud.example.com"
     }
    ]
  }
```
```json
  {
   "subject": "mudadmin@mud.example.com",
   "links":
    [
     {
      "rel": "urn:mud:websocket",
      "href": "https://mud.example.com"
     }
    ]
  }
```
