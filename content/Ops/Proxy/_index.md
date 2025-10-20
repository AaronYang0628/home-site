+++
title = "Porxy"
+++

 [ProxyAdmin](https://www.goproxy.win/) is a powerful web console of [snail007/goproxy](https://github.com/snail007/goproxy) .



### Installation
1. Quick Installation

If your VPS is a Linux 64-bit system, you only need to execute the following sentence to complete the automatic installation and configuration.

> [!IMPORTANT]
> All operations require `root` privileges.

```shell
curl -L https://mirrors.host900.com/https://raw.githubusercontent.com/snail007/proxy_admin_free/master/install_auto.sh | bash
```

When the installation completed, configuration directory is `/etc/gpa`. For more detailed usage, please refer to the manual directory above to learn more about the features you want to use.

If the installation fails or your vps is not a linux64-bit system, follow the manual installation steps below.
  
2. Manual Installation

Select the file that is appropriate for your system and download it, [click to download](https://github.com/snail007/proxy_admin_free/releases)

{{< tabs title="install on" >}}
{{% tab title="linux" %}}
```shell
./proxy-admin install
```
{{% /tab %}}
{{% tab title="MacOS" %}}
```shell
./proxy-admin install
```
{{% /tab %}}
{{% tab title="Windows" %}}
```bash
# Run As Administrator 
proxy-admin.exe install
```
{{% /tab %}}
{{< /tabs >}}



### Access Service

After the installation is successful, open the browser to access: `http://127.0.0.1:32080`, the first default account is `root`, the password is `123`.

Configuration file path:

{{< tabs title="configuration file saved at" >}}
{{% tab title="linux" %}}
`/etc/gpa/app.toml`
{{% /tab %}}
{{% tab title="MacOS" %}}
`/etc/gpa/app.toml`
{{% /tab %}}
{{% tab title="Windows" %}}
 `C:\gpa\app.toml`
{{% /tab %}}
{{< /tabs >}}

You can configure the listening port and logging.

## Remove Service

{{< tabs title="Uninstall on" >}}
{{% tab title="linux" %}}
```shell
./proxy-admin uninstall
```
{{% /tab %}}
{{% tab title="MacOS" %}}
```shell
./proxy-admin uninstall
```
{{% /tab %}}
{{% tab title="Windows" %}}
```bash
# Run As Administrator 
proxy-admin.exe uninstall
```
{{% /tab %}}
{{< /tabs >}}


## Service Management

{{< tabs title="Manage Service on" >}}
{{% tab title="linux" %}}
```shell
proxy-admin start

proxy-admin stop

proxy-admin restart

proxy-admin backup     #backup data

proxy-admin restore    #restore data
```
{{% /tab %}}
{{% tab title="MacOS" %}}
```shell
proxy-admin start

proxy-admin stop

proxy-admin restart

proxy-admin backup     #backup data

proxy-admin restore    #restore data
```
{{% /tab %}}
{{% tab title="Windows" %}}
```bash
# Run As Administrator 
c:\
cd gpa
proxy-admin start

proxy-admin stop

proxy-admin restart

proxy-admin backup     #backup data

proxy-admin restore    #restore data
```
{{% /tab %}}
{{< /tabs >}}


## Upgrade Service

{{< tabs title="Upgrade Service on" >}}
{{% tab title="linux" %}}
```shell
proxy-admin update
# proxy-admin update -f
```
{{% /tab %}}
{{% tab title="MacOS" %}}
```shell
proxy-admin update
# proxy-admin update -f
```
{{% /tab %}}
{{% tab title="Windows" %}}
```bash
# Run As Administrator 
c:\
cd gpa
proxy-admin update
# proxy-admin update -f
```
{{% /tab %}}
{{< /tabs >}}

