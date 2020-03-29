---
title: "Your Local Minecraft Server on the Internet"
date: 2020-03-28T21:59:04+05:30
draft: false
---

I recently got back into minecraft because of a friend. Me being interested mostly in redstone contraptions never got into multiplayer before. Since minecraft is pretty laid back, I thought it would be a great place to hangout with my friend. Whatever may be your reason to play minecraft, when you want to play with another person somewhere else, you start running into a few issues. 

I had a free trial month for Realms available to me, so we started our game there. And towards the end of the trial we decided that this is the pace of the game that we like and we don't want to share the world with people that we didn't know. Unfortunately that meant dishing out some excessive recurring payments to keep our access to realms or run a minecraft server on my local machine and expose that over the internet.

## Ngrok

[Ngrok](https://ngrok.com/) is a tool that gives you a secure tunnel to your localhost. I was already familiar with ngrok because it's something I use for my mobile development needs. When I want my mobile application to consume APIs from a local server I use Ngrok.

Setting up and using ngrok is a piece of cake. It doesn't need you to worry about NAT, firewalls , dynamic IP, DHCP. You just need to 
* Go to ngrok's site and create an account to get an access token. Use the free plan if you don't mind the domain name  changing for every session.
* Download ngrok tool and extract it to a convenient location.
* Replace with your auth token received above and run this ```./ngrok authtoken <YOUR_AUTH_TOKEN>``` to register your client with your account.
* Run ngrok ```./ngrok tcp 25565```.

>Assuming that you have gone with a vanilla minecraft server settings, the port by default is `25565`. You can change it if you want in `server.properties`


Voila! You are done. Ask your friend to connect to your server. The link will be on your terminal now where `ngrok` is running. 

### Cons 
This set-up is quite prone to latency. Since ngrok client sets up a tunnel with ngrok servers which are in US (at least the servers I got on the free plan) and my friend is connecting from only a couple of  kms away, the latency is twice as higher than necessary. If I can expose they can reach my server directly that should be faster.



## No-ip

This set-up has more steps but guaranteed to be faster than the previous solution. Thankfully [Noip](https://www.noip.com) has it's own document specific to hosting a minecraft server using it.

* Go to noip's website and sign up (I used the free account) and create and register a domain name. 
* Download and install no-ip's Dynamic DNS Update Client (DUC). 
* Start up the tool and sign in with your noip account
* Wait for the statuses to turn green. Your public IP, that the ISP has given you should show up here. Congrats your IP address has a name now. 

 > There are possibilities that your ISP will not let connections reach your router because of their NAT settings or something else. If this is the case, you might need to talk to your ISP (don't know where that'll get you) or change your ISP.


Assuming that your PC running your minecraft server is in a wireless network, your public IP is reaching your router and not your server yet. Time to go to the router and fiddle with some settings so that the connections from the internet passes through / forwards to your machine on your wifi/local network. This is port forwarding and is part of Network Address Translation or NAT. 

>This is where it gets troublesome, these settings vary with router manufacturers and models.

* Go to the router's setting page. You should be able to access it by going to your router's ip (that's the default gateway in your machine's network setting OR interestingly go your `noip` registered domain). 
* Login with your admin credentials.
* Find Port forwarding / Virtual Server setting. These are usually nested somewhere in **Advanced > NAT**.

>It would be reliable if you enable static ip for the machine that is going to host your server. Otherwise you have to repeat the below steps each time the local IP of your machine changes.

In port forwarding/virtual server

* Add the WAN port as a port that you want to expose to the world.
* Set the LAN port as `25565`, given you are running minecraft server on `25565`.
* Set the LAN IP Address as the local address of your hosting machine. (Some routers might let you specify mac address instead. This would let you not worry about DHCP)
* The protocol can be set to `tcp` / `tcp + udp`
* Start the minecraft server on your machine.

>Use the [portchecktool](https://www.portchecktool.com) from noip and enter the port as `25565`(Assuming that is the port that you have chosen to expose as the WAN port). The site should tell you if it can see the service running on that port and is not blocked by ISP/firewall/NAT.

You can also use `telnet` to check if your machine's port is open. Just make sure you are hitting the server from another network. The router is not going to port forward for a network request from within the network. You can connect to your server using your local IP.


If you are having trouble try changing the WAN port and rechecking using telnet or portchecktool. If you found your server, we are all good! Give your friend your server address. 
`<registered_domainname>:<wan_port>`.

**ENJOY PUNCHING TREES**

---

I figured out the right router setting for me by running an http server on my local machine first, making sure I can access it from my localhost, setting up noip and the port forwarding and then trying to access the page from my mobile using carrier data instead of wifi. Once I was sure the page was showing up on devices over the internet. I went to simply updating the ports and running the minecraft server instead.


