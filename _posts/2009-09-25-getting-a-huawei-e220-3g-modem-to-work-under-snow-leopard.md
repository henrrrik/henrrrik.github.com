--- 
layout: post
title: Getting a Huawei E220 3G modem to work under Snow Leopard
created: 1253871498
---
I had some problems getting my Huawei E220 3G USB modem running under Snow Leopard. The drivers from Telia and huawei.com didn't work. Turns out Huawei has another site over at huaweidevice.com with updated drivers. 

Here are the steps I took.
<!--break-->
Note that I don't use the Mobile Connect application. I stuck my SIM card in a mobile phone and disabled the PIN. That way I don't need Mobile Connect to enter it.

1. Download the [latest MobileConnectDriver Mac package from Huawei](http://www.huaweidevice.com/resource/mini/200910149695/testmobile1014/index.html)
2. Install it and reboot.
3. Plug in the modem and wait for the light to go blue. It should be automatically detected as a new network interface by Mac OS X.
4. Open System preferences and the Network pane. Select the **HUAWEI Mobile** interface in the list and enter *\*99#* as the telephone number. Then click **Advanced**. Set **Model** to **GRPS GSM/3G**, set the **APN** to whatever your provider uses (Telia uses *online.telia.se*) and hit **Apply**. 

Voilà, you can now click the **Connect** button to connect. I recommend enabling the **Show modem status in menu bar** option for easy access.

<ins>Updated the driver link.</ins>
