<!-- 
Generates all PKI assets necessary to establish the control plane

### Synopsis
 -->
生成所有建立控制平面所需的 PKI 数据

### 概要
<!-- 
Generates a self-signed CA to provision identities for each component in the cluster (including nodes) and client certificates to be used by various components. 

If a given certificate and private key pair both exist, kubeadm skips the generation step and existing files will be used. 

Alpha Disclaimer: this command is currently alpha. -->

生成自签名 CA，为集群中的每个组件（包括节点）提供标识，并为各种组件提供所需的客户端证书。

如果某指定的证书和私钥对都已经存在，kubeadm 跳过生成步骤而使用现有的文件。

Alpha 免责声明：此命令处于 Alpha 阶段。

```
kubeadm alpha phase certs all [flags]
```
<!-- 
### Examples

```
  # Creates all PKI assets necessary to establish the control plane,
  # functionally equivalent to what generated by kubeadm init.
  kubeadm alpha phase certs all
  
  # Creates all PKI assets using options read from a configuration file.
  kubeadm alpha phase certs all --config masterconfiguration.yaml
```
 -->

### 例子

```
  # 生成所有建立控制平面所需的 PKI 数据，
  # 功能等同于 kubeadm init 所生成的。
  kubeadm alpha phase certs all
  
  # 使用配置文件的选项创建所有 PKI 数据。
  kubeadm alpha phase certs all --config masterconfiguration.yaml
```

<!-- ### Options

<table style="width: 100%; table-layout: fixed;">
  <colgroup>
    <col span="1" style="width: 10px;" />
    <col span="1" />
  </colgroup>
  <tbody>

    <tr>
      <td colspan="2">--apiserver-advertise-address string</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">The IP address the API server is accessible on, to use for the API server serving cert</td>
    </tr>

    <tr>
      <td colspan="2">--apiserver-cert-extra-sans stringSlice</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">Optional extra altnames to use for the API server serving cert. Can be both IP addresses and DNS names</td>
    </tr>

    <tr>
      <td colspan="2">--cert-dir string&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Default: "/etc/kubernetes/pki"</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">The path where to save the certificates</td>
    </tr>

    <tr>
      <td colspan="2">--config string</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">Path to kubeadm config file (WARNING: Usage of a configuration file is experimental)</td>
    </tr>

    <tr>
      <td colspan="2">-h, --help</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">help for all</td>
    </tr>

    <tr>
      <td colspan="2">--service-cidr string&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Default: "10.96.0.0/12"</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">Alternative range of IP address for service VIPs, from which derives the internal API server VIP that will be added to the API Server serving cert</td>
    </tr>

    <tr>
      <td colspan="2">--service-dns-domain string&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Default: "cluster.local"</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">Alternative domain for services, to use for the API server serving cert</td>
    </tr>

  </tbody>
</table>
 -->
### 选择项

<table style="width: 100%; table-layout: fixed;">
  <colgroup>
    <col span="1" style="width: 10px;" />
    <col span="1" />
  </colgroup>
  <tbody>

    <tr>
      <td colspan="2">--apiserver-advertise-address string</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">可访问的 API 服务器 IP 地址，用于 API 服务器的服务证书</td>
    </tr>

    <tr>
      <td colspan="2">--apiserver-cert-extra-sans stringSlice</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">应用于 API 服务器服务证书的可选的额外的别名。可以使用 IP 地址和 dns 名</td>
    </tr>

    <tr>
      <td colspan="2">--cert-dir string&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;默认值： "/etc/kubernetes/pki"</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">认证存储路径</td>
    </tr>

    <tr>
      <td colspan="2">--config string</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">kubeadm 配置文件路径（警告: 配置文件的使用处于实验阶段）</td>
    </tr>

    <tr>
      <td colspan="2">-h, --help</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">帮助</td>
    </tr>

    <tr>
      <td colspan="2">--service-cidr string&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;默认值： "10.96.0.0/12"</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">可替换的服务 VIP 的 IP 地址范围，其中的内部 API 服务器的 VIP 将加到 API 服务器的服务证书 </td>
    </tr>

    <tr>
      <td colspan="2">--service-dns-domain string&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;默认值： "cluster.local"</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">可替换的服务域，用于 API 服务器的服务证书</td>
    </tr>

  </tbody>
</table>


<!-- ### Options inherited from parent commands

<table style="width: 100%; table-layout: fixed;">
  <colgroup>
    <col span="1" style="width: 10px;" />
    <col span="1" />
  </colgroup>
  <tbody>

    <tr>
      <td colspan="2">--rootfs string</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">[EXPERIMENTAL] The path to the 'real' host root filesystem.</td>
    </tr>

  </tbody>
</table> -->

### 继承于父命令的选择项

<table style="width: 100%; table-layout: fixed;">
  <colgroup>
    <col span="1" style="width: 10px;" />
    <col span="1" />
  </colgroup>
  <tbody>

    <tr>
      <td colspan="2">--rootfs string</td>
    </tr>
    <tr>
      <td></td><td style="line-height: 130%; word-wrap: break-word;">[实验性] 主机根目录文件系统“真实”路径。</td>
    </tr>

  </tbody>
</table>
