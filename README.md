# ALLiN

English | [简体中文](README_zh.md)

[![asciicast](https://asciinema.org/a/8P9RwnYreRrLFlnS1fHok4Soo.svg)](https://asciinema.org/a/8P9RwnYreRrLFlnS1fHok4Soo)

A comprehensive tool that assists penetration testing projects. It is a flexible, compact and efficient scan tool mainly used for lateral penetration of the intranet. The format of targets can be written by most of the various forms of link or CIDR and add any ports and paths to it.



<details>
<summary><b>Core developers</b></summary>
<li> @Like0x </li>
<li> @Christasa </li>
<li> @CoColi </li>
<li> @MiluOWO </li>
</details>

**Penetration test only**

## Features

- python2.7 - python3.x support without and depend
- Passively identify some frameworks, components of the site and over 1000 data of fingerprints
- Passively identify whether the site is on the cloud
- Almost support for import with the arbitrary format


## Some tricks

- Using across platforms after compiling by pyinstaller
  - Compiling with low-level GLIBC in Linux to be compatible with more kernel versions
  - Compiling with python2 on windows to reduce the error of lacking DLL
- Use it in VPS for resolving network problems
- Eliminate the account information when scanning on the intranet



Control the file through `--project`

- res_alivedomain.txt will save all the report automatically
- folder tree
  - result.txt
  - focuson.txt


Use the following script to scan if you have many tasks

target.txt

```
<name of a company> xxx.com
<name of another company> xxx.io
```

```bash

#!/bin/bash

while read line
do
    project=`echo $line | awk -F " " '{print $1}'`
    host=`echo $line | awk -F " " '{print $2}'`
    echo $host,$project
    python AlliN.py --host $host -q "(domain=\"$host\" || cert=\"$host\"  || title=\"$project\") &&  country=\"CN\" && region!=\"HK\" && region!=\"TW\" && region!=\"MO\"" -m sfscan --timeout 6 --project $project -t 100
done < target.txt
```



## Parameters

### --host

Designate the domains or hosts

Samples:

```
--host 10.1.1.1
--host 10.1.1.1-10.2.2.2
--host 10.1.1.1/24 # any CIDR
--host 10.1.1.1-255
--host <arbitrary format of the domain>
```

### --timeout

Default is 3

```
--timeout 3
```

### --ctimeout

Timeout of pscan, default is 0.5

```
--ctimeout 0.5
```

### --proxy

```
--proxy http://127.0.0.1:8081
--proxy socks5://127.0.0.1:1080
```

`pip install PySocks` before using it


### --project

Name of scan project, it will create a folder for saving results

### -p

Designate the ports

Samples:

```
-p 80
-p 80,443
-p 8000-9000
-p 80,8000-9000
-p-  all of ports
```

### -f 

Name of the input file

Samples:

`-f  iplist.txt`

There are many formats of targets, such as


```python
http://www.baidu.com

1.1.1.1/24

www.baidu.com/asd

www.baidu.com:80/123

1.1.1.1/sads

1.1.1.1:8080/123

1.1.1.1-250

1.1.1.1-1.1.1.250
```


also can use with `-p`



### -u

**Need to use with --host or -f**

Sample:

```shell
python AlliN.py --host "10.0.0.1-10.0.0.2" -u '/login/index.jsp' -p 80

# It will send the requests as
10.0.0.1/login/index.jsp
10.0.0.2/login/index.jsp

```

### -H

Including a custom header

Sample:
```
python AlliN.py -f domain.txt -H "Cookie: xxxxx; UxxxxxxxN=Sxxxxp"
```

### --uf

`-u` from the file

Sample:

```python
python AlliN.py --host "10.0.0.1-10.0.0.2" --uf urlpath.list -p 80

urlpathlist:
index.php
index.jsp


# It will send the requests as
10.0.0.1/index.php => 10.0.0.1/index.jsp =>10.0.0.2/index.php => 10.0.0.2/index.jsp

```

### -t

Number of threads, default is 200

Sample:

`-t 200`



### -o

Name of the report file

Sample:

`-o answer.txt`


### --oJ

Name of the report json file

Sample:

`--oJ answer.json`


### --nocert

Scanning without cert identification.

Sample:

`python AlliN.py --host 192.168.1.1/24 -p 443 --nocert`

Report with cert identification

```bash
[  https://1.1.1.1  | Server:Microsoft-HTTPAPI/2.0 |  400  | Size:334 |  Bad Request  | Certs URL: uat1.sandbox.operations.dynamics.cn | DigiCert Inc  ]
```


### --nobar

Close the processing bar

`python AlliN.py --host 192.168.1.1/24 --nobar`


### --only-show

Only displays the status codes which are allowed.

Sample:

`--only-show 200,301`


### --hidden

Filter specifies status code.


Sample:


`--hidden 404,400`



### --hiddensize

Filter the length of response

Sample:

`--hiddensize 27,5367`  # Filter the responses which length is 26 and 5367



### --dd

Header scan model

```python
python AlliN.py --host 192.168.1.1/24 -p 443 --dd
```

### --tp

Add an extra url scan of favicon.ico

```python
python AlliN.py --host 192.168.1.1/24 --tp
```

### --fs

Size of each result number by fofa scan, default is 10,000


### -m

Method of scan, default is tscan



#### oxid

Obtain the address of the remote host network card

`python AlliN.py -m oxid --host 192.168.129.130`

Port is 135

```python
[*] 127.0.0.1
        [->]hecs-xxxx0622130100
        [->]192.168.0.57
        [->]2001xxxxf57:ffc6
{'127.0.0.1': ['hxx-x-medixx-2-win-2020xxxx', '192.168.0.57', '2001xxxx3f57:ffc6']}
```



#### bakscan

Scan the backup of a site

`python AlliN.py --host 192.168.1.1/24 -p 443 -m bakscan`

`python AlliN.py --host 192.168.1.1/24 -p 443 -m bakscan -f dic.txt`

or use with the header model

`python AlliN.py --host 192.168.1.1/24 -p 443 -m bakscan --dd`



#### sfscan

Combine with subscan and fofascan

`python AlliN.py -q 'domain="xx.com"' --host xxx.com -m sfscan`


Use for several domains

`python AlliN.py -q 'domain="xx.com"' --host xxx.com,yyy.com -m sfscan`


#### shscan
Brute the key of shiro, only support CBC encryption currently

`python AlliN.py --host example.com -m shscan`


#### ddscan

Fuzz subdomain, it will replace the [fuzz] to host

Sample:

```shell
python AlliN.py --host "www.[fuzz]baidu.top" -f test.txt  -m ddscan
python AlliN.py --host "[fuzz].baidu.top" -f test.txt  -m ddscan
python AlliN.py --host "[fuzz].baidu.top" -f test.txt  -m ddscan --dd
```



#### hostscan

Fuzz the host of a request body

```http
GET /xxx.html HTTP/1.1
Host: [fuzz].example.com
Accept-Encoding: gzip, deflate
Accept: */*
Accept-Language: en
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36
Connection: close

```

Sample:

```
python AlliN.py -m hostscan --host https://1.1.1.1 --domain example.com -f dic.txt --nocert
```

#### vhostscan

Similary with `hostscan`, change the IP with a correct host

Sample:

```
python AlliN.py -m vhostscan -f dict.txt --domain example.com --nocert
```


#### fscan

Fofa scan, reference [https://fofa.info/api](https://fofa.info/api)


```
-q
--fs # default is 10,000
```

Sample:


`python AlliN.py -q domain="baidu.com" -m fscan --fs 200`

#### pscan

Port scan

Use the `-p-` to include all of the ports

Sample:


`python AlliN.py --host 10.1.1.1/24 -p 80 -m pscan`



#### tscan

Title scan, the default scan method

Sample:


`python AlliN.py --host 10.1.1.1/24 -p 80`



#### 17scan

MS17-010 vulnerable scan

Sample:

`python AlliN.py --host 10.1.1.1/24 -m 17scan`


#### dpscan

DOUBLEPULSAR backdoor check

Sample:

`python AlliN.py --host 10.1.1.1/24 -m dpscan --verbose`


#### nbscan

nbtscan

Sample:

`python AlliN.py --host 10.1.1.1/24 -m nbscan`



#### subscan

Subdomain scan

Sample:


`python AlliN.py --host "xx.com" -m subscan`



#### sscan

`tscan` without header of `rememberMe=xxx`

Sample:


`python AlliN.py --host 10.1.1.1/24 -p 80 -m sscan`



#### uncd

Decode model

Include powershell encode 、bash encode 、 F5 decode

Sample:

`python AlliN.py -m uncd -e f5 -s 185903296.21520.0000`

```
-e f5 f5decode
-e pw powershell encode
-e bh bash encode
```



#### 0708scan

CVE-2019-0708 vulnerable scan

Sample:

`python AlliN.py --host 192.168.1.1/24 -m 0708scan -p 3389`



#### ICMPT

NAT traversal

Nedd privileged of **icmp Ping**

There two steps to start it - server and client

first step: type `python AlliN.py -m icmpt ` on your own VPS

next step: In client, type `python AlliN.py -m icmpt --sip vps --cip 127.0.0.1 --cport 80` 

It will have the following content after you finish the second step

```
Accpet new client from : 14178 192.168.148.1
Your server port is :33127
```

And the 33127 port of your VPS is the correct 80 port of the client


Sample:

```
python AlliN.py -m icmpt --sip vps --cip 127.0.0.1 --cport 80

Then you can browse vps_ip:36267 to access target_ip:target_port
```



