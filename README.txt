A complete guide to dir brute force,admin panel and API endpoints:-

1:- Intro.
2:- Tools repos.
3:- Wordlists collection.
4:- Tips.


1:- Intro:-

Befor you start your attack you have to know what is the tech is running on the site to save your time.

There is a lot of programs that identify the tech such as:-

1:- httpx - https://github.com/projectdiscovery/httpx
2:- wappalyzer - https://www.wappalyzer.com/
3:- whatweb - https://github.com/urbanadventurer/WhatWeb
4:- BuiltWith - https://builtwith.com/
5:- Netcraft - https://www.netcraft.com/
6:- WhatRuns - https://www.whatruns.com/
7:- PageXray - https://chrome.google.com/webstore/detail/pagexray/aedmpdookgbneegaeajpoldpnpfbpmlb?hl=en
8:- SimilarTech - https://www.similartech.com/
9:- What CMS - https://whatcms.org/

Now after you get information about the website you can decide which wordlist will work.

There are many programs will do the job for you if you know how to use it correctly
----------------------------------------------------------------------------------------------------------------
 
Lets start with dirsearch ( my favorite) :-

The simple usage is :-

python3 dirsearch.py -u https://target

Now you notice we hav not point any wordlist to dirsearch becaust by defulte will look for dicc.txt on the db floder and use it.

Now we want to use our cusotm wordlist:-

python3 dirsearch.py -u https://target -w /root/wordlist

Now lets say we have a php site and we want to brute forc php extension you need to add -e option and add php.

python3 dirsearch.py -e php -u https://target -w /path/to/wordlist

You can add more than one extension like js,html ect.

If dirsearch found an endpoint lets call it admin, dirsearch have an option called Recursive scan it means it will brute after that endpiont:-

Just give it the depth and add the option -r with threads option -t 20 

python3 dirsearch.py -e php -u https://target -r --recursion-depth 3  --exclude-subdirs image/,css/ --recursion-status 200-399 

Dirseach have an option to send every request to burpsuite or any proxy you want just add the proxy ip and port and exclud some responses like 429

python3 dirsearch.py -e php,html,js -u https://target -x 400,403,429  --proxy 127.0.0.1:8080 

You can use dirsearch to find backup files like this

python3 dirsearch.py -e conf,config,bak,backup,swp,old,db,sql,asp,aspx,aspx~,asp~,py,py~,rb,rb~,php,php~,bak,bkp,cache,cgi,conf,csv,html,inc,jar,js,json,jsp,jsp~,lock,log,rar,old,sql,sql.gz,sql.zip,sql.tar.gz,sql~,swp,swp~,tar,tar.bz2,tar.gz,txt,wadl,zip,log,xml,js,json  -u https://target

For more usage check the dirseach repo.
----------------------------------------------------------------------------------------------------------------
ffuf:- A go program but faster than dirsearch and you can find the usage on the github page.
https://danielmiessler.com/study/ffuf/
DirBuster:- you can find the usage on the github page
----------------------------------------------------------------------------------------------------------------
Find admin panel:-

1:- Using google dorks. 
site:target.com inurl:admin | administrator | adm | login | l0gin | wp-login
intitle:"login" "admin" site:target.com
intitle:"index of /admin" site:target.com
inurl:admin intitle:admin intext:admin


2:- Using httpx and a wordlist:-
httpx -l hosts.txt -paths /root/admin-login.txt -threads 100 -random-agent -x GET,POST  -tech-detect -status-code  -follow-redirects -title -content-length

httpx -l hosts.txt-ports 80,443,8009,8080,8081,8090,8180,8443 -paths /root/admin-login.txt -threads 100 -random-agent -x GET,POST  -tech-detect -status-code  -follow-redirects -title -content-length

3:- Using some programs:-
https://github.com/the-c0d3r/admin-finder
https://github.com/RedVirus0/Admin-Finder
https://github.com/mIcHyAmRaNe/okadminfinder3
https://github.com/penucuriCode/findlogin
https://github.com/fnk0c/cangibrina
----------------------------------------------------------------------------------------------------------------
Find API endpoints:-

Kiterunner

https://blog.assetnote.io/2021/04/05/contextual-content-discovery/
https://github.com/assetnote/kiterunner/releases/download/v1.0.1/kiterunner_1.0.1_linux_amd64.tar.gz
https://wordlists-cdn.assetnote.io/data/kiterunner/routes-large.kite.tar.gz
https://wordlists-cdn.assetnote.io/data/kiterunner/routes-small.kite.tar.gz
https://wordlists-cdn.assetnote.io/rawdata/kiterunner/routes-large.json.tar.gz
https://wordlists-cdn.assetnote.io/rawdata/kiterunner/routes-small.json.tar.gz

# Just have a list of hosts and no wordlist
kr scan hosts.txt -A=apiroutes-210328:20000 -x 5 -j 100 --fail-status-codes 400,401,404,403,501,502,426,411

# You have your own wordlist but you want assetnote wordlists too
kr scan target.com -w routes.kite -A=apiroutes-210328:20000 -x 20 -j 1 --fail-status-codes 400,401,404,403,501,502,426,411

# Bruteforce like normal but with the first 20000 words
kr brute https://target.com/subapp/ -A=aspx-210328:20000 -x 20 -j 1

# Use a dirsearch style wordlist with %EXT%
kr brute https://target.com -w dirsearch.txt -x 20 -j 1 -exml,asp,aspx,ashx -D

kr brute <hosts-file> -w wordlist.txt -e asp,aspx,cfm,xml -x20 -j250 -A=apiroutes-210228

kr kb replay -w routes.kite "POST    500 [     40,    5,   1] https://redacted/user/create 0cc39f768fe347ac930d8fc3f6b5bf2e4b6be41e" --proxy http://localhost:8080

API Scanning

# single target
kr scan https://target.com:8443/ -w routes.kite -A=apiroutes-210228:20000 -x 10 --ignore-length=34

# single target, but you want to try http and https
kr scan target.com -w routes.kite -A=apiroutes-210228:20000 -x 10 --ignore-length=34

# a list of targets
kr scan targets.txt -w routes.kite -A=apiroutes-210228:20000 -x 10 --ignore-length=34

Vanilla Bruteforcing

kr brute https://target.com -A=raft-large-words -A=apiroutes-210228:20000 -x 10 -d=0 --ignore-length=34 -ejson,txt

# this will use the first 20000 lines in the api routes wordlist
kr scan targets.txt -A=apiroutes-210228:20000 -x 10 --ignore-length=34

# this will use the first 10 lines in the aspx wordlist
kr brute targets.txt -A=aspx-210228:10 -x 10 --ignore-length=34 -easp,aspx
----------------------------------------------------------------------------------------------------------------

3:- Tips:-

Genetate your custom wordlist:-

Lets say you are hunting on indeed program and this program use the same code on all the subdomains and to save time we need to generat a wordlist for indeed.

We will use gau to make this wordlist with unufurl and will exclude some extensions. 

gau -subs indeed.com | unfurl -u keys | tee -a wordlist.txt ; gau -subs indeed.com | unfurl -u paths|tee -a ends.txt; sed 's#/#\n#g' ends.txt | sort -u | tee -a wordlist.txt | sort -u ;rm ends.txt  | sed -i -e 's/\.css\|\.png\|\.jpeg\|\.jpg\|\.svg\|\.gif\|\.wolf\|\.bmp//g' wordlist.txt
----------------------------------------------------------------------------------------------------------------

Custom wordlist from robots.txt- Thanks to https://twitter.com/remonsec/status/1410481151433576449

step 1-
httpx -l urls.txt -paths /robots.txt -silent -o robots-url.txt

step 2- 
for url in $(cat robots-url.txt);do http -b $url | grep 'Disallow' | awk -F ' ' '{print $2}' | cut -c 2- | anew robot-words.txt;done

----------------------------------------------------------------------------------------------------------------
Extract endpoints form js files:-
https://gist.github.com/fuckup1337/49484c35c8ad8ae1d165ffe7d71375eb
----------------------------------------------------------------------------------------------------------------
IIS Short Name Scanner v2.3.9
A scanner for IIS short file name (8.3) disclosure vulnerability by using the tilde (~) character

Usage:-

- Example 0 (to see if the target is vulnerable):
 java -jar iis_shortname_scanner.jar http://example.com/folder/

- Example 1 (uses no thread - very slow):
 java -jar iis_shortname_scanner.jar 2 0 http://example.com/folder/new%20folder/

- Example 2 (uses 20 threads - recommended):
 java -jar iis_shortname_scanner.jar 2 20 http://example.com/folder/new%20folder/

- Example 3 (saves output in a text file):
 java -jar iis_shortname_scanner.jar 0 20 http://example.com/folder/new%20folder/ > c:\results.txt

- Example 4 (bypasses IIS basic authentication):
 java -jar iis_shortname_scanner.jar 2 20 http://example.com/folder/AuthNeeded:$I30:$Index_Allocation/

- Example 5 (using a new config file):
 java -jar iis_shortname_scanner.jar 2 20 http://example.com/folder/ newconfig.xml 
 
- Example 6 (scanning multiple targets using a linux box):
 ./multi_targets.sh scope.txt 1
----------------------------------------------------------------------------------------------------------------
SAML dir brute force:-

if you’re testing a website and it’s redirect to Onelogin. No problems just tracing the SAML request and decode it to find the hidden website in the request. Buggy Internal website:-

https://twitter.com/Alra3ees/status/1259969808969469954

SAML tracer
https://addons.mozilla.org/en-US/firefox/addon/saml-tracer/

SAML Decoder
https://www.samltool.com/decode.php
----------------------------------------------------------------------------------------------------------------

2:- Tools repos:-

https://github.com/maurosoria/dirsearch
https://github.com/ffuf/ffuf
https://github.com/s0md3v/Breacher
https://github.com/KajanM/DirBuster
https://github.com/epi052/feroxbuster
https://github.com/assetnote/kiterunner
https://github.com/Raz0r/aemscan
https://github.com/0ang3el/aem-hacker
https://github.com/irsdl/IIS-ShortName-Scanner
----------------------------------------------------------------------------------------------------------------


3:- Wordlists:-

https://github.com/six2dez/OneListForAll
https://wordlists.assetnote.io/
https://github.com/random-robbie/bruteforce-lists
https://github.com/Bo0oM/fuzz.txt
https://github.com/drtychai/wordlists
https://github.com/emadshanab/SAP-wordlist
https://github.com/emadshanab/Huge_DIR_wordlist
https://github.com/emadshanab/971-MB-content_discovery_wordlist
https://github.com/emadshanab/Combined-Wordlists
https://github.com/emadshanab/wordlistsbyNahamsec
https://github.com/emadshanab/WordLists-20111129
https://github.com/Karanxa/Bug-Bounty-Wordlists
https://github.com/chrislockard/api_wordlist
https://github.com/tauh33dkhan/ApiWordlist
https://github.com/bishal0x01/api_wordlist
https://github.com/fuzzdb-project/fuzzdb
https://github.com/danielmiessler/SecLists
https://github.com/1N3/IntruderPayloads
https://github.com/cujanovic/dirsearch-wordlist
https://github.com/Droidzzzio/EnumerationList
https://github.com/brycedrennan/wordlists
https://github.com/SilverPoision/a-full-list-of-wordlists
https://github.com/hypn/custom-wordlists
https://github.com/huntergregal/dir_list
https://github.com/bashexplode/directory-wordlists
https://github.com/JavierOlmedo/UltimateCMSWordlists
https://github.com/ZephrFish/Wordlists
https://github.com/emadshanab/admin-login
https://github.com/dewhurstsecurity/api_paths
https://github.com/clarkvoss/Drupal-Dir-Search
https://github.com/xajkep/wordlists
https://github.com/emadshanab/Adobe-Experience-Manager

----------------------------------------------------------------------------------------------------------------

4:- Tips:-
https://www.infosecmatter.com/bug-bounty-tips/
https://github.com/dwisiswant0/awesome-oneliner-bugbounty
https://github.com/KingOfBugbounty/KingOfBugBountyTips


Good luck.
