# Multi-Container Runtime

## 🔹 Team Information

* **Thanishq Raam Paatil** — PES1UG24CS502
* **Varun Rao** — PES1UG24CS520

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
   <img width="1727" height="712" alt="SCREENSHOT 1" src="https://github.com/user-attachments/assets/7f53dca6-9c14-4234-a84b-008df77b5b17" />
   The supervisor engine concurrently managing multiple active containers ('alpha' and 'beta') under a single parent process.

2. Metadata tracking (ps)
   <img width="896" height="761" alt="SCREENSHOT 2" src="https://github.com/user-attachments/assets/bf504535-9140-4827-a942-357da81aa328" />
   The CLI 'ps' command querying the supervisor to display dynamically tracked container metadata, including assigned process IDs (PIDs) and states.
   
3. Bounded-buffer logging
   <img width="2494" height="1560" alt="SCREENSHOT 3" src="https://github.com/user-attachments/assets/eeef4e16-0908-4404-bcd4-4a8d6664f02c" />
   Successful retrieval of container execution logs, verifying that the pipeline's consumer thread is actively capturing and writing standard output to disk.

4. CLI + IPC
   <img width="894" height="749" alt="SCREENSHOT 4" src="https://github.com/user-attachments/assets/0741d738-6702-4a32-9ca6-36c1323610a8" />
   Issuing a control command via the CLI client, which successfully communicates with the background supervisor through the Unix Domain Socket to terminate a container.

5. Soft-limit warning
   <img width="2485" height="567" alt="SCREENSHOT 55" src="https://github.com/user-attachments/assets/94c2758c-45e1-463e-b68f-785236dfc7bc" />

   CLI execution starting container 'gamma' with strict memory boundaries to trigger kernel-level monitoring.

6. Hard-limit enforcement
   <img width="2485" height="844" alt="SCREENSHOT 6" src="https://github.com/user-attachments/assets/c4fadb67-f9d9-4ad6-a7d3-39488987943f" />
   The custom Loadable Kernel Module detecting a memory boundary breach by container 'gamma' and successfully enforcing the constraint.

7. Scheduling experiment
   <img width="1086" height="749" alt="SCREENSHOT 7" src="https://github.com/user-attachments/assets/5650e696-3eb9-43e3-bda7-4b730ee7fb6d" />
   Process monitor confirming that the isolated 'cpu_hog' container is effectively scheduled by the OS and saturating its allocated CPU time slice.

8. Clean teardown
   <img width="936" height="761" alt="SCREENSHOT 8" src="https://github.com/user-attachments/assets/8cf926a0-3a0d-4cd2-8ed0-06c5a5d1cd97" />
   The supervisor gracefully intercepting a SIGINT (^C) signal, triggering the cleanup routine to close the IPC socket and reap all child processes to prevent zombies.

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
