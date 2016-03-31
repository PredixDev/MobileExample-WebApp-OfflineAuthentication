## Predix Mobile Offline Login example

This repo contains a Predix Mobile app demonstrating a simple offline login configuration.

It covers these key aspects of offline login support:

* Web app setup
* First time offline authentication password setup
* Authentication offline
* Managing a user's offline password

### Prerequisites

It is assumed you already have a Predix Mobile cloud services installation, have installed the Predix Mobile command line tool, and have installed a Predix Mobile iOS Container, following the Getting Started examples for those repos.

This example assumes that your Predix Mobile iOS Container sample app `info.plist` is configured to have `pmapp_name` as `Sample1` and `pmapp_version` as `1.0`. See the Predix Mobile iOS Container sample app repo for more information on this configuration.

### Step 1 - Web app setup

This repo contains two web apps and a Predix Mobile app configuration file. The two web apps should be published to your Predix Mobile cloud, and the Predix Mobile app configuration file should be defined using the pm tool.

You can run the script `setup-demo.sh` to quickly perform these steps for you.

### Step 2 - First time offline authentication password setup

  1. With your machine still connected to the internet, run your Predix Mobile container. After the normal online login, you will see the first time setup page for your offline password.

  The system has read the configration for this Predix Mobile app, and it has identified that an offline login web app has been configured. It has also identified that this system has no offline password configured, so it has initialized the setup step of offline login by calling the web page defined in the offline login web app's `setup` element.

  2. Enter an initial offline password, and tap OK. The system will then continue onto the main web app for this demo.

### Step 3 - Authentication offline

Now that you have successfully authenticated online, and setup your offline password, the next step is to see authentication offline in action.

  1. Disconnect your device or computer from the network and tap the Logout button.

  The Offline Login page is displayed.

  2. Enter the password you setup previously to authentication in offline mode.

  3. Once you've logged in offline, reconnect your device or computer to the network, and tap Logout again to re-login online.

### Step 4 - Managing a user's offline password

After logging back in to the system in Online mode, you can try changing your offline password. 

The Update Offline Password button will initialize this component, taking you to a basic `change password` page. For security reasons, you can only change your offline password while authenticated online.

## Technical details

### The Predix Mobile app definition

The `app.json` file for this example is:

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

The "offline" entry specifies the web app that is responsible for offline authentication.

### The Offline webapp

The `webapp.json` file for the offline-login web app in this example is:

    {
      "name": "offline-login",
      "version": "0.0.1",
      "main": "authenticate.html",
      "setup": "setup.html",
      "update": "update.html",
      "src-folder": "./dist/",
      "output-folder": "./dist-zip/" 
    }

While this is similar to all `webapp.json files`, there are additional entries for "setup" and "update". These are the web pages called for first time initialization of the user's offline password, and to change the user's password. Also, the "main" entry is the web page used for offline authentication itself.

The offline web app has some unique responsibilities, and requirements:

* It is run in a special locked-down mode of the container. The only container services this web app can call are the auth and logging services.
* It must support three web pages for authentication, setup, and update.
* When these pages are called, they are passed a one-time token. The auth service calls back to the container must include this token as part of it's path.
* Based on the functionality of the page called, only certain functions of the auth service are allowed. For example, if the container is expecting authentication, the page cannot call the auth service to update the offline password.

## Further understanding

Please review the javascript code in each of the offline-login web app pages, and in the demo-offline-app. Here you will find examples of calling the auth service to support offline login, and also examples of these services:

* log -- log service is called to send JavaScript console logs to the container's logging system.
* boot -- boot/research is called for logging out.
* connectivity -- connectivity is called to display the current connectivity status on the main demo-offline-app page.
