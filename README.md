here is the shell contents
-------------------------------------------------------------------------------------------------
#!/bin/sh
IP=${1}
#IP=$(wget -O - -q http://myip.dnsomatic.com/)
CURL=/usr/sbin/curl
URL="https://api.dnspod.com/"
TOKEN_CACHE="/tmp/ddns_token.cache"
DOMAINID_CACHE="/tmp/ddns_domainID.cache"
RECORDID_CACHE="/tmp/ddns_recordID.cache"
EMAIL="your account of the dnspod.com"
PASSWORD="password"
DOMAIN="domain"
SUBDOMAIN="subdomain"
TTL=1

if [ ! -f "$TOKEN_CACHE" ]; then
        $CURL -s POST "$URL"Auth -d "login_email=$EMAIL&login_password=$PASSWORD&format=json" | sed -e 's/[{}]/''/g' | awk -v RS=',"' -F: '/^user_token/{print $2}' | sed -e 's/\"//g' > "$TOKEN_CACHE"
fi
TOKEN=$(cat "$TOKEN_CACHE")

if [ ! -f "$DOMAINID_CACHE" ]; then
        $CURL -s POST "$URL"Domain.List -d "user_token=${TOKEN}&format=json" --trace /tmp/a.txt | grep -o "\"id\":.*,\"name\":\"${DOMAIN}\"" | awk -v RS=',"' -F: '/id/{print $2}' | sed 's/\"//g' > "$DOMAINID_CACHE"
fi
DOMAIN_ID=$(cat "$DOMAINID_CACHE")

if [ ! -f "$RECORDID_CACHE" ]; then
        $CURL -s POST "$URL"Record.List -d "user_token=$TOKEN&format=json&domain_id=$DOMAIN_ID" | grep -o "\"id\":.*,\"name\":\"${SUBDOMAIN}\"" | awk -v RS=',' -F: '/id/{print $NF}' | tail -1 | sed 's/\"//g' > "$RECORDID_CACHE"
fi
RECORD_ID=$(cat "$RECORDID_CACHE")

$CURL -s POST "$URL"Record.Modify -d "user_token=$TOKEN&format=json&domain_id=$DOMAIN_ID&record_id=$RECORD_ID&sub_domain=$SUBDOMAIN&value=$IP&record_type=A&record_line=default&ttl=$TTL" > /dev/null

if [ $? -eq 0 ]; then
        /sbin/ddns_custom_updated 1
else
        /sbin/ddns_custom_updated 0
fi
-------------------------------------------------------------------------------------------------

how to use?
frist you must have a domain,
second you need to register an account from https://www.dnspod.com

and then
login to your route,
click WAN
click DDNS
Enable the DDNS Client
Select the custom
input the hostname
type 1 for the "Forced refresh interval (in days)"
apply
done
