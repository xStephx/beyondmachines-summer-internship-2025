 # Security Vulnerabilities Report

 ### Introduction
 
This document summarizes the findings from the recent security analysis of the web application. The purpose of this report is to provide an overview of the identified vulnerabilities, their potential impact, and recommended mitigation steps to enhance the overall security posture of the application.  

### Attacker IP Address: `45.33.49.201`

- Reconnaissance: Probed for `/robots.txt`, `.git/HEAD`, `.env`, `config.php`, `wp-login.php` — looking for sensitive files and configuration leaks.
- Admin access attempts: Tried to access `/admin` and `/admin/login.php` — got 401 unauthorized initially.
- Brute force login: Multiple POST requests to `/admin/login.php` with failed (401) then a successful login (302 redirect).
- Post-login activity: Accessed admin dashboard, user management, settings, and backup pages.
- Backup download: Downloaded `backup_06052025.zip` — possible data theft.
- File upload & web shell: Uploaded a shell to `/uploads/shell.php` and executed multiple POST commands — shell interaction.
- Logout and re-login: Logged out and accessed login page again.
- Shell continued usage: Continued POST requests to the shell from various user agents (Linux, Android, Windows).
- Nmap scanning detected: Access attempts identified as Nmap scripting engine scanning /uploads.
- Shell commands: Large POST payloads to shell indicate remote command execution.

```
45.33.49.201 - - [06/May/2025:08:27:35 +0000] "GET /robots.txt HTTP/1.1" 404 452 "-" "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)"
45.33.49.201 - - [06/May/2025:08:27:42 +0000] "GET /.git/HEAD HTTP/1.1" 404 452 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/123.0.0.0 Safari/537.36"
45.33.49.201 - - [06/May/2025:08:44:08 +0000] "GET /.env HTTP/1.1" 404 452 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/123.0.0.0 Safari/537.36"
45.33.49.201 - - [06/May/2025:08:49:12 +0000] "GET /config.php HTTP/1.1" 404 452 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/123.0.0.0 Safari/537.36"
45.33.49.201 - - [06/May/2025:08:51:32 +0000] "GET /wp-login.php HTTP/1.1" 404 452 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/123.0.0.0 Safari/537.36"
45.33.49.201 - - [06/May/2025:08:53:11 +0000] "GET /admin HTTP/1.1" 301 566 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/123.0.0.0 Safari/537.36"
45.33.49.201 - - [06/May/2025:08:53:15 +0000] "GET /admin/ HTTP/1.1" 401 752 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/123.0.0.0 Safari/537.36"
45.33.49.201 - - [06/May/2025:09:12:27 +0000] "GET /phpmyadmin HTTP/1.1" 404 452 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/123.0.0.0 Safari/537.36"
45.33.49.201 - - [06/May/2025:09:13:05 +0000] "GET /backup HTTP/1.1" 404 452 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/123.0.0.0 Safari/537.36"
45.33.49.201 - - [06/May/2025:09:14:58 +0000] "GET /admin/login.php HTTP/1.1" 200 3452 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/123.0.0.0 Safari/537.36"
45.33.49.201 - - [06/May/2025:10:02:05 +0000] "POST /admin/login.php HTTP/1.1" 401 752 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:10:02:08 +0000] "POST /admin/login.php HTTP/1.1" 401 752 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:10:02:11 +0000] "POST /admin/login.php HTTP/1.1" 401 752 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:10:02:14 +0000] "POST /admin/login.php HTTP/1.1" 401 752 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:10:02:17 +0000] "POST /admin/login.php HTTP/1.1" 401 752 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:10:02:20 +0000] "POST /admin/login.php HTTP/1.1" 401 752 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:10:13:42 +0000] "POST /admin/login.php HTTP/1.1" 401 752 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:10:13:45 +0000] "POST /admin/login.php HTTP/1.1" 401 752 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:10:13:48 +0000] "POST /admin/login.php HTTP/1.1" 302 0 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:10:13:49 +0000] "GET /admin/dashboard.php HTTP/1.1" 200 15867 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:10:32:15 +0000] "GET /admin/users.php HTTP/1.1" 200 12567 "https://companyxyz.com/admin/dashboard.php" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:10:37:22 +0000] "GET /admin/settings.php HTTP/1.1" 200 8754 "https://companyxyz.com/admin/users.php" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:10:45:44 +0000] "GET /admin/backup.php HTTP/1.1" 200 4321 "https://companyxyz.com/admin/settings.php" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:10:47:02 +0000] "POST /admin/backup.php HTTP/1.1" 200 1289 "https://companyxyz.com/admin/backup.php" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:10:48:15 +0000] "GET /admin/backups/backup_06052025.zip HTTP/1.1" 200 1542783 "https://companyxyz.com/admin/backup.php" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:11:52:18 +0000] "GET /admin/uploads.php HTTP/1.1" 200 7832 "https://companyxyz.com/admin/dashboard.php" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:11:55:43 +0000] "POST /admin/uploads.php HTTP/1.1" 302 0 "https://companyxyz.com/admin/uploads.php" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:11:55:44 +0000] "GET /admin/uploads.php?status=success HTTP/1.1" 200 7946 "https://companyxyz.com/admin/uploads.php" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:11:56:12 +0000] "GET /uploads/shell.php HTTP/1.1" 200 18 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:11:56:48 +0000] "POST /uploads/shell.php HTTP/1.1" 200 4267 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:11:57:23 +0000] "POST /uploads/shell.php HTTP/1.1" 200 2198 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:11:58:05 +0000] "POST /uploads/shell.php HTTP/1.1" 200 43287 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:13:01:34 +0000] "POST /uploads/shell.php HTTP/1.1" 200 1854 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:13:17:19 +0000] "GET /admin/logout.php HTTP/1.1" 302 0 "https://companyxyz.com/admin/dashboard.php" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:13:17:20 +0000] "GET /admin/login.php HTTP/1.1" 200 3452 "https://companyxyz.com/admin/dashboard.php" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
45.33.49.201 - - [06/May/2025:14:22:17 +0000] "GET /uploads/shell.php HTTP/1.1" 200 18 "-" "Mozilla/5.0 (Linux; Android 10; K) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.0.0 Mobile Safari/537.36"
45.33.49.201 - - [06/May/2025:14:22:45 +0000] "POST /uploads/shell.php HTTP/1.1" 200 3542 "-" "Mozilla/5.0 (Linux; Android 10; K) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.0.0 Mobile Safari/537.36"
45.33.49.201 - - [06/May/2025:15:02:14 +0000] "POST /uploads/shell.php HTTP/1.1" 200 18543 "-" "Mozilla/5.0 (Linux; Android 10; K) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.0.0 Mobile Safari/537.36"
45.33.49.201 - - [06/May/2025:15:42:36 +0000] "POST /uploads/shell.php HTTP/1.1" 200 4387 "-" "Mozilla/5.0 (Linux; Android 10; K) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.0.0 Mobile Safari/537.36"
45.33.49.201 - - [06/May/2025:16:23:05 +0000] "GET /uploads HTTP/1.1" 403 752 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
45.33.49.201 - - [06/May/2025:16:23:18 +0000] "GET /uploads/ HTTP/1.1" 403 752 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
45.33.49.201 - - [06/May/2025:16:23:32 +0000] "GET /uploads/shell.php HTTP/1.1" 200 18 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
45.33.49.201 - - [06/May/2025:16:54:21 +0000] "POST /uploads/shell.php HTTP/1.1" 200 8765 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/123.0.0.0 Safari/537.36"
45.33.49.201 - - [06/May/2025:17:53:18 +0000] "GET /uploads/shell.php HTTP/1.1" 200 18 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; Trident/7.0; rv:11.0) like Gecko"
45.33.49.201 - - [06/May/2025:17:53:35 +0000] "POST /uploads/shell.php HTTP/1.1" 200 6532 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; Trident/7.0; rv:11.0) like Gecko"
45.33.49.201 - - [06/May/2025:18:12:47 +0000] "POST /uploads/shell.php HTTP/1.1" 200 87654 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; Trident/7.0; rv:11.0) like Gecko"
45.33.49.201 - - [06/May/2025:18:51:43 +0000] "POST /uploads/shell.php HTTP/1.1" 200 12543 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; Trident/7.0; rv:11.0) like Gecko"
45.33.49.201 - - [06/May/2025:18:57:26 +0000] "GET /admin/login.php HTTP/1.1" 200 3452 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36"
45.33.49.201 - - [06/May/2025:19:12:04 +0000] "POST /admin/login.php HTTP/1.1" 401 752 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36"
45.33.49.201 - - [06/May/2025:19:12:07 +0000] "POST /admin/login.php HTTP/1.1" 401 752 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36"
45.33.49.201 - - [06/May/2025:19:12:10 +0000] "POST /admin/login.php HTTP/1.1" 401 752 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36"
45.33.49.201 - - [06/May/2025:19:12:13 +0000] "POST /admin/login.php HTTP/1.1" 302 0 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36"
45.33.49.201 - - [06/May/2025:19:12:14 +0000] "GET /admin/dashboard.php HTTP/1.1" 200 15867 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36"
45.33.49.201 - - [06/May/2025:19:23:46 +0000] "GET /admin/users.php HTTP/1.1" 200 12567 "https://companyxyz.com/admin/dashboard.php" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36"
45.33.49.201 - - [06/May/2025:19:25:12 +0000] "GET /admin/user-edit.php?id=1 HTTP/1.1" 200 9823 "https://companyxyz.com/admin/users.php" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36"
45.33.49.201 - - [06/May/2025:19:27:34 +0000] "POST /admin/user-edit.php HTTP/1.1" 302 0 "https://companyxyz.com/admin/user-edit.php?id=1" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36"
45.33.49.201 - - [06/May/2025:19:27:35 +0000] "GET /admin/users.php HTTP/1.1" 200 12567 "https://companyxyz.com/admin/user-edit.php?id=1" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36"
```


### Attacker IP Address: `121.18.83.75`

- User-Agent: Pretends to be `Safari` browser on `Mac OS X 10.15.7`, likely to blend in with normal traffic.
- Initial Access: Searches for products (phone, monitor) indicating reconnaissance on product-related endpoints.
- SQL Injection Attempts: Payloads like `' OR '1'='1`, `'; DROP TABLE users; --` indicate attempts to exploit SQL injection vulnerabilities.
- Path Traversal & LFI Attempts: Tries to access `/download.php?file=../../../etc/passwd` to read sensitive files (file disclosure attack).
- XSS Attempts: Injects `<script>alert(1)</script>` in API query parameters, testing for cross-site scripting.
- Command Injection Attempts: Attempts to execute commands via `/cgi-bin/status?command=...` to list files and read /etc/passwd.
- API Abuse: Accessing user profiles `(/api/user/1/profile)`, but gets 403 Forbidden—no unauthorized access.
- Subscription Endpoint Testing: Posts to `/api/subscribe` with varied success (one 400 Bad Request, one 201 Created).
- Overall Behavior: Automated scanning and exploitation attempts mixing `SQLi`, `LFI`, `XSS`, and command injection. No clear successful data breach, but probing for vulnerabilities is persistent.

```
121.18.83.75 - - [06/May/2025:09:22:15 +0000] "GET /products/search?q=phone HTTP/1.1" 200 10845 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.4 Safari/605.1.15"
121.18.83.75 - - [06/May/2025:09:23:42 +0000] "GET /products/search?q=phone%27%20OR%20%271%27%3D%271 HTTP/1.1" 500 1243 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.4 Safari/605.1.15"
121.18.83.75 - - [06/May/2025:09:31:18 +0000] "GET /products/search?q=phone%27%3B%20DROP%20TABLE%20users%3B%20-- HTTP/1.1" 500 1243 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.4 Safari/605.1.15"
121.18.83.75 - - [06/May/2025:11:42:33 +0000] "GET /download.php?file=../../../etc/passwd HTTP/1.1" 404 452 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.4 Safari/605.1.15"
121.18.83.75 - - [06/May/2025:11:43:12 +0000] "GET /download.php?file=product_catalog.pdf HTTP/1.1" 200 1245673 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.4 Safari/605.1.15"
121.18.83.75 - - [06/May/2025:12:34:27 +0000] "GET /api/products/42?callback=<script>alert(1)</script> HTTP/1.1" 400 1253 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.4 Safari/605.1.15"
121.18.83.75 - - [06/May/2025:12:51:19 +0000] "POST /api/subscribe HTTP/1.1" 400 352 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.4 Safari/605.1.15"
121.18.83.75 - - [06/May/2025:12:52:08 +0000] "POST /api/subscribe HTTP/1.1" 201 142 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.4 Safari/605.1.15"
121.18.83.75 - - [06/May/2025:15:13:45 +0000] "GET /cgi-bin/status?command=ls%20-la HTTP/1.1" 404 452 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.4 Safari/605.1.15"
121.18.83.75 - - [06/May/2025:15:14:26 +0000] "GET /cgi-bin/status?command=;cat%20/etc/passwd HTTP/1.1" 404 452 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.4 Safari/605.1.15"
121.18.83.75 - - [06/May/2025:16:08:43 +0000] "GET /products/search?q=monitor HTTP/1.1" 200 12876 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.4 Safari/605.1.15"
121.18.83.75 - - [06/May/2025:16:10:27 +0000] "GET /products/search?q=monitor%3Cscript%3Ealert(document.cookie)%3C/script%3E HTTP/1.1" 400 1253 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.4 Safari/605.1.15"
121.18.83.75 - - [06/May/2025:17:32:47 +0000] "GET /api/user/1/profile HTTP/1.1" 403 321 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.4 Safari/605.1.15"
121.18.83.75 - - [06/May/2025:17:33:21 +0000] "GET /api/user/2/profile HTTP/1.1" 403 321 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.4 Safari/605.1.15"
121.18.83.75 - - [06/May/2025:19:03:18 +0000] "GET /api/status HTTP/1.1" 200 542 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.4 Safari/605.1.15"
121.18.83.75 - - [06/May/2025:19:04:27 +0000] "GET /api/debug HTTP/1.1" 404 452 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.4 Safari/605.1.15"
121.18.83.75 - - [06/May/2025:19:05:32 +0000] "GET /api/info HTTP/1.1" 200 1784 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.4 Safari/605.1.15"
```

### Attacker IP Address: `198.51.100.73`

- User-Agent: `Firefox` on `Windows 10`.
- XSS Attempts: Tried XSS attacks on `/blog/comment` and `/search` but got 400 errors (blocked).
- Posted to `/contact-form.php` — also got 400 error.
- Probed `/reset-password` with token/email — first rejected, then got page successfully.
- Attempted admin login — received 401 Unauthorized.

```
198.51.100.73 - - [06/May/2025:10:42:16 +0000] "GET /blog/comment?id=15&text=<script>alert('XSS')</script> HTTP/1.1" 400 1253 "https://companyxyz.com/blog/article/15" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:123.0) Gecko/20100101 Firefox/123.0"
198.51.100.73 - - [06/May/2025:12:02:51 +0000] "GET /search?q=<img src="x" onerror="alert('XSS')"> HTTP/1.1" 400 1253 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:123.0) Gecko/20100101 Firefox/123.0"
198.51.100.73 - - [06/May/2025:14:12:33 +0000] "POST /contact-form.php HTTP/1.1" 400 1253 "https://companyxyz.com/contact.html" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:123.0) Gecko/20100101 Firefox/123.0"
198.51.100.73 - - [06/May/2025:16:17:52 +0000] "GET /reset-password?token=1234&email=admin@companyxyz.com HTTP/1.1" 400 1253 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:123.0) Gecko/20100101 Firefox/123.0"
198.51.100.73 - - [06/May/2025:16:18:35 +0000] "GET /reset-password HTTP/1.1" 200 4321 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:123.0) Gecko/20100101 Firefox/123.0"
198.51.100.73 - - [06/May/2025:17:47:23 +0000] "POST /admin/login.php HTTP/1.1" 401 752 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:123.0) Gecko/20100101 Firefox/123.0"
```

### Attacker IP Address: `216.58.212.110`

- User-Agent: `PostmanRuntime` (API testing tool).
- API Access: Successfully retrieved product lists and details from `/api/products` (200 OK). Attempted to access `/api/users` but was blocked (403 Forbidden).
- Order Attempts: Tried creating orders at `/api/orders` — first unauthorized (401), then succeeded (201 Created).
- Pagination: Used query parameters to paginate product results `(limit=50&offset=100)`.

```
216.58.212.110 - - [06/May/2025:11:01:47 +0000] "GET /api/products HTTP/1.1" 200 47834 "-" "PostmanRuntime/7.32.3"
216.58.212.110 - - [06/May/2025:11:02:34 +0000] "GET /api/products/45892 HTTP/1.1" 200 1243 "-" "PostmanRuntime/7.32.3"
216.58.212.110 - - [06/May/2025:11:03:58 +0000] "GET /api/users HTTP/1.1" 403 321 "-" "PostmanRuntime/7.32.3"
216.58.212.110 - - [06/May/2025:12:15:37 +0000] "POST /api/orders HTTP/1.1" 401 341 "-" "PostmanRuntime/7.32.3"
216.58.212.110 - - [06/May/2025:12:16:12 +0000] "POST /api/orders HTTP/1.1" 201 982 "-" "PostmanRuntime/7.32.3"
216.58.212.110 - - [06/May/2025:15:37:28 +0000] "GET /api/products?limit=50&offset=100 HTTP/1.1" 200 68453 "-" "PostmanRuntime/7.32.3"
```

### Attacker IP Address: `203.0.113.42`

- User-Agent: `python-requests` (automated/scripted access).
- Search Request: Sent POST to `/products/search` — status 200 OK.
- Subscription: POST to `/api/subscribe` — status 201 Created.
- Unauthorized Access Attempt: Tried POST to `/api/inventory` — status 403 Forbidden.
- Reconnaissance: Fetched `/robots.txt` and `/sitemap.xml` — both 200 OK.
- Data Enumeration: Accessed `/api/products` and `/api/categories` — both 200 OK.

```
203.0.113.42 - - [06/May/2025:14:43:27 +0000] "POST /products/search HTTP/1.1" 200 10456 "-" "python-requests/2.31.0"
203.0.113.42 - - [06/May/2025:14:44:05 +0000] "POST /api/subscribe HTTP/1.1" 201 142 "-" "python-requests/2.31.0"
203.0.113.42 - - [06/May/2025:14:45:32 +0000] "POST /api/inventory HTTP/1.1" 403 321 "-" "python-requests/2.31.0"
203.0.113.42 - - [06/May/2025:17:13:28 +0000] "GET /robots.txt HTTP/1.1" 200 452 "-" "python-requests/2.31.0"
203.0.113.42 - - [06/May/2025:17:13:45 +0000] "GET /sitemap.xml HTTP/1.1" 200 24536 "-" "python-requests/2.31.0"
203.0.113.42 - - [06/May/2025:17:15:22 +0000] "GET /api/products HTTP/1.1" 200 47834 "-" "python-requests/2.31.0"
203.0.113.42 - - [06/May/2025:17:17:13 +0000] "GET /api/categories HTTP/1.1" 200 2843 "-" "python-requests/2.31.0"
```

### Summary

To mitigate the threat and secure the system, it is critical to block the identified IPs, investigate and remove any web shells, audit admin access, and enforce strong authentication measures. Multi-factor authentication (MFA) should be enabled for all admin and privileged accounts to prevent unauthorized access. The application code must be reviewed for input validation flaws, and access to sensitive files must be restricted. Additionally, implementing a Web Application Firewall (WAF), rate-limiting, and real-time monitoring will significantly reduce further risk.