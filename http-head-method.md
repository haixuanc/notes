## Definition

- The HEAD method is identical to GET except that the server MUST NOT return a message-body in the response. The metainformation contained in the HTTP headers in response to a HEAD request SHOULD be identical to the information sent in response to a GET request.
- This method can be used for obtaining metainformation about the entity implied by the request without transferring the entity-body itself.
- This method is often used for testing hypertext links for validity, accessibility, and recent modification.

## Usage 1. Existence checks

In cases where the content of a resource is not particularly important, but the existence of the resource is, HEAD is perfect. We can do everything just like a GET and check the response code, without the weight of the response body.

```bash
HEAD http://api.twitter.com/1/users/show.json?screen_name=jasonh_n_austin

# The server responds:
200 OK
# or
404 OK
```

## Usage 2. Cache checks

### Last-Modified header

`Last-Modified` header is meant to be used by a client to check if a cached resource is still valid.

A demo:

```bash
# Step 1. Send a GET request
curl -i https://api.github.com/users/jasonh-n-austin

# Step 2. Receives a response
HTTP/1.1 200 OK
Server: GitHub.com
Date: Thu, 14 Feb 2013 04:52:06 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Status: 200 OK
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 50
Vary: Accept
Cache-Control: public, max-age=60, s-maxage=60
Last-Modified: Tue, 12 Feb 2013 02:58:46 GMT
ETag: "f3effd3e870e79f8aed863cf8c8b319f"
X-GitHub-Media-Type: github.beta
X-Content-Type-Options: nosniff
Content-Length: 1482

{
	"login": "jasonh-n-austin",
	"id": 1305160
}

# Step 3. Send a HEAD request using the `Last-Modified` header in the last received response to check if resource has changed
curl --head -i https://api.github.com/users/jasonh-n-austin -H "If-Modified-Since: Tue, 12 Feb 2013 02:58:46 GMT"

# Step 4.a Receives a `304 Not Modified` response.
# The cached resource is still valid.
HTTP/1.1 304 Not Modified
Server: GitHub.com
Date: Thu, 14 Feb 2013 04:52:32 GMT
Connection: keep-alive
Status: 304 Not Modified
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 50
Vary: Accept
Cache-Control: public, max-age=60, s-maxage=60
Last-Modified: Tue, 12 Feb 2013 02:58:46 GMT
X-Content-Type-Options: nosniff

# Step 4.b Receives a `200 OK` response.
# The remote resource has changed. The cached resource is stale. We will need to send another GET request.
curl -i https://api.github.com/users/jasonh-n-austin
...
```

### Etag header

`Etag` header is meant to be used by a server to determine if a requested resource has changed:

- If not changed, the server can send back a `304 Not MOdified` response.
- If changed, the server can send back a `200 OK` response.

`Etag` header is first set by the server in a response. Then `Etag`s are cached by the web browser, and returned with subsequent requests for the same resource.

## References

- [RESTful Patterns for the HEAD Verb](http://www.pragmaticapi.com/blog/2013/02/14/restful-patterns-for-the-head-verb)
- [ETags](http://blog.existy.me/http/2015/01/07/etags/)
