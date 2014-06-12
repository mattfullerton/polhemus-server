---
layout: default
title: Polhemus Server Documentation
---

# Polhemus server
## The device
The Polhemus Liberty is an impressive piece of equipment. There are 8- and 16-sensor versions which can track movement at 240Hz [1]. There is a simple, well documented USB protocol for the device, which also works over a serial port, albeit at a lower frequency [2]. Polhemus provide a "terminal" style program for interacting with the tracker and logging data [3].

## Server
We have developed a program that interacts with the Liberty tracker according to commands sent over UDP. This kind of architecture enables many kinds of clients to work with the tracker even if they are single threaded (e.g. MATLAB). Our specific motivation was to enable MATLAB to interact with the tracker and its data whilst simultaneously logging data continuously to a file.

## Recording data
In general, there are two expected modes of operation for the tracker: polled and continuous [3]. Polling returns the current data of all sensors to the USB port. Continuous streams data continuously; the computer reads data over USB and should try to keep up. There is a USB buffer on the Liberty side but so far our experience has been that we have better results when we turn it off. The server includes a buffer internally in case there are delays in file writing.

## Server architecture
The server runs with up to three threads:

1. The main thread waits for commands to be sent over UDP. The commands are given below.
2. A thread which continuously reads data from the tracker and stores it using multiple buffering [4]. The thread also updates a single buffer which contains the latest data read from the tracker.
3. A thread which continuously reads data from the buffer and writes it to a file.

The clever bit is that while data is being read and logged in continuous mode, you can poll for data like you would in polled mode thanks to the buffer containing the latest data. What's more, we monitor, check and clean this data to ensure that you get good data back and not, e.g. multiple records (which can happen when USB buffering is turned on).

1. [Polhemus Liberty] (http://polhemus.com/motion-tracking/all-trackers/liberty)
2. [piterm download page] (ftp://ftp.polhemus.com/pub/Linux_tools)
3. [Liberty User Manual] (http://polhemus.com/_assets/img/LIBERTY_User_Manual_URM03PH156-H.pdf)
4. [Multiple Buffering] (http://en.wikipedia.org/wiki/Multiple_buffering)
