
+++
title = "PorxyAdmin Ops"
hidden = true
+++


More Infomation, you can check this link: [https://github.com/snail007/proxy_admin_free](https://github.com/snail007/proxy_admin_free)
1. download proxy admin binary
{{< tabs groupid="proxy-binary" style="transparent" >}}
{{% tab title="github" icon="fa-brands fa-github" %}}
```shell
curl -L https://github.com/snail007/proxy_admin_free/blob/master/install_auto.sh | bash  
```
{{% /tab %}}
{{% tab title="NFS" icon="fa-solid fa-hard-drive"%}}
```shell
wget -o /root/proxy-admin_linux-amd64.tar.gz https://github.com/snail007/proxy_admin_free/blob/master/proxy-admin_linux-amd64.tar.gz
```
{{% /tab %}}
{{< /tabs >}}

2. install proxy-admin
{{< tabs groupid="proxy-binary" >}}
{{% tab title="github" icon="fa-brands fa-github" %}}
Just wait, it will install automatically
{{% /tab %}}
{{% tab title="NFS" icon="fa-solid fa-hard-drive"%}}
```shell
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
{{% /tab %}}
{{< /tabs >}}


3. check proxy-admin status
```shell
systemctl status proxyadmin
```