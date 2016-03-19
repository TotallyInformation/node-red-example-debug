This is the code from the `script1` template node. This is used to define the browser script that 
connects from the browser to your MQTT broker via the Apache Paho MQTT JavaScript library.

```
<script>
var mqttStart = function() {
	// @see http://www.eclipse.org/paho/files/jsdoc/symbols/Paho.MQTT.Client.html
	
	var reconnectTimeout = 2000; 
	
	// Create a client instance
	var mqtt = new Paho.MQTT.Client( location.hostname, 9001, "web_" + parseInt(Math.random() * 100, 10) );

	// @see http://www.eclipse.org/paho/files/jsdoc/symbols/Paho.MQTT.Client.html#connect
	var options = {
		timeout: 3,
		//userName: "xxxxx",
		//password: "xxxxx",
		//useSSL: useTLS,
		cleanSession: true,
		onSuccess: onConnect,
		onFailure: onConnectionLost
	};
	// set callback handlers
	mqtt.onConnectionLost = onConnectionLost;
	mqtt.onMessageArrived = onMessageArrived;

	// connect the client
	mqtt.connect(options);

	// called when the client connects
	function onConnect() {
		// Once a connection has been made, make a subscription and send a message.
		console.log("onConnect");

    	// Subscribe to required msgs
    	mqtt.subscribe("DEBUG/#");
		
		// Send something (test)
		var message = new Paho.MQTT.Message("Hello");
		message.destinationName = "/World";
		mqtt.send(message); 
	}

	// called when the client loses its connection
	function onConnectionLost(responseObject) {
		$('#status').val("Connection failed or lost: " + responseObject.errorMessage + " (code: " + responseObject.errorCode + "). Retrying");
		if (responseObject.errorCode !== 0) {
			console.log("onConnectionLost:"+responseObject.errorMessage);
		}
		setTimeout(mqttStart, reconnectTimeout);
	}

	// called when a message arrives
	function onMessageArrived(message) {
		//console.log("onMessageArrived: " + message.payloadString + ", From: " + message.destinationName + " (QoS: " + message.qos +", Retained: " + message.retained + ")");
        console.log("MQTT Msg Recieved: " + message.destinationName + " (QoS: " + message.qos +", Retained: " + message.retained + ")");

    	// Get the topic
    	var topic = message.destinationName.replace(/^DEBUG\//,'');
    	// Make the topic ID friendly by replacing / with _ & whitespace with _
    	var topicId = topic.replace(/\/|\s/g,'_');
    	console.log( "Target Section ID: " + topicId );

    	// Create a timestamp for the latest update
    	var ts = new Date();
    	// Try to parse JSON input, assume string if this fails
    	var myobj = '';
    	try {
    	    myobj = JSON.stringify(JSON.parse(message.payloadString),null,2);
    	} catch(e) {
    	    myobj = message.payloadString;
    	}

    	// We want an output for EACH TOPIC - check if container exists, if not create it
    	var myDivs = $('#output').children('#' + topicId);
        if(myDivs.length === 0){
            myDivs = $('<section id="' + topicId + '"><h2>' + topic + '</h2><p></p><pre></pre></div>')
                .appendTo('#output');
        }
        // Now add the content
        $('#' + topicId + ' >p').
            fadeOut().
            text('Last Update: ' + ts).stop().
            css('boxShadow', '0 0 30px #FF5500').
            fadeIn().
            animate({boxShadow: '0 0 0 #000'});
        $('#' + topicId + ' >pre').
            text( myobj );
        	//html( syntaxHighlight(JSON.parse(message.payloadString)) );
    
	}
}; // --- End of function mqttStart --- //

function syntaxHighlight(json) {
    if (typeof json != 'string') {
         json = JSON.stringify(json, undefined, 2);
    }
    json = json.replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;');
    return json.replace(/("(\\u[a-zA-Z0-9]{4}|\\[^u]|[^\\"])*"(\s*:)?|\b(true|false|null)\b|-?\d+(?:\.\d*)?(?:[eE][+\-]?\d+)?)/g, function (match) {
        var cls = 'number';
        if (/^"/.test(match)) {
            if (/:$/.test(match)) {
                cls = 'key';
            } else {
                cls = 'string';
            }
        } else if (/true|false/.test(match)) {
            cls = 'boolean';
        } else if (/null/.test(match)) {
            cls = 'null';
        }
        return '<span class="' + cls + '">' + match + '</span>';
    });
};

// Only kick things off when the whole document is loaded
$( document ).ready(function() {
	mqttStart();
});
</script>
```
