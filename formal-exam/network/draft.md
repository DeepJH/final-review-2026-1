第一次握手：客户端发送连接请求。SYN = 1 seq = random-x。SYN-SEND
第二次握手：服务端发送确认请求。SYN = 1 ACK = 1 seq = random-y ack = random-y + 1。SYN-RCVD
第三次握手：客户端发送最终确认。ACK = 1 ack = random-y + 1。ESTABLISHED