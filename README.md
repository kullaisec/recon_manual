# recon_manual
manual recon

## Subdomains Full gather

```
subfinder -d redacted.com -all -silent -nc -o subdomains.txt
```
```
assetfinder --subs-only redacted.com | sort -u >> subdomains.txt
```
```
amass enum -passive -d redacted.com -silent -nocolor | sort -u >> subdomains.txt
```

I utilized the Shuffledns tool to perform Subdomain Bruteforce and saved the resulting output in a file named subdomains.txt.

```
shuffledns -d redacted.com -r /root/tools/massdns/lists/resolvers.txt -w subdomains-top1million-5000.txt -silent -nc | sort -u >> subdomains.txt
```
In the Subdomain Permutation step, I used the Altrex tool to create a new list of subdomains with permutations.

```
cat subdomains.txt | alterx -silent -o subdomain-permutation.txt
```
Apart from other tools, one can also utilize the dnsgen tool. While working on DNS resolving, I used the massdns tool. Although dnsx is also a viable option, previous tests revealed that massdns performs much faster.

```
cat subdomain-permutation.txt | massdns -r /root/tools/massdns/lists/resolvers.txt -o L -w dns-resolving.txt
```
## All Live Domains finding using httpx
```
cat dns-resolving.txt | httpx -sc -title -td -favicon -asn -silent -nc -o service-discovery.txt
```

All done run your scans ...

## Subdomain Takeover
```
subzy run --targets subdomain-permutation.txt --hide_fails --https --timeout 10 | notify
```

## XSS Automation oneliner
```
cat onsurity-final-live.txt | katana | Gxss -c 100 | dalfox pipe --skip-bav --skip-mining-all --skip-grepping 
```

## SQLi oneliner
```
cat subdomain-permutation.txt | dnsx | waybackurls | uro | grep "\?" | head -20 | httpx -silent > urls;sqlmap -m urls --batch --random-agent --level=1 | tee sql_sara.txt
```
```
cat subdomain-permutation.txt | dnsx | waybackurls | sort -u | gf sqli > gf_sql.txt; sqlmap -m gf_sqli.txt --batch --risk 3 --random-agent | tee -a gf_sqli.txt
```
