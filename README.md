# SQLi-Hunter

SQLi-Hunter is a simple HTTP/HTTPS proxy server and a SQLMAP API wrapper that makes digging SQLi easy.

## 0x0 Installation

### Using Docker

- Build the Docker image:

```
docker build -t sqli-hunter https://github.com/zt2/sqli-hunter.git
```

- Run the Docker image:

```
docker run -ti -p 8080:8080 -p 8081:8081 -v /tmp:/tmp --rm sqli-hunter --host=0.0.0.0
```

The volume argument allows SQLi-Hunter to persist output files to be accessed on the host system. The port mapping argument will enable SQLi-Hunter to start a proxy server and a reverse SSL proxy server to be accessed on the host system.

- Install CA (`cert/sqli-hunter.pem`) on the device you want to test
- Setup proxy (port `8080`) in the browser and you are ready to go.

```
keytool -printcert -file sqli-hunter.pem
```

Optional:
1. Navigate to /usr/share/ca-certificates/.
2. Create a folder there (for example, sudo mkdir /usr/share/ca-certificates/work).
3. Copy the .CRT file into that newly created folder. If you'd rather not do it manually, you can use this command instead: 
   ```
   sudo cp sqli-hunter.crt /usr/share/ca-certificates/work/sqli-hunter.crt
   ```
   Make sure the permissions are set correctly (755 for the folder and 644 for the file).
4. Run the 'sudo update-ca-certificates' command.
	```
	sudo update-ca-certificates
	```
Convert a PEM File
With OpenSSL ([get the Windows version here(http://gnuwin32.sourceforge.net/packages/openssl.htm)]), you can convert the PEM file to PFX with the following command:
```
openssl pkcs12 -inkey sqli-hunter.pem -in sqli-hunter.cert -export -out sqli-hunter.pfx
```
If you have a PEM file that needs to be converted to CRT, like is the case with Ubuntu, use this command with OpenSSL:
```
openssl x509 -in sqli-hunter.pem -inform PEM -out sqli-hunter.crt
```
OpenSSL also supports converting .PEM to .P12 (PKCS#12, or Public Key Cryptography Standard #12), but append the ".TXT" file extension at the end of the file before running this command:
```
openssl pkcs12 -export -inkey sqli-hunter.pem.txt -in sqli-hunter.pem.txt -out sqli-hunter.p12
```
Here's an example:
```
-----BEGIN PRIVATE KEY-----
MIICdgIBADANBgkqhkiG9w0BAQEFAASCAmAwggJcAgEAAoGBAMLgD0kAKDb5cFyP
jbwNfR5CtewdXC+kMXAWD8DLxiTTvhMW7qVnlwOm36mZlszHKvsRf05lT4pegiFM
9z2j1OlaN+ci/X7NU22TNN6crYSiN77FjYJP464j876ndSxyD+rzys386T+1r1aZ
aggEdkj1TsSsv1zWIYKlPIjlvhuxAgMBAAECgYA0aH+T2Vf3WOPv8KdkcJg6gCRe
yJKXOWgWRcicx/CUzOEsTxmFIDPLxqAWA3k7v0B+3vjGw5Y9lycV/5XqXNoQI14j
y09iNsumds13u5AKkGdTJnZhQ7UKdoVHfuP44ZdOv/rJ5/VD6F4zWywpe90pcbK+
AWDVtusgGQBSieEl1QJBAOyVrUG5l2234raSDfm/DYyXlIthQO/A3/LngDW
5/ydGxVsT7lAVOgCsoT+0L4efTh90PjzW8LPQrPBWVMCQQDS3h/FtYYd5lfz+FNL
9CEe1F1w9l8P749uNUD0g317zv1tatIqVCsQWHfVHNdVvfQ+vSFw38OORO00Xqs9
1GJrAkBkoXXEkxCZoy4PteheO/8IWWLGGr6L7di6MzFl1lIqwT6D8L9oaV2vynFT
DnKop0pa09Unhjyw57KMNmSE2SUJAkEArloTEzpgRmCq4IK2/NpCeGdHS5uqRlbh
1VIa/xGps7EWQl5Mn8swQDel/YP3WGHTjfx7pgSegQfkyaRtGpZ9OQJAa9Vumj8m
JAAtI0Bnga8hgQx7BhTQY4CadDxyiRGOGYhwUzYVCqkb2sbVRH9HnwUaJT7cWBY3
RnJdHOMXWem7/w==
-----END PRIVATE KEY-----
```
One PEM file can contain multiple certificates, in which case the "END" and "BEGIN" sections neighbor each other.

### From source

- Build from the latest release of the source code:

```
git clone https://github.com/zt2/sqlihunter.git
cd sqlihunter
gem install bundler
bundler install
```

- Start SQLMAP API server manually.

```
cd sqlmap
python3 sqlmapapi.py -s
```

- Run SQLi-Hunter

```
ruby bin/sqli-hunter.rb
```

- Configure proxy server settings in your browser

## 0x1 Usage

```

   _____ _____ __    _     _____         _
  |   __|     |  |  |_|___|  |  |_ _ ___| |_ ___ ___
  |__   |  |  |  |__| |___|     | | |   |  _| -_|  _|
  |_____|__  _|_____|_|   |__|__|___|_|_|_| |___|_|
		   |__|

      SQLMAP API wrapper by ztz (github.com/zt2)

  Usage: bin/sqli-hunter.rb [options]

Common options:
    -h, --host=[HOST]                Bind host for proxy server (default is localhost)
    -p, --port=<PORT>                Bind port for proxy server (default is 8080)
        --sqlmap-host=[HOST]         Host for sqlmap api (default is localhost)
        --sqlmap-port=[PORT]         Port for sqlmap api (default is 8775)
        --targeted-hosts=[HOSTS]     Targeted hosts split by comma (default is all)
        --version                    Display version

SQLMAP options
        --technique=[TECH]           SQL injection techniques to use (default "BEUSTQ")
        --threads=[THREADS]          Max number of concurrent HTTP(s) requests (default 5)
        --dbms=[DBMS]                Force back-end DBMS to this value
        --os=[OS]                    Force back-end DBMS operating system to this value
        --tamper=[TAMPER]            Use given script(s) for tampering injection data
        --level=[LEVEL]              Level of tests to perform (1-5, default 1)
        --risk=[RISK]                Risk of tests to perform (0-3, default 1)
        --mobile                     Imitate smartphone through HTTP User-Agent header
        --smart                      Conduct through tests only if positive heuristic(s)
        --random-agent               Use randomly selected HTTP User-Agent header value
```



Output:

```
➜  sqli-hunter git:(master) ruby bin/sqli-hunter.rb --targeted-hosts=demo.aisec.cn --threads=15 --random-agent --smart
  [01:50:17] [INFO] [bdf9f3495bb70fbc] task created
  [01:50:17] [INFO] [bdf9f3495bb70fbc] task started
  [01:50:20] [INFO] [bdf9f3495bb70fbc] task finished
  [01:50:20][SUCCESS] [bdf9f3495bb70fbc] task vulnerable, use 'sqlmap -r /var/folders/kb/rwf8j7051x71q4flc_s39wzm0000gn/T/d20191021-40013-17a62ve/5f8a3ad452a15777219b8a5c8c7ec3b6' to exploit
```
