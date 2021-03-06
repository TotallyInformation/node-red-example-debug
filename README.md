# node-red-example-debug

This is some example code to help with debugging Node-Red application flows.

To use it, you need a flow to output a webpage on "/debug", a subflow that outputs 
the msg object to MQTT, an MQTT broker that supports access over websockets 
(e.g. Mosquitto compiled with websockets or Mosca) and access to JQuery, Paho (MQTT web client) and the
[jquery-animate-shadow](http://www.bitstorm.org/jquery/shadow-animation/)
plugin for consumption in the web page.

- [Subflow](Web_Debug_Subflow.md). This adds a "DEBUG/" prefix to the msg.topic and outputs to MQTT.
  ![web_debug_subflow](https://cloud.githubusercontent.com/assets/1591850/7382163/85713a06-ee03-11e4-959f-026a14b09309.png)
- [Page Flow](Debug_Page_Flow.md). This listens for and outputs a web page on "/debug".
  ![debug_page_flow](https://cloud.githubusercontent.com/assets/1591850/7382150/5bcb12b2-ee03-11e4-8903-1bc3e2619631.png)

The resulting web page will be initially blank except for a title. However, it will be listening for output
from your MQTT broker on the "DEBUG/#" topic. Any recieved output will be dumped into an appropriate section element.
Each unique topic will get it's own output that will be updated on reciept of a new message for that topic.

Some JQuery animation magic can also be used to highlight which topic has just been updated.

The output of the message is formatted using the JavaScript JSON.stringify function if it is valid JSON, otherwise it is
treated as a simple string.

You have probably realised that the Page Flow could easily be simplified. It is this structure because I took it from another set of flows that I use to allow [dynamic form updates](https://github.com/TotallyInformation/node-red-example-liveupdates).

## Dependencies

- JQuery (Used from cdnjs in the code given)
- jquery.animate-shadow-min.js, a JQuery library
- [mqttws31.js](http://git.eclipse.org/c/paho/org.eclipse.paho.mqtt.javascript.git/plain/src/mqttws31.js), the Paho client library
- I've also used modernizer.js to support older browsers and detect mobile browsers. This probably isn't needed.

## Configuration

This example needs a few local files as shown in Dependencies above. You need to put these in a place that Express (the web server that Node-Red uses behind the scenes) will find them and serve them up a "static" files for you.

To do this, assuming you have the recommended install on a Raspberry Pi, create a folder:

    /home/pi/.nodered/public
Adjust things according to your installation. Put the static files such as mqttws31.js in that folder or a subfolder of it (I tend to split things up into `js`, `css` and `images` folders.

Then edit the `settings.js` file that should be in `/home/pi/.nodered` unless you've moved things around. Add the following:

    httpStatic: '/home/pi/.nodered/public',
    
You can quickly test this is correct by putting a simple html file (e.g. `blah.html`) into the public folder and calling the URL `http://localhost:1880/blah.html`. You should see your html rendered into the browser.
