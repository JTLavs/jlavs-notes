---
title: Network Protocols - TCP
date: "2022-07-27"
tags: ["networks"]
---
- Operates on the Transport Layer of the OSI model
- Reliable, full featured transport layer protocol
- Divides data into segments
- TCP Header fields:
    - Source port (16 bit) - identifies source port
    - Destination port (16 bit) - identifies destination port
    - Sequence number (32 bits) - used for data reassembly
    - Acknowledgement no. (32 bits) - used to indicate data received and next byte expected
    - Header length (4 bits) - indicates length of TCP segment header
    - Reserved (6 bits) - Reserved for future use
    - Control bits (6 bits) - Includes bit codes, flags which indicate purpose/function of TCP segment
    - Window size (16 bits) - Indicates no. of bytes that can be accepted at once
    - Checksum (16 bits) - Used for error checking of segment header & data
    - Urgent (16 bits) - Used to indicate if contained data is urgent

Control bits are also known as flags. The 6 controls flags are:
- URG (Urgent)
- ACK (Acknowledgement) - used in establishment & termination
- PSH (push function)
- RST (reset the connection when timeout/error occurs)
- FIN (no more data from sender and used for session temination)