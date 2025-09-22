---
order: 15
title: Wireless Coding
---

You would think something like this is straightofrward, exactly like it would be on OnBotJava. Just connect to the Control Hub Wi-Fi, abd upload the code. However, it isn't this simple.

In order to build a program in Android Studio, you need internet access to fetch libraries. So you can't just connect to the Control Hub Wi-Fi.

So, in order to code wirelessly, you will need 2 available connections to your computer (atleast one of which being Wi-Fi). This can be done by
1. **Ethernet**: This is the best solution for PCs as it provides a fast connection to the internet, and it quite common already. By using Ethernet for your main internet connection, you free up your Wi-Fi connection to connect to the Control Hub.
2. **Secondary Wi-Fi Card**: This is a good solution for PCs, but it is not recommended for mobile devices as it can be challenging to set up.
3. **USB Wi-FI Adapter**: This is the simplest solution, and is recomended for mobile devices. It is extremely simple to set up, just plug it into your device, and it will provide you with a second Wi-Fi connection.

For the purposes of this guide, we will be using a USB Wi-Fi Adapter, specifically the [TP-Link N150](https://www.tp-link.com/us/home-networking/usb-adapter/tl-wn725n/). We will connect to the internet with our main Wi-Fi card to prioritize speed, and then use the USB Wi-Fi Adapter to connect to the Control Hub.

Once you are connected to both networks, follow these steps:
1. Open the ADB Wi-Fi tab on the right side of Android Studio
2. Connect to the Control Hub with a USB-C cable
3. Click "Connect" on the REV Robotics Control Hub
4. Unplug the USB cable, you should stay connected to the Control Hub wirelessly now
5. To verify that you are connected correctly, restart the Control Hub and make sure it can reconnect without the USB cable

Thats it for setup! Now it's time for Pedro Pathing.