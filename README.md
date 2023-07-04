# particle_integration_guide_blynk
A guide to connecting Particle to Blynk

![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/dc9755b4f964f6f0454ab2ead3860d36c17260f1/Blynk%2BParticle.png "Particle + Blynk")

Blynk is hardware agnostic platform that provides a no code web dashboard and mobile apps for visualization of data, and remote control of any IoT device. &nbsp; Both the native mobile apps for iOS and Android, and the web dashboard are built with drag-and-drop widgets, eliminating the need to write code for the front end. &nbsp; Blynk also includes other tools such as over-the-air (OTA), device provisioning, user management, organaizations, alerts and notifications, automations, and data analytics. &nbsp; 

This integration will configure bi-directional communication between any [Particle](https://www.particle.io/) device and [Blynk IoT](https://blynk.io/). &nbsp; Both a Blynk web dashboard and mobile app will be generated that will display data from the Particle device, and they will be used to control the Particle device remotely.  

Any Particle device (Tracker One, Tracker SoM, Boron, B Series SoM, Photon 2, P2, Argon, Photon, Electron, E Series, Core) running the provided firmware will be sending two channels of simulated sensor data from the hardware to Blynk. &nbsp; One channel will be integer values, and the other will be a floating point value. &nbsp; The data sent will be visualized on the Blynk web dashboard or mobile app in both a chart and a value display. &nbsp;  Additionally, a switch widget on the web dashboard and mobile app will send data to the hardware to control it. &nbsp; The switch data is simply an ON/Off (1/0) state that will be sent back to Blynk by the firmware to control a Blynk LED widget, and it will toggle the state of the built-in LED on the Particle device if it exists. &nbsp; A UTC based timestamp will also be displayed on the web dashboard and mobile app so the last time data was published from the Particle device will be known. &nbsp; 

## Functional Requirements
- The firmware on the Particle hardware will push data that includes an integer value and a floating point number (simulated sensor value or other source) at a regular interval of every 5 minutes (adjustable in the firmware).
- A date/time stamp in UTC must be included with data sent to Blynk so the last time data was sent will be visible.
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

The blueprint includes a sketch (.ino file) that is uploaded to the Particle hardware. &nbsp; The Blynk device template, datastreams, web dashboard ,and mobile app (iOS/Android) are all pre-configured. &nbsp; Instructions are included later in this readme.md on how to configure the Particle integration webhook, generate a Particle access token, and how to create a Blynk webhook. &nbsp;

## Components Used in This Project
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



## Configuring Blynk Services
Navigate to Blynk online and either [login](https://blynk.cloud/dashboard/login) or [create a new account](https://blynk.cloud/dashboard/register). &nbsp; A FREE account is available, or check the [pricing](https://blynk.io/pricing) page for subscription options. &nbsp;

Two options exist for configuring Blynk services:
1. Use the Particle Device Blueprint. &nbsp; This automatically configures Blynk datastreams and creates a web dashboard and mobile app with widgets. &nbsp;  It also assists in activating a device and configuring the firmware for the activated device. &nbsp;
2. Manually configure the Blynk datastreams, web dashboard widgets and mobile app widgets, and activate a device and then configure the firmware with the device activation credentials. &nbsp; 

The end result of the two options above is exactly the same. &nbsp;  

### Use the Particle Device Blueprint
In June 2023, Blynk released a new feature called [**Blueprints**](https://blynk.io/blog/meet-blueprints-your-pre-built-iot-solution). &nbsp;  A [blueprint](https://blynk.cloud/dashboard/blueprints/Library) is a pre-built IoT project template that includes a web and mobile dashboard, firmware examples, and a step-by-step tutorial to activate and start using the project. &nbsp;  The blueprint '**Particle Device**' provides firmware for a Particle device, and configures Blynk for bi-directional communication between **any** Particle device and Blynk. &nbsp; 

From the Blynk.Console, navigate to 'Templates' -> 'All Blueprints' -> '**Particle Device**'. &nbsp;  A detailed guide will display on how to use the blueprint. &nbsp; In the upper right of the page, click the 'Use Blueprint' button. &nbsp; When the blueprint configuration is complete, the device template will appear and you can click on the tabs such as 'Datastreams' and 'Dashboard' to see what was prepared. &nbsp; 

#### Activate New Blynk Device
While still in the Blynk.Console templates 'Home' tab, click on 'Activate New Device'. &nbsp; A 'New Device Activation' dialog will appear. &nbsp; Click the 'Generate AuthToken' and then securely store this token. &nbsp; 

#### Get the Firmware
Click the 'Next' button at the bottom right of the 'New Device Activation' dialog and the complete firmware code with that Blynk AuthToken will appear. &nbsp; Copy or download that code and install it to your Particle device. &nbsp;  Click the 'Firmware Uploaded' button when you are finished. &nbsp;

#### Next Steps
When the 'New Device Activation' dialog is closed, you will see a list of devices associated with that blueprint, their online/offline status, and the AuthToken. &nbsp; You can add more devices by click the 'New Device' button on this page. &nbsp;

From this point you need to create a Blynk webhook, and a Particle integration webhook. &nbsp; All of the details on how to do this are available by clicking on the 'Read the tutorial' button on the template 'Home' tab. &nbsp; Those details are repeated in the sections 'Create Blynk Webhook', 'Create Particle Integration Webhook', and 'Testing' that follow. &nbsp;

### Manually Configure Blynk (without using the Particle Device Blueprint)

#### Configure Blynk Datastreams

#### Configure Blynk Web Dashboard

#### Configure Blynk App

#### Activate New Blynk Device

#### Get the Firmware

#### Next Steps
From this point you need to create a Blynk webhook, and a Particle integration webhook. &nbsp; All of the details on how to do this follow in the sections 'Create Blynk Webhook', 'Create Particle Integration Webhook', and 'Testing'' that follow'. &nbsp;

## Create Blynk Webhook

## Create Particle Integraton Webhook

## Testing

## Related Links

[Blynk Troubleshooting guide](https://docs.blynk.io/en/troubleshooting/general-issues)
[Blynk Documentation](https://docs.blynk.io/)
[How to connect a Particle device to Blynk](https://docs.blynk.io/en/hardware-guides/particle)
[How to control a Particle device with Blynk](https://docs.blynk.io/en/hardware-guides/particle-part-ii)