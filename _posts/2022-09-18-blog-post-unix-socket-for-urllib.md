## Extend urrlib http connection with local unix socket

By default Python [urllib](https://docs.python.org/3/library/urllib.html) library don't support connection via local unix socket, because [http.client](https://docs.python.org/3/library/http.client.html#module-http.client) library, which is used by `urllib` set [IPPROTO_TCP](https://github.com/python/cpython/blob/3.7/Lib/http/client.py#L949) socket type.

In this blog post I will explain how to use local unix socket for your urllib connection.

Why would we need it? In situation you connect to remote host via Ansible using SSH, and later using authenticated sockets.
So it's a way of using secure connection via SSH and not exposing the password via HTTP. The reason we had to use it,
was for [suds](https://github.com/suds-community/suds) SOAP client for SAP management. SAP administrators are not used to use
passwords for SOAP, but are rather used to SSH to SAP machine and execute the specific command, which is levaraing the local unix socket.

---

### Extending the urllib

urllib has handler mechanism. You can write custom handler to handle the connections. We will create the custom handler which will
use the local unix socket instead of the TCP socket.

#### Urllib handler

```python
import http.client
import socket
import urllib.request


class LocalHttpConnection(http.client.HTTPConnection):
    def __init__(self, host, port=None, timeout=socket._GLOBAL_DEFAULT_TIMEOUT,
                 source_address=None, socketpath=None):
        super().__init__(host, port, timeout, source_address)
        self.sock = socket.socket(socket.AF_UNIX, socket.SOCK_STREAM)
        self.sock.connect(socketpath)


class ExtendedHandler(urllib.request.HTTPHandler):

    def __init__(self, debuglevel=0, socketpath=None):
        self._debuglevel = debuglevel
        self._socketpath = socketpath

    def http_open(self, req):
        return self.do_open(LocalHttpConnection, req, self._socketpath)
```

### suds client

The [suds](https://github.com/suds-community/suds) client is a simple SOAP client. The suds client support custom transport method.
To use our custom handler in the suds client, just pass the our handler in the suds contructor.

#### Final code

{% gist eb1abd1c73024911adb2668b3cfbab30 %}
