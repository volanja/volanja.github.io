---
title: "Starting govc"
date: 2021-06-13T19:10:18+09:00
draft: false
categories:
  - VMware
slug: starting-govc
tags:
  - govc
  - govmomi
  - vcsim
---

# VMware govcとは
Go言語で書かれたAPI Client  
https://github.com/vmware/govmomi/blob/master/govc/README.md  

VMware vSphere APIのためのGo言語ライブラリである[govmomi](https://github.com/vmware/govmomi)に含まれる。  
そのほかにgovmomiは以下のソフトウェアを含む。
+ [vcsim](https://github.com/vmware/govmomi/blob/master/vcsim/README.md)  
Go言語で書かれたvSphere Simulator  

# 執筆時点の環境

```console
$ govc version -l
Build Version: 0.25.0
Build Commit: bb0307eb
Build Date: 2021-04-16T16:00:59Z
```

# 設定
## govcの環境変数設定

govcからvCenterやESXi, vcsimにアクセスするには環境変数を設定する。

```
$ govc env
GOVC_USERNAME=root
GOVC_PASSWORD=VMware1!
GOVC_URL=192.168.100.120
```

例えばvcsimにアクセスするにはvcsim起動後に表示されるexportコマンドを実行して、環境変数を設定する。

```
export GOVC_URL=https://user:pass@127.0.0.1:8989/sdk GOVC_SIM_PID=42915
```

# govcのTips

govc実行時に何も指定しないとサーバ証明書の検証エラーが出る。

```
$ govc host.info /DC0/host/DC0_H0
govc: Post "https://127.0.0.1:8989/sdk": x509: certificate signed by unknown authority
```

-kをつけると検証をスキップする。

```
$ govc host.info --help
Usage: govc host.info [OPTIONS]
Options:
  -k=false      Skip verification of server certificate [GOVC_INSECURE]
```

毎回-kを付けるのは手間がかかり忘れがちなので環境変数にオプションを設定する。  
(セキュリティ上はきちんと設定した方が良い。)

```
$ export GOVC_INSECURE=1
```

## govcの詳細表示

govcで取れる情報がすくない、、と思っても`--json`を付けると詳細が出てくる。

### jsonなし
```console
$ govc host.vswitch.info --host=/DC0/host/DC0_H0
Name:             vSwitch0
Portgroup:        VM Network
Pnic:
MTU:              0
Ports:            0
Ports Available:  0
$
```

### jsonあり
```json
$ govc host.vswitch.info  --json --host=/DC0/host/DC0_H0
{
  "Vswitch": [
    {
      "Name": "vSwitch0",
      "Key": "",
      "NumPorts": 0,
      "NumPortsAvailable": 0,
      "Mtu": 0,
      "Portgroup": [
        "VM Network"
      ],
      "Pnic": null,
      "Spec": {
        "NumPorts": 0,
        "Bridge": null,
        "Policy": null,
        "Mtu": 0
      }
    }
  ]
}
```

# 参考
## govc - info
govcのinfo関連の表示オプションを抜粋

```
$ govc --help 2>&1 |grep info
  cluster.override.info
  cluster.rule.info
  datacenter.info
  datastore.cluster.info
  datastore.disk.info
  datastore.info
  device.info
  dvs.portgroup.info
  extension.info
  fields.info
  folder.info
  host.autostart.info
  host.cert.info
  host.date.info
  host.info
  host.portgroup.info
  host.storage.info
  host.vnic.info
  host.vswitch.info
  library.info
  library.subscriber.info
  metric.info
  metric.interval.info
  pool.info
  storage.policy.info
  tags.category.info
  tags.info
  vcsa.log.forwarding.info
  vm.info
  vm.option.info
```

## govc - find 
pathを参照するコマンド。

```
$ govc find --help
Usage: govc find [OPTIONS] [ROOT] [KEY VAL]...

Find managed objects.

ROOT can be an inventory path or ManagedObjectReference.
ROOT defaults to '.', an alias for the root folder or DC if set.

Optional KEY VAL pairs can be used to filter results against object instance properties.
Use the govc 'object.collect' command to view possible object property keys.

The '-type' flag value can be a managed entity type or one of the following aliases:

  a    VirtualApp
  c    ClusterComputeResource
  d    Datacenter
  f    Folder
  g    DistributedVirtualPortgroup
  h    HostSystem
  m    VirtualMachine
  n    Network
  o    OpaqueNetwork
  p    ResourcePool
  r    ComputeResource
  s    Datastore
  w    DistributedVirtualSwitch

Examples:
  govc find
  govc find -l / # include object type in output
  govc find /dc1 -type c
  govc find vm -name my-vm-*
  govc find . -type n
  govc find -p /folder-a/dc-1/host/folder-b/cluster-a -type Datacenter # prints /folder-a/dc-1
  govc find . -type m -runtime.powerState poweredOn
  govc find . -type m -datastore $(govc find -i datastore -name vsanDatastore)
  govc find . -type s -summary.type vsan
  govc find . -type s -customValue *:prod # Key:Value
  govc find . -type h -hardware.cpuInfo.numCpuCores 16

Options:
  -cert=                 Certificate [GOVC_CERTIFICATE]
  -dc=                   Datacenter [GOVC_DATACENTER]
  -debug=false           Store debug logs [GOVC_DEBUG]
  -dump=false            Enable Go output
  -i=false               Print the managed object reference
  -json=false            Enable JSON output
  -k=false               Skip verification of server certificate [GOVC_INSECURE]
  -key=                  Private key [GOVC_PRIVATE_KEY]
  -l=false               Long listing format
  -maxdepth=-1           Max depth
  -name=*                Resource name
  -p=false               Find parent objects
  -persist-session=true  Persist session to disk [GOVC_PERSIST_SESSION]
  -tls-ca-certs=         TLS CA certificates file [GOVC_TLS_CA_CERTS]
  -tls-known-hosts=      TLS known hosts file [GOVC_TLS_KNOWN_HOSTS]
  -trace=false           Write SOAP/REST traffic to stderr
  -type=[]               Resource type
  -u=                    ESX or vCenter URL [GOVC_URL]
  -verbose=false         Write request/response data to stderr
  -vim-namespace=vim25   Vim namespace [GOVC_VIM_NAMESPACE]
  -vim-version=7.0       Vim version [GOVC_VIM_VERSION]
  -xml=false             Enable XML output
```
