# Multi-Container Runtime

## 🔹 Team Information

* **Urav B S** — PES1UG24CS507
* **Uttam B S** — PES1UG24CS508

---

## 🔹 Overview

This project implements a lightweight container runtime in C, inspired by core Linux container principles. It demonstrates how operating system concepts like process isolation, scheduling, and kernel interaction work together in a practical system.

### 🔧 Key Features

* Multi-container execution using Linux namespaces
* Centralized supervisor process
* Per-container logging system
* Kernel-level monitoring using a Loadable Kernel Module (LKM)
* CPU vs I/O scheduling experiments
* Clean lifecycle management (no zombie processes)

---

## 🔹 Build, Load, and Run Instructions

### 1. Build the Project

```bash
cd boilerplate
make
```

---

### 2. Load Kernel Module

```bash
sudo insmod monitor.ko
sudo dmesg | tail -n 20
```

---

### 3. Start Supervisor

```bash
sudo ./engine supervisor ./rootfs-base
```

---

### 4. Prepare Container Root Filesystems

```bash
cp -a ./rootfs-base ./rootfs-alpha
cp -a ./rootfs-base ./rootfs-beta
```

---

### 5. Start Containers

```bash
sudo ./engine start alpha ./rootfs-alpha /cpu_hog
sudo ./engine start beta ./rootfs-beta /io_pulse
```

---

### 6. Inspect Containers

```bash
sudo ./engine ps
```

---

### 7. View Logs

```bash
ls logs
cat logs/alpha.log
```

---

### 8. Stop Containers

```bash
sudo ./engine stop alpha
sudo ./engine stop beta
```

---

## 📸 Demo with Screenshots
1. Multi-container supervision
   <img width="1536" height="1024" alt="os1" src="https://github.com/user-attachments/assets/08a8197f-26de-4824-b03c-1ca5343e8e71" />
   Two containers running concurrently under a single supervisor process

2. Metadata tracking (ps)
   <img width="1536" height="1024" alt="os2" src="https://github.com/user-attachments/assets/aae9ffc8-7cfe-46ce-ad99-4959212064b5" />
   Supervisor maintains metadata including container ID, PID, and state
   
3. Bounded-buffer logging
   <img width="827" height="565" alt="WhatsApp Image 2026-04-15 at 12 06 27" src="https://github.com/user-attachments/assets/96bd0b2a-a478-4b6e-9c3b-b5e9e860a39e" />
   Container output captured through bounded-buffer logging pipeline

4. CLI + IPC
   <img width="952" height="489" alt="image" src="https://github.com/user-attachments/assets/7b816482-46ce-4262-8486-cad275ba9856" />
   CLI communicates with supervisor via IPC mechanism

5. Soft-limit warning
   <img width="1263" height="640" alt="image" src="https://github.com/user-attachments/assets/90bcd554-80f8-4790-864b-c32bb773bfba" />
   <img width="1178" height="625" alt="image" src="https://github.com/user-attachments/assets/7e44fc57-1e01-4599-b028-db9df806b4cd" />

   Soft memory limit exceeded triggers warning without terminating container

6. Hard-limit enforcement
   <img width="984" height="593" alt="image" src="https://github.com/user-attachments/assets/c8218f4e-f366-4d3a-84e6-5136e9f8ce46" />
   Hard memory limit enforcement terminates container process

7. Scheduling experiment
   <img width="1241" height="652" alt="image" src="https://github.com/user-attachments/assets/f12830ab-32d2-4dcb-af07-9ff58da7f494" />
   <img width="805" height="523" alt="image" src="https://github.com/user-attachments/assets/1a4d1024-76c9-413d-ba27-4a209652eeca" />
   Linux scheduler prioritizes lower nice value (higher priority) container

8. Clean teardown
   <img width="1254" height="646" alt="image" src="https://github.com/user-attachments/assets/0d38fbe5-748d-4a6c-8006-05a68c031027" />
   All containers terminated cleanly with no zombie processes remaining

## 🔹 Engineering Analysis

### 🔹 Process Isolation

Containers are created using Linux namespaces:

* Separate process trees
* Isolated filesystem environments
* Independent execution contexts

---

### 🔹 Supervisor Design

The supervisor acts as:

* A central controller for container lifecycle
* A process manager for spawning and stopping containers
* A coordinator for logging and monitoring

---

### 🔹 Logging System

* Each container writes to its own log file
* Output is captured via pipes
* Logs persist even after container termination

---

### 🔹 Kernel Monitoring

* Implemented as a Loadable Kernel Module
* Interacts with user-space runtime
* Demonstrates kernel-user communication

---

### 🔹 Scheduling Behavior

* CPU-bound processes utilize maximum CPU
* I/O-bound processes frequently yield CPU
* Demonstrates real-world Linux scheduling behavior

---

## 🔹 Design Decisions & Tradeoffs

### Container Isolation

* **Approach:** Namespace-based
* **Tradeoff:** Lightweight but less secure than full container runtimes

### Supervisor

* **Approach:** Single process controller
* **Tradeoff:** Simple design but single point of failure

### Logging

* **Approach:** File-based logging
* **Tradeoff:** Easy implementation, limited scalability

### Kernel Monitoring

* **Approach:** LKM-based tracking
* **Tradeoff:** Powerful but increases complexity

---

## 🔹 Observations

### CPU vs IO

* `cpu_hog` consumes significantly higher CPU
* `io_pulse` uses minimal CPU

### System Behavior

* Containers execute and terminate cleanly
* Logs are consistently generated
* No zombie processes observed

---

##  Notes

* All outputs captured from a Linux VM environment
* Kernel module successfully loads and initializes
* Some container commands may exit quickly, but system behavior remains consistent

---

##  Conclusion

This project demonstrates a simplified container runtime that integrates:

* Process isolation
* Logging pipeline
* Kernel interaction
* Scheduling behavior

It provides a strong practical understanding of how operating systems manage processes, resources, and execution environments.
