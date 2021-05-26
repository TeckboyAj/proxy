# HTTP Proxies

In a few countries there’s a lot of restrictions according to Internet usage, restricted websites, banned web services or any kind of internal censorship. In most of the times people appeal for anonymity services to bypass that bar, one common element is the Web Proxy.

A **Web** or **HTTP proxy** is a server that acts **middle man between two communications (client - server)**, in a certain way, this **proxy will mask your IP address** by giving you an **alternative internet identity**.

A proxy is used for more than privacy, this tool helps to bypass filters, logging, and eavesdropping, caching, filtering blocking, manipulate and modify network traffic and more.

I’ll emphasize on network traffic on this writing. Have you ever think about what can happen if you use a free proxy service? Your privacy can be guaranteed?.

There’s a lot of paid tools that can be trusted, but a lot of people do not pay for this service. The person or group controller a proxy can see your traffic, especially if it is a free proxy.

### How's it works?

There’s a lot of proxies out there. I’m going to shortly explain the types of proxies and how it works.

##### Forward Proxies

For this type, the client sends the request to the proxy and fetch the response on behalf. Where the client can or not know about the proxy, but the server doesn’t know about the proxy at all

The order will be something like:

​	- Client => Proxy (Internet) <= Server

##### Reverse Proxies

This is used to hide behind network architecture for distributing load traffic to the servers. The connections are made to the proxy instead of the servers, and then, the same proxy will handle the request upon.

It's some like this:

​	- Client => Proxy (Internet) => Servers

##### Transparent Proxies

This kind of proxy does not modify any information without any client configuration. This is commonly used for ISP for faster responses.

​	- Client => Proxy (Internet) => Servers

#### Great, how do we connect to a Proxy?

This is more a browser settings setup instead of doing anything techie. Let's use **Firefox** for this scenario

Open the **Firefox** browser -> Settings -> Advance Network -> Manual Proxy Settings

Where you are going to add the IP address and the Port provided by the **Proxy Service**

At this point, we are all set to start using a **Proxy**, but there’s some missing part here, we need to install and configure a Proxy Server. I’m gonna be quick for this explanation because later on, I’ll get into details about this.

For this, I recommend using a Linux-Based OS (Debian preferable). We are going to install proxychains, this tool forces any TCP connection made by any application to follow through a Proxy. You can check it [here](https://github.com/haad/proxychains)

##### Installing

Just `apt-get install proxychains`

##### Checking configuration

Check the `/etc/proxychains.conf`

We have to **comment** the `strict_chain`  option, basically this means that if one to the proxies for this list is down, the whole chain will be unusable.

After this, you can **uncomment** the `dynamic_chain` option, regardless if all the proxies are available the chain will work, dead proxies are skipped.

The **ProxyList** will use by default the **Tor network** in case you won't put something on the **ProxyList** in order to anonymize you.

##### How to use it?

As a non-root user, do the following:

`proxychains4 firefox`

Leave this Terminal session open in order to persist the connection, and with the **Firefox** settings correctly placed, check your public IP, the network speed might be slower but you will be bypassing some privacy controls by doing this.





