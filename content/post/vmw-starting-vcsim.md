---
title: "Starting vcsim"
date: 2021-06-13T19:10:18+09:00
draft: false
categories:
  - VMware
slug: vmw-starting-vcsim
tags:
  - govc
  - govmomi
  - vcsim
---

# VMware vcsimとは
Go言語で書かれたvSphere Simulator  
https://github.com/vmware/govmomi/blob/master/vcsim/README.md  

VMware vSphere APIのためのGo言語ライブラリである[govmomi](https://github.com/vmware/govmomi)に含まれる。  
そのほかにgovmomiは以下のソフトウェアを含む。
+ [govc](https://github.com/vmware/govmomi/blob/master/govc/README.md)  
Go言語で書かれたAPI Client  

# 利用用途
+ govcのテスト用シミュレーター
+ Ansible moduleのテスト用シミュレーター
など

# 執筆時点の環境

```console
$ vcsim version
Build Version: 0.25.0
Build Commit: bb0307eb
Build Date: 2021-04-16T16:00:59Z
```

# vcsimの起動
## デフォルト

デフォルトではデータセンター1個、ホスト3台、仮想マシン4台の環境を構築する。

```
$ vcsim
export GOVC_URL=https://user:pass@127.0.0.1:8989/sdk GOVC_SIM_PID=42915
```

## ESXi 1台のみ

`-esx`を追加することでESXi 1台のみも構築できる。

```
$ vcsim -esx
export GOVC_URL=https://user:pass@127.0.0.1:8989/sdk GOVC_SIM_PID=42917
```
# govcからのアクセス

vcsim起動後に表示される次のコマンドを実行する。
```
export GOVC_URL=https://user:pass@127.0.0.1:8989/sdk GOVC_SIM_PID=42917
```

詳細は[Starting govc](../starting-govc)を参照のこと。

# 参考
## vcsim help

```
$ vcsim --help
Usage of /Users/volanja/local/go/bin/vcsim:
  -E string
    	Output vcsim variables to the given fifo or stdout (default "-")
  -api-version string
    	API version (default "6.5")
  -app int
    	Number of virtual apps per compute resource
  -autostart
    	Autostart model created VMs (default true)
  -cluster int
    	Number of clusters (default 1)
  -dc int
    	Number of datacenters (default 1)
  -delay int
    	Method response delay across all methods
  -delay-jitter float
    	Delay jitter coefficient of variation (tip: 0.5 is a good starting value)
  -ds int
    	Number of local datastores (default 1)
  -esx
    	Simulate standalone ESX
  -folder int
    	Number of folders
  -host int
    	Number of hosts per cluster (default 3)
  -l string
    	Listen address for vcsim (default "127.0.0.1:8989")
  -load string
    	Load model from directory
  -method-delay string
    	Delay per method on the form 'method1:delay1,method2:delay2...'
  -nsx int
    	Number of NSX backed opaque networks
  -password string
    	Login password for vcsim (any password allowed by default)
  -pg int
    	Number of port groups (default 1)
  -pg-nsx int
    	Number of NSX backed port groups
  -pod int
    	Number of storage pods per datacenter
  -pool int
    	Number of resource pools per compute resource
  -standalone-host int
    	Number of standalone hosts (default 1)
  -stdinexit
    	Press any key to exit
  -tls
    	Enable TLS (default true)
  -tlscert string
    	Path to TLS certificate file
  -tlskey string
    	Path to TLS key file
  -trace
    	Trace SOAP to -trace-file
  -trace-file string
    	Trace output file (defaults to stderr)
  -tunnel int
    	SDK tunnel port (default -1)
  -username string
    	Login username for vcsim (any username allowed by default)
  -vm int
    	Number of virtual machines per resource pool (default 2)
```

