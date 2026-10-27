---
title: TJREVERB Iridium Comms Protocol
date: 2022-11-25
---
#Cubesat #Iridium #TJSpace 

![[images/reverb-flatsat.jpg]]

This is one of my proudest contributions to the TJREVERB project, even though it is something that probably happens in industry all the time. A couple months into the year, after we had decided to use Iridium as our primary radio, and had finally set up communications with a groundstation email, I realized that simply parsing data to string and encoding to ASCII was incredibly inefficient, so I designed a protocol to encode numerical data into byte floats, and information about the message such as date and command to as dense a header as possible.

## How does Iridium work

Iridium is a satellite phone company that operates a constellation of communications satellites in Low Earth Orbit. TJREVERB's official mission is to evaluate the viability of using this network as a primary radio on a CubeSat. TJREVERB has a 9602N Short Burst Data (SBD) modem on board (referred to by Iridium as the Iridium Subscriber Unit (ISU)), which sends and receives messages to the Iridium constellation. The constellation then communicates these messages to Iridium's groundstation, called the Gateway, using the Gateway Subsystem (GSS). The gateway then sends and receives messages via email to our purely software groundstation. Each message transfer is full duplex, consisting of one Mobile Originated (MO) and one Mobile Terminated (MT) message. Each MO and MT message has a respective Message Sequence Number (MSN) which increments by one at each message transfer. The flight computer, our Raspberry Pi, that communicates with the modem by UART is considered the Field Application (FA).

![[iridium_overview.png|Image credit: Cubic-i Ltd.]]

Iridium is advantageous primarily because it eliminates the need for us to operate a hardware groundstation. We simply send emails to the Iridium SBD service and receive data from the satellite back in emails from the service. Iridium also implements its own handshaking and checksum protocols, so we don't have to worry about making sure we have a contact window or making sure all the data in a packet was transmitted within that window. Finally, Iridium implements built in functions between the constellation and the modem for signal strength (CSQ), geolocation measured using dopper shift from constellation satellites, date and time of the constellation (measured in the number of 90ms intervals since the Iridium epoch, which as of now is 11 May 2014 at 14:23:55 UTC), and ring alerts which indicate when an MT message is ready to be received from the constellation. This last feature was not used on TJREVERB, and we instead just polled the constellation for any MT messages on defined intervals.

## The Protocol

TJREVERB's behavior is mostly dependent on ground commands. Most messages returned from the satellite will be in response to commands from the ground, with a few exceptions. All commands to the satellite will result in a return message, except for the power reset command. Commands are defined in the command registry and classified by three letter strings. The command message format is as follows:

Command byte | args ->

Commands are quite simple, with the first byte being the command being sent and the second and beyond being the arguments. Most commands take numerical data (see below for encoding protocol), but a couple commands take ASCII string data. For these, the argument bytes are decoded as ASCII characters in a string. One command, ARS, simulates the execution of another command, so the first argument byte is another command byte.

The data return message format is much more complicated, and is as follows:

Settings byte | index byte | date msb | date lsb | \[descriptor byte] | \[msn msb | msn lsb] | data ->

The settings byte indicates what type of message the packet is transmitting. The second LSB indicates whether the transmission is in response to a command (1) or unsolicited (0). The first LSB indicates whether the transmission is to be decoded numerically (1) or as an ASCII string (0). This byte also determines whether to decode the descriptor byte and MSN bytes. If the command is unsolicited, it will not include an MSN, because it is not in response to a ground command. If the command is unsolicited AND containing string data, it will not include a descriptor, because it is not in response to a defined command. Numerical unsolicited data will still have a descriptor to describe what kind of data is being returned. Unsolicited strings are typically satellite error messages.

The index byte indicates the index of the packet if it was split, starting from zero and incrementing by one. Splitting packets is sometimes required to send down large volumes of data while staying within the 300 byte packet size limit of Iridium. This allows large data to be pieced back together on the ground

The two byte date value indicates the date, hour, and minute UTC that the command was executed or message was produced. This is so that we know when certain data was collected, regardless of how long the command sat in the command queue or how long the return message sat in the transmission queue waiting to be transmitted from the satellite. It is encoded byte wise as follows:

0bDDDDDHHHHHMMMMMM

Where D is date, H is hour, and M is minute. In other words, Timestamp = (date << 11) | (hour << 6) | (minute). An alternative would have been to mod a UTC timestamp and divide into minutes rather than seconds, but this would have been less convenient to decode and would have had less predictable rollover periods.

Descriptor will only appear in numerical or response packets, and describes the data being sent, or the command which was issued to solicit the data. These are the same as the command byte in the command message format.

MSN is a two byte number only present in response packets that coincides with the message sequence number of the message containing the command which prompted the data return. It is omitted from unsolicited packets as they are not triggered by a command

All bytes after the MSN byte are data bytes. Error and announcement messages are primarily encoded as ASCII. Unsolicited numerical is mainly used in proof of life beaconing, which includes statistics about the mission. In the case of non numerical (ASCII) encoding, each of the data bytes will be decoded as a single ASCII character. Each character only corresponds to a single byte, so UTF-8 is not always supported. In the case of numerical encoding, each number is represented as a 3 byte signed float, which allows for 5 significant figures of precision, with exponent from 10^-16 to 10^15.

  |exp||------coeff------|  
0bSXXXXSXXXXXXXXXXXXXXXXXX

- The first five bits are the exponent, in twos complement format, meaning the values range from -16 to 15 (First bit is the sign bit)
- The last 19 bits are the coefficient, in twos complement format, meaning the values range from -262144 to 262144 (effectively -99999 to 99999 according to actual implementation) (Sixth bit is the sign bit)

Coefficient is treated as a decimal between 0 and 10, so if the bytes read 356, they will be treated as 3.56. Numerical data is transmitted MSB First.

## Implementation

The full Iridium driver is available [here](https://github.com/TJREVERB/pfs-rewrite/blob/flight-dev/Drivers/iridium.py).

### Command Decode Implementation

![[reverb-decode.png]]

### Return Data Encode Implementation

![[reverb-encode.png]]

  
The encode and decode functions on groundstation are similar, but inverted.