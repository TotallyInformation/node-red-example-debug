Create a subflow in Node-Red called "Web Debug" and import the following flow:

```
[{"id":"3a35caa3.80b476","type":"mqtt-broker","broker":"localhost","port":"1883","clientid":""},{"id":"cf00fc5e.675d58","type":"function","name":"","func":"var msg1 = {};\nif ('topic' in msg) {\n    msg1.topic = \"DEBUG/\" + msg.topic;\n} else {\n    msg1.topic = \"DEBUG/No-Topic-Set\";\n}\nmsg1.payload = msg.payload;\nreturn msg1;","outputs":1,"valid":true,"x":230,"y":80,"z":"a340a063.ee835","wires":[["1f73e116.d6567f"]]},{"id":"1f73e116.d6567f","type":"mqtt out","name":"/DEBUG/#","topic":"","qos":"0","retain":"false","broker":"3a35caa3.80b476","x":422.5,"y":80,"z":"a340a063.ee835","wires":[]}]
```
