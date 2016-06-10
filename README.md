# IoT for Automotive Starter app
A demo app that uses Internet of Things Platform, Context Mapping and Driver Behavior services.

## Overview
With IoT for Automotive Starter app, you will experience a simulation of how Tom, an automobile owner and driver, can use a mobile app to rent a car provided by an automotive company. This new type of service will help the automotive company attract and retain customers. The design of future cars and services can be based on this new source of customer and vehicle data. You will use a mobile app on an iOS phone and experience how Tom can find an available car located near him and reserve the car. You will experience how Tom can review his driving behavior, which is analyzed by Driver Behavior service on Bluemix.

This app demonstrates how quickly you can build an app on Bluemix using Internet of Things Platform, Context Mapping and Driver Behavior services. You can follow the steps below to set up the IoT for Automotive Starter app.

## Application Requirements
You need to install a mobile app on an iOS phone to experience the simulation.

## Deploy the app on Bluemix
You can deploy your own instance of IoT Automotive Starter app to Bluemix.
To do this, you can either use the _Deploy to Bluemix_ button for an automated deployment or follow the steps below to create and deploy your app manually.

[![Deploy to Bluemix](https://bluemix.net/deploy/button.png)](https://bluemix.net/deploy?repository=https://github.com/mwakao/iota-starter-server.git)

1. Create a Bluemix Account.

  [Sign up][bluemix_signup_url] for Bluemix, or use an existing account.

2. Download and install the [Cloud-foundry CLI][cloud_foundry_url] tool.

3. Clone the app to your local environment from your terminal using the following command:

  ```
  git clone https://github.com/ibm-watson-iot/iota-starter-server.git
  ```

4. `cd` into this newly created directory.

5. Edit the `manifest.yml` file and change the `<name>` and `<host>` to something unique.

  ```
  applications:
         :
    disk_quota: 1024M
    host: iot-automotive-starter
    name: IoT-Automotive-Starter
    path: .
    instances: 1
    memory: 640M
         :
  ```
  The host you use will determinate your application URL initially, for example, `<host>.mybluemix.net`.

6. Connect to Bluemix in the command line tool and follow the prompts to log in:

  ```
  $ cf api https://api.ng.bluemix.net
  $ cf login
  ```

7. Create Internet of Things Platform, Context Mapping and Driver Behavior service in Bluemix.

  ```
  $ cf create-service iotf-service iotf-service-free IoTPlatform
  $ cf create-service mapinsights free ContextMapping
  $ cf create-service driverinsights free DriverBehavior
  ```

8. This app uses Cloudant NoSQL DB service as well. Create the services in Bluemix.

  ```
  $ cf create-service cloudantNoSQLDB Shared MobilityDB
  ```

9. Push the app to Bluemix. You need to perform additional steps when it is deployed, so you must add the option --no-start argument.

  ```
  $ cf push --no-start
  ```

You now have your very own instance of the IoT for Automotive Starter app on Bluemix.  

## Before using the app
Before using the IoT for Automotive Starter app, you need to set up services and install a mobile app.

### Activate Context Mapping and Driver Behavior services  
Follow the steps below to make the Context Mapping and Driver Behavior services ready for use.

1. Make sure that the app is not running on Bluemix.

2. Open the [Bluemix dashboard][bluemix_dashboard_url] in your browser.

3. Open the Context Mapping service and wait for a few seconds until credentials show up.

4. Likewise, open the Driver Behavior service from the dashboard.

### Build and install a mobile app
If you have not installed a mobile app on you iOS phone, follow the [instructions][mobile_app_readme].

## Start the app
1. Open the [Bluemix dashboard][bluemix_dashboard_url] in your browser.

2. Start the app.

Congratulations! You are ready to use your own instance of IoT for Automotive Starter app now. Open `http://<host>.mybluemix.net` in your browser and follow the instructions in the top page to connect your mobile app to the IoT for Automotive Starter app.

## (Optional) Connect your own device to the app
When you start your mobile app, you might see some cars around the current location in the map. They are simulated cars generated automatically by a simulation engine of the app. Instead of them, you can try with your own sensor device that can send location data by registering it to the Internet of Things Platform service.

### Disable a simulation engine
1. In your [Bluemix dashboard][bluemix_dashboard_url], open the app.

2. Stop the app.

3. Click the _Environment Variables_ in the left bar.

4. Open USER_DEFINED.

5. Add the following variable and save it.
```
DISABLE_DEMO_CAR_DEVICES=true
```

### Register your device to Internet of Things Platform
1. In your [Bluemix dashboard][bluemix_dashboard_url], open the Internet of Things Platform service.

2. Click the _Launch dashboard_ button under _Connect your devices_.

3. Open the _DEVICES_ page.

4. Delete all devices if they exist.

5. See the following IBM Watson IoT Platform pages and add your own device.
 * [Quickstart](https://quickstart.internetofthings.ibmcloud.com/)
 * [How to Register Devices in IBM Watson IoT Platform](https://developer.ibm.com/recipes/tutorials/how-to-register-devices-in-ibm-iot-foundation/)

A device type for your device can be anything. If you have run the app with a simulation engine enabled, __ConnectedCarDevice__ is already registered as a device type for simulated cars. You can either use it or create new one for your device.

### Add your device information to Cloudant NoSQL DB
1. In your [Bluemix dashboard][bluemix_dashboard_url], open the Cloudant NoSQL DB service.

2. Click the _LAUNCH_ icon.

3. Open __mobilitystarterappdb__ database or create it if it does not exist.

4. Delete all documents in the database if they exist.

5. Create new document with the following format.
```
  {
    "_id": "<device id string>",
    "deviceDetails": {
       "name": "<name in string>",
       "model": {
          "makeModel": "<your car model in string>",
          "year": <model year in number>,
          "mileage": <mileage in number>,
          "stars": <score in number(0-5)>,
          "hourlyRate": <hourly rate in number>,
          "dailyRate": <daily rate in number>,
          "thumbnailURL": "<secure URL to your car image>"
       }
     }
   }
```
The `_id` must be the device id that you have specified in the Internet of Things Platform. The other values are optional.

Now, go to your [Bluemix dashboard][bluemix_dashboard_url] and restart the app.

### Event format
Your device is expected to publish the following event to the Internet of Things Platform.

```
{
  "d": {
    "lat": <latitude in double>,
    "lng": <longitude in double>,
    "trip_id": "<trip id in string>",
    "speed": <vehicle speed in double (km/h)>
  }
}
```
The `lat` and `lng` are required to show your car on a map on your mobile app. Set the  location of your device as values of the `lat` and `lng`. When you record your trip route after you reserve the car, the `trip_id` and `speed` are also required. The same `trip_id` must be set during the reservation.

## Report Bugs
If you find a bug, please report it using the [Issues section](https://github.com/ibm-watson-iot/iota-starter-server/issues).

## Troubleshooting
The primary source of debugging information for your Bluemix app is the logs. To see them, run the following command using the Cloud Foundry CLI:

  ```
  $ cf logs <application-name> --recent
  ```
For more detailed information on troubleshooting your application, see the [Troubleshooting section](https://www.ng.bluemix.net/docs/troubleshoot/tr.html) in the Bluemix documentation.

## Privacy Notice

The IoT for Automotive Starter app includes code to track deployments to [IBM Bluemix](https://www.bluemix.net/) and other Cloud Foundry platforms. The following information is sent to a [Deployment Tracker](https://github.com/cloudant-labs/deployment-tracker) service on each deployment:

* Application Name (`application_name`)
* Space ID (`space_id`)
* Application Version (`application_version`)
* Application URIs (`application_uris`)
* Labels of bound services
* Number of instances for each bound service

This data is collected from the `VCAP_APPLICATION` and `VCAP_SERVICES` environment variables in IBM Bluemix and other Cloud Foundry platforms. This data is used by IBM to track metrics around deployments of sample applications to IBM Bluemix to measure the usefulness of our examples, so that we can continuously improve the content we offer to you. Only deployments of sample applications that include code to ping the Deployment Tracker service will be tracked.

### Disabling Deployment Tracking

Deployment tracking can be disabled by removing `require("cf-deployment-tracker-client").track();` from the beginning of the `app.js` main server file.

## Useful links
[IBM Bluemix](https://bluemix.net/)  
[IBM Bluemix Documentation](https://www.ng.bluemix.net/docs/)  
[IBM Bluemix Developers Community](http://developer.ibm.com/bluemix)  
[IBM Watson Internet of Things](http://www.ibm.com/internet-of-things/)  
[IBM Watson IoT Platform](http://www.ibm.com/internet-of-things/iot-solutions/watson-iot-platform/)   
[IBM Watson IoT Platform Developers Community](https://developer.ibm.com/iotplatform/)

[bluemix_dashboard_url]: https://console.ng.bluemix.net/dashboard/
[bluemix_signup_url]: https://console.ng.bluemix.net/registration/
[cloud_foundry_url]: https://github.com/cloudfoundry/cli
[mobile_app_readme]: https://github.com/ibm-watson-iot/iota-starter-carsharing/blob/master/README.md
