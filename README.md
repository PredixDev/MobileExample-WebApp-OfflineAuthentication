## Predix Mobile Offline Login example

This repo contains a Predix Mobile app demonstrating a simple offline login configuration.

It covers these key aspects of offline login support:

* Web app setup
* First time offline authentication password setup
* Authentication offline
* Management of a users offline password

### Step 0 - Prerequisites

It is assumed you already have a Predix Mobile cloud services installation, have installed the Predix Mobile command line tool, and have installed a Predix Mobile iOS Container. To get started, follow this documentation:
* [Get Started with the Mobile Service and Mobile SDK] (https://www.predix.io/docs#rae4EfJ6) 
* [Running the Predix Mobile Sample App] (https://www.predix.io/docs#EGUzWwcC)
* [Creating a Mobile Hello World Webapp] (https://www.predix.io/docs#DrBWuHkl) 


### Step 1 - Webapp setup

This repo contains two web apps, and a Predix Mobile app configuration file. The two web apps should be published to your Predix Mobile cloud, and the Predix Mobile app configuration file should be defined using the pm tool.

The script "setup-demo.sh" has been provided to quickly perform these steps for you, if you desire.

This example assumes you have your Predix Mobile iOS Container sample app info.plist configured to have "pmapp_name" as "Sample1" and "pmapp_version" as "1.0". See the Predix Mobile iOS Container sample app repo for more information on this configuration.

### Step 2 - First time offline authentication password setup

With your machine still connected to the internet, run your Predix Mobile container. After the normal online login, you will see the first time setup page for your offline password.

What's happening here is the system has read the configration for this Predix Mobile app, and has identified that an offline login web app has been configured. Further, it has identified that this system has no offline password configured, so it has initialized the setup step of offline login by calling the web page defined in the offline login webapp's "setup" element.

At this time, you should enter an initial offline password, and tap OK. The system will then continue onto the main web app for this demo.

### Step 3 - Authentication offline

Now that you have successfully authenticated online, and setup your offline password, the next step is to see authentication offline in action.

Disconnect your device or computer from the network, then tap the Logout button.

You will then see the Offline Login page.

Here you can enter the password you setup previously to authentication in offline mode.

Once you've logged in offline, reconnect your device or computer to the network, and tap Logout again to re-login online.

### Step 4 - Management of a users offline password

After logging back in to the system in Online mode, you can try changing your offline password. The Update Offline Password button will initialize this component, taking you to a basic "change password" type page. For security reasons, you can only change your offline password while authenticated online.

## Technical details

### The Predix Mobile app definition

The app.json file for this example is:

    {
        "name": "Sample1",
        "version": "1.0",
        "starter": "demo-offline-app",
        "offline": "offline-login",
        "dependencies": {
            "demo-offline-app": "0.0.1",
            "offline-login": "0.0.1"
         }
    }

Note "starter" and "offline" entries. The "starter" you may be familiar with from other examples. It defines the main web app that the system starts after authentication.

The "offline" entry specifies the webapp that is responsible for offline authentication.

### The Offline webapp

The webapp.json file for the offline-login web app in this example is:

    {
      "name": "offline-login",
      "version": "0.0.1",
      "main": "authenticate.html",
      "setup": "setup.html",
      "update": "update.html",
      "src-folder": "./dist/",
      "output-folder": "./dist-zip/" 
    }

While similar to all webapp.json files, you can see here, the additional entries for "setup" and "update". These are the web pages called for first time initialization of the user's offline password, and to change the user's password. Also note, the "main" entry is the web page used for offline authentication itself.

The offline webapp has some unique responsibilities, and requirements:

* It's run in a special locked-down mode of the container. The only container services this webapp can call are the auth and logging services.
* It must support three web pages for authentication, setup, and update.
* When these pages are called, they are passed a one-time token. The auth service calls back to the container must include this token as part of it's path.
* Based on the functionality of the page called, only certain functions of the auth service are allowed. For example, if the container is expecting authentication, the page cannot call the auth service to update the offline password.

## Further understanding

Please review the javascript code in each of the offline-login web app pages, and in the demo-offline-app. Here you will find examples of not only calling the auth service to support offline login, but also examples of these services:

* log -- log service is called to send JavaScript console logs to the container's logging system.
* boot -- boot/research is called for logging out.
* connectivity -- connectivity is called to display the current connectivity status on the main demo-offline-app page.
