# Nginx  

<img src="3630px-Nginx_logo.png">

The Most powerful and highly used web server in the world .
Almost every third website runs on nginx.

## URL of OWASP 

['URL'] ('https://github.com/coreruleset/coreruleset//')

## adding rules in support 

```
 271  cd  /etc/nginx/modsecurity/
  272  git clone https://github.com/SpiderLabs/owasp-modsecurity-crs.git
  273  ls
  274  cd owasp-modsecurity-crs/
  275  ls
  276  ls  rules/

cp crs-setup.conf.example. crs-setup.conf
cp -v rules/REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf.example  rules/REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf

```

## creating a file to add rules

```
vim /etc/nginx/modsecurity/modsecurity_includes.conf
[root@ip-172-31-22-247 modsecurity]# cat  modsecurity_includes.conf 
include modsecurity.conf
include owasp-modsecurity-crs/crs-setup.conf
include owasp-modsecurity-crs/rules/REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf
```

### like you can keep adding rules for the same 

## Now you can add to a particular website  under /etc/nginx/conf.d/website.conf 

```
modsecurity on;
modsecurity_rules_file /etc/nginx/modsecurity/modsecurity_includes.conf;
```


