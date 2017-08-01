this is a ddns update script of dnspod.com for the asuswrt-merlin

how to use?

at frist, you must have a domain

second you need to register an account from https://www.dnspod.com and change the domain ns record to dnspod.com

and then

login to your route via ssh

copy the ddns-start to /jffs/scripts/ddns-start

login to your route

click WAN

click DDNS

Enable the DDNS Client

Select the custom

input the hostname

type 1 for the "Forced refresh interval (in days)"

apply

done
