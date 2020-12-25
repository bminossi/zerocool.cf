#
# ____                 _                _             
#/ ___| _   _ _ __    | |__   __ _  ___| | _____ _ __ 
#\___ \| | | | '_ \   | '_ \ / _` |/ __| |/ / _ \ '__|
# ___) | |_| | |_) |  | | | | (_| | (__|   <  __/ |   
#|____/ \__,_| .__( ) |_| |_|\__,_|\___|_|\_\___|_|   
#            |_|  |/                                  
#

<a href="https://twitter.com/zeroc00I?ref_src=twsrc%5Etfw" class="twitter-follow-button" data-show-count="false">Hit me on @zeroc00I</a><script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

---
## Manual Filtering alive hosts

xargs -P 500 -a lista -I@ sh -c 'dig @ | grep -q NOERROR 1>/dev/null && echo | echo @;'

xargs -P 500 -a lista -I@ sh -c 'nc -w1 -z -v @ 80 2>/dev/null && echo @'

## Extract Only Http using gospider (required anew instalation)

xargs -P 500 -a hosts -I@ sh -c 'nc -w1 -z -v @ 443 2>/dev/null && echo @' | xargs -I@ -P10 sh -c 'gospider -a -s "https://@" -d 2 | grep -Eo "(http|https)://[^/\"]+" | anew httponly'

## Extract only JS using gospider (required anew instalation)

xargs -P 500 -a hosts -I@ sh -c 'nc -w1 -z -v @ 8443 2>/dev/null && echo @' | xargs -I@ -P10 sh -c 'gospider -a -s "https://@" -d 2 | grep -Eo "(http|https)://[^/\"].*.js+" | sed "s#\] \- #\n#g" | anew jsonly'

## Extract only using openssl (required anew installation and openssl)
xargs -P100 -a hosts -I@ sh -c 'ip=$(dig +short @);[ ! -z "$ip" ] && printf "GET / HTTP/1.1\r\nHost: $ip\r\n\r\n" | timeout 2 openssl s_client -connect $ip:443 2>/dev/null' | sed 's# \|/\|=#\n#g' | grep paypal | anew

## Geting domains using reverse DNS

### Command

xargs -P 500 -a hosts -I@ sh -c 'dig @' 2>/dev/null | awk -F'<<>>' '{print $3}' | xargs -n1 | tee -a hosts

## Getting domains wich resolve to some IP (avoiding false positives)

### Command

cat hostUnicos.txt | while read line;do xargs -P 500 -a /SecList/subbrute/names_small.txt -I@ sh -c "dig +noidnout +noidnin +short @.$line | grep -c '^' 1>/dev/null && echo @.$line | tee -a hostsDig";done
