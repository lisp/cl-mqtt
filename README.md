MQTT line protocol implementation library

[MQTT](http://en.wikipedia.org/wiki/MQTT) is a lightweight pubsub
messaging protocol. It's much simpler than complex messaging protocols
like AMQP but nevertheless may be quite handy for Internet of Things
and many other tasks. Among other things, it supports nice WebSocket
encapsulation which may be later added to this driver, too.

This library unbundles the cl-mqtt line protocol implementation from the
process management aspects of that library. (https://github.com/ivan4th/cl-mqtt)

Example code:
```cl
(defun test-it (host port)
  (let ((conn (mqtt:connect host :port port)))
    (bt:make-thread #'(lambda ()
                        (let ((subscription (mqtt:subscribe conn "/a/#")))
                          (loop until (null (let ((message (mqtt:receive subscription)))
                                              (when (plusp (length message))
                                                (format t "~%RECEIVED: ~s~%"
                                                        (babel:octets-to-string
                                                         (mqtt:mqtt-message-payload message)
                                                         :encoding :utf-8)))))))))
    (mqtt:publish conn "/a/b" "whatever1")
    (mqtt:publish conn "/a/b" "whatever2")
    (mqtt:publish conn "/a/b" "")
    (mqtt:disconnect conn)))

