---
layout: wp
title: websocket, tcp
---

some different object: i will write offently, every technology word will be an article.  

 i will talk about: [websocket protocol](https://tools.ietf.org/html/rfc6455).     

websocket 协议包括两部分内容：   

* handshake
* data transfer   

#### handshake ####

通过 http 协议发送一个 协议升级的 chat：   

```
The handshake from the client looks as follows:

```
curl -v -i -H "Connection: Upgrade" -H "Upgrade: websocket" -H "Host: echo.websocket.org" -H "Origin: http://www.websocket.org" http://echo.websocket.org

        GET /chat HTTP/1.1
        Host: server.example.com
        Upgrade: websocket
        Connection: Upgrade
        Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
        Origin: http://example.com
        Sec-WebSocket-Protocol: chat, superchat
        Sec-WebSocket-Version: 13
   The handshake from the server looks as follows:

        HTTP/1.1 101 Switching Protocols
        Upgrade: websocket
        Connection: Upgrade
        Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
        Sec-WebSocket-Protocol: chat
```

一旦客户端和服务器端都发送了 handshakes 并成功连接， data transfer 开始。

server code 101:   
        HTTP/1.1 101 Switching Protocols  
状态码： 101 表示 handshake 完成  

WebSocket 设计哲学是用最小化 framing. 

> The WebSocket Protocol is an independent TCP-based protocol.  Its
   only relationship to HTTP is that its handshake is interpreted by
   HTTP servers as an Upgrade request.

   By default, the WebSocket Protocol uses port 80 for regular WebSocket
   connections and port 443 for WebSocket connections tunneled over
   Transport Layer Security (TLS)


#### Data Framing ####

```

      0                   1                   2                   3
      0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
     +-+-+-+-+-------+-+-------------+-------------------------------+
     |F|R|R|R| opcode|M| Payload len |    Extended payload length    |
     |I|S|S|S|  (4)  |A|     (7)     |             (16/64)           |
     |N|V|V|V|       |S|             |   (if payload len==126/127)   |
     | |1|2|3|       |K|             |                               |
     +-+-+-+-+-------+-+-------------+ - - - - - - - - - - - - - - - +
     |     Extended payload length continued, if payload len == 127  |
     + - - - - - - - - - - - - - - - +-------------------------------+
     |                               |Masking-key, if MASK set to 1  |
     +-------------------------------+-------------------------------+
     | Masking-key (continued)       |          Payload Data         |
     +-------------------------------- - - - - - - - - - - - - - - - +
     :                     Payload Data continued ...                :
     + - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - +
     |                     Payload Data continued ...                |
     +---------------------------------------------------------------+

```

看下发送的代码   


```
    def format(self):
        """
        format this object to string(byte array) to send data to server.
        """
        if any(x not in (0, 1) for x in [self.fin, self.rsv1, self.rsv2, self.rsv3]):
            raise ValueError("not 0 or 1")
        if self.opcode not in ABNF.OPCODES:
            raise ValueError("Invalid OPCODE")
        length = len(self.data)
        if length >= ABNF.LENGTH_63:
            raise ValueError("data is too long")

        frame_header = chr(self.fin << 7
                           | self.rsv1 << 6 | self.rsv2 << 5 | self.rsv3 << 4
                           | self.opcode)
        if length < ABNF.LENGTH_7:
            frame_header += chr(self.mask << 7 | length)
            frame_header = six.b(frame_header)
        elif length < ABNF.LENGTH_16:
            frame_header += chr(self.mask << 7 | 0x7e)
            frame_header = six.b(frame_header)
            frame_header += struct.pack("!H", length)
        else:
            frame_header += chr(self.mask << 7 | 0x7f)
            frame_header = six.b(frame_header)
            frame_header += struct.pack("!Q", length)

        if not self.mask:
            return frame_header + self.data
        else:
            mask_key = self.get_mask_key(4)
            return frame_header + self._get_masked(mask_key)
```


#### tcp server ####

写一个 tcp server 来看一下， socket 通信过程

```
# coding: utf-8
import socket
import select
import threading

class TCPServer():
    def __init__(self, host, port):
        self.socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        self.socket.bind((host, port))
        self.socket.listen(10)

    def fileno(self):
        return self.socket.fileno()

    def serve_forever(self,):
        while True:
            r, w, e = select.select([self], [], [], 0.5)
            if self in r:
                self.handle_request()

    def handle_request(self, ):
        try:
            request_conn, client_address = self.socket.accept()
        except socket.error:
            return
        self.multi_process_request(request_conn, client_address)

    def multi_process_request(self, conn, addr):
        t = threading.Thread(target=self.process_request, args=(conn, addr))
        t.daeamon = False
        t.start()

    def process_request(self, conn, addr):
        data = conn.recv(1024)
        if data:
            text = """
HTTP/1.1 302 Moved Temporarily
Content-Type: text/html
Location: https://www.google.com/
Proxy-Connection: Keep-alive

"""
            print data
            conn.send(text)
        else:
            conn.close()
        return

if __name__ == "__main__":
    server = TCPServer("0.0.0.0", 9008)
    server.serve_forever()
```


