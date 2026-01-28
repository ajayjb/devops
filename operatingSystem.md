
# Parts of an Operating System (OS)

## Overview

An **Operating System (OS)** is system software that acts as an interface between the user, applications, and computer hardware. It manages hardware resources, provides common services to applications, and ensures security and stability.

---

## High-Level Architecture

```
User
 ↓
Applications
 ↓
System Libraries & APIs
 ↓
System Services / Daemons
 ↓
Kernel
 ↓
Hardware
```

---

## 1. Kernel

The **kernel** is the core of the operating system. It runs in privileged mode and has complete access to hardware.

### Responsibilities:

* CPU scheduling
* Process and thread management
* Memory management
* Device and driver management
* System calls
* Security and access control

### Types of Kernels:

* **Monolithic Kernel** (Linux)
* **Microkernel** (MINIX)
* **Hybrid Kernel** (Windows, macOS)

---

## 2. Process Management

Handles execution of programs.

### Functions:

* Process creation and termination
* Context switching
* Scheduling
* Multitasking and multiprocessing
* Inter-Process Communication (IPC)

### Examples:

* Multiple browser tabs running simultaneously

---

## 3. Memory Management

Controls usage of RAM and virtual memory.

### Functions:

* Allocation and deallocation of memory
* Paging and segmentation
* Virtual memory handling
* Memory protection

### Key Concepts:

* Stack and Heap
* Page Tables
* Cache

---

## 4. File System Management

Manages storage and retrieval of data.

### Functions:

* File creation, deletion, reading, and writing
* Directory management
* Permissions and ownership
* Disk space management

### Common File Systems:

* **Windows:** NTFS
* **Linux:** ext4
* **macOS:** APFS

---

## 5. Device Management (I/O Management)

Controls communication with hardware devices.

### Functions:

* Device drivers
* Interrupt handling
* Buffering and caching
* I/O scheduling

### Devices Managed:

* Keyboard
* Mouse
* Disk
* Printer
* Network Interface

---

## 6. Security and Protection

Ensures system safety and controlled access.

### Functions:

* Authentication (login)
* Authorization (permissions)
* User and group management
* Encryption
* Sandboxing applications

### Examples:

* User vs Administrator access
* File read/write/execute permissions

---

## 7. User Interface (UI)

Allows users to interact with the system.

### Types:

* **GUI (Graphical User Interface)**

  * Windows
  * macOS
  * Desktop Linux

* **CLI (Command Line Interface)**

  * Bash
  * PowerShell
  * CMD

---

## 8. System Services and Utilities

Background programs that support system functionality.

### Functions:

* System startup and shutdown
* Logging and monitoring
* Printing services
* Software updates
* Time and power management

### Examples:

* systemd (Linux)
* Windows Services
* Task Manager

---

## 9. Networking

Provides communication capabilities.

### Functions:

* TCP/IP stack implementation
* Socket management
* Network configuration
* Firewall and security rules

### Examples:

* Internet browsing
* LAN communication
* Cloud connectivity

---

## User Space vs Kernel Space

| Feature      | User Space   | Kernel Space |
| ------------ | ------------ | ------------ |
| Access Level | Restricted   | Full access  |
| Components   | Applications | Kernel       |
| Safety       | High         | Critical     |
| Crash Impact | App crashes  | System crash |

---

## Real-World Analogy

| Computer System | Real World             |
| --------------- | ---------------------- |
| Hardware        | Building               |
| Kernel          | Security & maintenance |
| OS              | Management & rules     |
| Applications    | People working         |

---

## Summary

An Operating System consists of multiple components working together:

* Kernel
* Process Management
* Memory Management
* File System
* Device Management
* Security
* User Interface
* System Services
* Networking

Together, these components make computing safe, efficient, and user-friendly.

---

## End of Document
