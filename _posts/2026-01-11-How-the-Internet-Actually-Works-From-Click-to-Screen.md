---
layout: post
title: How the Internet Actually Works, From Click to Screen
subtitle: A Step-by-Step Breakdown of the Hidden Machinery Behind Every Webpage Page
---

---
I used to think daily use meant I understood the internet. That assumption didn't survive five minutes of actual research. Once I started digging, I realized how many pieces have to align perfectly for data to travel from a server halfway around the world to my screen. Here's how it actually works, step by step.
---
 
It starts the moment you type "google.com" into the browser. Your device has no idea where that is; it just knows you want something. So it leans on the TCP/IP stack, the protocol suite that governs basically all internet communication, to figure out what to do next.
 
Before any data moves, your device needs to get out of your house. The modem handles that first step: it converts your ISP's signal (whether that's cable, fiber, or DSL) into something your local network can use. From there, your router takes over, assigning local IP addresses (something like 192.168.1.10) and building a packet. That packet is the unit of travel for everything that follows. It carries a source IP, a destination IP (still unknown at this point), and the actual payload.
 
Once the packet leaves your router, it enters your ISP's network. The ISP stamps it with a public IP address (say, 73.42.198.12) and passes it upstream. But it still doesn't know the actual address for "google.com." That's where DNS comes in.
 
The device sends a query to a DNS server, often Google's 8.8.8.8 or whatever your ISP defaults to, and gets back a real IP address: something like 142.250.190.78. Now the packet has a destination.
 
From here, the packet moves through a chain of routers. Each one reads the destination IP, makes a forwarding decision, and passes the packet along. If you've ever run a traceroute, you've seen this path laid out: local ISP, regional backbone, maybe a transcontinental link. TCP handles reliability throughout; if a packet gets lost, it gets retransmitted.
 
Eventually the packet reaches a data center, or more likely a CDN edge node located closer to you. The server processes the request, assembles a response (HTML, CSS, images, whatever the page needs) and breaks that response back into packets for the return trip.
 
The browser's side of this exchange runs over HTTP or HTTPS. The browser sends a "GET / HTTP/1.1" request; the server replies with a status code, headers, and the response body. HTTPS layers TLS encryption on top of all of this, which is why the padlock shows up in your address bar.
 
When the packets arrive back at your machine, TCP puts them back in order. The browser then parses the HTML, fires off additional requests for any assets it needs, and renders the page. The whole round trip, every step described above, typically takes under 100 milliseconds.
 
None of this happens over thin air (well, except Wi-Fi, which uses the same TCP/IP rules but over radio). The backbone of the internet is physical: fiber optic cables, undersea lines crossing ocean floors, and Internet Exchange Points where independent networks hand traffic off to each other.
 
A few terms worth keeping straight: the internet is the global network of networks; the web is the HTTP-based service that runs on top of it; a website is a collection of related pages; a webpage is a single document. They're related but not the same thing.
 
I'm planning to go deeper on QUIC, IPv6, and BGP next. The more I learn, the more impressive it is that this system works as reliably as it does.
