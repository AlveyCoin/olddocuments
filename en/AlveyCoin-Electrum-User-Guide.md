# Alvey Electrum User Guide

Alvey Electrum is a lightweight Alvey wallet based on the popular bitcoin wallet [Electrum](https://electrum.org/)。

Compared with the Alvey Core wallet, Alvey Electrum needs smaller disk space, synchronization blocks needed shorter time, it supports multiple signature and hardware wallets, also cold wallet model support, support import mobile wallet word mnemonic, adopt the SPV validation to ensure the safety at the same time.

## How to use it

### Installation

Open [https://github.com/alveyproject/alvey-electrum/releases/latest](https://github.com/alveyproject/alvey-electrum/releases/latest) or [https://alveyeco.io/wallet](https://alveyeco.io/wallet) to find the latest download link.


If you are using linux, please refer to [https://github.com/alveyproject/alvey-electrum/blob/master/README.md](https://github.com/alveyproject/alvey-electrum/blob/master/README.md).

### Create & Recover

The first you open the wallet, choose “Auto Connect” to connect the servers.

Next, choose the type of wallet you want to create.

![](http://ojaivn2ch.bkt.clouddn.com/cfaf17237ff138adf4c601eadedea24b.png)

It is recommended to use `Standard Wallet` for common users，choose `Create a new seed` to create a new wallet, choose `I already have a seed` to recover you old wallet。

If you want to recover a mnemonic from alvey mobile wallet, choose `Alvey mobile wallet compatible`.

### Send and Receive ALVEY

![](http://ojaivn2ch.bkt.clouddn.com/d2ef6659a47a55686b6c6ef2fec58331.png)
All the related transactions are shown in `History` page.
<br>

![](http://ojaivn2ch.bkt.clouddn.com/7cdacbe408a98d3a00a9e128beb26e30.png)
In `Send` page, input the sender's Alvey address and the amount you want to send, use the default fee rate, click `send`.
<br>

![](http://ojaivn2ch.bkt.clouddn.com/4e994a885963f09389d2c1be10e5924e.png)
You can get your address or qr code in `Reveive` page.


### ARC20 Token
   
![](https://s.alvey.site/uploads/9aaa8fa63651af737cceb6b59f339b45.png)
Click `Tokens` on the tab to go to token page.  
<br>

![](https://s.alvey.site/uploads/213e6caa5a8640e62ab616541de12627.png)
Right click at the while space, choose `Add Token`.
<br>
   
![](https://s.alvey.site/uploads/0f92a355a82b1326493e2d643319f383.png)
Input the token contract address and select your binding address.
You can find token contract address on [https://alveyscan.com/](https://alveyscan.com/) by searching the token name.
<br> 
   
![](https://s.alvey.site/uploads/4bb33de12c19de3b59f8df2c90a704f1.png)
You can see the token balance and transfer history once added.
<br>
   
![](https://s.alvey.site/uploads/4eaa85f66778d2e051b7f1ddcb5107b9.png)
Right click on your token, choose the functions.
<br>
   
![](https://s.alvey.site/uploads/53eac2382ad17d543c060261497299b5.png)
Go to send page by click `Send`, input sender's address and the amount you want to send.

### Multisig 
Refer to [http://docs.electrum.org/en/latest/multisig.html](http://docs.electrum.org/en/latest/multisig.html)

### Hardward wallet

Here we take ledger as an example.

#### Before you start 
Verify you have:

* a Ledger Nano S or a Ledger Blue;
* Windows 7+, macOS 10.8+ or Linux;
* a USB port. Use an adapter for USB-C ports;
* Google Chrome / Chromium installed.
* Alvey Electrum installed.

#### Install the Alvey application
* Launch the [Ledger Manager](https://support.ledgerwallet.com/hc/en-us/articles/115005173209-How-to-use-the-Ledger-Manager).
* Connect and unlock your device using your PIN code. 
* Find Alvey in the list on the APPLICATIONS tab.
* Click on the green arrow button.
* Press the right button on your device when asked: Allow Ledger Manager? 
* Verify that the application is correctly installed on your device by viewing its icon on the dashboard. 
Note: Check the solutions in this article if the device displays Unable to install application.

#### Use Alvey Electrum 

* Connect and unlock your device with your PIN code.
* On a Ledger Blue only: Open the Alvey app and set Browser support to No within the settings of the app.
* Launch Alvey Electrum on your computer. The wallet will open after synchronization.
* create new wallet, choose `Standard wallet` -> `Use a hardware device` , click `Next` to continue.
* ![](http://ojaivn2ch.bkt.clouddn.com/0b2b70d7163e15df5efe59448d54ebc7.png)

## What's more

Alvey Electrum is under beta test now, issue reporting is welcomed [https://github.com/alveyproject/alvey-electrum/issues](https://github.com/alveyproject/alvey-electrum/issues)



