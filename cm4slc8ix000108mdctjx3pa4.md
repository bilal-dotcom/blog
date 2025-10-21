---
title: "Subdomain Enumeration"
seoTitle: "Subdomains : Effective Research Methods"
seoDescription: "Discover how to identify subdomains using tools like Amass, OneForAll, Chaos (ProjectDiscovery), Subfinder, Assetfinder,Sublist3r."
datePublished: Tue Dec 17 2024 15:00:35 GMT+0000 (Coordinated Universal Time)
cuid: cm4slc8ix000108mdctjx3pa4
slug: subdomain-enumeration
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1734137232240/0be7c83f-1014-4a78-a689-94198a44fe81.jpeg
tags: hashnode, subfinder, 2articles1week, cybersecurity-1, cybersec, bugbounty, subdomains, bugbountytips, subdomain-enumeration, subdomain-discovery, assetfinder, amass, sublist3r

---

Subdomain research allows the identification of subdomains linked to a main domain that could be vulnerable, thus serving as entry points for misconfigured services. Discovering subdomains helps gather information about the technologies and configurations used.

## Crt.sh

[**Crt.sh**](http://Crt.sh) is an online tool that allows you to search for and view SSL/TLS certificates for various domains. It can also be used to find subdomains. By searching for a main domain, you can see the certificates associated, but also those issued for its subdomains.

## Virus Total

[VirusTotal](https://www.virustotal.com/gui/) is an online search tool that allows you to analyze files and URLs to detect potential viruses. It performs a complete scan by using multiple antivirus engines et URLs detection tools. It can also be used to search for subdomains et other information.

The subdomains associated to the search will be listed in the Relations tab. The image below shows a list of subdomains associated with Amazon.ca

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730480512189/389d5154-f775-4c5b-bbdc-36b533f0b940.png align="center")

## Amass

Amass is a tool developped by OWASP (Open Web Application Security Project) that is used for identifying subdomains and IP addresses associated by using public sources such as APIs, SSL certificates, DNS, etc.

The official documentation can be found on [**GitHub**](https://github.com/owasp-amass/amass)**.**

To install Amass, use the following command:

```plaintext
go install -v github.com/owasp-amass/amass/v4/...@master
```

### Options

* `amass enum` : this mode is used to enumerate subdomains by using public sources and active reconnaissance technique
    
* `amass intel` : this mode is used to discover domains and others assets that can be explored in more details during the enumeration process.
    
* `amass enum -d website.com -active -brute -aw /PATH -bl “word“`
    
    * the `-d` option specified the domain fow which we want to enumerate subdomains
        
    * the `-active` option allows to discover active subdomains
        
    * the `-aw` option allows you to specify a wordlist file that will be used for altering the names of subdomains. This enables dynamic modification of subdomains by adding terms to the subdomain names that are not visible in standard DNS results.
        
    * the `-bl` option allows you to specify domains to exclude from the search
        

The image below shows the Amass command used, which discovered several pieces of information, including MX, A, AAAA records, and several other subdomains. It also includes ASN information from CloudFlare, which likely manages the IP addresses of these subdomains.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731637967194/67052a86-6ebd-4ee1-a971-5c194d1e4753.png align="center")

## Chaos projectdiscovery

[Chaos](https://chaos.projectdiscovery.io/) is a project by ProjectDiscovery that provides information on subdomains. It has a database with a large number of subdomains for public domains, which is regularly updated.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730732080367/c021ae5b-7ed5-4b7e-9faf-541409009056.png align="center")

Simply download the .zip file to obtain a list of subdomains associated with the term or domain in the search.

Chaos ProjectDiscovery is an open-source project and can also be used via GitHub. Using this tool with the command-line API is much more flexible and powerful in terms of searches. By using the command-line tool, you get a more comprehensive list, unlike the web version, which may not return any results for certain domains.

To install it, use the following command:

```plaintext
go install -v github.com/projectdiscovery/chaos-client/cmd/chaos@latest
```

### Options

* `chaos -h`: This option lists all the available options for Chaos ProjectDiscovery.
    
* `export PDCP_API_KEY=[API_KEY]`:
    
    * this option allows you to specify the API key, which can be obtained simply by signing up on the website
        
    * the API key must be specified for using the command-line tool.
        
* `chaos -d [site.com] -count -o list.txt`
    
    * the `-d` option specifies the domain to analyze.
        
    * the `-count` option displays the number of subdomains without listing them.
        
    * the `-o` option specifies a file (in this case, list.txt) to store the output.
        
* `chaos -dL [list_domains.txt] -json | grep [STRING] > list.txt`
    
    * the `-dL` option specifies a file containing a list of domains to analyze.
        
    * the `-json` option displays the output in JSON format.
        
    * the `grep` option filters the search results according to the defined string and saves them in the file list.txt.
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730734621204/84dc8d3e-161e-4467-a504-ea6e0b66b14b.png align="center")

## Subfinder

Subfinder is a subdomain enumeration tool written in the Go programming language, developed by ProjectDiscovery. It is used to perform passive enumeration of subdomains for a target domain, meaning it uses public online sources to identify subdomains without directly interacting with the target servers. It utilizes sources such as Censys, Chaos, [Recon.dev](http://Recon.dev), Shodan, Spyse, Virustotal, and others.

The official documentation can be found on [GitHub](https://github.com/projectdiscovery/subfinder).

To install Subfinder, use the following command:

```plaintext
go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
```

### Options

* `subfinder -h`: the `-h` option lists all the options available for Subfinder.
    
* `subfinder -ls`: the `-ls` option lists all possible data sources.
    
* `subfinder -d [site.com] -all -json -cs -o [list.txt]`:
    
    * the `-d` option specifies the domain to analyze.
        
    * the `-all` option uses all sources for enumeration.
        
    * the `-json` option displays the output in JSON format.
        
    * the `-cs` option shows the source in the command result.
        
    * the `-o` option saves the results to a file.
        

Here, the domain hackerone.com is analyzed, displaying the sources of each subdomain with the `-cs` option, and excluding sources from the Google server 8.8.8.8 with the `-es` option.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730743484088/7cbc99d4-ff88-444c-81e0-26a35f294ebf.png align="center")

* `subfinder -dL [list_domains.txt] -s/-es [SOURCES] -rl [INT]`
    
    * the `-dL` option specifies a file containing a list of domains to analyze.
        
    * the `-s` option specifies the sources to use during the search.
        
    * the `-es` option excludes sources during the search.
        
    * the `-rl` option determines the number of HTTPS requests per second, which limits and prevents overload.
        
* `subfinder -d hackerone.com --active -oI`
    
    * the `--active` or `-nW` option checks subdomains in real-time. It verifies if the subdomains are active and responsive; otherwise, they are not listed.
        
    * the `-oI` or `-ip` option includes the IP addresses of the subdomains in the result. This option is only used in combination with the `--active` or `-nW` option.
        
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730746300133/9a589fef-a0de-48d6-a6ba-6811d5ab9b89.png align="center")
    

## Assetfinder

Assetfinder is also a tool developed in the Go programming language, used to enumerate subdomains associated with a primary domain. It uses sources such as Certspotter, Hackertarget, Facebook, Virustotal, etc.

The official documentation can be found on [GitHub](https://github.com/tomnomnom/assetfinder).

To install Assetfinder, use the following command:

```plaintext
go install github.com/tomnomnom/assetfinder
```

### Options

* `assetfinder -h`: the `-h` option lists all the options available for Assetfinder.
    
* `assetfinder -subs-only [SITE.COM] > list.txt`:
    
    * the `-subs-only` option limits the output to subdomains of the target domain. This is the only option available for Assetfinder.
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730748576730/b64bfe0d-7523-47d7-97ed-7fbf40eed732.png align="center")

## **Sublist3r**

Sublist3r is a subdomain enumeration tool that uses search engines and other online sources. It integrates the tool Subbrute, a brute force tool for subdomain enumeration, to enhance the ability to find more subdomains. Subbrute uses an improved wordlist to systematically test potential subdomains.

The official documentation can be found on [GitHub](https://github.com/aboul3la/Sublist3r).

To install Sublist3r, use the following command:

```plaintext
git clone https://github.com/aboul3la/Sublist3r.git
cd Sublist3r
pip install -r requirements.txt
```

### Options

* `python sublist3r.py -h`: the `-h` option lists all the options available for Sublist3r.
    
* `python sublist3r.py -b -d [site.com] -p 80,21 -o result.txt`:
    
    * the `-d` option specifies the target domain.
        
    * the `-b` option enables brute force mode, which helps discover subdomains.
        
    * the `-p` option specifies the ports to test to check if a subdomain is active on these ports.
        
    * the `-o` option saves the results to a file
        
* `python sublist3r.py -e google,virustotal -v -d [site.com]`:
    
    * the `-e` option allows you to specify the search engine to use.
        
    * the `-v` option enables verbose mode, which displays more details during script execution and shows subdomains in real-time.
        

The command executed below enumerates the subdomains of google.com and enables verbose mode, allowing subdomains to be displayed in real-time.

The command searches for subdomains from multiple sources, including Baidu, Yahoo, Google, Bing, SSL Certificates, etc.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731947680161/b9251afd-4e84-4196-bdd1-99ab4f68058b.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731947705826/e38a8163-0c8e-48a8-b14e-10d69196c913.png align="center")

## OneForAll

OneForAll is a subdomain enumeration tool used to list the subdomains of a primary domain. This tool uses several techniques such as DNS resolution, brute-forcing subdomains, and also collects data from public sources that other tools do not typically query. OneForAll addresses the power or efficiency issues that can be seen with other tools.

The official documentation can be found on [GitHub](https://github.com/shmilylty/OneForAll).

To install OneForAll, use the following commands:

First, check the versions of Python and pip3. The Python3 version must be greater than 3.6.0, and the pip3 version must be greater than 19.2.2.

```plaintext
python -V
pip3 -V
```

Clone the project repository

```plaintext
git clone https://github.com/shmilylty/OneForAll.git
```

Create a virtual environment to separate the installation from your Python environment.

```plaintext
virtualenv -p python3 .venv
source .venv/bin/activate
```

Install the dependencies.

```plaintext
cd OneForAll
python3 -m pip install -U pip setuptools wheel
pip3 install -r requirements.txt
```

### Options

* `python3 oneforall.py -h`: This option lists all the options available for OneForAll.
    
* `python oneforall.py --target [site.com] --brute FALSE --dns FALSE --valid TRUE run`:
    
    * the`--target` option specifies the URL or domain to analyze.
        
    * the `--brute` option enables/disables the brute force module for searching subdomains. By default, this option is enabled.
        
    * the `--dns` option enables/disables DNS resolution during subdomain analysis. This option is also enabled by default.
        
    * the `--valid` option specifies that only active and valid subdomains will be considered. The tool will query each subdomain to verify their validity.
        
* `python oneforall.py --targets list_urls.txt --takeover TRUE --path /PATH/TO/FILE run`:
    
    * the `--targets` option specifies a list of domains contained in a file.
        
    * the `--takeover` option checks if a found subdomain is vulnerable to subdomain takeover. This option is crucial for identifying misconfigured subdomains and is disabled by default.
        
    * the `--path` option specifies the file path where the subdomain collection results will be stored. If a file is not specified, OneForAll will automatically generate a results file.
        
    * As shown in the image below, the tool automatically generates a .csv file listing the results in the `/results` folder.
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731290129863/dd8e94e3-1729-4a26-91eb-92005077424d.png align="center")

Interpretation of results.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731279673339/8f583716-d6db-4520-8505-a765df582355.png align="center")

The results of the OneForAll command on https://hackerone.com show the executed modules and the number of subdomains found. For example, with the results of this query, it is observed that the CertInfo module found 4 subdomains in 0.1 seconds, access to the site dnsgrep.cn was denied (status 403), among other information.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731290099020/204d1d6d-7444-43a0-8db8-b01979d7aaee.png align="center")

The servers a.ns.hackerone.com and b.ns.hackerone.com were also found, which allowed the retrieval of IP addresses 162.159.0.31 and 162.159.1.31.

The found subdomains will be listed in the /results folder by default in a CSV file, which can be complex to read. You can format the columns for display using the `column` and `less` commands:

```plaintext
column -s, -t < hackerone.com.csv | less -#2 -N -S
```

* the `-s,` option (with a comma following `-s`) specifies that the comma (`,`) is the delimiter separating columns in the CSV file.
    
* the `-t` option tells `column` to create a table with aligned columns.
    
* `< hackerone.com.csv` redirects the content of the hackerone.com.csv file as input for the `column` command.
    
* `less -#2 -N -S` paginates the output with `less`, displays line numbers with `-N`, allows horizontal scrolling with `-S`, and sets tab spacing to 2 with `-#2`. You can exit the display by pressing the `q` key.
    

The file with the results in a standard display appears as follows:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732226018213/a9a55b8d-66c6-4fa6-a787-9901d650b261.png align="center")

The display transitions from the image above to those below using the command `column -s, -t < hackerone.com.csv | less -#2 -N -S`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732241237226/1f2caa58-61dd-482e-a049-ceb066949a9c.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732241262762/c63c29e9-ff72-40e2-bc88-12c698f62fec.png align="center")

Subdomain enumeration is a crucial step in information gathering, allowing the identification of parts of a domain that might be misconfigured or vulnerable. With tools like OneForAll, Subfinder for passive search, Assetfinder for its speed, and Sublist3r for combining passive search and brute force, valuable information about a domain's infrastructure can be obtained.

<mark>It is important to note that the use of these tools should always be conducted within a legal framework.</mark>