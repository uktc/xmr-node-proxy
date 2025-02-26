# xmr-node-proxy


## Setup Instructions

Based on a clean Ubuntu 16.04 LTS minimal install

## Deployment via Installer

1. Create a user 'nodeproxy' and assign a password (or add an SSH key. If you prefer that, you should already know how to do it)

```bash
useradd -d /home/nodeproxy -m -s /bin/bash nodeproxy
passwd nodeproxy
```

2. Add your user to `/etc/sudoers`, this must be done so the script can sudo up and do it's job.  We suggest passwordless sudo.  Suggested line: `<USER> ALL=(ALL) NOPASSWD:ALL`.  Our sample builds use: `nodeproxy ALL=(ALL) NOPASSWD:ALL`

```bash
echo "nodeproxy ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
```

3. Log in as the **NON-ROOT USER** you just created and run the [deploy script](https://raw.githubusercontent.com/uktc/xmr-node-proxy/master/install.sh).  This is very important!  This script will install the proxy to whatever user it's running under!

```bash
curl -L https://raw.githubusercontent.com/uktc/xmr-node-proxy/master/install.sh | bash
```

3. Once it's complete, EDIT `config.json` .
4. Run: `source ~/.bashrc`  This will activate NVM and get things working for the following pm2 steps.
8. Once you're happy with the settings, go ahead and start all the proxy daemon, commands follow.

```shell
cd ~/xmr-node-proxy/
pm2 start proxy.js --name=proxy --log-date-format="YYYY-MM-DD HH:mm Z"
pm2 save
```
You can check the status of your proxy by either issuing

```
pm2 logs proxy
```

or using the pm2 monitor

```
pm2 monit
```

## Known Issues

VMs with 512Mb or less RAM will need some swap space in order to compile the C extensions for node.  Bignum and the CN libraries can chew through some serious memory during compile.  In regards to this, one of our users has put together a guide for T2.Micro servers: https://docs.google.com/document/d/1m8E4_pDwKuFo0TnWJaO13LDHqOmbL6YrzyR6FvzqGgU (Credit goes to MayDay30 for his work with this!)

If not running on an Ubuntu 16.04 system, please make sure your kernel is at least 3.2 or higher, as older versions will not work for this.

Many smaller VMs come with ulimits set very low. We suggest looking into setting the ulimit higher. In particular, `nofile` (Number of files open) needs to be raised for high-usage instances.

If your system MUST have AES-NI, then it will throw an error during the node-multi-hashing install, as this requires AES-NI.


## Performance

The proxy gains a massive boost over a basic pool by accepting that the majority of the hashes submitted _will_ not be valid (does not exceed the required difficulty of the pool).  Due to this, the proxy doesn't bother with attempting to validate the hash state nor value until the share difficulty exceeds the pool difficulty.

In testing, we've seen AWS t2.micro instances take upwards of 2k connections, while t2.small taking 6k.  The proxy is extremely light weight, and while there are more features on the way, it's our goal to keep the proxy as light weight as possible.

## Configuration Guidelines

...

## Developer Donations

The proxy is pre-configured for a 1% donation. This is easily toggled inside of it's configuration. If you'd like to make a one time donation, the addresses are as follows:

* XMR - 43vH9PSSEd5UK2EeLNPKhQQm3QaGMHBeTYKTpca5VY1P7AwVZ9MMDksaQg9FgRRqi7ERiebk5kuHAQ5kCx53ndxG9w4yh8F
* BTC - bc1qsnj42w5qyphp77e9rm4kmhcjwwmkds9tghcv0h

## Installation/Configuration Assistance

If you need help installing the pool from scratch, please have your servers ready, which would be Ubuntu 16.04 servers, blank and clean, DNS records pointed.  These need to be x86_64 boxes with AES-NI Available.

Installation assistance is 4 XMR, with a 2 XMR deposit, with remainder to be paid on completion.  
Configuration assistance is 2 XMR with a 1 XMR deposit, and includes debugging your proxy configurations, ensuring that everything is running, and tuning for your uses/needs.  

SSH access with a sudo-enabled user will be needed for installs, preferably the user that is slated to run the pool.

Please contact me at: support@derko.net

## Known Working Pools

* [XMRPool.net](https://xmrpool.net)
* [supportXMR.com](https://supportxmr.com)
* [pool.xmr.pt](https://pool.xmr.pt)
* [minemonero.pro](https://minemonero.pro)
* [XMRPool.xyz](https://xmrpool.xyz)
* [ViaXMR.com](https://viaxmr.com)
* [mine.MoneroPRO.com](https://mine.moneropro.com)
* [MinerCircle.com](https://www.minercircle.com)
* [xmr.p00ls.net](https://www.p00ls.net)
* [MoriaXMR.com](https://moriaxmr.com)
* [MoneroOcean.stream](https://moneroocean.stream)
* [SECUmine.net](https://secumine.net)
* [Chinaenter.cn](http://xmr.chinaenter.cn)
* [XMRPool.eu](https://xmrpool.eu)

If you'd like to have your pool added, please make a pull request here!
