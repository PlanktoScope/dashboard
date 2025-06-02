# dashboard

This is the new [Node-RED](https://nodered.org/) project for the [PlanktoScope](https://www.planktoscope.org/).

## Setup

1. **Prepare the SD Card**

   - Download the [latest development PlanktoScope OS](https://github.com/PlanktoScope/PlanktoScope/blob/master/documentation/docs/community/contribute/tips-and-tricks.md#development-os).

   - Flash the `.img.xz` file to an SD card using [Raspberry Pi Imager](https://www.raspberrypi.com/software/).

     - Open Raspberry Pi Imager.
     - Select **"Choose OS"** and locate the PlanktoScope image.
     - Select **"Choose Storage"** and pick your SD card.
     - Click **"Write"** to flash the image.

   - Insert the SD card into the PlanktoScope.

2. **Connect to the Local Network**

   - Use an Ethernet cable to connect the PlanktoScope to your local router.
   - Power on the PlanktoScope.

3. **Connect the PlanktoScope to Wi-Fi**

   - Once the PlanktoScope’s Wi-Fi becomes visible, use your computer or mobile device to connect it to your local router’s Wi-Fi network.

4. **Switch to the Node-RED dashboard project**

- Go to [Node-RED admin](http://192.168.1.84/admin/ps/node-red-v2/)
- In the main menu, select Projects -> Open -> dashboard
- In the history tab, make sure to use the latest version of the dashboard

With these steps, your PlanktoScope is fully configured and ready for use.

## Development

See [Development Environment](https://github.com/PlanktoScope/PlanktoScope/blob/master/documentation/docs/community/contribute/tips-and-tricks.md#development-environment)

---

## Read and Write Data in global.json

With Context Storage enabled, data can be stored in a file located at: [`/home/pi/PlanktoScope/node-red/context/global/global.json`](http://planktoscope.local/admin/fs/files/home/pi/PlanktoScope/node-red/context/global/global.json).

### Read Data

To retrieve a value stored in this file, use the following script in a Function Node:

```javascript
// Retrieve the global variable
msg.variable = global.get("variable")
return msg
```

### Template Node to Display and Modify Data

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

### Write Data

To set a value in the file, use the following script in a Function Node:

```javascript
// Set a value in the global context
global.set("variable", msg.variable)
return msg
```
