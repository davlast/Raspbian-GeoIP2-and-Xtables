# Raspbian-GeoIP2-and-Xtables
Block IP ranges with geoip2 on Raspbian

1) Install the xtables-addons
sudo apt-get install raspberrypi-kernel-headers

cd /opt/
wget https://netix.dl.sourceforge.net/project/xtables-addons/Xtables-addons/xtables-addons-3.3.tar.xz

tar xf xtables-addons-3.3.tar.xz

cd xtables-addons-3.3/

./configure

make

make install



2) Create a file
nano /usr/local/bin/installGeoIP.sh

```
#!/bin/bash
set -euo pipefail

set +e
if ! dpkg -l xtables-addons-common >/dev/null ; then
        apt install xtables-addons-common
fi
if ! dpkg -l libtext-csv-xs-perl >/dev/null ; then
        apt install libtext-csv-xs-perl
fi
set -e

if [ ! -d /usr/share/xt_geoip ]; then
        mkdir /usr/share/xt_geoip
fi

geotmpdir=$(mktemp -d)
OLDPWD="${PWD}"
cd "${geotmpdir}"
/usr/lib/xtables-addons/xt_geoip_dl
mv * geoip
cd geoip
/usr/lib/xtables-addons/xt_geoip_build -D /usr/share/xt_geoip .*.csv
cd "${OLDPWD}"
rm -r "${geotmpdir}"
exit 0
```

3) Make this file executable and run
chmod +x /usr/local/bin/installGeoIP.sh
/usr/local/bin/installGeoIP.sh

4) Add iptables rules
Example:

iptables -A INPUT -m geoip --src-cc US,NZ -m conntrack --ctstate NEW -j ACCEPT



ISSUES 

run depmod -a
