# particle_integration_guide_blynk
A guide to connecting Particle to Blynk

![alt text](https://github.com/markwkiehl/particle_integration_guide_blynk/raw/dc9755b4f964f6f0454ab2ead3860d36c17260f1/Blynk%2BParticle.png "Particle + Blynk")

Blynk is hardware agnostic platform that provides a no code web dashboard and mobile apps for visualization of data, and remote control of any IoT device. &nbsp; Both the native mobile apps for iOS and Android, and the web dashboard are built with drag-and-drop widgets, eliminating the need to write code for the front end. &nbsp; Blynk also includes other tools such as over-the-air (OTA), device provisioning, user management, organaizations, alerts and notifications, automations, and data analytics. &nbsp; 

## Configuring Blynk Services
Navigate to Blynk online and either [login](https://blynk.cloud/dashboard/login) or [create a new account](https://blynk.cloud/dashboard/register). &nbsp; A FREE account is available, or check the [pricing](https://blynk.io/pricing) page for subscription options. &nbsp;

Two options exist for configuring Blynk services:
1. Use the Particle Device Blueprint. &nbsp; This automatically configures Blynk datastreams and creates a web dashboard and mobile app with widgets. &nbsp;  It also assists in activating a device and configuring the firmware for the activated device. &nbsp;
2. Manually configure the Blynk datastreams, web dashboard widgets and mobile app widgets, and activate a device and then configure the firmware with the device activation credentials. &nbsp; 

The end result of the two options above is exactly the same. &nbsp;  Note that configuring the web dashboard and mobile app widgets requires no code. &nbsp;

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
