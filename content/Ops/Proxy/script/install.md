```
#!/bin/bash

echo -e ">>> installing ... \n"
#install proxy-admin
tar zxvf /root/proxy-admin_linux-amd64.tar.gz >/dev/null 2>&1
rm -rf /root/proxy-admin_linux-amd64.tar.gz
chmod +x proxy-admin
mkdir -p /usr/local/bin/
cp -f proxy-admin /usr/local/bin/
set +e
cd /usr/local/bin/
./proxy-admin uninstall >/dev/null 2>&1
cp -f /tmp/proxy/proxy-admin /usr/local/bin/
set -e
./proxy-admin install
./proxy-admin start
set +e
systemctl status proxyadmin &
set -e
sleep 2
echo  -e "\n>>> install done, thanks for using snail007/proxy-admin\n"
echo  -e ">>> install path /usr/local/bin/proxy-admin\n"
echo  -e ">>> configuration path /etc/gpa\n"
echo  -e ">>> uninstall just exec : /usr/local/bin/proxy-admin uninstall && rm /etc/gpa\n"
echo  -e ">>> please visit : http://YOUR_IP:32080/ username: root, password: 123\n"

```