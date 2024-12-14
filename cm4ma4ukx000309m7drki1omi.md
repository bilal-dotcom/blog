---
title: "Information Gathering in Bug Bounty"
seoTitle: "Bug Bounty: Gathering Essential Information"
seoDescription: "Guide for gathering information :  Google Dorking, BugBounty Search Engine, Github Recon, Enum4Linux, WaybackURL, Arjun, Censys, Gowitnes"
datePublished: Fri Dec 13 2024 05:00:17 GMT+0000 (Coordinated Universal Time)
cuid: cm4ma4ukx000309m7drki1omi
slug: information-gathering-in-bug-bounty
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/SYofhg_IX3A/upload/61cfc03d26bc96175a6feeb27d8993c2.jpeg
tags: hashnode, 2articles1week, cybersecurity-1, cybersec, waybackurls, bugbountytips, googledorks, information-gathering, github-recon, bugbountysearchengine, censys, enum4linux, karmav2, crunchbase, arjun

---

In the context of Bug Bounty or security testing, information gathering is a step that involves collecting as much data as possible about a target. The goal is to better understand its environment and, above all, identify potential vulnerabilities. This step relies on various techniques, and we’ll dive into some of them later. In this article, we’ll break down a few tools that are super handy for information gathering.

## Google Dorking

Google Dorking is a search technique that uses advanced queries to find sensitive information that isn’t accessible through a standard Google search. It helps narrow down the results to uncover web pages, files, and other hidden data.

### Options

* `<keyword> site:www.website.com filetype:pdf`
    
    * `site:` limits the search to the specified website.
        
    * `filetype:` searches for specific file types, like PDFs in this case.
        
* `intitle:<keyword> inurl:<keyword> cache:<keyword>`
    
    * `intitle` finds web pages where the specified keyword appears in the title.
        
    * `inurl`: searches for web pages with the keyword in the URL. This is useful for finding specific pages, directories, or files that include the keyword.
        
    * `cache`: displays the cached version of a web page, essentially a backup saved by a search engine. Often used to view pages that have been deleted but are still accessible in the search engine’s cache. Also helpful for seeing how a site looked before major changes.
        

In the image below, we can see that the search only returns PDFs file that have “bug bounty“ term in their titles

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731352149546/d926860e-80cf-420f-9e13-5a75cc49734f.png align="center")

We can use Google Dorks for more sensitive informations.

* `intitle:"index of" "server-status" site:www.website.com`: this query searches for pages on the site that contains “index of“ and “server-status“ in the title. This can provide some informations about the server or web server status pages.
    
* `"public" "key" filetype:pem site:www.website.com` : this query is used to search for PEM files on the site that contains the words “public“ and “key“. Theses files are often used to store cryptography keys. So, we can potentially find exposed public keys
    

Google Dorks can be effective means to identify vulnerabilities in a system. There are also files and lists available on Internet containing Google Dorks fot various searches.

## **Bug Bounty Search Engine**

[**BugBountySearchEngine**](https://nitinyadav00.github.io/Bug-Bounty-Search-Engine/) is a search engine that consolidates many methods for searching vulnerabilities and sensitive information on domains, websites, and even individuals. It offers options to find configuration or backup files, exposed databases, subdomains, and more. You can also search for specific datas on public sources like Github, Shodans, Censys and more.

The tool is availaible [here](https://nitinyadav00.github.io/Bug-Bounty-Search-Engine/).

To use it, enter a target domain in the textbox. Then select one of the predefined searches based on the information you are looking for.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731372249570/0646ddb6-87c6-4678-a1ab-d1c57c632ed0.png align="left")

## **Github Recon**

Github Recon is a technique used to search information on projects, organizations or even individuals by exploring Github repositories. It involves analyzing commit histories and monitoring activities to discover sensitive information. You can also use Github Dorks to refine searches. Sometimes, developers may accidentally leave sensitive information such as API keys, default passwords, and more.

In the search bar, just enter the desired dorks or queries. Then, examine the code and commits to gather information that can be useful.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731375930408/cbb7150b-4c0a-4384-93e3-aafb3d48eee1.png align="left")

## **Censys**

[**Censys**](https://search.censys.io/) is a search engine and plateform that contains data from various sources about internet-connected infrastructures. It provides real-time data and returns informations on devices, services or domains. It can therefore be very useful in a process of gathering information on a target.

Censys is used to collect information such as IP adresses, services and open ports. With advanced filters, we can refine the results according to the labels (IPv6 hosts, remote access, email, etc…), server locations, and software providers (Amazon, Akamai, Google, Apache, nginx).

By doing a Google search, you can see that you have access to a lot of information, and the search result returns a lot of hosts that could be analyzed.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730477903707/ba854e66-5155-43c0-989f-74c2aa28c0cc.png align="left")

By selecting a host, you can access additional information such as routing, services, location, associated event history, and a Whois tab that provides details about the owner of the IP address.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730477808584/293379d7-0280-4d8c-aac9-35cd63a86acd.png align="left")

## **Enum4Linux**

Enum4linux is an enumeration tool used to extract data from systems using the SMB (Server Message Block) protocol. It allows the collection of various information about user accounts, domain names, local groups, and shared folders on a network.

### **Options**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1728488102092/7988f104-95e8-4549-8f44-8bf3497faaeb.png align="left")

* `enum4linux -U -S -P -G [ip_adress]`
    
    * The `-U` option lists the users on the target system.
        
    * The `-S` option lists the available shares on the target system.
        
    * The `-P` option displays the password policy.
        
    * The `-G` option lists the groups on the system.
        
    * The `-a` option combines all options (`-U -S -G -P -r -o -n -i`). It is the default option when none is selected.
        
* `enum4linux -u [username]-p [password] -U [ip_adresss]`
    
    * The `-u` and `-p` options specify a username and a password.
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1729861779011/2ca2b547-12e0-4dbf-b74d-2bc29828e016.png align="left")

You can see several users on the system: Administrator, Guest, etc.

Enum4linux can be very useful because information such as users, file shares on the network, and groups and permissions can be exploited to identify potential vulnerabilities.

## **WaybackURL**

WaybackURL is a service that allows access to old or archived versions of websites using the Internet Archive, a web page archiving service. It is used to view old versions of websites, and even if they have been modified, they may still contain vulnerabilities that have not been fixed or are still accessible through these archives.

The official documentation can be found on [GitHub](https://github.com/tomnomnom/waybackurls).

To install WaybackURL, use the following command:

```python
go install github.com/tomnomnom/waybackurls@latest
```

### **Options**

* `waybackurls -h`: The `-h` option lists all the options for waybackurls.
    
* `waybackurls` [`https://site.com`](https://site.com) `-dates | grep [word]`
    
    * the `-dates` option displays the archival date of each URL.
        
    * `grep` filters the results to show only the URLs containing the term "word".
        
* `cat domains.txt | waybackurls | grep -vE "\.(js|txt)$" > urls.txt`
    
    * This command reads the file `domains.txt` and pipes the output to waybackurls. The results are then stored in the file `urls.txt`.
        
    * The `-vE` option excludes lines matching the expression specified immediately after (with the `-v` option) and enables extended regular expressions (with the `-E` option).
        
    * The expression `"\.(js|txt)$"` is used to match strings that end with `.js` or `.txt`.
        
    * Therefore, this command will list the archived URLs, excluding those that end with `.js` or `.txt`.
        
* `echo "https://hackerone.com" | waybackurls -no-subs`
    
* The `-no-subs` option excludes subdomains of the target domain from the results.
    
* This command will display all archived URLs of [hackerone.com](http://hackerone.com).
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731341340729/80c29897-cd6b-47e6-b36b-998a76339c7b.png align="left")

## **Gowitness**

Gowitness is a tool used to take screenshots of websites, collect information on HTTP headers, and create reports on visited sites in an automated manner.

This tool, although it might seem "useless" because one could simply visit the web pages, is actually very practical when there are multiple sites to check without wasting a lot of time opening them one by one.

To install Gowitness, use the following command:

```python
go install github.com/sensepost/gowitness@latest
```

### **Options**

* `gowitness -h`: This option lists all the options for gowitness.
    
* `gowitness scan -h`: Shows the available options for performing scans.
    
* `gowitness scan single --url` [`https://google.com`](https://google.com) `--screenshot-path ./PATH --screenshot-format png --screenshot-fullpage`:
    
    * The `--url` option specifies the URL to capture.
        
    * The `--screenshot-path` option sets the path where the screenshot will be saved.
        
    * The `--screenshot-format` option sets the format of the screenshot. The default is JPEG, but only JPEG and PNG are valid formats.
        
    * The `--screenshot-fullpage` option captures the entire page, rather than just the visible part. This is useful for pages with a lot of scrolling content.
        
* `gowitness scan file -f ./PATH --log-scan-errors --write-db`
    
    * The `file -f` option specifies a file containing a list of URLs to display.
        
    * The `--log-scan-errors` option reports errors that occurred during the scans.
        
    * The `--write-db` option saves the results in a database for later consultation.
        
    
    The image below shows the execution of a command with gowitness. Messages indicate a failed attempt via port 80 for unsecured HTTP connections. However, the connection succeeded via port 443 for HTTPS connections, with screenshots taken.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731508066707/5f041b74-ed39-4dc2-95bc-d3e1611eeae3.png align="left")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731508214727/3a46ae75-d700-4183-8a06-bec0802b63f2.png align="left")

## **KarmaV2**

KarmaV2 is an OSINT reconnaissance tool that allows you to find public and in-depth information about a target. The official documentation can be found on GitHub.

You can install KarmaV2 with the following commands.

1. Clone the repository:
    
    ```python
     git clone https://github.com/Dheerajmadhukar/karma_v2.git
    ```
    
2. Install Shodan and the Python module mmh3.
    
    ```python
     python3 -m pip install shodan mmh3
    ```
    
3. Install the JSON parser JQ if it is not already installed.
    
    ```python
     apt install jq -y
    ```
    
4. Install httprobe
    
    ```python
     go install -v github.com/tomnomnom/httprobe@master
     cd httprobe
     go build main.go
     mv main httprobe
     sudo mv httprobe /bin/
    ```
    
5. Install Interface
    
    ```python
     git clone https://github.com/codingo/Interlace.git
     cd Interlace
     pip install -r requirements.txt
     sudo python3 setup.py install
    ```
    
6. Install Nuclei
    
    ```python
     go install -v github.com/projectdiscovery/nuclei/v2/cmd/nuclei@latest
    ```
    
7. Install Lolcat
    
    ```python
     apt install lolcat -y
    ```
    
8. Install anew
    
    ```python
     git clone https://github.com/tomnomnom/anew.git
     cd anew
     go build main.go
     mv main anew
     sudo mv anew /bin/
    ```
    
9. Create an account on [Shodan.io](http://Shodan.io) to obtain your Shodan API key.
    
    Insert the key into the `.token` file in the `/karma_v2` directory.
    
    ```python
     nano ./token
     SHODAN_API_KEY_FROM_SHODAN
    ```
    
10. Run karma with the following command
    
    ```python
    bash karma_v2 -h
    ```
    

Depending on your system, you may need to run karma\_v2 in a virtual environment.

### Options

* `bash karma_v2 -h`: To see the list of all options for Karma\_v2.
    
* `bash karma_v2 -d [DOMAIN] -l [INT] -ip -cve`
    
    * The `-d` option specifies the domain.
        
    * The `-ip` option searches for IP addresses associated with this domain.
        
    * The `-l` option specifies the number of results to return. You can specify `-1` to indicate an unlimited number of results.
        
    * The `-cve` option searches for vulnerabilities associated with the domain.
        
* The options `-d`, `-l`, and the mode options (`-ip`, `-asn`, `-cve`, etc.) are required to run karma\_v2.
    
* The `-deep` option supports all options: count, ip, asn, cve, favicon, leaks.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727707001799/f22c0cce-30ce-4985-bd1c-e1db2812657b.png?auto=compress,format&format=webp align="left")

## Crunchbase

[Crunchbase](https://www.crunchbase.com/) is a paid platform that provides information on companies and investors. It offers data that allows for the analysis of company acquisitions in the market, such as startups, as well as information about company members.

## Arjun

Arjun is a tool that helps discover hidden HTTP parameters in GET and POST requests that could be vulnerable in web applications. It identifies potential entry points for attacks by testing multiple possible parameters. For context, GET and POST requests are methods for sending data to a web server. The GET method sends data via the URL, meaning it appears in the browser's address bar. The POST method sends data in the body of the HTTP request, making it invisible in the URL. Arjun is used to search for these parameters in applications.

The official documentation can be found on [GitHub](https://github.com/s0md3v/Arjun).

To install Arjun, use the following command:

```python
pipx install arjun
```

### Options

* `arjun -h`: This option displays all the options for the tool.
    
* `arjun -u http://website.com -m POST -o output.json -oB`
    
    * The `-u` option specifies the site to analyze.
        
    * The `-o` option saves the data to a file.
        
    * The `-m` option sets the HTTP method to use for the request. Valid formats are GET or POST for the HTTP method and JSON or XML for the request body.
        
    * The `-oB` option redirects traffic through Burp Suite for analysis. By default, Burp listens on `127.0.0.1:8080`, allowing you to view the requests and responses intercepted by Burp.
        
* `arjun -i urls.txt -w /PATH/TO/FILE --include “token=qwerty“`
    
    * The `-i` option specifies a file with a list of URLs to analyze.
        
    * The `-w` option allows you to specify a file containing a list of words. This list includes potential parameter names that Arjun will use to test the target URL. By default, the tool uses a built-in word list located at `{arjundir}/db/large.txt`.
        
    * The `--include` option allows you to insert data for each request. The variable `token` with a specific value is used in applications requiring authentication tokens. You can include any type of data, such as API keys, authentication keys, and others.
        
    * You can also use the options `-m XML --include='<?xml><root>$arjun$</root>'` to include a specific XML structure in the request. The marker `$arjun$` is where the tool will insert the parameters it tests.
        

### Interpretation of the results.

The image above shows the command `arjun -u http://testphp.vulnweb.com/listproducts.php -oB -o output.json` executed.

It shows that the command identified the parameters `artist` and `cat` in the response body.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731603651357/7900ebba-4c9a-4749-9ec5-f23736d5d634.png align="center")

For the `-oB` option to work, Burp must be open and configured to intercept requests. You can open a terminal and launch Burp with the command `burpsuite`. Go to the Proxy settings and ensure that the proxy address in Burp is configured to `127.0.0.1:8080`. Then, enable interception.

You can see two query parameters, "cat=111110" and "artist=11111", along with other details such as the host, client software, etc.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731602051433/005fea5a-2a79-487f-8919-7a777b010b78.png align="left")

These tools are suitable for the reconnaissance phase in a security test or bug bounty, where the goal is to discover potential vulnerabilities by gathering data on the target.

<mark>It is important to note that the use of these tools should always be conducted within a legal framework.</mark>