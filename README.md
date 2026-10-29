# Multicast IP Communication Project

This project demonstrates a simple Java multicast communication system that sends and receives messages over a multicast group using UDP. It is designed to simulate message transmission and detect possible omission or loss of packets from a sender by monitoring the number of messages received per source IP.

## Overview

The application consists of two main components:

- `MulticastSender.java`: sends periodic messages to the multicast group `224.0.0.2` on port `8877`.
- `MulticastReceiver.java`: joins the same multicast group, receives the packets, counts messages by source IP, and calculates a suspicion level when expected traffic is not received.

Each sender message includes:

- a timestamp in milliseconds
- the local IP address of the sender

Example payload:

`1712345678901,192.168.0.10`

---

## Requirements

### Linux

If Java is not installed yet, run:

```bash
sudo apt update
sudo apt install openjdk-17-jdk -y
```

Verify the installation:

```bash
java -version
```

It is also recommended to install the Java extension pack in VS Code to make compilation and execution easier.

---

## How to Run

The application requires both sender and receiver to run at the same time.

### 1. Start the receiver

Open a terminal in the project folder and run:

```bash
cd Multicast/src
javac MulticastReceiver.java
java MulticastReceiver
```

This starts the receiver and joins the multicast group `224.0.0.2` on port `8877`.

### 2. Start the sender

Open a second terminal and run:

```bash
cd Multicast/src
javac MulticastSender.java
java MulticastSender
```

The sender will send a packet every 5 seconds, containing its local IP and a timestamp.

---

## How the Algorithm Works

The receiver uses two threads:

1. Listening thread
   - Receives multicast packets.
   - Extracts the source IP.
   - Counts how many packets were received from each IP address.

2. Monitoring thread
   - Waits for a defined interval (30 seconds in this implementation).
   - Compares the number of received packets against the expected value.
   - If fewer packets were received than expected, it estimates a suspicion percentage.

The expected number of messages is calculated as:

$$
\text{expected} = \frac{30000}{5000} = 6
$$

That means, for a 30-second monitoring window and a sender interval of 5 seconds, the system expects 6 packets.

The suspicion formula is:

$$
\text{Suspicion\%} = \frac{\text{expected} - \text{received}}{\text{expected}} \times 100
$$

If fewer packets than expected are received, the system marks that source as suspicious and resets the counter for the next cycle.

---

## Project Structure

- `Multicast/src/MulticastSender.java`: sends multicast datagrams with the sender's host identification.
- `Multicast/src/MulticastReceiver.java`: receives multicast packets, performs counting, and analyzes possible omissions.

---

## Purpose

This project is a practical example of multicast networking in Java and illustrates how to monitor communication reliability in distributed systems. It is useful for learning UDP multicast communication, packet counting, and basic fault-detection logic.
