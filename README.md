# **Dashboard Update for PlanktoScope**

## **Setting Up Your PlanktoScope**

1. **Prepare the SD Card**

   * Download the [latest PlanktoScope release](https://github.com/PlanktoScope/PlanktoScope/releases).

   * Flash the release to an SD card using [Raspberry Pi Imager](https://www.raspberrypi.com/software/).

     * Open Raspberry Pi Imager.
     * Select **"Choose OS"** and locate the PlanktoScope image.
     * Select **"Choose Storage"** and pick your SD card.
     * Click **"Write"** to flash the image.

   * Insert the SD card into the PlanktoScope.

2. **Connect to the Local Network**

   * Use an Ethernet cable to connect the PlanktoScope to your local router.
   * Power on the PlanktoScope.

3. **Connect the PlanktoScope to Wi-Fi**

   * Once the PlanktoScope’s Wi-Fi becomes visible, use your computer or mobile device to connect it to your local router’s Wi-Fi network.

***

## **Locating the IP Address of Your PlanktoScope**

1. **Check Your Router’s Admin Panel**

   * Open your router’s admin interface by entering one of these common addresses into your browser:

     * `http://192.168.0.1`
     * `http://192.168.1.1`
     * `http://routerlogin.net`

   * Log in using your router’s credentials (check your router label or manual for default credentials).

2. **Locate Connected Devices**

   * Navigate to the section listing connected devices, such as:

     * "Connected Devices"
     * "Device List"
     * "DHCP Clients"

   * Look for the device labeled **PlanktoScope** or a similar name under the **Ethernet** or **Wi-Fi** section.

3. **Use Network Scanning Tools (Optional)**\
   If you can’t access your router:

   * **Windows:** Use [Advanced IP Scanner](https://www.advanced-ip-scanner.com/).

   * **macOS/Linux:** Run a network scan with `nmap` or similar tools:

     ```bash
     nmap -sn 192.168.1.0/24
     ```

   * Look for the PlanktoScope in the results.

4. **Verify the Landing Page**

   * Open a browser and enter the PlanktoScope’s IP address (e.g., `http://192.168.x.x`) to access the **Landing Page**.

***

## **Configuring Node-RED Settings**

To enable essential Node-RED features, edit the `settings.js` file.

1. **Access the File**

   * Use SSH or navigate via the **System File Manager** link on the Landing Page:\
     [http://192.168.x.x/admin/fs/files/etc/nodered/settings.js](http://%3Cyour-planktoscope-ip%3E/admin/fs/files/etc/nodered/settings.js).

2. **Enable Context Storage**

   * This feature allows data to persist between Node-RED reboots.

   * Remove the comments between **line 265** and **line 269**:

     ```javascript
     contextStorage: {
         default: {
             module: "localfilesystem"
         },
     },
     ```

3. **Enable Project Mode**

   * This feature enables version control in Node-RED.

   * Set the `enabled` value to `true` on **line 338**:

     ```javascript
     projects: {
         /** To enable the Projects feature, set this value to true */
         enabled: true,
         workflow: {
             /** Set the default projects workflow mode.
              *  - manual - you must manually commit changes
              *  - auto - changes are automatically committed
              * This can be overridden per-user from the 'Git config'
              * section of 'User Settings' within the editor
              */
             mode: "manual"
         }
     },
     ```

4. **Save and Restart**

   * Save the changes to `settings.js`.
   * Restart the PlanktoScope using the **Reboot** button in the Node-RED dashboard:\
     [http://192.168.x.x/ps/node-red-v2/ui/#!/8](http://%3Cyour-planktoscope-ip%3E/ps/node-red-v2/ui/#!/8).

***

With these steps, your PlanktoScope is fully configured and ready for use.

## **Link Node-RED to GitHub**

After rebooting, access the dashboard editor at: [http://192.168.x.x/admin/ps/node-red-v2/](http://%3Cyour-planktoscope-ip%3E/admin/ps/node-red-v2/).

Node-RED will display a pop-up inviting you to clone the dashboard repository. Click on **Clone Repository**.

![Clone Repository](https://raw.githubusercontent.com/PlanktoScope/dashboard/refs/heads/main/img/node-red-clone-repo.png)

### **Enter Your GitHub Credentials**

![Setup Version Control Client](https://raw.githubusercontent.com/PlanktoScope/dashboard/refs/heads/main/img/setup-your-version-control-client.png)

To complete the form, you need to create a Personal Access Token (classic):

1. Visit <https://github.com/settings/tokens>.
2. Click **Generate new token** or **Generate new token (classic)**.
3. Enter a name for the token, such as **node-red**.
4. Select **No Expiration** and check the **repo** and **user** scopes.
5. Click **Generate token** at the bottom of the page.

![New Personal Access Token](https://raw.githubusercontent.com/PlanktoScope/dashboard/refs/heads/main/img/new-personal-access-token-classic.png)

Copy the generated token from GitHub. Complete the remaining fields in the Node-RED form using your GitHub credentials, and click **Clone Project** to link Node-RED to your GitHub account.

![Clone a Project](https://raw.githubusercontent.com/PlanktoScope/dashboard/refs/heads/main/img/clone-a-project.png)

### **Clean Up Existing Palettes**

Remove unnecessary palettes to streamline your Node-RED setup:

* node-red-contrib-dir2files
* node-red-contrib-gpsd
* node-red-contrib-python3-function
* node-red-contrib-ui-multistate-switch
* node-red-dashboard
* node-red-node-pi-gpio
* node-red-node-ui-list

### **Install Required Nodes**

After cleanup, install the following nodes:

* `@flowfuse/node-red-dashboard`
* `@flowfuse/node-red-dashboard-2-ui-flowviewer`

***

## **Read and Write Data in global.json**

With Context Storage enabled, data can be stored in a file located at:

[http://192.168.x.x/admin/fs/files/home/pi/.node-red/context/global/global.json](http://192.168.x.x/).

### **Read Data**

To retrieve a value stored in this file, use the following script in a Function Node:

```javascript
// Retrieve the global variable
msg.variable = global.get('variable');
return msg;
```

### **Template Node to Display and Modify Data**

```html
<template>
    <v-text-field
        label="My variable"
        variant="outlined"
        v-model="msg.variable"
        @update:model-value="send({ variable: msg.variable })"
    ></v-text-field>
</template>
```

### **Write Data**

To set a value in the file, use the following script in a Function Node:

```javascript
// Set a value in the global context
global.set('variable', msg.variable);

return msg;
```

