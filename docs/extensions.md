# ASGI Extensions

**Uvicorn Experimental** implements some of the ASGI extensions.

## HTTP Trailers

HTTP trailers are a feature of HTTP/1.1 that allow additional headers to be sent after
the response body has been sent.

The implementation on **Uvicorn Experimental** follows what is defined in the **[ASGI specification]**.

You can read more about what you can do with it on this **[blog post]**.

As an example of application:

```python
async def app(scope, receive, send) -> None:
    await send({
        "type": "http.response.start",
        "status": 200,
        "headers": [(b"transfer-encoding", b"chunked"), (b"te", b"trailers")],
        "trailers": True,
    })
    await send(
        {"type": "http.response.body", "body": b"Hello, world!", "more_body": False}
    )
    await send(
        {
            "type": "http.response.trailers",
            "headers": [(b"x-trailer-test", b"test")],
            "more_trailers": False,
        }
    )
```

Be aware that "te" headers needs to be sent on the `http.response.start`, otherwise **Uvicorn Experimental**
will not send the trailers.


[ASGI Specification]: https://asgi.readthedocs.io/en/latest/extensions.html#http-trailers
[blog post]: https://www.fastly.com/blog/supercharging-server-timing-http-trailers
