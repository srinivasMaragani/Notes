# 📘 **TCP (Transmission Control Protocol)**

***

## 1. **Definition**

-   TCP stands for **Transmission Control Protocol**.
    
-   It is one of the core protocols in the **TCP/IP suite** (used for almost all communication on the internet).
    
-   Purpose:
    
    -   To provide **reliable**, **ordered**, and **error-checked** delivery of data between applications running on devices in a network.
        

***

## 2. **Why TCP is Needed**

-   When you send data over the internet:
    
    -   Data travels as **packets** (small chunks of information).
        
    -   Without rules, packets could:
        
        -   Arrive **out of order**,
            
        -   Get **lost** in transit,
            
        -   Be **duplicated**,
            
        -   Or get **corrupted**.
            
-   TCP solves these problems by:
    
    -   Breaking data into packets,
        
    -   Numbering them,
        
    -   Ensuring they arrive in the right order,
        
    -   Retransmitting lost packets,
        
    -   Verifying no corruption occurred.
        

👉 **Example:**  
If you’re downloading a file (say 100 MB), TCP ensures **every byte is delivered in order**. Without TCP, you might get a corrupted or incomplete file.

***

## 3. **Relationship with IP**

-   TCP works **on top of IP (Internet Protocol)**.
    
-   IP: Handles addressing and routing (like an envelope telling where to deliver).
    
-   TCP: Ensures the actual data is delivered properly (like confirming the full message arrives intact).
    

📌 Think of **IP as the address system** (street & house number),  
and **TCP as the delivery service ensuring you got the whole package**.

***

## 4. **TCP Features**

1.  **Connection-oriented** → Requires establishing a connection before data transfer.
    
2.  **Reliable delivery** → Guarantees packets are delivered.
    
3.  **Error detection** → Uses checksums to detect corrupted data.
    
4.  **Flow control** → Prevents sender from overwhelming receiver.
    
5.  **Congestion control** → Avoids flooding the network.
    
6.  **Ordered delivery** → Packets arrive in sequence.
    
7.  **Full duplex** → Both sides can send/receive simultaneously.
    

***

## 5. **TCP Segments & Header Structure**

TCP transmits data in **segments**.  
Each segment has a **header** (control info) + **payload** (actual data).

### TCP Header Fields (Important Ones):

1.  **Source Port & Destination Port**
    
    -   Identifies which process/application is sending/receiving.
        
    -   Example: Web traffic usually uses port **80 (HTTP)** or **443 (HTTPS)**.
        
2.  **Sequence Number**
    
    -   A unique number assigned to each byte of data.
        
    -   Ensures ordering of packets.
        
3.  **Acknowledgment Number**
    
    -   Confirms receipt of data.
        
    -   Tells sender: "I got everything up to byte X, send me the rest."
        
4.  **Flags (Control Bits)**
    
    -   These are single-bit indicators that control connection.
        
    -   Important ones:
        
        -   **SYN (Synchronize)** → Used to start a connection.
            
        -   **ACK (Acknowledgment)** → Confirms received packets.
            
        -   **FIN (Finish)** → Request to close connection.
            
        -   **RST (Reset)** → Abruptly close/reset connection.
            
        -   **PSH (Push)** → Deliver data immediately.
            
        -   **URG (Urgent)** → Marks data as urgent.
            
5.  **Checksum**
    
    -   Used for error detection.
        
    -   Ensures data was not corrupted in transit.
        
6.  **Window Size**
    
    -   Used for flow control.
        
    -   Defines how much data can be sent before needing acknowledgment.
        

***

## 6. **How TCP Connection is Established (3-Way Handshake)**

Before two computers exchange data, TCP sets up a **connection** using SYN & ACK flags.

### Steps:

1.  **Client → Server (SYN):**
    
    -   Client sends a packet with **SYN=1** and a random **initial sequence number (ISN)**.
        
    -   Example: `SYN, Seq=1000`
        
2.  **Server → Client (SYN + ACK):**
    
    -   Server acknowledges by sending **SYN=1, ACK=1**.
        
    -   Acknowledgment number = Client’s ISN + 1.
        
    -   Example: `SYN, ACK, Seq=2000, Ack=1001`
        
3.  **Client → Server (ACK):**
    
    -   Client sends an acknowledgment confirming server’s ISN.
        
    -   Example: `ACK, Seq=1001, Ack=2001`
        

Now the connection is established. ✅

👉 **Analogy:**  
It’s like two people starting a phone call:

-   Person A: "Hello, can you hear me?" (SYN)
    
-   Person B: "Yes, I can hear you. Can you hear me?" (SYN+ACK)
    
-   Person A: "Yes, I can hear you." (ACK)
    
-   Now they can talk (data exchange).
    

***

## 7. **Data Transmission**

-   Once connected, data is sent in segments.
    
-   Each segment carries a **sequence number**.
    
-   Receiver replies with **ACKs** to confirm receipt.
    
-   If sender doesn’t get ACK (packet lost), it **retransmits**.
    

👉 **Example:**

-   Client sends 1000 bytes with `Seq=1000`.
    
-   Server receives and replies: `ACK=2000`.
    
-   If packet lost, no ACK is received → client resends.
    

***

## 8. **Connection Termination (4-Way Handshake)**

Closing a TCP connection requires **4 steps** (because TCP is full-duplex → both sides must agree to close).

1.  **Client → Server: FIN** (Client says "I’m done").
    
2.  **Server → Client: ACK** (Acknowledges client’s FIN).
    
3.  **Server → Client: FIN** (Server says "I’m done too").
    
4.  **Client → Server: ACK** (Acknowledges server’s FIN).
    

👉 Connection is now closed.

***

## 9. **Real-World Example**

Imagine you open a browser and go to `http://example.com`:

1.  Browser creates a **TCP connection** to server (3-way handshake).
    
2.  Browser sends **HTTP request** (data wrapped in TCP).
    
3.  Server responds with **HTTP response** (again over TCP).
    
4.  Both sides eventually **close the connection** with FIN/ACK.
    

***

## 10. **Why TCP is Important**

-   Without TCP, you’d see:
    
    -   Broken images on websites,
        
    -   Corrupted files,
        
    -   Video/audio glitches,
        
    -   Incomplete messages.
        
-   TCP is the reason the internet feels smooth & reliable for most applications (web, email, file transfer).