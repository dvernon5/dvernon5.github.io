---
layout: post
title: How the Internet Actually Works: From Click to Screen
subtitle: A step-by-step breakdown of the hidden machinery behind every webpage
---

# How the Internet Actually Works: From Click to Screen

## A Step-by-Step Breakdown of the Hidden Machinery Behind Every Webpage

I used to think daily use meant I understood the internet. Wrong. The moment I dug in, I saw how many pieces must align for data to reach my screen. Let’s trace the path, step by step, without fluff.

### 1. Your Device Starts the Request
You type "google.com" in the browser. The device does not know the location. It relies on the TCP/IP stack, the protocol suite that governs all internet communication.

### 2. Modem and Router at Home
The modem converts the ISP signal (cable, fiber, or DSL) into Ethernet. The router assigns local IP addresses (like 192.168.1.10) and builds a packet. Each packet contains source IP, destination IP (still unknown), and payload.

### 3. ISP: Gateway to the Wider Net
The packet leaves the router and enters the ISP network. The ISP provides the public IP (for example, 73.42.198.12) and forwards the packet upstream.

### 4. DNS Lookup
The device queries a DNS server (often 8.8.8.8 or the ISP default) to resolve "google.com" to an IP, such as 142.250.190.78. The packet now carries the real destination.

### 5. Routing Across the Internet
Routers at each hop read the destination IP and forward the packet. Tools like traceroute reveal the path: local ISP, regional backbone, transcontinental links. TCP guarantees delivery; lost packets trigger retransmission.

### 6. Server and CDN Response
The packet arrives at a data center or CDN edge node. The server processes the request, builds the response (HTML, CSS, images), and splits it into packets for the return trip.

### 7. HTTP/HTTPS Protocol
The browser sends "GET / HTTP/1.1". The server replies with status 200, headers, and body. HTTPS adds TLS encryption for security.

### 8. Reassembly and Display
TCP reassembles packets in order. The browser parses HTML, fetches assets via new requests, and renders the page. Latency often stays under 100 ms.

### 9. Physical Infrastructure
Traffic travels over fiber optic cables, undersea lines, and Internet Exchange Points where networks interconnect. Wi-Fi uses the same TCP/IP rules over radio.

### 10. Key Distinctions
- **Internet**: global network of networks.
- **Web**: HTTP-based service on the internet.
- **Website**: group of related webpages.
- **Webpage**: single document.

I plan to explore QUIC, IPv6, and BGP next. The deeper I go, the more the system’s reliability stands out.

Which step surprises you most? Comment below.
