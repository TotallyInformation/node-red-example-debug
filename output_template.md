This file shows the contents of the output_template template node.

```html
<!DOCTYPE html> 
<html>
<head>
	<title>{{payload.pgTitle}}</title>
	<meta name="viewport" content="width=device-width, initial-scale=1">
	<meta charset="utf-8">
	<meta http-equiv="X-UA-Compatible" content="IE=edge" />
	{{! Supports old browsers AND detects mobile browsers: install via Bower }}
	<script src="/bower_components/modernizer/modernizr.js"></script>
	<style>
	    body {font-family:sans-serif;font-size:93%;}
	    #output >section >p {color:silver;font-size:83%;}
	    #output >section >pre {font-family:"Lucida Console", monospace;}
	</style>
</head><body>
    <h1>Node-Red Debug Page</h1>
    <div id="output"></div>
    
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/2.1.3/jquery.min.js"></script>
    <script src="/js/jquery.animate-shadow-min.js"></script>
    {{! Load mqtt.js to get MQTT over web services }}
    {{! @see http://git.eclipse.org/c/paho/org.eclipse.paho.mqtt.javascript.git/plain/src/mqttws31.js }}
    <script src="/js/mqttws31.js"></script>
    {{! Load startup script }}
    {{{payload.script1}}}
</body>
</html>
```
