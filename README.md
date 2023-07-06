# particle_integration_guide_blynk
A guide to connecting Particle to Blynk

![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/dc9755b4f964f6f0454ab2ead3860d36c17260f1/Blynk%2BParticle.png "Particle + Blynk")

## Introduction
Blynk is hardware agnostic platform that provides a no code web dashboard and mobile apps for visualization of data, and remote control of any IoT device. &nbsp; Both the native mobile apps for iOS and Android, and the web dashboard are built with drag-and-drop widgets, eliminating the need to write code for the front end. &nbsp; Blynk also includes other tools such as over-the-air (OTA), device provisioning, user management, organaizations, alerts and notifications, automations, and data analytics. &nbsp; 

This integration will configure bi-directional communication between any [Particle](https://www.particle.io/) device and [Blynk IoT](https://blynk.io/). &nbsp; Both a Blynk web dashboard and mobile app will be generated that will display data from the Particle device, and they will be used to control the Particle device remotely.  

<!--
Any Particle device (Tracker One, Tracker SoM, Boron, B Series SoM, Photon 2, P2, Argon, Photon, Electron, E Series, Core) running the provided firmware will be sending two channels of simulated sensor data from the hardware to Blynk. &nbsp; One channel will be integer values, and the other will be a floating point value. &nbsp; The data sent will be visualized on the Blynk web dashboard or mobile app in both a chart and a value display. &nbsp;  Additionally, a switch widget on the web dashboard and mobile app will send data to the hardware to control it. &nbsp; The switch data is simply an ON/Off (1/0) state that will be sent back to Blynk by the firmware to control a Blynk LED widget, and it will toggle the state of the built-in LED on the Particle device if it exists. &nbsp; A UTC based timestamp will also be displayed on the web dashboard and mobile app so the last time data was published from the Particle device will be known. &nbsp; 
-->

## Functional Requirements
- Works with any Particle device (Tracker One, Tracker SoM, Boron, B Series SoM, Photon 2, P2, Argon, Photon, Electron, E Series, Core)
- The firmware on the Particle hardware will push data that includes an integer value and a floating point number (simulated sensor value or other source) at a regular interval of every 5 minutes (adjustable in the firmware).
- A date/time stamp in UTC must be included with data sent to Blynk and visible to the user.
- The last integer value, floating point value, and UTC datetime stamp must be displayed on the web dashboard and mobile app.
- The historical values for the integer and floating point value must be displayed in a line chart.  
- Multiple Particle devices must be able to use the same Particle webhook. 
- A button widget on the Blynk web dashboard and mobile app will be configured to send a state change command (1/0 value) back to the Particle hardware that will turn ON/OFF the built-in LED (if no LED on GPIO D7 then the user must configure custom GPIO and connect LED).
- Communication frequency must be configured to minimize data usage by default for the case when a cellular device is employed.  

## How It Works
All communication between the Particle hardware and Blynk will utilize the Blynk HTTP API rather than the [Blynk library](http://library.to/). &nbsp; This minimizes data usage when a Particle cellular device is employed.  

The BLYNK AUTH TOKEN will be sent by the device with each data packet and the Particle webhook will pass this on when it communicates with Blynk via the Blynk HTTP API.  This allows the same Particle integration webhook to be used with multiple Particle devices.  

The firmware sketch sends an integer value from the millis() function (number of milliseconds elapsed since the device started) and a constant floating point value every 5 minutes. &nbsp; The data is pushed from the Particle hardware to a Particle integration webhook. &nbsp; The webhook makes a call to the Blynk HTTP API and updates the Blynk datastreams on the Blynk cloud. &nbsp; The updated datastreams cause the Blynk web dashboard and mobile app widgets configured for those datastreams to be updated.  

The Particle hardware will also be controlled remotely from the Blynk web dashboard or mobile app. &nbsp; When the state of the switch widget on the Blynk web dashboard and mobile app is changed, a Blynk webhook is called. &nbsp; The webhook makes a Particle HTTP API call to a Particle cloud function with a device unique token that sends data to the Particle hardware. &nbsp; A firmware function on the Particle hardware reacts to the data received, returns a result code, and then toggles the virtual LED widget on the Blynk web dashboard and mobile app, and toggles the state of the built-in LED if the Particle hardware has one.  &nbsp;

## Components Used in This Integration
- [Particle Console](https://console.particle.io/) to activate the hardware and set up the integration/webhook. 
- Blynk Console and [Blynk App](https://docs.blynk.io/en/downloads/blynk-apps-for-ios-and-android?_gl=1*hxem43*_ga*NTQ1NjUzMTkwLjE2NjY1NTA3MTk.*_ga_E376ZQ635Y*MTY4NzE3MTI1Mi44OC4xLjE2ODcxNzEyNTQuMC4wLjA.) for web and mobile dashboards
- Particle Web IDE, Particle Workbench or Particle Comand Line to upload firmware.
- Any of the following Particle IoT devices:
  - **Tracker One** is complete system including an enclosure. It also comes as a SoM (system on module) known as **Tracker SoM**.  See [Tracker button and LEDs](https://docs.particle.io/hardware/tracker/projects/tracker-buttons-leds/) for details on how to add an LED to the Tracker One. 
  - Any **Boron** cellular devices, including those that are depreciated. &nbsp; All have a built-in LED is on D7.
  - **B Series SoM** system-on-a-module. &nbsp; No built-in LED, must custom install.  
  - **Photon 2** is a WiFi device with built in LED is on D7.
  - **P2** is an SMD module with WiFi and a microcontroller.  No built-in LED.  Replaces the depreciated P1. 
  - **Argon** is a depreciated WiFi device with built in LED is on D7.
  - **Photon** is a depreciated WiFi device with built-in LED on D7.
  - **Electron** is a depreciated cellular device with built-in LED on D7.
  - **E Series** is a cellular device.  No built-in LED.
  - **Core** is a WiFi device with built-in LED on D7. 

<!-- Tracker One, Tracker SOM, Boron, B Series SoM, Photon 2, P2, Argon, Photon, Electron, E Series, Core -->

## 1. Prepare Your Hardware
1. All Particle devices will require suitable power connection and either a cellular or WiFi connection. &nbsp; 
2. A virtual LED (Blynk widget) will be used for all hardware to push back data to Blynk and cause the LED widget to turn on or off.  Particle devices with a built-in LED connected to GPIO D7 GPIO D7 (Boron, Argon, Photon 2, Photon, Electron, Core) will be detected by the firmware and this physical LED will be turned ON/OFF in addition to the LED widget. 

## 2. Configure Blynk Services
Navigate to Blynk online and either [login](https://blynk.cloud/dashboard/login) or [create a new account](https://blynk.cloud/dashboard/register). &nbsp; A FREE account is available, or check the [pricing](https://blynk.io/pricing) page for subscription options. &nbsp;

Two options exist for configuring Blynk services:
1. **Use the Particle Device Blueprint**. &nbsp; This automatically configures Blynk template, datastreams and creates a web dashboard and mobile app with widgets. &nbsp;  It also assists in activating a device and configuring the firmware for the activated device. &nbsp;
2. **Manually Configure Blynk**.  Manually configure the Blynk template, datastreams, web dashboard widgets and mobile app widgets, and activate a device, and then configure the firmware with the device activation credentials. &nbsp; 

The end result of the two options above is exactly the same. &nbsp;  

### Use the Particle Device Blueprint
In June 2023, Blynk released a new feature called [**Blueprints**](https://blynk.io/blog/meet-blueprints-your-pre-built-iot-solution). &nbsp;  A [blueprint](https://blynk.cloud/dashboard/blueprints/Library) is a pre-built IoT project template that includes a web and mobile dashboard, firmware examples, and a step-by-step tutorial to activate and start using the project. &nbsp;  The blueprint '**Particle Device**' provides firmware for a Particle device, and configures Blynk for bi-directional communication between **any** Particle device and Blynk. &nbsp; 

1. From the Blynk.Console, navigate to 'Templates' -> 'All Blueprints' -> '**Particle Device**'. &nbsp;  A detailed guide will display on how to use the blueprint. &nbsp; 
2. In the upper right of the page, click the 'Use Blueprint' button. &nbsp; 
3. When the blueprint configuration is complete, the device template will appear and you can click on the tabs such as 'Datastreams' and 'Dashboard' to see what was prepared. &nbsp; 

#### Activate New Blynk Device
Every device that connects to Blynk requires activation in order to generate an AuthToken. &nbsp;

1. While still in the Blynk.Console templates 'Home' tab, click on 'Activate New Device'. &nbsp; A 'New Device Activation' dialog will appear. &nbsp; 
2. Click the 'Generate AuthToken' and then securely store this token. &nbsp; 

#### Get the Firmware
The firmware is compatible with any Particle device. &nbsp;

1. Click the 'Next' button at the bottom right of the 'New Device Activation' dialog and the complete firmware code with that Blynk AuthToken will appear. &nbsp; 
2. Copy or download that code and install it to your Particle device. &nbsp;  
3. Click the 'Firmware Uploaded' button when you are finished. &nbsp;

#### Next
When the 'New Device Activation' dialog is closed, you will see a list of devices associated with that blueprint, their online/offline status, and the AuthToken. &nbsp; You can add more devices by click the 'New Device' button on this page. &nbsp;

From this point you need to create a Blynk webhook, and a Particle integration webhook. &nbsp; All of the details on how to do this are available by clicking on the 'Read the tutorial' button on the template 'Home' tab. &nbsp; Those details are repeated in the sections 'Create Blynk Webhook', 'Create Particle Integration Webhook', and 'Testing' that follow. &nbsp;

### Manually Configure Blynk (without using the Particle Device Blueprint)

#### Configure Blynk Template
A [Device Template](https://docs.blynk.io/en/concepts/device-template) (or simply 'Template') is a collection of configurations shared by devices of a similar type. &nbsp; For this integration, the important contents in that template are the datastreams and the web dashboard. &nbsp;

##### Create Template and Datastreams
1. In the Blynk.Console, navitate to 'Templates' -> 'My Templates' and click on the 'New Template' button at the upper right of the page. &nbsp; Give the template a name such as 'ParticleDeviceIntegration', set the 'HARDWARE' field to 'Particle', and choose the appropriate 'CONNECTION TYPE' of 'GSM' for cellular devices, or 'WiFi'. &nbsp;
2. Click 'Done' when finished to close the dialog.
3. The template will be shown. &nbsp; Click on the 'Datastreams' tab. &nbsp; 
4. Click the 'New Datastream' button and begin defining the five datastreams as shown in the 'Datastream Table' that follows. &nbsp; It is important to configure the Pin, Data Type, Is Raw, Min, Max, and Default Value as shown.
5. Click the 'Save' button at the upper right of the screen when all of the datastreams have been defined. 

Blynk Datastreams are bi-directional channels assigned a data type and link to data values stored on the Blynk Cloud. &nbsp; You reference them as virtual pins between the range of V0 and V255. &nbsp; 

<table>
  <caption>Datastream Table</caption>
  <tr>
    <th>Datastream <br/> Virtual Pin</th>
    <th>Data Type</th>
    <th>Values</th>
    <th>Comment</th>
  </tr>
  <tr>
    <td>V6</td>
    <td>String</td>
    <td>last_publish</td>
    <td>The Last date/time in UTC data was published from the Particle hardware. &nbsp; Updated by the Particle webhook.
    </td>
  </tr>
  <tr>
    <td>V14</td>
    <td>integer</td>
    <td>millis()</td>
    <td>Simulated sensor data generated by the firmware (C++ millis() function). &nbsp; Could be adapted to represent actual sensor data.
    </td>
  </tr>
  <tr>
    <td>V15</td>
    <td>double</td>
    <td>3.14159</td>
    <td>Simulated sensor data generated by the firmware. &nbsp; A constant floating point value of 3.14159.  &nbsp; Could be adapted to represent actual sensor data.</td>
  </tr>
  <tr>
    <td>V16</td>
    <td>integer</td>
    <td>0/1</td>
    <td>Controlled by switch widget on Blynk web dashboard and mobile app. &nbsp; Changes in the datastream value will be received by the hardware and then the state of the built-in LED will be changed (if available), and the state of the LED widget on the Blynk web dashboard and mobile app will be updated (V17).</td>
  </tr>
  <tr>
    <td>V17</td>
    <td>integer</td>
    <td>0/1</td>
    <td>LED widget on Blynk web dashboard and mobile app that echos changes received by the hardware for V16. 
	  </td>
  </tr>
</table>

The datastreams should appear like the image shown below when properly configured (fields Pin, Data Type, Is Raw, Min, Max, and Default Value must match exactly).

![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/3f01dddde87793a48977412cc597aca8b060f49b/particle_integration_guide(20).png "Datastreams")


##### Create Web Dashboard
The [Web Dashboard](https://docs.blynk.io/en/blynk.console/templates/dashboard) allows you to visualize data from a device or control the device using [Widgets](https://docs.blynk.io/en/blynk.console/widgets-console) (GUI elements). 

1. Click on the 'Web Dashboard' tab and then the 'Edit' button at the upper right of the page. 
2. Drag and drop the following widgets to the dashboard (top to bottom, left to right) using the layout shown in the image that follows: Label, Chart, Label, Chart, Switch, LED, Label.  
3. Configure each widget as showin in the images that follow. 
4. After all widgets are configured, click 'Save and Apply'. 

![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/ca586c7617805509a877adc0ae7209e98ef8075f/particle_integration_guide(21).png "Dashboard layout")
![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/ca586c7617805509a877adc0ae7209e98ef8075f/particle_integration_guide(22).png "V14 label")
![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/030237331da358ff5a659b5e954ffa3517379508/particle_integration_guide(23).png "V14 chart")
![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/030237331da358ff5a659b5e954ffa3517379508/particle_integration_guide(24).png "V15 label")
![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/030237331da358ff5a659b5e954ffa3517379508/particle_integration_guide(25).png "V15 chart")
![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/030237331da358ff5a659b5e954ffa3517379508/particle_integration_guide(26).png "V16 switch")
![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/030237331da358ff5a659b5e954ffa3517379508/particle_integration_guide(27).png "V17 LED")
![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/030237331da358ff5a659b5e954ffa3517379508/particle_integration_guide(28).png "V6 label")

#### Get the Firmware
The firmware sketch ['particle_device_blueprint.ino'](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/56bf8a5643239507113253e454ab05506da877e5/particle_device_blueprint.ino) can be downloaded by clicking on the link. &nbsp;
Note that in the next step you will modify the BLYNK_TEMPLATE_ID, BLYNK_TEMPLATE_NAME and BLYNK_AUTH_TOKEN constants in that sketch to match the activated Blynk device. &nbsp;

#### Activate New Blynk Device
Static tokens will be used for device authentication because the Particle hardware may be  cellular. &nbsp; We will generate a static authentication token (BLYNK_AUTH_TOKEN) using the manual method. &nbsp; 

1. In the Blynk.Console, navigate to 'Search' -> 'DEVICES' and choose '+ New Device' .
2. Choose the 'From template' option.
3. Select the template 'ParticleDeviceIntegration' from the 'TEMPLATE' drop down list.
4. In the 'DEVICE NAME' field, enter a name such as the Particle device name or ID listed in the [Particle Console](https://docs.particle.io/getting-started/console/console/#devices). &nbsp;
5. Click the 'Create' button. 
6. In the upper right the BLYNK_TEMPLATE_ID, BLYNK_TEMPLATE_NAME and BLYNK_AUTH_TOKEN will be displayed. &nbsp; Use the copy function and then update the firmware sketch ['particle_device_blueprint.ino'](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/56bf8a5643239507113253e454ab05506da877e5/particle_device_blueprint.ino) you previously downloaded with this information.  &nbsp; Use this information for only one device connected to Blynk. &nbsp; This information is always available under the 'Device Info' tab. &nbsp;

<pre><code>
/////////////////////////////////////////////////////////////////////////
// Blynk

// Update below with your Blynk auth token for your device (automatically populated by Blueprint)
#define BLYNK_TEMPLATE_ID "13 char template id"
#define BLYNK_TEMPLATE_NAME "ParticleDeviceBlueprint"
#define BLYNK_AUTH_TOKEN "your Blynk 32 char auth token"
</code></pre>

#### Create Blynk App
The Blynk app in [Developer Mode](https://docs.blynk.io/en/blynk.apps/overview#developer-mode) enables you to interactively build a custom app by selecting widgets and then configuring them. &nbsp; When the app is in [End-user Mode](https://docs.blynk.io/en/blynk.apps/overview#end-user-mode) it will present the app with widgets to the user with a behaviour just like any other native app. &nbsp;

Create a custom app while in the [Developer Mode](https://docs.blynk.io/en/blynk.apps/constructor#how-to-enable-developer-mode) using the [Mobile Dashboard Editor](https://docs.blynk.io/en/blynk.apps/constructor). &nbsp; The exact steps vary between iOS and Android, but begin by tapping on a the Particle device you activated earlier, and tap on the wrench icon and then the ‘+’ icon to add a widget. Create the following widgets:
1. SuperChart (V14 &amp; V15)
2. Labeled Value (V14)
3. Labeled Value (V15)
4. Button (V16)
5. LED (V17)
6. Labeled Value (V6)

Tap and hold your finger on a widget, and then when the resize handles appear, drag it to the correct position, and then release and tap on the handle to resize the widget. &nbsp; Do this for each widget until the page appears as shown below.



After the widget is added to the dashboard canvas, you can tap on it to configure it. &nbsp; Every widget has a 'Settings' and 'Design' options at the bottom of the 
Configure each widget as shown in the images below:

1. SuperChart.  

#### Next
From this point you need to create a Blynk webhook, and a Particle integration webhook. &nbsp; All of the details on how to do this follow in the sections 'Create Blynk Webhook', 'Create Particle Integration Webhook', and 'Testing'' that follow'. &nbsp;

## 3. Create Particle Integration Webhook
We are going to create a Particle integration webhook running on the Particle cloud that will accept the data from the Particle.publish() function executing on the device, and transform it into a HTTPs GET that will post data to the Blynk cloud, updating the corresponding Blynk datastream values. &nbsp;

1. Login to your [Particle Console](https://docs.particle.io/getting-started/console/console/) and click on the ‘[Integrations](https://console.particle.io/integrations)’ sidebar option. &nbsp; Click on the ‘NEW INTEGRATION’ shown on the page, and then select the ‘Webhook’ option. 

![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/1d642d81da33278da8cb33254d4c2e98dc024112/particle_device_blueprint%20(1).jpeg "New Integration")

![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/1d642d81da33278da8cb33254d4c2e98dc024112/particle_device_blueprint%20(2).jpeg "New Integration")

2. Fill out the Webhook form as shown below. &nbsp; The ‘Event Name’ is what will be called later by your device firmware with the Particle.publish() function, so it is critical that it matches what is called in the firmware. &nbsp; The server address of ‘ny3.blynk.cloud’ for the ‘URL’ field should be replaced with the server address from [this list](https://docs.blynk.io/en/blynk.cloud/troubleshooting) that matches what you defined for your Blynk HTTP GET request. &nbsp; Change the ‘Request Type’ to ‘GET’, and then click on the ‘CREATE WEBHOOK’ button at the bottom of the form. 

![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/1d642d81da33278da8cb33254d4c2e98dc024112/particle_device_blueprint%20(5).png "New Integration")

3. The webhook is not complete yet. &nbsp; The query parameters need to be defined. &nbsp; Click on the ‘EDIT’ button at the top right of the screen. &nbsp;

![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/1d642d81da33278da8cb33254d4c2e98dc024112/particle_device_blueprint%20(6).jpeg "New Integration")

4. Click on the ‘Advanced Settings’ link at the bottom of the page and then under the ‘QUERY PARAMETERS’ section, choose the ‘Custom’ option. &nbsp; Build the query parameters as shown below, using the ‘+ ADD ROW’ button at the bottom to add a row for each query parameter. &nbsp;

![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/1d642d81da33278da8cb33254d4c2e98dc024112/particle_device_blueprint%20(7).png "New Integration")

The keys on the left (token, V6, V14, V15, V16, V17) refer to Blynk virtual pins (datastreams), and the values on the right for ‘{{t}} {{PARTICLE_PUBLISHED_AT}} {{v14}} {{v15}} ..’ are variables from the firmware that will be passed from the Particle.publish() function. &nbsp; Those values passed by the Particle.publish() function will replace the placeholders in the '{{}}' [Mustache Template](https://docs.particle.io/reference/cloud-apis/webhooks/#variable-substitution) (see code snippet that follows). &nbsp; 

Particle.publish() call in the firmware:
<pre><code>
char data[90]; 
// Note the escaped double quotes around the &quot;&quot;t&quot;&quot; for BLYNK_AUTH_TOKEN.  
snprintf(data, sizeof(data), &quot;{\&quot;t\&quot;:\&quot;%s\&quot;,\&quot;v14\&quot;:%u,\&quot;v15\&quot;:%f,\&quot;v16\&quot;:%u,\&quot;v17\&quot;:%u}&quot;, BLYNK_AUTH_TOKEN, millis(), v15, led_state, led_state);
bool pub_result = Particle.publish(&quot;blynk_https_get&quot;, data, PRIVATE);
</code></pre>

The value ‘PARTICLE_PUBLISHED_AT’ for virtual pin V6 is a Particle pre-defined variable that provides a UTC timestamp for when the webhook is executed. &nbsp; 

Note that the firmware will pass the unique BLYNK_AUTH_TOKEN defined for each device to the Particle webhook as the variable ‘{{t}}’. &nbsp; This allows each device to call the same webhook, at the expense of increasing the cellular payload for each transmission by 32 bytes. &nbsp; 

5. At the bottom of the form, make sure the ‘ENFORCE SSL’ option is set to ‘Yes’, and then click the ‘SAVE’ button to save your changes. 

![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/a4f6f5beb63fc93c58233c2adb3fe6d357ca5d01/particle_device_blueprint%20(8).jpeg "New Integration")

6. After you save your webhook, a summary of the configuration will be shown. &nbsp; Make sure it matches exactly what is shown below (except for the server address).

![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/a4f6f5beb63fc93c58233c2adb3fe6d357ca5d01/particle_device_blueprint%20(9).png "New Integration")

You can learn more about Particle webhooks by visiting this [documentation link](https://docs.particle.io/reference/cloud-apis/webhooks/). 

## 4. Generate a Particle Access Token
The Blynk webhook will need a Particle access token in order to make a Particle HTTP API call to the Particle cloud function. &nbsp;

1. Login to your Particle account.
2. Browse to the Particle documentation section ‘[Create a token (browser-based)](https://docs.particle.io/reference/cloud-apis/access-tokens/#create-a-token-browser-based-)’. 
3. Enter your Particle login email and password into the form. &nbsp; If you have MFA (multi-factor authentication) enabled on your account, you will need your MFA code to generate the access token. 
4. Click the ‘Create token’ button to generate a token. &nbsp; Keep this token confidential. 

![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/c257e1b217dbcd2a2efb05ccbf25776940c10544/particle_device_blueprint%20(10).jpeg "Particle Access Token")

## 5. Create the Blynk Webhook
Control of the Particle hardware remotely from the Blynk web dashboard or mobile app is accomplished using a Blynk webhook and the Particle HTTP API. &nbsp; When the state of the switch widget on the Blynk web dashboard or mobile app is changed, a Blynk webhook assigned to the same datastream is called. &nbsp; The webhook makes a Particle HTTP API call to a Particle cloud function with a device unique token that sends data to the Particle hardware. &nbsp; 

1. In the [Blynk.Console](https://blynk.cloud/), navigate to ‘Settings -> Webhooks’ and create one new webhook for datastream V16 based on the information shown in the next image. &nbsp; 
2. After you are finished configuring the webhook, click the ‘Test webhook’ to verify it doesn’t throw an error (it won’t send the datastream value here, so don’t expect to see the LED on your Particle device change). &nbsp; 
3. Click the ‘Create Webhook’ button to save it and close the dialog. 

Note that the **Blynk webhook request quota is 1 per minute*** so any datastream value changes sooner than 60 seconds will not execute the webhook. &nbsp;

![alt text](https://github.com/markwkiehl/particle_device_blueprint/raw/4551a69255324fa9f0c551440f7514cb6764b464/particle_device_blueprint%20(11).png "Blynk Webhook")


**WEBHOOK URL**

The format is: 
<pre><code>https://api.particle.io/v1/devices/[your 24 character Particle device ID]/blynk_led</code></pre> &nbsp; 

Substitute your Particle [device ID](https://console.particle.io/) and the 40 character access token you generated earlier in the corresponding form fields. &nbsp;  Your 24 character Particle device ID is available in the [Particle console[(https://console.particle.io/). &nbsp; 

The "blynk_led" at the end of the WEBHOOK URL is the Particle cloud function key that is referenced in the firmware as:

<pre><code>
void setup() {
  ...
  Particle.function("blynk_led", blynkLED);
  ...
}
</code></pre>

**HTTP Headers (optional)**

The 'HTTP Headers' with key "Authorization" has a value consisting of the string "Bearer " (with a space after it), and then followed by the 40 character Particle access token. &nbsp;
<pre><code>
Bearer 40_character_Particle_access_token
</code></pre>

## 6. Testing
1. Test the Particle cloud function running in the firmware by calling it from the Particle console. &nbsp;  With your Particle hardware running, visit [here](https://docs.blynk.io/en/hardware-guides/particle-part-ii#firmware) for detailed instructions on how to call 'blynk_led'. &nbsp; Go to your [Particle console](https://console.particle.io/), select the Particle device, and then under the section ‘FUNCTIONS’ on the right side of the screen you will see the function key of ‘blynk_led’ listed. &nbsp; Enter ‘on’ in the ‘Argument’ input area and click the ‘CALL’ button. &nbsp; Observe the Particle device to confirm that the built-in blue LED on D7 turns on. &nbsp; Repeat, this time with the ‘off’ argument to turn off the LED. &nbsp;

![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/59f149a5b40fb303f964560b048a17e580acb575/particle_device_blueprint%20(12).jpg "Particle Function")

2. Verify that your Particle access token is correct by using the Particle API to test it. &nbsp; Detailed instructions on how to do this using [Postman](https://www.postman.com/) can be found [here](https://docs.blynk.io/en/hardware-guides/particle-part-ii#particle-api). &nbsp;

3. Test the Blynk webhook by installing the firmware on your Particle hardware, and then click the ‘Test webhook’ to verify it doesn’t throw an error (it won’t send the datastream value here, so don’t expect to see the LED on your Particle device change). &nbsp;  Then from the Blynk web dashboard or mobile app, toggle the switch assigned to datastream V16 and observe the built-in LED on the hardware if it exists, or the Blynk LED widget if no built-in LED exists. &nbsp;  Wait 60 seconds between each toggle of the switch widget. &nbsp;

4. Review the Particle device log to confirm the device is connected and to see what data has been published from the hardware to the Particle cloud. 

5. Review the Particle integration log to see if it was triggered successfully and the data what was pushed to it from the Particle device. 

## Troubleshooting
Make sure the BLYNK_AUTH_TOKEN in the sketch 'particle_device_blueprint.ino' matches what is shown in the Blynk console 'Search' -> 'Device' -> 'Device Info'. &nbsp;

If your Particle device has a built-in RGB then it should be breathing cyan if it is connected to the Particle cloud.  &nbsp; 

Perform all of the tests under **Testing** to be sure that each communication step from the Particle hardware to Blynk and back works properly. &nbsp;

## Conclusion
By connecting any Particle device to Blynk, you can remotely visualize data sent by the device, and control it remotely. &nbsp; Blynk's no-code web dashboard and mobile apps make it easy to create a custom dashboard for interaction with your Particle device. &nbsp;

## Next Steps
This integration is easily modified to include more functionality. &nbsp; Up to 255 datastreams can be configured on Blynk for bi-directional communication between a Particle device and the Blynk web dashboard and mobile app. &nbsp; 

Blynk [Automations](https://docs.blynk.io/en/concepts/automations) allow the end-user of your app to create scenarios where the device automatically performs one or more actions based on a condition. &nbsp; For example, you can trigger a phone notification or send an email when the Particle device detects a sensor condition of interest. &nbsp; 

Explore Blynk [ADD-ONS](https://docs.blynk.io/en/add-ons/add-on-list) for enhancing Blynk. &nbsp; 

The data publishing interval default of 5 minutes set in the firmware can be reduced down to every one second. &nbsp; 

## Related Links
[Blynk Troubleshooting guide](https://docs.blynk.io/en/troubleshooting/general-issues)<br/>
[Blynk Documentation](https://docs.blynk.io/)<br/>
[How to connect a Particle device to Blynk](https://docs.blynk.io/en/hardware-guides/particle)<br/>
[How to control a Particle device with Blynk](https://docs.blynk.io/en/hardware-guides/particle-part-ii)<br/>