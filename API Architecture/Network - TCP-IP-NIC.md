# **Network Notes: TCP, IP, NIC**

***

## **1️⃣ TCP (Transmission Control Protocol)**

### **Definition**

-   TCP is a **transport layer protocol** (OSI Layer 4) used for **reliable, ordered, and error-checked delivery** of data between applications over a network.
    

### **Key Features**

1.  **Connection-oriented**: Establishes a connection before sending data (three-way handshake).
    
2.  **Reliable delivery**: Ensures all data reaches the destination; lost packets are retransmitted.
    
3.  **Segmentation & reassembly**: Breaks large messages into smaller segments; reassembles them at the receiver.
    
4.  **Flow control**: Manages how much data is sent before waiting for acknowledgment.
    
5.  **Error detection**: Uses checksums to detect corrupted packets.
    

### **Three-way Handshake**

1.  **SYN**: Client → Server (requests connection)
    
2.  **SYN-ACK**: Server → Client (acknowledges)
    
3.  **ACK**: Client → Server (connection established)
    

### **Example**

`curl https://example.com`

-   The browser sends HTTP data using **TCP segments**.
    
-   Each segment contains sequence numbers for ordering.
    

### **Use Cases**

-   Web browsing (HTTP/HTTPS)
    
-   Email (SMTP, IMAP, POP3)
    
-   File transfer (FTP)
    

***

## **2️⃣ IP (Internet Protocol)**

### **Definition**

-   IP is a **network layer protocol** (OSI Layer 3) responsible for **addressing and routing packets** across networks.
    

### **Key Features**

1.  **Addressing**: Each device has an IP address (IPv4: 32-bit, IPv6: 128-bit).
    
2.  **Packet delivery**: Breaks data from TCP into packets and routes them through the internet.
    
3.  **Connectionless**: IP does not guarantee delivery; it simply sends packets. Reliability is handled by TCP.
    
4.  **Routing**: Determines the path from source to destination using routers.
    

### **Packet Structure (Simplified IPv4)**

| Field | Purpose |
| --- | --- |
| Source IP | Sender’s address |
| Destination IP | Receiver’s address |
| TTL | Time to live (prevents infinite loops) |
| Protocol | Indicates upper-layer protocol (TCP/UDP) |
| Checksum | Header error check |
| Data | Encapsulated TCP/UDP segment |

### **Example**

`Data from curl: [HTTP request] → TCP segment → IP packet → sent over internet to server IP 93.184.216.34`

### **Use Cases**

-   Routing packets across the internet
    
-   Supporting multiple devices on a network
    
-   IP addressing for all network communication
    

***

## **3️⃣ NIC (Network Interface Card)**

### **Definition**

-   NIC is a **hardware component** that **connects a computer to a network** and handles the transmission and reception of data frames over the physical medium.
    

### **Key Features**

1.  **Frame encapsulation**: Wraps IP packets into Ethernet/Wi-Fi frames.
    
2.  **Physical signaling**: Converts digital data to electrical, optical, or radio signals.
    
3.  **MAC address**: Unique hardware identifier for communication on the local network.
    
4.  **Receive/transmit buffers**: Temporarily stores data before sending or after receiving.
    

### **Example**

-   Ethernet NIC:
    
    -   Converts IP packets into electrical signals sent through a cable.
        
-   Wi-Fi NIC:
    
    -   Converts packets into radio signals to communicate with an access point.
        

### **How it Works with OS**

1.  OS prepares **IP packet** with TCP segment inside.
    
2.  NIC receives packet from OS driver → encapsulates into **frame** → sends physical signals.
    
3.  On receiving side: NIC converts signals → extracts frame → passes IP packet to OS → TCP → Application.
    

***

## **4️⃣ How TCP, IP, and NIC Work Together**

**Flow Example:**

`Browser sends HTTP request → TCP segment created → IP packet created → NIC converts packet to electrical/wireless signal → travels over network → server NIC receives → IP layer reads packet → TCP layer reassembles → Web server application processes HTTP`

**Visualization:**

`Application (HTTP)         ↓ TCP (segments, reliable)        ↓ IP (packet, routing)        ↓ NIC (frames, physical signals)        ↓ Physical medium (cable/wifi)        ↓ Server NIC → IP → TCP → Application`

***

## **5️⃣ Quick Comparison Table**

| Protocol/Hardware | Layer | Purpose | Example |
| --- | --- | --- | --- |
| TCP | Transport | Reliable delivery, connection-oriented | curl, browser HTTP |
| IP  | Network | Addressing & routing, connectionless | Sending packet to server IP |
| NIC | Data Link / Physical | Converts packets to signals, sends/receives frames | Ethernet card, Wi-Fi adapter |

***

✅ **Summary**

-   **TCP** = reliability + ordering
    
-   **IP** = addressing + routing
    
-   **NIC** = hardware interface + physical transmission