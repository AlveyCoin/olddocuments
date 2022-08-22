# Alvey Staking（PoS mining）Tutorial

Alvey employs PoS (Proof of Stake) consensus mechanism, which is different from Bitcoin's PoW (Proof of Work). The mining process in PoS system is called staking. The block producer will get 1ALVEY, as well as the transaction fees and gases as block reward. So the real reward is usually more than 1ALVEY in total.

**Alvey blocks are produced in average every 32s**

Basic requirements for staking：

1. Run a Alvey fullnode, and keep online (Since Alvey is using PoS, we don't need any mining machine, just PC or even Raspberry Pi can run a fullnode);
2. Have some ALVEY in the wallet (fullnode)（Any amount of ALVEY can be used for staking, more ALVEY means higher possibility to stake).

If you have no ALVEY yet, please get some from market before you doing following staking settings.

Currently, Alvey Core wallet is the only wallet that support Alvey PoS staking. Note that other wallets like mobile wallet and Alvey Electrum are not able to stake for the time being.

Two ways to stake:

* Method 1：Staking with alveyd, using command line, suitable for Linux/OSX/Windows/Raspberry Pi users who are familiar with command line tools.
* Method 2：Staking with `alvey-qt` wallet, with GUI, suitable for common users.

Either way works in the same way for staking, so you can choose either method you like.

## Method 1：Staking with `alveyd` (command line)

### 1. Run `alveyd`

To run `alveyd`, please refer to"[How to deploy Alvey node](../Guidance-of-Alvey-Deployment-and-RPC-Settings.md)".

Follow the guidance to run `alveyd`:

```
./alveyd -daemon
```

Staking is default on for alveyd, so no need for other options if you only want to stake.

### 2. Send some ALVEY to your wallet

First you can generate a new address with：

```
./alvey-cli getnewaddress
```

This will generate a new address with Prefix 'Q'. You can send some ALVEY to this new generated address for staking. You can generate as many addresses as you like, and send arbitrary ALVEY as you like for staking.

Note：**The coin should wait for 2000 blocks before being able to stake, i.e. about 17 hours to MATURE.**. 


