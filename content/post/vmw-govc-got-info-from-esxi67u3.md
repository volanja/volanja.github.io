---
title: "govcでESXi 6.7u3から情報取得する。"
date: 2021-06-13T21:32:02+09:00
draft: true
categories:
  - VMware
slug: vmw-govc-got-info-from-esxi67u3
tags:
  - govc
---

# 前提

ESXi 6.7u3をVirtual Boxにて以下の通り構築した。

+ ESXi 6.7u3 (Build:14320388)
+ Storage
  + datastore1
+ pNIC
  + vmnic0
  + vmnic1
+ vSwtich
  + vSwitch0
    + Uplink: vmnic0
  + vSwitch1
    + Uplink: vmnic1
+ PortGroup
  + Management Network
    + vSwitch: vSwitch0
  + VM Network
    + vSwitch: vSwitch0
  + VM Network2
    + vSwitch: vSwitch1
+ Virtual Machine
  + Windows Server 2019
    + PortGoup: VM Network
    + OS is not install. Virtual Machine only.
  + CentOS8
    + PortGoup: VM Network2
    + OS is not install. Virtual Machine only.

# コマンド実行メモ
## 環境変数

```
$ govc env
GOVC_USERNAME=root
GOVC_PASSWORD=VMware1!
GOVC_URL=192.168.100.120
GOVC_INSECURE=1
```

## Basic information

```
$ govc about
FullName:     VMware ESXi 6.7.0 build-14320388
Name:         VMware ESXi
Vendor:       VMware, Inc.
Version:      6.7.0
Build:        14320388
OS type:      vmnix-x86
API type:     HostAgent
API version:  6.7.3
Product ID:   embeddedEsx
UUID:         
```

```json
$ govc about --json
{
  "About": {
    "Name": "VMware ESXi",
    "FullName": "VMware ESXi 6.7.0 build-14320388",
    "Vendor": "VMware, Inc.",
    "Version": "6.7.0",
    "Build": "14320388",
    "LocaleVersion": "INTL",
    "LocaleBuild": "000",
    "OsType": "vmnix-x86",
    "ProductLineId": "embeddedEsx",
    "ApiType": "HostAgent",
    "ApiVersion": "6.7.3",
    "InstanceUuid": "",
    "LicenseProductName": "VMware ESX Server",
    "LicenseProductVersion": "6.0"
  }
}
```

```
$ govc find -l
Folder           /
Datacenter       /ha-datacenter
Folder           /ha-datacenter/vm
Folder           /ha-datacenter/host
Folder           /ha-datacenter/datastore
Folder           /ha-datacenter/network
Network          /ha-datacenter/network/VM Network
Network          /ha-datacenter/network/VM Network2
Datastore        /ha-datacenter/datastore/datastore1
ComputeResource  /ha-datacenter/host/localhost.
ResourcePool     /ha-datacenter/host/localhost./Resources
HostSystem       /ha-datacenter/host/localhost./localhost.localdomain
VirtualMachine   /ha-datacenter/vm/WindowsServer2019
VirtualMachine   /ha-datacenter/vm/CentOS8
```

## Datacenter

```
$ govc datacenter.info
Name:                ha-datacenter
  Path:              /ha-datacenter
  Hosts:             1
  Clusters:          0
  Virtual Machines:  2
  Networks:          2
  Datastores:        1
```

```json
$ govc datacenter.info --json
{
  "Datacenters": [
    {
      "Self": {
        "Type": "Datacenter",
        "Value": "ha-datacenter"
      },
      "Value": null,
      "AvailableField": null,
      "Parent": {
        "Type": "Folder",
        "Value": "ha-folder-root"
      },
      "CustomValue": null,
      "OverallStatus": "green",
      "ConfigStatus": "green",
      "ConfigIssue": null,
      "EffectiveRole": [
        -1
      ],
      "Permission": null,
      "Name": "ha-datacenter",
      "DisabledMethod": null,
      "RecentTask": null,
      "DeclaredAlarmState": null,
      "TriggeredAlarmState": null,
      "AlarmActionsEnabled": null,
      "Tag": null,
      "VmFolder": {
        "Type": "Folder",
        "Value": "ha-folder-vm"
      },
      "HostFolder": {
        "Type": "Folder",
        "Value": "ha-folder-host"
      },
      "DatastoreFolder": {
        "Type": "Folder",
        "Value": "ha-folder-datastore"
      },
      "NetworkFolder": {
        "Type": "Folder",
        "Value": "ha-folder-network"
      },
      "Datastore": [
        {
          "Type": "Datastore",
          "Value": "60c4dd35-141ece76-d699-0800272601f9"
        }
      ],
      "Network": [
        {
          "Type": "Network",
          "Value": "HaNetwork-VM Network"
        },
        {
          "Type": "Network",
          "Value": "HaNetwork-VM Network2"
        }
      ],
      "Configuration": {
        "DefaultHardwareVersionKey": ""
      }
    }
  ]
}
```

## Cluster

```
$ govc cluster.override.info
```

```
$ govc cluster.rule.info
```

## Datastore

```
$ govc datastore.info
Name:        datastore1
  Path:      /ha-datacenter/datastore/datastore1
  Type:      VMFS
  URL:       /vmfs/volumes/60c4dd35-141ece76-d699-0800272601f9
  Capacity:  2.5 GB
  Free:      1.1 GB
```

```json
$ govc datastore.info --json
{
  "Datastores": [
    {
      "Self": {
        "Type": "Datastore",
        "Value": "60c4dd35-141ece76-d699-0800272601f9"
      },
      "Value": null,
      "AvailableField": null,
      "Parent": {
        "Type": "Folder",
        "Value": "ha-folder-datastore"
      },
      "CustomValue": null,
      "OverallStatus": "green",
      "ConfigStatus": "green",
      "ConfigIssue": null,
      "EffectiveRole": [
        -1
      ],
      "Permission": null,
      "Name": "datastore1",
      "DisabledMethod": null,
      "RecentTask": null,
      "DeclaredAlarmState": null,
      "TriggeredAlarmState": null,
      "AlarmActionsEnabled": null,
      "Tag": null,
      "Info": {
        "Name": "datastore1",
        "Url": "/vmfs/volumes/60c4dd35-141ece76-d699-0800272601f9",
        "FreeSpace": 1175453696,
        "MaxFileSize": 70368744177664,
        "MaxVirtualDiskCapacity": 68169720922112,
        "MaxMemoryFileSize": 70368744177664,
        "Timestamp": "2021-06-13T11:55:55.000725Z",
        "ContainerId": "",
        "AliasOf": "",
        "MaxPhysicalRDMFileSize": 70368744177664,
        "MaxVirtualRDMFileSize": 68169720922112,
        "Vmfs": {
          "Type": "VMFS",
          "Name": "datastore1",
          "Capacity": 2684354560,
          "BlockSizeMb": 1,
          "BlockSize": 1024,
          "UnmapGranularity": 1024,
          "UnmapPriority": "low",
          "UnmapBandwidthSpec": null,
          "MaxBlocks": 63963136,
          "MajorVersion": 6,
          "Version": "6.82",
          "Uuid": "60c4dd35-141ece76-d699-0800272601f9",
          "Extent": [
            {
              "DiskName": "t10.ATA_____VBOX_HARDDISK___________________________VB22cd9e3f2Df90775ba_",
              "Partition": 3
            }
          ],
          "VmfsUpgradable": false,
          "ForceMountedInfo": null,
          "Ssd": false,
          "Local": true,
          "ScsiDiskType": ""
        }
      },
      "Summary": {
        "Datastore": {
          "Type": "Datastore",
          "Value": "60c4dd35-141ece76-d699-0800272601f9"
        },
        "Name": "datastore1",
        "Url": "/vmfs/volumes/60c4dd35-141ece76-d699-0800272601f9",
        "Capacity": 2684354560,
        "FreeSpace": 1175453696,
        "Uncommitted": 0,
        "Accessible": true,
        "MultipleHostAccess": null,
        "Type": "VMFS",
        "MaintenanceMode": ""
      },
      "Host": [
        {
          "Key": {
            "Type": "HostSystem",
            "Value": "ha-host"
          },
          "MountInfo": {
            "Path": "/vmfs/volumes/60c4dd35-141ece76-d699-0800272601f9",
            "AccessMode": "readWrite",
            "Mounted": true,
            "Accessible": true,
            "InaccessibleReason": ""
          }
        }
      ],
      "Vm": [
        {
          "Type": "VirtualMachine",
          "Value": "1"
        },
        {
          "Type": "VirtualMachine",
          "Value": "2"
        }
      ],
      "Browser": {
        "Type": "HostDatastoreBrowser",
        "Value": "60c4dd35-141ece76-d699-0800272601f9-datastorebrowser"
      },
      "Capability": {
        "DirectoryHierarchySupported": true,
        "RawDiskMappingsSupported": true,
        "PerFileThinProvisioningSupported": true,
        "StorageIORMSupported": true,
        "NativeSnapshotSupported": false,
        "TopLevelDirectoryCreateSupported": true,
        "SeSparseSupported": true,
        "VmfsSparseSupported": false,
        "VsanSparseSupported": false,
        "UpitSupported": false,
        "VmdkExpandSupported": true,
        "ClusteredVmdkSupported": null
      },
      "IormConfiguration": {
        "Enabled": false,
        "CongestionThresholdMode": "",
        "CongestionThreshold": 232,
        "PercentOfPeakThroughput": 0,
        "StatsCollectionEnabled": false,
        "ReservationEnabled": true,
        "StatsAggregationDisabled": null,
        "ReservableIopsThreshold": 0
      }
    }
  ]
}
```

```
$ govc datastore.disk.info CentOS8/CentOS8.vmdk
Name:      CentOS8/CentOS8.vmdk
  Type:    thin
  Parent:  
```

```json
$ govc datastore.disk.info --json CentOS8/CentOS8.vmdk
[
  {
    "Name": "[datastore1] CentOS8/CentOS8.vmdk",
    "DiskType": "thin",
    "Parent": ""
  }
]
```

```
$ govc datastore.disk.info WindowsServer2019/WindowsServer2019.vmdk
Name:      WindowsServer2019/WindowsServer2019.vmdk
  Type:    thin
  Parent:  
```

```json
$ govc datastore.disk.info --json WindowsServer2019/WindowsServer2019.vmdk
[
  {
    "Name": "[datastore1] WindowsServer2019/WindowsServer2019.vmdk",
    "DiskType": "thin",
    "Parent": ""
  }
]
```

```
$ govc datastore.cluster.info
```

## Folder

```
$ govc folder.info
Name:        root
  Path:      /
  Types:     Datacenter
  Children:  1
```

```json
$ govc folder.info --json
{
  "Folders": [
    {
      "Self": {
        "Type": "Folder",
        "Value": "ha-folder-root"
      },
      "Value": null,
      "AvailableField": null,
      "Parent": null,
      "CustomValue": null,
      "OverallStatus": "green",
      "ConfigStatus": "green",
      "ConfigIssue": null,
      "EffectiveRole": [
        -1
      ],
      "Permission": [
        {
          "Entity": {
            "Type": "Folder",
            "Value": "ha-folder-root"
          },
          "Principal": "vpxuser",
          "Group": false,
          "RoleId": -1,
          "Propagate": true
        },
        {
          "Entity": {
            "Type": "Folder",
            "Value": "ha-folder-root"
          },
          "Principal": "dcui",
          "Group": false,
          "RoleId": -1,
          "Propagate": true
        },
        {
          "Entity": {
            "Type": "Folder",
            "Value": "ha-folder-root"
          },
          "Principal": "root",
          "Group": false,
          "RoleId": -1,
          "Propagate": true
        }
      ],
      "Name": "root",
      "DisabledMethod": null,
      "RecentTask": null,
      "DeclaredAlarmState": null,
      "TriggeredAlarmState": null,
      "AlarmActionsEnabled": null,
      "Tag": null,
      "ChildType": [
        "Datacenter"
      ],
      "ChildEntity": [
        {
          "Type": "Datacenter",
          "Value": "ha-datacenter"
        }
      ],
      "Namespace": null
    }
  ]
}
```

## Host

```
$ govc host.info
Name:              localhost.localdomain
  Path:            /ha-datacenter/host/localhost./localhost.localdomain
  Manufacturer:    innotek GmbH
  Logical CPUs:    2 CPUs @ 2304MHz
  Processor type:  Intel(R) Core(TM) i5-8259U CPU @ 2.30GHz
  CPU usage:       331 MHz (7.2%)
  Memory:          4091MB
  Memory usage:    1142 MB (27.9%)
  Boot time:       2021-06-13 00:54:09.052747 +0000 UTC
  State:           connected
```

```json
$ govc host.info --json
{
  "HostSystems": [
    {
      "Self": {
        "Type": "HostSystem",
        "Value": "ha-host"
      },
      "Value": null,
      "AvailableField": null,
      "Parent": {
        "Type": "ComputeResource",
        "Value": "ha-compute-res"
      },
      "CustomValue": null,
      "OverallStatus": "green",
      "ConfigStatus": "yellow",
      "ConfigIssue": [
        {
          "Key": 1,
          "ChainId": 0,
          "CreatedTime": "1970-01-01T00:00:00Z",
          "UserName": "",
          "Datacenter": null,
          "ComputeResource": null,
          "Host": {
            "Name": "localhost.localdomain",
            "Host": {
              "Type": "HostSystem",
              "Value": "ha-host"
            }
          },
          "Vm": null,
          "Ds": null,
          "Net": null,
          "Dvs": null,
          "FullFormattedMessage": "ESXi Shell for the host localhost.localdomain has been enabled",
          "ChangeTag": ""
        },
        {
          "Key": 2,
          "ChainId": 0,
          "CreatedTime": "1970-01-01T00:00:00Z",
          "UserName": "",
          "Datacenter": null,
          "ComputeResource": null,
          "Host": {
            "Name": "localhost.localdomain",
            "Host": {
              "Type": "HostSystem",
              "Value": "ha-host"
            }
          },
          "Vm": null,
          "Ds": null,
          "Net": null,
          "Dvs": null,
          "FullFormattedMessage": "SSH for the host localhost.localdomain has been enabled",
          "ChangeTag": ""
        }
      ],
      "EffectiveRole": [
        -1
      ],
      "Permission": null,
      "Name": "localhost.localdomain",
      "DisabledMethod": [
        "DisconnectHost_Task",
        "ReconnectHost_Task",
        "ReconfigureHostForDAS_Task",
        "PowerUpHostFromStandBy_Task",
        "ExitMaintenanceMode_Task"
      ],
      "RecentTask": [
        {
          "Type": "Task",
          "Value": "haTask-ha-host-vim.host.ImageConfigManager.installDate-2341573439"
        }
      ],
      "DeclaredAlarmState": null,
      "TriggeredAlarmState": null,
      "AlarmActionsEnabled": null,
      "Tag": null,
      "Runtime": {
        "ConnectionState": "connected",
        "PowerState": "poweredOn",
        "StandbyMode": "",
        "InMaintenanceMode": false,
        "InQuarantineMode": null,
        "BootTime": "2021-06-13T00:54:09.052747Z",
        "HealthSystemRuntime": {
          "SystemHealthInfo": {
            "NumericSensorInfo": null
          },
          "HardwareStatusInfo": {
            "MemoryStatusInfo": null,
            "CpuStatusInfo": null,
            "StorageStatusInfo": null
          }
        },
        "DasHostState": null,
        "TpmPcrValues": null,
        "VsanRuntimeInfo": {
          "MembershipList": null,
          "DiskIssues": null,
          "AccessGenNo": 0
        },
        "NetworkRuntimeInfo": {
          "NetStackInstanceRuntimeInfo": [
            {
              "NetStackInstanceKey": "defaultTcpipStack",
              "State": "active",
              "VmknicKeys": [
                "vmk0"
              ],
              "MaxNumberOfConnections": 11000,
              "CurrentIpV6Enabled": true
            }
          ],
          "NetworkResourceRuntime": null
        },
        "VFlashResourceRuntimeInfo": null,
        "HostMaxVirtualDiskCapacity": 68169720922112,
        "CryptoState": "incapable",
        "CryptoKeyId": null
      },
      "Summary": {
        "Host": {
          "Type": "HostSystem",
          "Value": "ha-host"
        },
        "Hardware": {
          "Vendor": "innotek GmbH",
          "Model": "VirtualBox",
          "Uuid": "f1e79e59-c826-437c-b8f0-b92f8ba16e23",
          "OtherIdentifyingInfo": [
            {
              "IdentifierValue": "",
              "IdentifierType": {
                "Label": "Asset Tag",
                "Summary": "Asset tag of the system",
                "Key": "AssetTag"
              }
            },
            {
              "IdentifierValue": "0",
              "IdentifierType": {
                "Label": "Service tag",
                "Summary": "Service tag of the system",
                "Key": "ServiceTag"
              }
            },
            {
              "IdentifierValue": "",
              "IdentifierType": {
                "Label": "Enclosure serial number tag",
                "Summary": "Enclosure serial number tag of the system",
                "Key": "EnclosureSerialNumberTag"
              }
            },
            {
              "IdentifierValue": "0",
              "IdentifierType": {
                "Label": "Serial number tag",
                "Summary": "Serial number tag of the system",
                "Key": "SerialNumberTag"
              }
            },
            {
              "IdentifierValue": "vboxVer_6.0.8",
              "IdentifierType": {
                "Label": "OEM specific string",
                "Summary": "OEM specific string",
                "Key": "OemSpecificString"
              }
            },
            {
              "IdentifierValue": "vboxRev_130520",
              "IdentifierType": {
                "Label": "OEM specific string",
                "Summary": "OEM specific string",
                "Key": "OemSpecificString"
              }
            }
          ],
          "MemorySize": 4290236416,
          "CpuModel": "Intel(R) Core(TM) i5-8259U CPU @ 2.30GHz",
          "CpuMhz": 2304,
          "NumCpuPkgs": 1,
          "NumCpuCores": 2,
          "NumCpuThreads": 2,
          "NumNics": 2,
          "NumHBAs": 2
        },
        "Runtime": {
          "ConnectionState": "connected",
          "PowerState": "poweredOn",
          "StandbyMode": "",
          "InMaintenanceMode": false,
          "InQuarantineMode": null,
          "BootTime": "2021-06-13T00:54:09.052747Z",
          "HealthSystemRuntime": {
            "SystemHealthInfo": {
              "NumericSensorInfo": null
            },
            "HardwareStatusInfo": {
              "MemoryStatusInfo": null,
              "CpuStatusInfo": null,
              "StorageStatusInfo": null
            }
          },
          "DasHostState": null,
          "TpmPcrValues": null,
          "VsanRuntimeInfo": {
            "MembershipList": null,
            "DiskIssues": null,
            "AccessGenNo": 0
          },
          "NetworkRuntimeInfo": {
            "NetStackInstanceRuntimeInfo": [
              {
                "NetStackInstanceKey": "defaultTcpipStack",
                "State": "active",
                "VmknicKeys": [
                  "vmk0"
                ],
                "MaxNumberOfConnections": 11000,
                "CurrentIpV6Enabled": true
              }
            ],
            "NetworkResourceRuntime": null
          },
          "VFlashResourceRuntimeInfo": null,
          "HostMaxVirtualDiskCapacity": 68169720922112,
          "CryptoState": "incapable",
          "CryptoKeyId": null
        },
        "Config": {
          "Name": "localhost.localdomain",
          "Port": 902,
          "SslThumbprint": "",
          "Product": {
            "Name": "VMware ESXi",
            "FullName": "VMware ESXi 6.7.0 build-14320388",
            "Vendor": "VMware, Inc.",
            "Version": "6.7.0",
            "Build": "14320388",
            "LocaleVersion": "INTL",
            "LocaleBuild": "000",
            "OsType": "vmnix-x86",
            "ProductLineId": "embeddedEsx",
            "ApiType": "HostAgent",
            "ApiVersion": "6.7.3",
            "InstanceUuid": "",
            "LicenseProductName": "VMware ESX Server",
            "LicenseProductVersion": "6.0"
          },
          "VmotionEnabled": false,
          "FaultToleranceEnabled": true,
          "FeatureVersion": null,
          "AgentVmDatastore": null,
          "AgentVmNetwork": null
        },
        "QuickStats": {
          "OverallCpuUsage": 331,
          "OverallMemoryUsage": 1142,
          "DistributedCpuFairness": 0,
          "DistributedMemoryFairness": 0,
          "AvailablePMemCapacity": 0,
          "Uptime": 40110
        },
        "OverallStatus": "gray",
        "RebootRequired": false,
        "CustomValue": null,
        "ManagementServerIp": "",
        "MaxEVCModeKey": "",
        "CurrentEVCModeKey": "",
        "CurrentEVCGraphicsModeKey": "",
        "Gateway": null,
        "TpmAttestation": null,
        "TrustAuthorityAttestationInfos": null
      },
      "Hardware": {
        "SystemInfo": {
          "Vendor": "innotek GmbH",
          "Model": "VirtualBox",
          "Uuid": "f1e79e59-c826-437c-b8f0-b92f8ba16e23",
          "OtherIdentifyingInfo": [
            {
              "IdentifierValue": "",
              "IdentifierType": {
                "Label": "Asset Tag",
                "Summary": "Asset tag of the system",
                "Key": "AssetTag"
              }
            },
            {
              "IdentifierValue": "0",
              "IdentifierType": {
                "Label": "Service tag",
                "Summary": "Service tag of the system",
                "Key": "ServiceTag"
              }
            },
            {
              "IdentifierValue": "",
              "IdentifierType": {
                "Label": "Enclosure serial number tag",
                "Summary": "Enclosure serial number tag of the system",
                "Key": "EnclosureSerialNumberTag"
              }
            },
            {
              "IdentifierValue": "0",
              "IdentifierType": {
                "Label": "Serial number tag",
                "Summary": "Serial number tag of the system",
                "Key": "SerialNumberTag"
              }
            },
            {
              "IdentifierValue": "vboxVer_6.0.8",
              "IdentifierType": {
                "Label": "OEM specific string",
                "Summary": "OEM specific string",
                "Key": "OemSpecificString"
              }
            },
            {
              "IdentifierValue": "vboxRev_130520",
              "IdentifierType": {
                "Label": "OEM specific string",
                "Summary": "OEM specific string",
                "Key": "OemSpecificString"
              }
            }
          ],
          "SerialNumber": "0"
        },
        "CpuPowerManagementInfo": {
          "CurrentPolicy": "Balanced",
          "HardwareSupport": ""
        },
        "CpuInfo": {
          "NumCpuPackages": 1,
          "NumCpuCores": 2,
          "NumCpuThreads": 2,
          "Hz": 2304000518
        },
        "CpuPkg": [
          {
            "Index": 0,
            "Vendor": "intel",
            "Hz": 2304000518,
            "BusHz": 1000000247,
            "Description": "Intel(R) Core(TM) i5-8259U CPU @ 2.30GHz",
            "ThreadId": [
              0,
              1
            ],
            "CpuFeature": [
              {
                "Level": 0,
                "Vendor": "",
                "Eax": "0000:0000:0000:0000:0000:0000:0001:0110",
                "Ebx": "0111:0101:0110:1110:0110:0101:0100:0111",
                "Ecx": "0110:1100:0110:0101:0111:0100:0110:1110",
                "Edx": "0100:1001:0110:0101:0110:1110:0110:1001"
              },
              {
                "Level": 1,
                "Vendor": "",
                "Eax": "0000:0000:0000:1000:0000:0110:1110:1010",
                "Ebx": "0000:0000:0000:0010:0000:1000:0000:0000",
                "Ecx": "0101:0110:1101:1010:0010:0010:0000:0011",
                "Edx": "0001:0111:1000:1011:1111:1011:1111:1111"
              },
              {
                "Level": -2147483648,
                "Vendor": "",
                "Eax": "1000:0000:0000:0000:0000:0000:0000:1000",
                "Ebx": "0000:0000:0000:0000:0000:0000:0000:0000",
                "Ecx": "0000:0000:0000:0000:0000:0000:0000:0000",
                "Edx": "0000:0000:0000:0000:0000:0000:0000:0000"
              },
              {
                "Level": -2147483647,
                "Vendor": "",
                "Eax": "0000:0000:0000:0000:0000:0000:0000:0000",
                "Ebx": "0000:0000:0000:0000:0000:0000:0000:0000",
                "Ecx": "0000:0000:0000:0000:0000:0001:0010:0001",
                "Edx": "0010:1000:0001:0000:0000:1000:0000:0000"
              },
              {
                "Level": -2147483640,
                "Vendor": "",
                "Eax": "0000:0000:0000:0000:0011:0000:0010:0111",
                "Ebx": "0000:0000:0000:0000:0000:0000:0000:0000",
                "Ecx": "0000:0000:0000:0000:0000:0000:0000:0000",
                "Edx": "0000:0000:0000:0000:0000:0000:0000:0000"
              }
            ]
          }
        ],
        "MemorySize": 4290236416,
        "NumaInfo": {
          "Type": "NUMA",
          "NumNodes": 1,
          "NumaNode": [
            {
              "TypeId": 0,
              "CpuID": [
                1,
                0
              ],
              "MemoryRangeBegin": 4096,
              "MemoryRangeLength": 4096,
              "PciId": null
            }
          ]
        },
        "SmcPresent": false,
        "PciDevice": [
          {
            "Id": "0000:00:00.0",
            "ClassId": 1536,
            "Bus": 0,
            "Slot": 0,
            "Function": 0,
            "VendorId": -32634,
            "SubVendorId": 0,
            "VendorName": "Intel Corporation",
            "DeviceId": 4663,
            "SubDeviceId": 0,
            "ParentBridge": "",
            "DeviceName": "440FX - 82441FX PMC [Natoma]"
          },
          {
            "Id": "0000:00:01.0",
            "ClassId": 1537,
            "Bus": 0,
            "Slot": 1,
            "Function": 0,
            "VendorId": -32634,
            "SubVendorId": 0,
            "VendorName": "Intel Corporation",
            "DeviceId": 28672,
            "SubDeviceId": 0,
            "ParentBridge": "",
            "DeviceName": "82371SB PIIX3 ISA [Natoma/Triton II]"
          },
          {
            "Id": "0000:00:01.1",
            "ClassId": 257,
            "Bus": 0,
            "Slot": 1,
            "Function": 1,
            "VendorId": -32634,
            "SubVendorId": 0,
            "VendorName": "Intel Corporation",
            "DeviceId": 28945,
            "SubDeviceId": 0,
            "ParentBridge": "",
            "DeviceName": "PIIX4 for 430TX/440BX/MX IDE Controller"
          },
          {
            "Id": "0000:00:02.0",
            "ClassId": 768,
            "Bus": 0,
            "Slot": 2,
            "Function": 0,
            "VendorId": -32530,
            "SubVendorId": 0,
            "VendorName": "InnoTek Systemberatung GmbH",
            "DeviceId": -16657,
            "SubDeviceId": 0,
            "ParentBridge": "",
            "DeviceName": "VirtualBox Graphics Adapter"
          },
          {
            "Id": "0000:00:03.0",
            "ClassId": 512,
            "Bus": 0,
            "Slot": 3,
            "Function": 0,
            "VendorId": -32634,
            "SubVendorId": -32634,
            "VendorName": "Intel Corporation",
            "DeviceId": 4110,
            "SubDeviceId": 30,
            "ParentBridge": "",
            "DeviceName": "82540EM Gigabit Ethernet Controller"
          },
          {
            "Id": "0000:00:04.0",
            "ClassId": 2176,
            "Bus": 0,
            "Slot": 4,
            "Function": 0,
            "VendorId": -32530,
            "SubVendorId": 0,
            "VendorName": "InnoTek Systemberatung GmbH",
            "DeviceId": -13570,
            "SubDeviceId": 0,
            "ParentBridge": "",
            "DeviceName": "VirtualBox Guest Service"
          },
          {
            "Id": "0000:00:05.0",
            "ClassId": 1025,
            "Bus": 0,
            "Slot": 5,
            "Function": 0,
            "VendorId": -32634,
            "SubVendorId": -32634,
            "VendorName": "Intel Corporation",
            "DeviceId": 9237,
            "SubDeviceId": 0,
            "ParentBridge": "",
            "DeviceName": "82801AA AC'97 Audio Controller"
          },
          {
            "Id": "0000:00:06.0",
            "ClassId": 3075,
            "Bus": 0,
            "Slot": 6,
            "Function": 0,
            "VendorId": 4203,
            "SubVendorId": 0,
            "VendorName": "Apple Inc.",
            "DeviceId": 63,
            "SubDeviceId": 0,
            "ParentBridge": "",
            "DeviceName": "KeyLargo/Intrepid USB"
          },
          {
            "Id": "0000:00:07.0",
            "ClassId": 1664,
            "Bus": 0,
            "Slot": 7,
            "Function": 0,
            "VendorId": -32634,
            "SubVendorId": 0,
            "VendorName": "Intel Corporation",
            "DeviceId": 28947,
            "SubDeviceId": 0,
            "ParentBridge": "",
            "DeviceName": "82371AB/EB/MB PIIX4 ACPI"
          },
          {
            "Id": "0000:00:08.0",
            "ClassId": 512,
            "Bus": 0,
            "Slot": 8,
            "Function": 0,
            "VendorId": -32634,
            "SubVendorId": -32634,
            "VendorName": "Intel Corporation",
            "DeviceId": 4110,
            "SubDeviceId": 30,
            "ParentBridge": "",
            "DeviceName": "82540EM Gigabit Ethernet Controller"
          }
        ],
        "CpuFeature": [
          {
            "Level": 0,
            "Vendor": "",
            "Eax": "0000:0000:0000:0000:0000:0000:0001:0110",
            "Ebx": "0111:0101:0110:1110:0110:0101:0100:0111",
            "Ecx": "0110:1100:0110:0101:0111:0100:0110:1110",
            "Edx": "0100:1001:0110:0101:0110:1110:0110:1001"
          },
          {
            "Level": 1,
            "Vendor": "",
            "Eax": "0000:0000:0000:1000:0000:0110:1110:1010",
            "Ebx": "0000:0000:0000:0010:0000:1000:0000:0000",
            "Ecx": "0101:0110:1101:1010:0010:0010:0000:0011",
            "Edx": "0001:0111:1000:1011:1111:1011:1111:1111"
          },
          {
            "Level": -2147483648,
            "Vendor": "",
            "Eax": "1000:0000:0000:0000:0000:0000:0000:1000",
            "Ebx": "0000:0000:0000:0000:0000:0000:0000:0000",
            "Ecx": "0000:0000:0000:0000:0000:0000:0000:0000",
            "Edx": "0000:0000:0000:0000:0000:0000:0000:0000"
          },
          {
            "Level": -2147483647,
            "Vendor": "",
            "Eax": "0000:0000:0000:0000:0000:0000:0000:0000",
            "Ebx": "0000:0000:0000:0000:0000:0000:0000:0000",
            "Ecx": "0000:0000:0000:0000:0000:0001:0010:0001",
            "Edx": "0000:1000:0001:0000:0000:1000:0000:0000"
          },
          {
            "Level": -2147483640,
            "Vendor": "",
            "Eax": "0000:0000:0000:0000:0011:0000:0010:0111",
            "Ebx": "0000:0000:0000:0000:0000:0000:0000:0000",
            "Ecx": "0000:0000:0000:0000:0000:0000:0000:0000",
            "Edx": "0000:0000:0000:0000:0000:0000:0000:0000"
          }
        ],
        "BiosInfo": {
          "BiosVersion": "VirtualBox",
          "ReleaseDate": "2006-12-01T00:00:00Z",
          "Vendor": "innotek GmbH",
          "MajorRelease": 255,
          "MinorRelease": 255,
          "FirmwareMajorRelease": 255,
          "FirmwareMinorRelease": 255
        },
        "ReliableMemoryInfo": {
          "MemorySize": 0
        },
        "PersistentMemoryInfo": null,
        "SgxInfo": null,
        "SevInfo": null
      },
      "Capability": {
        "RecursiveResourcePoolsSupported": true,
        "CpuMemoryResourceConfigurationSupported": true,
        "RebootSupported": true,
        "ShutdownSupported": true,
        "VmotionSupported": true,
        "StandbySupported": true,
        "IpmiSupported": false,
        "MaxSupportedVMs": 0,
        "MaxRunningVMs": 0,
        "MaxSupportedVcpus": 0,
        "MaxRegisteredVMs": 32,
        "DatastorePrincipalSupported": false,
        "SanSupported": true,
        "NfsSupported": true,
        "IscsiSupported": true,
        "VlanTaggingSupported": true,
        "NicTeamingSupported": true,
        "HighGuestMemSupported": true,
        "MaintenanceModeSupported": true,
        "SuspendedRelocateSupported": true,
        "RestrictedSnapshotRelocateSupported": true,
        "PerVmSwapFiles": true,
        "LocalSwapDatastoreSupported": true,
        "UnsharedSwapVMotionSupported": true,
        "BackgroundSnapshotsSupported": false,
        "PreAssignedPCIUnitNumbersSupported": true,
        "ScreenshotSupported": true,
        "ScaledScreenshotSupported": true,
        "StorageVMotionSupported": true,
        "VmotionWithStorageVMotionSupported": true,
        "VmotionAcrossNetworkSupported": true,
        "MaxNumDisksSVMotion": 256,
        "HbrNicSelectionSupported": true,
        "VrNfcNicSelectionSupported": true,
        "RecordReplaySupported": false,
        "FtSupported": false,
        "ReplayUnsupportedReason": "incompatibleCpu",
        "ReplayCompatibilityIssues": null,
        "SmpFtSupported": false,
        "FtCompatibilityIssues": [
          "incompatibleCpu"
        ],
        "SmpFtCompatibilityIssues": [
          "cpuHwmmuUnsupported",
          "cpuHvUnsupported"
        ],
        "MaxVcpusPerFtVm": 8,
        "LoginBySSLThumbprintSupported": true,
        "CloneFromSnapshotSupported": true,
        "DeltaDiskBackingsSupported": true,
        "PerVMNetworkTrafficShapingSupported": false,
        "TpmSupported": false,
        "TpmVersion": "",
        "TxtEnabled": null,
        "SupportedCpuFeature": null,
        "VirtualExecUsageSupported": true,
        "StorageIORMSupported": true,
        "VmDirectPathGen2Supported": false,
        "VmDirectPathGen2UnsupportedReason": [
          "hostNptIncompatibleHardware"
        ],
        "VmDirectPathGen2UnsupportedReasonExtended": "",
        "SupportedVmfsMajorVersion": [
          5,
          6
        ],
        "VStorageCapable": true,
        "SnapshotRelayoutSupported": true,
        "FirewallIpRulesSupported": true,
        "ServicePackageInfoSupported": true,
        "MaxHostRunningVms": 8,
        "MaxHostSupportedVcpus": 64,
        "VmfsDatastoreMountCapable": true,
        "EightPlusHostVmfsSharedAccessSupported": true,
        "NestedHVSupported": false,
        "VPMCSupported": false,
        "InterVMCommunicationThroughVMCISupported": false,
        "ScheduledHardwareUpgradeSupported": true,
        "FeatureCapabilitiesSupported": true,
        "LatencySensitivitySupported": true,
        "StoragePolicySupported": true,
        "Accel3dSupported": false,
        "ReliableMemoryAware": true,
        "MultipleNetworkStackInstanceSupported": true,
        "MessageBusProxySupported": true,
        "VsanSupported": true,
        "VFlashSupported": true,
        "HostAccessManagerSupported": true,
        "ProvisioningNicSelectionSupported": true,
        "Nfs41Supported": true,
        "Nfs41Krb5iSupported": true,
        "TurnDiskLocatorLedSupported": true,
        "VirtualVolumeDatastoreSupported": true,
        "MarkAsSsdSupported": true,
        "MarkAsLocalSupported": true,
        "SmartCardAuthenticationSupported": true,
        "PMemSupported": true,
        "PMemSnapshotSupported": false,
        "CryptoSupported": true,
        "OneKVolumeAPIsSupported": true,
        "GatewayOnNicSupported": true,
        "UpitSupported": null,
        "CpuHwMmuSupported": false,
        "EncryptedVMotionSupported": true,
        "EncryptionChangeOnAddRemoveSupported": false,
        "EncryptionHotOperationSupported": false,
        "EncryptionWithSnapshotsSupported": false,
        "EncryptionFaultToleranceSupported": false,
        "EncryptionMemorySaveSupported": true,
        "EncryptionRDMSupported": false,
        "EncryptionVFlashSupported": false,
        "EncryptionCBRCSupported": false,
        "EncryptionHBRSupported": true,
        "FtEfiSupported": false,
        "UnmapMethodSupported": "fixed",
        "MaxMemMBPerFtVm": 131072,
        "VirtualMmuUsageIgnored": true,
        "VirtualExecUsageIgnored": true,
        "VmCreateDateSupported": true,
        "Vmfs3EOLSupported": true,
        "FtVmcpSupported": true,
        "QuickBootSupported": false,
        "AssignableHardwareSupported": null,
        "UseFeatureReqsForOldHWv": null,
        "MarkPerenniallyReservedSupported": true,
        "HppPspSupported": null,
        "DeviceRebindWithoutRebootSupported": null,
        "StoragePolicyChangeSupported": null,
        "PrecisionTimeProtocolSupported": null,
        "RemoteDeviceVMotionSupported": null,
        "MaxSupportedVmMemory": 0
      },
      "LicensableResource": {
        "Resource": [
          {
            "Key": "numCpuPackages",
            "Value": 1
          },
          {
            "Key": "numCpuCores",
            "Value": 2
          },
          {
            "Key": "numVmsStarted",
            "Value": 0
          }
        ]
      },
      "RemediationState": null,
      "PrecheckRemediationResult": null,
      "RemediationResult": null,
      "ComplianceCheckState": null,
      "ComplianceCheckResult": null,
      "ConfigManager": {
        "CpuScheduler": {
          "Type": "HostCpuSchedulerSystem",
          "Value": "cpuSchedulerSystem"
        },
        "DatastoreSystem": {
          "Type": "HostDatastoreSystem",
          "Value": "ha-datastoresystem"
        },
        "MemoryManager": {
          "Type": "HostMemorySystem",
          "Value": "memoryManagerSystem"
        },
        "StorageSystem": {
          "Type": "HostStorageSystem",
          "Value": "storageSystem"
        },
        "NetworkSystem": {
          "Type": "HostNetworkSystem",
          "Value": "networkSystem"
        },
        "VmotionSystem": {
          "Type": "HostVMotionSystem",
          "Value": "ha-vmotion-system"
        },
        "VirtualNicManager": {
          "Type": "HostVirtualNicManager",
          "Value": "ha-vnic-mgr"
        },
        "ServiceSystem": {
          "Type": "HostServiceSystem",
          "Value": "serviceSystem"
        },
        "FirewallSystem": {
          "Type": "HostFirewallSystem",
          "Value": "firewallSystem"
        },
        "AdvancedOption": {
          "Type": "OptionManager",
          "Value": "ha-adv-options"
        },
        "DiagnosticSystem": {
          "Type": "HostDiagnosticSystem",
          "Value": "diagnosticsystem"
        },
        "AutoStartManager": {
          "Type": "HostAutoStartManager",
          "Value": "ha-autostart-mgr"
        },
        "SnmpSystem": {
          "Type": "HostSnmpSystem",
          "Value": "ha-snmp-agent"
        },
        "DateTimeSystem": {
          "Type": "HostDateTimeSystem",
          "Value": "dateTimeSystem"
        },
        "PatchManager": {
          "Type": "HostPatchManager",
          "Value": "ha-host-patch-manager"
        },
        "ImageConfigManager": {
          "Type": "HostImageConfigManager",
          "Value": "ha-image-config-manager"
        },
        "BootDeviceSystem": null,
        "FirmwareSystem": {
          "Type": "HostFirmwareSystem",
          "Value": "ha-firmwareSystem"
        },
        "HealthStatusSystem": {
          "Type": "HostHealthStatusSystem",
          "Value": "healthStatusSystem"
        },
        "PciPassthruSystem": {
          "Type": "HostPciPassthruSystem",
          "Value": "ha-pcipassthrusystem"
        },
        "LicenseManager": {
          "Type": "LicenseManager",
          "Value": "ha-license-manager"
        },
        "KernelModuleSystem": {
          "Type": "HostKernelModuleSystem",
          "Value": "kernelModuleSystem"
        },
        "AuthenticationManager": {
          "Type": "HostAuthenticationManager",
          "Value": "ha-auth-manager"
        },
        "PowerSystem": {
          "Type": "HostPowerSystem",
          "Value": "ha-power-system"
        },
        "CacheConfigurationManager": {
          "Type": "HostCacheConfigurationManager",
          "Value": "ha-cache-configuration-manager"
        },
        "EsxAgentHostManager": null,
        "IscsiManager": {
          "Type": "IscsiManager",
          "Value": "iscsiManager"
        },
        "VFlashManager": {
          "Type": "HostVFlashManager",
          "Value": "ha-vflash-manager"
        },
        "VsanSystem": {
          "Type": "HostVsanSystem",
          "Value": "vsanSystem"
        },
        "MessageBusProxy": {
          "Type": "MessageBusProxy",
          "Value": "messageBusProxy"
        },
        "UserDirectory": {
          "Type": "UserDirectory",
          "Value": "ha-user-directory"
        },
        "AccountManager": {
          "Type": "HostLocalAccountManager",
          "Value": "ha-localacctmgr"
        },
        "HostAccessManager": {
          "Type": "HostAccessManager",
          "Value": "ha-host-access-manager"
        },
        "GraphicsManager": {
          "Type": "HostGraphicsManager",
          "Value": "ha-graphics-manager"
        },
        "VsanInternalSystem": {
          "Type": "HostVsanInternalSystem",
          "Value": "ha-vsan-internal-system"
        },
        "CertificateManager": {
          "Type": "HostCertificateManager",
          "Value": "ha-certificate-manager"
        },
        "CryptoManager": {
          "Type": "CryptoManager",
          "Value": "ha-crypto-manager"
        },
        "NvdimmSystem": {
          "Type": "HostNvdimmSystem",
          "Value": "ha-nvdimm-system"
        },
        "AssignableHardwareManager": null
      },
      "Config": {
        "Host": {
          "Type": "HostSystem",
          "Value": "ha-host"
        },
        "Product": {
          "Name": "VMware ESXi",
          "FullName": "VMware ESXi 6.7.0 build-14320388",
          "Vendor": "VMware, Inc.",
          "Version": "6.7.0",
          "Build": "14320388",
          "LocaleVersion": "INTL",
          "LocaleBuild": "000",
          "OsType": "vmnix-x86",
          "ProductLineId": "embeddedEsx",
          "ApiType": "HostAgent",
          "ApiVersion": "6.7.3",
          "InstanceUuid": "",
          "LicenseProductName": "VMware ESX Server",
          "LicenseProductVersion": "6.0"
        },
        "DeploymentInfo": {
          "BootedFromStatelessCache": false
        },
        "HyperThread": {
          "Available": false,
          "Active": false,
          "Config": true
        },
        "ConsoleReservation": null,
        "VirtualMachineReservation": null,
        "StorageDevice": {
          "HostBusAdapter": [
            {
              "Key": "key-vim.host.BlockHba-vmhba0",
              "Device": "vmhba0",
              "Bus": 0,
              "Status": "unknown",
              "Model": "PIIX4 for 430TX/440BX/MX IDE Controller",
              "Driver": "vmkata",
              "Pci": "0000:00:01.1",
              "StorageProtocol": ""
            },
            {
              "Key": "key-vim.host.BlockHba-vmhba64",
              "Device": "vmhba64",
              "Bus": 0,
              "Status": "unknown",
              "Model": "PIIX4 for 430TX/440BX/MX IDE Controller",
              "Driver": "vmkata",
              "Pci": "0000:00:01.1",
              "StorageProtocol": ""
            }
          ],
          "ScsiLun": [
            {
              "DeviceName": "/vmfs/devices/disks/t10.ATA_____VBOX_HARDDISK___________________________VB22cd9e3f2Df90775ba_",
              "DeviceType": "disk",
              "Key": "key-vim.host.ScsiDisk-0100000000564232326364396533662d66393037373562612056424f582048",
              "Uuid": "0100000000564232326364396533662d66393037373562612056424f582048",
              "Descriptor": [
                {
                  "Quality": "highQuality",
                  "Id": "t10.ATA_____VBOX_HARDDISK___________________________VB22cd9e3f2Df90775ba_"
                },
                {
                  "Quality": "mediumQuality",
                  "Id": "vml.0100000000564232326364396533662d66393037373562612056424f582048"
                },
                {
                  "Quality": "mediumQuality",
                  "Id": "0100000000564232326364396533662d66393037373562612056424f582048"
                }
              ],
              "CanonicalName": "t10.ATA_____VBOX_HARDDISK___________________________VB22cd9e3f2Df90775ba_",
              "DisplayName": "Local ATA Disk (t10.ATA_____VBOX_HARDDISK___________________________VB22cd9e3f2Df90775ba_)",
              "LunType": "disk",
              "Vendor": "ATA     ",
              "Model": "VBOX HARDDISK   ",
              "Revision": "1.0 ",
              "ScsiLevel": 5,
              "SerialNumber": "unavailable",
              "DurableName": {
                "Namespace": "SERIALNUM",
                "NamespaceId": 4,
                "Data": "MzI="
              },
              "AlternateName": [
                {
                  "Namespace": "GENERIC_VPD",
                  "NamespaceId": 5,
                  "Data": "LTc5"
                },
                {
                  "Namespace": "SERIALNUM",
                  "NamespaceId": 4,
                  "Data": "MzI="
                },
                {
                  "Namespace": "UNKNOWN",
                  "NamespaceId": 5,
                  "Data": "MzI="
                },
                {
                  "Namespace": "GENERIC_VPD",
                  "NamespaceId": 5,
                  "Data": "MA=="
                },
                {
                  "Namespace": "GENERIC_VPD",
                  "NamespaceId": 5,
                  "Data": "MA=="
                },
                {
                  "Namespace": "GENERIC_VPD",
                  "NamespaceId": 5,
                  "Data": "MA=="
                }
              ],
              "StandardInquiry": "MA==",
              "QueueDepth": 1,
              "OperationalState": [
                "ok"
              ],
              "Capabilities": {
                "UpdateDisplayNameSupported": true
              },
              "VStorageSupport": "vStorageUnsupported",
              "ProtocolEndpoint": false,
              "PerenniallyReserved": false,
              "ClusteredVmdkSupported": null,
              "Capacity": {
                "BlockSize": 512,
                "Block": 20971520
              },
              "DevicePath": "/vmfs/devices/disks/t10.ATA_____VBOX_HARDDISK___________________________VB22cd9e3f2Df90775ba_",
              "Ssd": false,
              "LocalDisk": true,
              "PhysicalLocation": null,
              "EmulatedDIXDIFEnabled": false,
              "VsanDiskInfo": null,
              "ScsiDiskType": "native512"
            },
            {
              "DeviceName": "/vmfs/devices/cdrom/mpx.vmhba64:C0:T0:L0",
              "DeviceType": "cdrom",
              "Key": "key-vim.host.ScsiLun-0005000000766d68626136343a303a30",
              "Uuid": "0005000000766d68626136343a303a30",
              "Descriptor": [
                {
                  "Quality": "lowQuality",
                  "Id": "mpx.vmhba64:C0:T0:L0"
                },
                {
                  "Quality": "lowQuality",
                  "Id": "vml.0005000000766d68626136343a303a30"
                },
                {
                  "Quality": "lowQuality",
                  "Id": "0005000000766d68626136343a303a30"
                }
              ],
              "CanonicalName": "mpx.vmhba64:C0:T0:L0",
              "DisplayName": "Local VBOX CD-ROM (mpx.vmhba64:C0:T0:L0)",
              "LunType": "cdrom",
              "Vendor": "VBOX    ",
              "Model": "CD-ROM          ",
              "Revision": "1.0 ",
              "ScsiLevel": 5,
              "SerialNumber": "unavailable",
              "DurableName": null,
              "AlternateName": null,
              "StandardInquiry": "MA==",
              "QueueDepth": 0,
              "OperationalState": [
                "ok"
              ],
              "Capabilities": {
                "UpdateDisplayNameSupported": false
              },
              "VStorageSupport": "vStorageUnsupported",
              "ProtocolEndpoint": false,
              "PerenniallyReserved": false,
              "ClusteredVmdkSupported": null
            }
          ],
          "ScsiTopology": {
            "Adapter": [
              {
                "Key": "key-vim.host.ScsiTopology.Interface-vmhba0",
                "Adapter": "key-vim.host.BlockHba-vmhba0",
                "Target": [
                  {
                    "Key": "key-vim.host.ScsiTopology.Target-vmhba0:0:0",
                    "Target": 0,
                    "Lun": [
                      {
                        "Key": "key-vim.host.ScsiTopology.Lun-0100000000564232326364396533662d66393037373562612056424f582048",
                        "Lun": 0,
                        "ScsiLun": "key-vim.host.ScsiDisk-0100000000564232326364396533662d66393037373562612056424f582048"
                      }
                    ],
                    "Transport": {}
                  }
                ]
              },
              {
                "Key": "key-vim.host.ScsiTopology.Interface-vmhba64",
                "Adapter": "key-vim.host.BlockHba-vmhba64",
                "Target": [
                  {
                    "Key": "key-vim.host.ScsiTopology.Target-vmhba64:0:0",
                    "Target": 0,
                    "Lun": [
                      {
                        "Key": "key-vim.host.ScsiTopology.Lun-0005000000766d68626136343a303a30",
                        "Lun": 0,
                        "ScsiLun": "key-vim.host.ScsiLun-0005000000766d68626136343a303a30"
                      }
                    ],
                    "Transport": {}
                  }
                ]
              }
            ]
          },
          "NvmeTopology": null,
          "MultipathInfo": {
            "Lun": [
              {
                "Key": "key-vim.host.MultipathInfo.LogicalUnit-0100000000564232326364396533662d66393037373562612056424f582048",
                "Id": "0100000000564232326364396533662d66393037373562612056424f582048",
                "Lun": "key-vim.host.ScsiDisk-0100000000564232326364396533662d66393037373562612056424f582048",
                "Path": [
                  {
                    "Key": "key-vim.host.MultipathInfo.Path-vmhba0:C0:T0:L0",
                    "Name": "vmhba0:C0:T0:L0",
                    "PathState": "active",
                    "State": "active",
                    "IsWorkingPath": true,
                    "Adapter": "key-vim.host.BlockHba-vmhba0",
                    "Lun": "key-vim.host.MultipathInfo.LogicalUnit-0100000000564232326364396533662d66393037373562612056424f582048",
                    "Transport": {}
                  }
                ],
                "Policy": {
                  "Policy": "VMW_PSP_FIXED",
                  "Prefer": "vmhba0:C0:T0:L0"
                },
                "StorageArrayTypePolicy": {
                  "Policy": "VMW_SATP_LOCAL"
                }
              },
              {
                "Key": "key-vim.host.MultipathInfo.LogicalUnit-0005000000766d68626136343a303a30",
                "Id": "0005000000766d68626136343a303a30",
                "Lun": "key-vim.host.ScsiLun-0005000000766d68626136343a303a30",
                "Path": [
                  {
                    "Key": "key-vim.host.MultipathInfo.Path-vmhba64:C0:T0:L0",
                    "Name": "vmhba64:C0:T0:L0",
                    "PathState": "active",
                    "State": "active",
                    "IsWorkingPath": true,
                    "Adapter": "key-vim.host.BlockHba-vmhba64",
                    "Lun": "key-vim.host.MultipathInfo.LogicalUnit-0005000000766d68626136343a303a30",
                    "Transport": {}
                  }
                ],
                "Policy": {
                  "Policy": "VMW_PSP_FIXED",
                  "Prefer": "vmhba64:C0:T0:L0"
                },
                "StorageArrayTypePolicy": {
                  "Policy": "VMW_SATP_LOCAL"
                }
              }
            ]
          },
          "PlugStoreTopology": {
            "Adapter": [
              {
                "Key": "key-vim.host.PlugStoreTopology.Adapter-vmhba0",
                "Adapter": "key-vim.host.BlockHba-vmhba0",
                "Path": [
                  "key-vim.host.PlugStoreTopology.Path-vmhba0:C0:T0:L0"
                ]
              },
              {
                "Key": "key-vim.host.PlugStoreTopology.Adapter-vmhba64",
                "Adapter": "key-vim.host.BlockHba-vmhba64",
                "Path": [
                  "key-vim.host.PlugStoreTopology.Path-vmhba64:C0:T0:L0"
                ]
              }
            ],
            "Path": [
              {
                "Key": "key-vim.host.PlugStoreTopology.Path-vmhba0:C0:T0:L0",
                "Name": "vmhba0:C0:T0:L0",
                "ChannelNumber": 0,
                "TargetNumber": 0,
                "LunNumber": 0,
                "Adapter": "key-vim.host.PlugStoreTopology.Adapter-vmhba0",
                "Target": "key-vim.host.PlugStoreTopology.Target-ide.0:0",
                "Device": "key-vim.host.PlugStoreTopology.Device-0100000000564232326364396533662d66393037373562612056424f582048"
              },
              {
                "Key": "key-vim.host.PlugStoreTopology.Path-vmhba64:C0:T0:L0",
                "Name": "vmhba64:C0:T0:L0",
                "ChannelNumber": 0,
                "TargetNumber": 0,
                "LunNumber": 0,
                "Adapter": "key-vim.host.PlugStoreTopology.Adapter-vmhba64",
                "Target": "key-vim.host.PlugStoreTopology.Target-ide.0:0",
                "Device": "key-vim.host.PlugStoreTopology.Device-0005000000766d68626136343a303a30"
              }
            ],
            "Target": [
              {
                "Key": "key-vim.host.PlugStoreTopology.Target-ide.0:0",
                "Transport": {}
              }
            ],
            "Device": [
              {
                "Key": "key-vim.host.PlugStoreTopology.Device-0100000000564232326364396533662d66393037373562612056424f582048",
                "Lun": "key-vim.host.ScsiDisk-0100000000564232326364396533662d66393037373562612056424f582048",
                "Path": [
                  "key-vim.host.PlugStoreTopology.Path-vmhba0:C0:T0:L0"
                ]
              },
              {
                "Key": "key-vim.host.PlugStoreTopology.Device-0005000000766d68626136343a303a30",
                "Lun": "key-vim.host.ScsiLun-0005000000766d68626136343a303a30",
                "Path": [
                  "key-vim.host.PlugStoreTopology.Path-vmhba64:C0:T0:L0"
                ]
              }
            ],
            "Plugin": [
              {
                "Key": "key-vim.host.PlugStoreTopology.Plugin-HPP",
                "Name": "HPP",
                "Device": null,
                "ClaimedPath": null
              },
              {
                "Key": "key-vim.host.PlugStoreTopology.Plugin-NMP",
                "Name": "NMP",
                "Device": [
                  "key-vim.host.PlugStoreTopology.Device-0100000000564232326364396533662d66393037373562612056424f582048",
                  "key-vim.host.PlugStoreTopology.Device-0005000000766d68626136343a303a30"
                ],
                "ClaimedPath": [
                  "key-vim.host.PlugStoreTopology.Path-vmhba64:C0:T0:L0",
                  "key-vim.host.PlugStoreTopology.Path-vmhba0:C0:T0:L0"
                ]
              }
            ]
          },
          "SoftwareInternetScsiEnabled": false
        },
        "MultipathState": {
          "Path": [
            {
              "Name": "vmhba0:C0:T0:L0",
              "PathState": "active"
            },
            {
              "Name": "vmhba64:C0:T0:L0",
              "PathState": "active"
            }
          ]
        },
        "FileSystemVolume": {
          "VolumeTypeList": [
            "VMFS",
            "NFS",
            "NFS41",
            "vsan",
            "VVOL",
            "VFFS",
            "OTHER",
            "PMEM"
          ],
          "MountInfo": [
            {
              "MountInfo": {
                "Path": "/vmfs/volumes/60c4dd35-141ece76-d699-0800272601f9",
                "AccessMode": "readWrite",
                "Mounted": true,
                "Accessible": true,
                "InaccessibleReason": ""
              },
              "Volume": {
                "Type": "VMFS",
                "Name": "datastore1",
                "Capacity": 2684354560,
                "BlockSizeMb": 1,
                "BlockSize": 1024,
                "UnmapGranularity": 1024,
                "UnmapPriority": "low",
                "UnmapBandwidthSpec": null,
                "MaxBlocks": 63963136,
                "MajorVersion": 6,
                "Version": "6.82",
                "Uuid": "60c4dd35-141ece76-d699-0800272601f9",
                "Extent": [
                  {
                    "DiskName": "t10.ATA_____VBOX_HARDDISK___________________________VB22cd9e3f2Df90775ba_",
                    "Partition": 3
                  }
                ],
                "VmfsUpgradable": false,
                "ForceMountedInfo": null,
                "Ssd": false,
                "Local": true,
                "ScsiDiskType": ""
              },
              "VStorageSupport": "vStorageUnsupported"
            },
            {
              "MountInfo": {
                "Path": "/vmfs/volumes/bdde4a74-a82ac71f-7a13-2f1804833403",
                "AccessMode": "readOnly",
                "Mounted": true,
                "Accessible": true,
                "InaccessibleReason": ""
              },
              "Volume": {
                "Type": "OTHER",
                "Name": "",
                "Capacity": 261853184
              },
              "VStorageSupport": ""
            },
            {
              "MountInfo": {
                "Path": "/vmfs/volumes/60c4dd36-32ef1a2f-8d22-0800272601f9",
                "AccessMode": "readOnly",
                "Mounted": true,
                "Accessible": true,
                "InaccessibleReason": ""
              },
              "Volume": {
                "Type": "OTHER",
                "Name": "",
                "Capacity": 4293591040
              },
              "VStorageSupport": ""
            },
            {
              "MountInfo": {
                "Path": "/vmfs/volumes/de1b2d7a-94ee883a-0a43-ada37e3393b2",
                "AccessMode": "readOnly",
                "Mounted": true,
                "Accessible": true,
                "InaccessibleReason": ""
              },
              "Volume": {
                "Type": "OTHER",
                "Name": "",
                "Capacity": 261853184
              },
              "VStorageSupport": ""
            },
            {
              "MountInfo": {
                "Path": "/vmfs/volumes/60c4dd2f-b439629d-bec9-0800272601f9",
                "AccessMode": "readOnly",
                "Mounted": true,
                "Accessible": true,
                "InaccessibleReason": ""
              },
              "Volume": {
                "Type": "OTHER",
                "Name": "",
                "Capacity": 299712512
              },
              "VStorageSupport": ""
            }
          ]
        },
        "SystemFile": null,
        "Network": {
          "Vswitch": [
            {
              "Name": "vSwitch0",
              "Key": "key-vim.host.VirtualSwitch-vSwitch0",
              "NumPorts": 2560,
              "NumPortsAvailable": 2552,
              "Mtu": 1500,
              "Portgroup": [
                "key-vim.host.PortGroup-VM Network",
                "key-vim.host.PortGroup-Management Network"
              ],
              "Pnic": [
                "key-vim.host.PhysicalNic-vmnic0"
              ],
              "Spec": {
                "NumPorts": 128,
                "Bridge": {
                  "NicDevice": [
                    "vmnic0"
                  ],
                  "Beacon": {
                    "Interval": 1
                  },
                  "LinkDiscoveryProtocolConfig": {
                    "Protocol": "cdp",
                    "Operation": "listen"
                  }
                },
                "Policy": {
                  "Security": {
                    "AllowPromiscuous": false,
                    "MacChanges": true,
                    "ForgedTransmits": true
                  },
                  "NicTeaming": {
                    "Policy": "loadbalance_srcid",
                    "ReversePolicy": true,
                    "NotifySwitches": true,
                    "RollingOrder": false,
                    "FailureCriteria": {
                      "CheckSpeed": "minimum",
                      "Speed": 10,
                      "CheckDuplex": false,
                      "FullDuplex": false,
                      "CheckErrorPercent": false,
                      "Percentage": 0,
                      "CheckBeacon": false
                    },
                    "NicOrder": {
                      "ActiveNic": [
                        "vmnic0"
                      ],
                      "StandbyNic": null
                    }
                  },
                  "OffloadPolicy": {
                    "CsumOffload": true,
                    "TcpSegmentation": true,
                    "ZeroCopyXmit": true
                  },
                  "ShapingPolicy": {
                    "Enabled": false,
                    "AverageBandwidth": 0,
                    "PeakBandwidth": 0,
                    "BurstSize": 0
                  }
                },
                "Mtu": 0
              }
            },
            {
              "Name": "vSwitch1",
              "Key": "key-vim.host.VirtualSwitch-vSwitch1",
              "NumPorts": 2560,
              "NumPortsAvailable": 2552,
              "Mtu": 1500,
              "Portgroup": [
                "key-vim.host.PortGroup-VM Network2"
              ],
              "Pnic": [
                "key-vim.host.PhysicalNic-vmnic1"
              ],
              "Spec": {
                "NumPorts": 1024,
                "Bridge": {
                  "NicDevice": [
                    "vmnic1"
                  ],
                  "Beacon": {
                    "Interval": 1
                  },
                  "LinkDiscoveryProtocolConfig": {
                    "Protocol": "cdp",
                    "Operation": "listen"
                  }
                },
                "Policy": {
                  "Security": {
                    "AllowPromiscuous": false,
                    "MacChanges": false,
                    "ForgedTransmits": false
                  },
                  "NicTeaming": {
                    "Policy": "loadbalance_srcid",
                    "ReversePolicy": true,
                    "NotifySwitches": true,
                    "RollingOrder": false,
                    "FailureCriteria": {
                      "CheckSpeed": "minimum",
                      "Speed": 10,
                      "CheckDuplex": false,
                      "FullDuplex": false,
                      "CheckErrorPercent": false,
                      "Percentage": 0,
                      "CheckBeacon": false
                    },
                    "NicOrder": {
                      "ActiveNic": [
                        "vmnic1"
                      ],
                      "StandbyNic": null
                    }
                  },
                  "OffloadPolicy": {
                    "CsumOffload": true,
                    "TcpSegmentation": true,
                    "ZeroCopyXmit": true
                  },
                  "ShapingPolicy": {
                    "Enabled": false,
                    "AverageBandwidth": 0,
                    "PeakBandwidth": 0,
                    "BurstSize": 0
                  }
                },
                "Mtu": 0
              }
            }
          ],
          "ProxySwitch": null,
          "Portgroup": [
            {
              "Key": "key-vim.host.PortGroup-VM Network",
              "Port": null,
              "Vswitch": "key-vim.host.VirtualSwitch-vSwitch0",
              "ComputedPolicy": {
                "Security": {
                  "AllowPromiscuous": false,
                  "MacChanges": true,
                  "ForgedTransmits": true
                },
                "NicTeaming": {
                  "Policy": "loadbalance_srcid",
                  "ReversePolicy": true,
                  "NotifySwitches": true,
                  "RollingOrder": false,
                  "FailureCriteria": {
                    "CheckSpeed": "minimum",
                    "Speed": 10,
                    "CheckDuplex": false,
                    "FullDuplex": false,
                    "CheckErrorPercent": false,
                    "Percentage": 0,
                    "CheckBeacon": false
                  },
                  "NicOrder": {
                    "ActiveNic": [
                      "vmnic0"
                    ],
                    "StandbyNic": null
                  }
                },
                "OffloadPolicy": {
                  "CsumOffload": true,
                  "TcpSegmentation": true,
                  "ZeroCopyXmit": true
                },
                "ShapingPolicy": {
                  "Enabled": false,
                  "AverageBandwidth": 0,
                  "PeakBandwidth": 0,
                  "BurstSize": 0
                }
              },
              "Spec": {
                "Name": "VM Network",
                "VlanId": 0,
                "VswitchName": "vSwitch0",
                "Policy": {
                  "Security": {
                    "AllowPromiscuous": null,
                    "MacChanges": null,
                    "ForgedTransmits": null
                  },
                  "NicTeaming": {
                    "Policy": "",
                    "ReversePolicy": null,
                    "NotifySwitches": null,
                    "RollingOrder": null,
                    "FailureCriteria": {
                      "CheckSpeed": "",
                      "Speed": 0,
                      "CheckDuplex": null,
                      "FullDuplex": null,
                      "CheckErrorPercent": null,
                      "Percentage": 0,
                      "CheckBeacon": null
                    },
                    "NicOrder": null
                  },
                  "OffloadPolicy": {
                    "CsumOffload": null,
                    "TcpSegmentation": null,
                    "ZeroCopyXmit": null
                  },
                  "ShapingPolicy": {
                    "Enabled": null,
                    "AverageBandwidth": 0,
                    "PeakBandwidth": 0,
                    "BurstSize": 0
                  }
                }
              }
            },
            {
              "Key": "key-vim.host.PortGroup-Management Network",
              "Port": [
                {
                  "Key": "key-vim.host.PortGroup.Port-33554436",
                  "Mac": [
                    "08:00:27:26:01:f9"
                  ],
                  "Type": "host"
                }
              ],
              "Vswitch": "key-vim.host.VirtualSwitch-vSwitch0",
              "ComputedPolicy": {
                "Security": {
                  "AllowPromiscuous": false,
                  "MacChanges": true,
                  "ForgedTransmits": true
                },
                "NicTeaming": {
                  "Policy": "loadbalance_srcid",
                  "ReversePolicy": true,
                  "NotifySwitches": true,
                  "RollingOrder": false,
                  "FailureCriteria": {
                    "CheckSpeed": "minimum",
                    "Speed": 10,
                    "CheckDuplex": false,
                    "FullDuplex": false,
                    "CheckErrorPercent": false,
                    "Percentage": 0,
                    "CheckBeacon": false
                  },
                  "NicOrder": {
                    "ActiveNic": [
                      "vmnic0"
                    ],
                    "StandbyNic": null
                  }
                },
                "OffloadPolicy": {
                  "CsumOffload": true,
                  "TcpSegmentation": true,
                  "ZeroCopyXmit": true
                },
                "ShapingPolicy": {
                  "Enabled": false,
                  "AverageBandwidth": 0,
                  "PeakBandwidth": 0,
                  "BurstSize": 0
                }
              },
              "Spec": {
                "Name": "Management Network",
                "VlanId": 0,
                "VswitchName": "vSwitch0",
                "Policy": {
                  "Security": {
                    "AllowPromiscuous": null,
                    "MacChanges": null,
                    "ForgedTransmits": null
                  },
                  "NicTeaming": {
                    "Policy": "loadbalance_srcid",
                    "ReversePolicy": null,
                    "NotifySwitches": true,
                    "RollingOrder": false,
                    "FailureCriteria": {
                      "CheckSpeed": "",
                      "Speed": 0,
                      "CheckDuplex": null,
                      "FullDuplex": null,
                      "CheckErrorPercent": null,
                      "Percentage": 0,
                      "CheckBeacon": false
                    },
                    "NicOrder": {
                      "ActiveNic": [
                        "vmnic0"
                      ],
                      "StandbyNic": null
                    }
                  },
                  "OffloadPolicy": {
                    "CsumOffload": null,
                    "TcpSegmentation": null,
                    "ZeroCopyXmit": null
                  },
                  "ShapingPolicy": {
                    "Enabled": null,
                    "AverageBandwidth": 0,
                    "PeakBandwidth": 0,
                    "BurstSize": 0
                  }
                }
              }
            },
            {
              "Key": "key-vim.host.PortGroup-VM Network2",
              "Port": null,
              "Vswitch": "key-vim.host.VirtualSwitch-vSwitch1",
              "ComputedPolicy": {
                "Security": {
                  "AllowPromiscuous": false,
                  "MacChanges": false,
                  "ForgedTransmits": false
                },
                "NicTeaming": {
                  "Policy": "loadbalance_srcid",
                  "ReversePolicy": true,
                  "NotifySwitches": true,
                  "RollingOrder": false,
                  "FailureCriteria": {
                    "CheckSpeed": "minimum",
                    "Speed": 10,
                    "CheckDuplex": false,
                    "FullDuplex": false,
                    "CheckErrorPercent": false,
                    "Percentage": 0,
                    "CheckBeacon": false
                  },
                  "NicOrder": {
                    "ActiveNic": [
                      "vmnic1"
                    ],
                    "StandbyNic": null
                  }
                },
                "OffloadPolicy": {
                  "CsumOffload": true,
                  "TcpSegmentation": true,
                  "ZeroCopyXmit": true
                },
                "ShapingPolicy": {
                  "Enabled": false,
                  "AverageBandwidth": 0,
                  "PeakBandwidth": 0,
                  "BurstSize": 0
                }
              },
              "Spec": {
                "Name": "VM Network2",
                "VlanId": 0,
                "VswitchName": "vSwitch1",
                "Policy": {
                  "Security": {
                    "AllowPromiscuous": null,
                    "MacChanges": null,
                    "ForgedTransmits": null
                  },
                  "NicTeaming": {
                    "Policy": "",
                    "ReversePolicy": null,
                    "NotifySwitches": null,
                    "RollingOrder": null,
                    "FailureCriteria": {
                      "CheckSpeed": "",
                      "Speed": 0,
                      "CheckDuplex": null,
                      "FullDuplex": null,
                      "CheckErrorPercent": null,
                      "Percentage": 0,
                      "CheckBeacon": null
                    },
                    "NicOrder": null
                  },
                  "OffloadPolicy": {
                    "CsumOffload": null,
                    "TcpSegmentation": null,
                    "ZeroCopyXmit": null
                  },
                  "ShapingPolicy": {
                    "Enabled": null,
                    "AverageBandwidth": 0,
                    "PeakBandwidth": 0,
                    "BurstSize": 0
                  }
                }
              }
            }
          ],
          "Pnic": [
            {
              "Key": "key-vim.host.PhysicalNic-vmnic0",
              "Device": "vmnic0",
              "Pci": "0000:00:03.0",
              "Driver": "e1000",
              "LinkSpeed": {
                "SpeedMb": 1000,
                "Duplex": true
              },
              "ValidLinkSpecification": [
                {
                  "SpeedMb": 10,
                  "Duplex": false
                },
                {
                  "SpeedMb": 10,
                  "Duplex": true
                },
                {
                  "SpeedMb": 100,
                  "Duplex": false
                },
                {
                  "SpeedMb": 100,
                  "Duplex": true
                },
                {
                  "SpeedMb": 1000,
                  "Duplex": true
                }
              ],
              "Spec": {
                "Ip": {
                  "Dhcp": false,
                  "IpAddress": "",
                  "SubnetMask": "",
                  "IpV6Config": null
                },
                "LinkSpeed": null,
                "EnableEnhancedNetworkingStack": false,
                "EnsInterruptEnabled": null
              },
              "WakeOnLanSupported": true,
              "Mac": "08:00:27:26:01:f9",
              "FcoeConfiguration": null,
              "VmDirectPathGen2Supported": false,
              "VmDirectPathGen2SupportedMode": "",
              "ResourcePoolSchedulerAllowed": true,
              "ResourcePoolSchedulerDisallowedReason": null,
              "AutoNegotiateSupported": true,
              "EnhancedNetworkingStackSupported": false,
              "EnsInterruptSupported": null,
              "RdmaDevice": ""
            },
            {
              "Key": "key-vim.host.PhysicalNic-vmnic1",
              "Device": "vmnic1",
              "Pci": "0000:00:08.0",
              "Driver": "e1000",
              "LinkSpeed": {
                "SpeedMb": 1000,
                "Duplex": true
              },
              "ValidLinkSpecification": [
                {
                  "SpeedMb": 10,
                  "Duplex": false
                },
                {
                  "SpeedMb": 10,
                  "Duplex": true
                },
                {
                  "SpeedMb": 100,
                  "Duplex": false
                },
                {
                  "SpeedMb": 100,
                  "Duplex": true
                },
                {
                  "SpeedMb": 1000,
                  "Duplex": true
                }
              ],
              "Spec": {
                "Ip": {
                  "Dhcp": false,
                  "IpAddress": "",
                  "SubnetMask": "",
                  "IpV6Config": null
                },
                "LinkSpeed": null,
                "EnableEnhancedNetworkingStack": false,
                "EnsInterruptEnabled": null
              },
              "WakeOnLanSupported": true,
              "Mac": "08:00:27:82:1d:9f",
              "FcoeConfiguration": null,
              "VmDirectPathGen2Supported": false,
              "VmDirectPathGen2SupportedMode": "",
              "ResourcePoolSchedulerAllowed": true,
              "ResourcePoolSchedulerDisallowedReason": null,
              "AutoNegotiateSupported": true,
              "EnhancedNetworkingStackSupported": false,
              "EnsInterruptSupported": null,
              "RdmaDevice": ""
            }
          ],
          "RdmaDevice": null,
          "Vnic": [
            {
              "Device": "vmk0",
              "Key": "key-vim.host.VirtualNic-vmk0",
              "Portgroup": "Management Network",
              "Spec": {
                "Ip": {
                  "Dhcp": false,
                  "IpAddress": "192.168.100.120",
                  "SubnetMask": "255.255.255.0",
                  "IpV6Config": {
                    "IpV6Address": [
                      {
                        "IpAddress": "fe80::a00:27ff:fe26:1f9",
                        "PrefixLength": 64,
                        "Origin": "other",
                        "DadState": "preferred",
                        "Lifetime": null,
                        "Operation": ""
                      },
                      {
                        "IpAddress": "2001:268:c00a:c109:a00:27ff:fe26:1f9",
                        "PrefixLength": 64,
                        "Origin": "linklayer",
                        "DadState": "preferred",
                        "Lifetime": null,
                        "Operation": ""
                      },
                      {
                        "IpAddress": "2001:268:c00a:c109:44f:4c8c:1fbf:2",
                        "PrefixLength": 64,
                        "Origin": "dhcp",
                        "DadState": "preferred",
                        "Lifetime": null,
                        "Operation": ""
                      }
                    ],
                    "AutoConfigurationEnabled": true,
                    "DhcpV6Enabled": false
                  }
                },
                "Mac": "08:00:27:26:01:f9",
                "DistributedVirtualPort": null,
                "Portgroup": "Management Network",
                "Mtu": 1500,
                "TsoEnabled": true,
                "NetStackInstanceKey": "defaultTcpipStack",
                "OpaqueNetwork": null,
                "ExternalId": "",
                "PinnedPnic": "",
                "IpRouteSpec": null,
                "SystemOwned": null
              },
              "Port": "key-vim.host.PortGroup.Port-33554436"
            }
          ],
          "ConsoleVnic": null,
          "DnsConfig": {
            "Dhcp": false,
            "VirtualNicDevice": "",
            "Ipv6VirtualNicDevice": "",
            "HostName": "localhost",
            "DomainName": "",
            "Address": [
              "192.168.100.1"
            ],
            "SearchDomain": null
          },
          "IpRouteConfig": {
            "DefaultGateway": "",
            "GatewayDevice": "",
            "IpV6DefaultGateway": "fe80::64f:4cff:fe8c:1fbf",
            "IpV6GatewayDevice": "vmk0"
          },
          "ConsoleIpRouteConfig": null,
          "RouteTableInfo": {
            "IpRoute": [
              {
                "Network": "192.168.100.0",
                "PrefixLength": 24,
                "Gateway": "0.0.0.0",
                "DeviceName": "vmk0"
              }
            ],
            "Ipv6Route": [
              {
                "Network": "::",
                "PrefixLength": 0,
                "Gateway": "fe80::64f:4cff:fe8c:1fbf",
                "DeviceName": "vmk0"
              },
              {
                "Network": "2001:268:c00a:c109::",
                "PrefixLength": 64,
                "Gateway": "::",
                "DeviceName": "vmk0"
              },
              {
                "Network": "fe80::",
                "PrefixLength": 64,
                "Gateway": "::",
                "DeviceName": "lo0"
              },
              {
                "Network": "fe80::",
                "PrefixLength": 64,
                "Gateway": "::",
                "DeviceName": "vmk0"
              },
              {
                "Network": "ff01::",
                "PrefixLength": 32,
                "Gateway": "::1",
                "DeviceName": "lo0"
              },
              {
                "Network": "ff01::",
                "PrefixLength": 32,
                "Gateway": "fe80::a00:27ff:fe26:1f9",
                "DeviceName": "vmk0"
              },
              {
                "Network": "ff02::",
                "PrefixLength": 32,
                "Gateway": "::1",
                "DeviceName": "lo0"
              },
              {
                "Network": "ff02::",
                "PrefixLength": 32,
                "Gateway": "fe80::a00:27ff:fe26:1f9",
                "DeviceName": "vmk0"
              }
            ]
          },
          "Dhcp": null,
          "Nat": null,
          "IpV6Enabled": true,
          "AtBootIpV6Enabled": true,
          "NetStackInstance": [
            {
              "Key": "vSphereProvisioning",
              "Name": "",
              "DnsConfig": {
                "Dhcp": false,
                "VirtualNicDevice": "",
                "Ipv6VirtualNicDevice": "",
                "HostName": "",
                "DomainName": "",
                "Address": null,
                "SearchDomain": null
              },
              "IpRouteConfig": {
                "DefaultGateway": "",
                "GatewayDevice": "",
                "IpV6DefaultGateway": "",
                "IpV6GatewayDevice": ""
              },
              "RequestedMaxNumberOfConnections": 11000,
              "CongestionControlAlgorithm": "newreno",
              "IpV6Enabled": true,
              "RouteTableConfig": null
            },
            {
              "Key": "vmotion",
              "Name": "",
              "DnsConfig": {
                "Dhcp": false,
                "VirtualNicDevice": "",
                "Ipv6VirtualNicDevice": "",
                "HostName": "",
                "DomainName": "",
                "Address": null,
                "SearchDomain": null
              },
              "IpRouteConfig": {
                "DefaultGateway": "",
                "GatewayDevice": "",
                "IpV6DefaultGateway": "",
                "IpV6GatewayDevice": ""
              },
              "RequestedMaxNumberOfConnections": 11000,
              "CongestionControlAlgorithm": "newreno",
              "IpV6Enabled": true,
              "RouteTableConfig": null
            },
            {
              "Key": "defaultTcpipStack",
              "Name": "defaultTcpipStack",
              "DnsConfig": {
                "Dhcp": false,
                "VirtualNicDevice": "",
                "Ipv6VirtualNicDevice": "",
                "HostName": "localhost",
                "DomainName": "",
                "Address": [
                  "192.168.100.1"
                ],
                "SearchDomain": null
              },
              "IpRouteConfig": {
                "DefaultGateway": "",
                "GatewayDevice": "",
                "IpV6DefaultGateway": "fe80::64f:4cff:fe8c:1fbf",
                "IpV6GatewayDevice": "vmk0"
              },
              "RequestedMaxNumberOfConnections": 11000,
              "CongestionControlAlgorithm": "newreno",
              "IpV6Enabled": true,
              "RouteTableConfig": {
                "IpRoute": [
                  {
                    "ChangeOperation": "ignore",
                    "Route": {
                      "Network": "192.168.100.0",
                      "PrefixLength": 24,
                      "Gateway": "0.0.0.0",
                      "DeviceName": "vmk0"
                    }
                  }
                ],
                "Ipv6Route": [
                  {
                    "ChangeOperation": "ignore",
                    "Route": {
                      "Network": "::",
                      "PrefixLength": 0,
                      "Gateway": "fe80::64f:4cff:fe8c:1fbf",
                      "DeviceName": "vmk0"
                    }
                  },
                  {
                    "ChangeOperation": "ignore",
                    "Route": {
                      "Network": "2001:268:c00a:c109::",
                      "PrefixLength": 64,
                      "Gateway": "::",
                      "DeviceName": "vmk0"
                    }
                  },
                  {
                    "ChangeOperation": "ignore",
                    "Route": {
                      "Network": "fe80::",
                      "PrefixLength": 64,
                      "Gateway": "::",
                      "DeviceName": "lo0"
                    }
                  },
                  {
                    "ChangeOperation": "ignore",
                    "Route": {
                      "Network": "fe80::",
                      "PrefixLength": 64,
                      "Gateway": "::",
                      "DeviceName": "vmk0"
                    }
                  },
                  {
                    "ChangeOperation": "ignore",
                    "Route": {
                      "Network": "ff01::",
                      "PrefixLength": 32,
                      "Gateway": "::1",
                      "DeviceName": "lo0"
                    }
                  },
                  {
                    "ChangeOperation": "ignore",
                    "Route": {
                      "Network": "ff01::",
                      "PrefixLength": 32,
                      "Gateway": "fe80::a00:27ff:fe26:1f9",
                      "DeviceName": "vmk0"
                    }
                  },
                  {
                    "ChangeOperation": "ignore",
                    "Route": {
                      "Network": "ff02::",
                      "PrefixLength": 32,
                      "Gateway": "::1",
                      "DeviceName": "lo0"
                    }
                  },
                  {
                    "ChangeOperation": "ignore",
                    "Route": {
                      "Network": "ff02::",
                      "PrefixLength": 32,
                      "Gateway": "fe80::a00:27ff:fe26:1f9",
                      "DeviceName": "vmk0"
                    }
                  }
                ]
              }
            }
          ],
          "OpaqueSwitch": null,
          "OpaqueNetwork": null,
          "NsxTransportNodeId": ""
        },
        "Vmotion": {
          "NetConfig": {
            "CandidateVnic": [
              {
                "Device": "vmk0",
                "Key": "VMotionConfig.vmotion.key-vim.host.VirtualNic-vmk0",
                "Portgroup": "Management Network",
                "Spec": {
                  "Ip": {
                    "Dhcp": false,
                    "IpAddress": "192.168.100.120",
                    "SubnetMask": "255.255.255.0",
                    "IpV6Config": {
                      "IpV6Address": [
                        {
                          "IpAddress": "fe80::a00:27ff:fe26:1f9",
                          "PrefixLength": 64,
                          "Origin": "other",
                          "DadState": "preferred",
                          "Lifetime": null,
                          "Operation": ""
                        },
                        {
                          "IpAddress": "2001:268:c00a:c109:a00:27ff:fe26:1f9",
                          "PrefixLength": 64,
                          "Origin": "linklayer",
                          "DadState": "preferred",
                          "Lifetime": null,
                          "Operation": ""
                        },
                        {
                          "IpAddress": "2001:268:c00a:c109:44f:4c8c:1fbf:2",
                          "PrefixLength": 64,
                          "Origin": "dhcp",
                          "DadState": "preferred",
                          "Lifetime": null,
                          "Operation": ""
                        }
                      ],
                      "AutoConfigurationEnabled": true,
                      "DhcpV6Enabled": false
                    }
                  },
                  "Mac": "08:00:27:26:01:f9",
                  "DistributedVirtualPort": null,
                  "Portgroup": "Management Network",
                  "Mtu": 1500,
                  "TsoEnabled": true,
                  "NetStackInstanceKey": "defaultTcpipStack",
                  "OpaqueNetwork": null,
                  "ExternalId": "",
                  "PinnedPnic": "",
                  "IpRouteSpec": null,
                  "SystemOwned": null
                },
                "Port": ""
              }
            ],
            "SelectedVnic": ""
          },
          "IpConfig": null
        },
        "VirtualNicManagerInfo": {
          "NetConfig": [
            {
              "NicType": "faultToleranceLogging",
              "MultiSelectAllowed": true,
              "CandidateVnic": [
                {
                  "Device": "vmk0",
                  "Key": "faultToleranceLogging.key-vim.host.VirtualNic-vmk0",
                  "Portgroup": "Management Network",
                  "Spec": {
                    "Ip": {
                      "Dhcp": false,
                      "IpAddress": "192.168.100.120",
                      "SubnetMask": "255.255.255.0",
                      "IpV6Config": {
                        "IpV6Address": [
                          {
                            "IpAddress": "fe80::a00:27ff:fe26:1f9",
                            "PrefixLength": 64,
                            "Origin": "other",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          },
                          {
                            "IpAddress": "2001:268:c00a:c109:a00:27ff:fe26:1f9",
                            "PrefixLength": 64,
                            "Origin": "linklayer",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          },
                          {
                            "IpAddress": "2001:268:c00a:c109:44f:4c8c:1fbf:2",
                            "PrefixLength": 64,
                            "Origin": "dhcp",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          }
                        ],
                        "AutoConfigurationEnabled": true,
                        "DhcpV6Enabled": false
                      }
                    },
                    "Mac": "08:00:27:26:01:f9",
                    "DistributedVirtualPort": null,
                    "Portgroup": "Management Network",
                    "Mtu": 1500,
                    "TsoEnabled": true,
                    "NetStackInstanceKey": "defaultTcpipStack",
                    "OpaqueNetwork": null,
                    "ExternalId": "",
                    "PinnedPnic": "",
                    "IpRouteSpec": null,
                    "SystemOwned": null
                  },
                  "Port": ""
                }
              ],
              "SelectedVnic": null
            },
            {
              "NicType": "management",
              "MultiSelectAllowed": true,
              "CandidateVnic": [
                {
                  "Device": "vmk0",
                  "Key": "management.key-vim.host.VirtualNic-vmk0",
                  "Portgroup": "Management Network",
                  "Spec": {
                    "Ip": {
                      "Dhcp": false,
                      "IpAddress": "192.168.100.120",
                      "SubnetMask": "255.255.255.0",
                      "IpV6Config": {
                        "IpV6Address": [
                          {
                            "IpAddress": "fe80::a00:27ff:fe26:1f9",
                            "PrefixLength": 64,
                            "Origin": "other",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          },
                          {
                            "IpAddress": "2001:268:c00a:c109:a00:27ff:fe26:1f9",
                            "PrefixLength": 64,
                            "Origin": "linklayer",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          },
                          {
                            "IpAddress": "2001:268:c00a:c109:44f:4c8c:1fbf:2",
                            "PrefixLength": 64,
                            "Origin": "dhcp",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          }
                        ],
                        "AutoConfigurationEnabled": true,
                        "DhcpV6Enabled": false
                      }
                    },
                    "Mac": "08:00:27:26:01:f9",
                    "DistributedVirtualPort": null,
                    "Portgroup": "Management Network",
                    "Mtu": 1500,
                    "TsoEnabled": true,
                    "NetStackInstanceKey": "defaultTcpipStack",
                    "OpaqueNetwork": null,
                    "ExternalId": "",
                    "PinnedPnic": "",
                    "IpRouteSpec": null,
                    "SystemOwned": null
                  },
                  "Port": ""
                }
              ],
              "SelectedVnic": [
                "management.key-vim.host.VirtualNic-vmk0"
              ]
            },
            {
              "NicType": "vSphereProvisioning",
              "MultiSelectAllowed": true,
              "CandidateVnic": [
                {
                  "Device": "vmk0",
                  "Key": "vSphereProvisioning.key-vim.host.VirtualNic-vmk0",
                  "Portgroup": "Management Network",
                  "Spec": {
                    "Ip": {
                      "Dhcp": false,
                      "IpAddress": "192.168.100.120",
                      "SubnetMask": "255.255.255.0",
                      "IpV6Config": {
                        "IpV6Address": [
                          {
                            "IpAddress": "fe80::a00:27ff:fe26:1f9",
                            "PrefixLength": 64,
                            "Origin": "other",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          },
                          {
                            "IpAddress": "2001:268:c00a:c109:a00:27ff:fe26:1f9",
                            "PrefixLength": 64,
                            "Origin": "linklayer",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          },
                          {
                            "IpAddress": "2001:268:c00a:c109:44f:4c8c:1fbf:2",
                            "PrefixLength": 64,
                            "Origin": "dhcp",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          }
                        ],
                        "AutoConfigurationEnabled": true,
                        "DhcpV6Enabled": false
                      }
                    },
                    "Mac": "08:00:27:26:01:f9",
                    "DistributedVirtualPort": null,
                    "Portgroup": "Management Network",
                    "Mtu": 1500,
                    "TsoEnabled": true,
                    "NetStackInstanceKey": "defaultTcpipStack",
                    "OpaqueNetwork": null,
                    "ExternalId": "",
                    "PinnedPnic": "",
                    "IpRouteSpec": null,
                    "SystemOwned": null
                  },
                  "Port": ""
                }
              ],
              "SelectedVnic": null
            },
            {
              "NicType": "vSphereReplication",
              "MultiSelectAllowed": true,
              "CandidateVnic": [
                {
                  "Device": "vmk0",
                  "Key": "vSphereReplication.key-vim.host.VirtualNic-vmk0",
                  "Portgroup": "Management Network",
                  "Spec": {
                    "Ip": {
                      "Dhcp": false,
                      "IpAddress": "192.168.100.120",
                      "SubnetMask": "255.255.255.0",
                      "IpV6Config": {
                        "IpV6Address": [
                          {
                            "IpAddress": "fe80::a00:27ff:fe26:1f9",
                            "PrefixLength": 64,
                            "Origin": "other",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          },
                          {
                            "IpAddress": "2001:268:c00a:c109:a00:27ff:fe26:1f9",
                            "PrefixLength": 64,
                            "Origin": "linklayer",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          },
                          {
                            "IpAddress": "2001:268:c00a:c109:44f:4c8c:1fbf:2",
                            "PrefixLength": 64,
                            "Origin": "dhcp",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          }
                        ],
                        "AutoConfigurationEnabled": true,
                        "DhcpV6Enabled": false
                      }
                    },
                    "Mac": "08:00:27:26:01:f9",
                    "DistributedVirtualPort": null,
                    "Portgroup": "Management Network",
                    "Mtu": 1500,
                    "TsoEnabled": true,
                    "NetStackInstanceKey": "defaultTcpipStack",
                    "OpaqueNetwork": null,
                    "ExternalId": "",
                    "PinnedPnic": "",
                    "IpRouteSpec": null,
                    "SystemOwned": null
                  },
                  "Port": ""
                }
              ],
              "SelectedVnic": null
            },
            {
              "NicType": "vSphereReplicationNFC",
              "MultiSelectAllowed": true,
              "CandidateVnic": [
                {
                  "Device": "vmk0",
                  "Key": "vSphereReplicationNFC.key-vim.host.VirtualNic-vmk0",
                  "Portgroup": "Management Network",
                  "Spec": {
                    "Ip": {
                      "Dhcp": false,
                      "IpAddress": "192.168.100.120",
                      "SubnetMask": "255.255.255.0",
                      "IpV6Config": {
                        "IpV6Address": [
                          {
                            "IpAddress": "fe80::a00:27ff:fe26:1f9",
                            "PrefixLength": 64,
                            "Origin": "other",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          },
                          {
                            "IpAddress": "2001:268:c00a:c109:a00:27ff:fe26:1f9",
                            "PrefixLength": 64,
                            "Origin": "linklayer",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          },
                          {
                            "IpAddress": "2001:268:c00a:c109:44f:4c8c:1fbf:2",
                            "PrefixLength": 64,
                            "Origin": "dhcp",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          }
                        ],
                        "AutoConfigurationEnabled": true,
                        "DhcpV6Enabled": false
                      }
                    },
                    "Mac": "08:00:27:26:01:f9",
                    "DistributedVirtualPort": null,
                    "Portgroup": "Management Network",
                    "Mtu": 1500,
                    "TsoEnabled": true,
                    "NetStackInstanceKey": "defaultTcpipStack",
                    "OpaqueNetwork": null,
                    "ExternalId": "",
                    "PinnedPnic": "",
                    "IpRouteSpec": null,
                    "SystemOwned": null
                  },
                  "Port": ""
                }
              ],
              "SelectedVnic": null
            },
            {
              "NicType": "vmotion",
              "MultiSelectAllowed": true,
              "CandidateVnic": [
                {
                  "Device": "vmk0",
                  "Key": "vmotion.key-vim.host.VirtualNic-vmk0",
                  "Portgroup": "Management Network",
                  "Spec": {
                    "Ip": {
                      "Dhcp": false,
                      "IpAddress": "192.168.100.120",
                      "SubnetMask": "255.255.255.0",
                      "IpV6Config": {
                        "IpV6Address": [
                          {
                            "IpAddress": "fe80::a00:27ff:fe26:1f9",
                            "PrefixLength": 64,
                            "Origin": "other",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          },
                          {
                            "IpAddress": "2001:268:c00a:c109:a00:27ff:fe26:1f9",
                            "PrefixLength": 64,
                            "Origin": "linklayer",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          },
                          {
                            "IpAddress": "2001:268:c00a:c109:44f:4c8c:1fbf:2",
                            "PrefixLength": 64,
                            "Origin": "dhcp",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          }
                        ],
                        "AutoConfigurationEnabled": true,
                        "DhcpV6Enabled": false
                      }
                    },
                    "Mac": "08:00:27:26:01:f9",
                    "DistributedVirtualPort": null,
                    "Portgroup": "Management Network",
                    "Mtu": 1500,
                    "TsoEnabled": true,
                    "NetStackInstanceKey": "defaultTcpipStack",
                    "OpaqueNetwork": null,
                    "ExternalId": "",
                    "PinnedPnic": "",
                    "IpRouteSpec": null,
                    "SystemOwned": null
                  },
                  "Port": ""
                }
              ],
              "SelectedVnic": null
            },
            {
              "NicType": "vsan",
              "MultiSelectAllowed": true,
              "CandidateVnic": [
                {
                  "Device": "vmk0",
                  "Key": "vsan.key-vim.host.VirtualNic-vmk0",
                  "Portgroup": "Management Network",
                  "Spec": {
                    "Ip": {
                      "Dhcp": false,
                      "IpAddress": "192.168.100.120",
                      "SubnetMask": "255.255.255.0",
                      "IpV6Config": {
                        "IpV6Address": [
                          {
                            "IpAddress": "fe80::a00:27ff:fe26:1f9",
                            "PrefixLength": 64,
                            "Origin": "other",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          },
                          {
                            "IpAddress": "2001:268:c00a:c109:a00:27ff:fe26:1f9",
                            "PrefixLength": 64,
                            "Origin": "linklayer",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          },
                          {
                            "IpAddress": "2001:268:c00a:c109:44f:4c8c:1fbf:2",
                            "PrefixLength": 64,
                            "Origin": "dhcp",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          }
                        ],
                        "AutoConfigurationEnabled": true,
                        "DhcpV6Enabled": false
                      }
                    },
                    "Mac": "08:00:27:26:01:f9",
                    "DistributedVirtualPort": null,
                    "Portgroup": "Management Network",
                    "Mtu": 1500,
                    "TsoEnabled": true,
                    "NetStackInstanceKey": "defaultTcpipStack",
                    "OpaqueNetwork": null,
                    "ExternalId": "",
                    "PinnedPnic": "",
                    "IpRouteSpec": null,
                    "SystemOwned": null
                  },
                  "Port": ""
                }
              ],
              "SelectedVnic": null
            },
            {
              "NicType": "vsanWitness",
              "MultiSelectAllowed": true,
              "CandidateVnic": [
                {
                  "Device": "vmk0",
                  "Key": "vsanWitness.key-vim.host.VirtualNic-vmk0",
                  "Portgroup": "Management Network",
                  "Spec": {
                    "Ip": {
                      "Dhcp": false,
                      "IpAddress": "192.168.100.120",
                      "SubnetMask": "255.255.255.0",
                      "IpV6Config": {
                        "IpV6Address": [
                          {
                            "IpAddress": "fe80::a00:27ff:fe26:1f9",
                            "PrefixLength": 64,
                            "Origin": "other",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          },
                          {
                            "IpAddress": "2001:268:c00a:c109:a00:27ff:fe26:1f9",
                            "PrefixLength": 64,
                            "Origin": "linklayer",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          },
                          {
                            "IpAddress": "2001:268:c00a:c109:44f:4c8c:1fbf:2",
                            "PrefixLength": 64,
                            "Origin": "dhcp",
                            "DadState": "preferred",
                            "Lifetime": null,
                            "Operation": ""
                          }
                        ],
                        "AutoConfigurationEnabled": true,
                        "DhcpV6Enabled": false
                      }
                    },
                    "Mac": "08:00:27:26:01:f9",
                    "DistributedVirtualPort": null,
                    "Portgroup": "Management Network",
                    "Mtu": 1500,
                    "TsoEnabled": true,
                    "NetStackInstanceKey": "defaultTcpipStack",
                    "OpaqueNetwork": null,
                    "ExternalId": "",
                    "PinnedPnic": "",
                    "IpRouteSpec": null,
                    "SystemOwned": null
                  },
                  "Port": ""
                }
              ],
              "SelectedVnic": null
            }
          ]
        },
        "Capabilities": {
          "CanSetPhysicalNicLinkSpeed": true,
          "SupportsNicTeaming": true,
          "NicTeamingPolicy": [
            "loadbalance_ip",
            "loadbalance_srcmac",
            "loadbalance_srcid",
            "failover_explicit"
          ],
          "SupportsVlan": true,
          "UsesServiceConsoleNic": false,
          "SupportsNetworkHints": true,
          "MaxPortGroupsPerVswitch": 0,
          "VswitchConfigSupported": true,
          "VnicConfigSupported": true,
          "IpRouteConfigSupported": true,
          "DnsConfigSupported": true,
          "DhcpOnVnicSupported": true,
          "IpV6Supported": true,
          "BackupNfcNiocSupported": null
        },
        "DatastoreCapabilities": {
          "NfsMountCreationRequired": true,
          "NfsMountCreationSupported": true,
          "LocalDatastoreSupported": false,
          "VmfsExtentExpansionSupported": true
        },
        "OffloadCapabilities": {
          "CsumOffload": true,
          "TcpSegmentation": true,
          "ZeroCopyXmit": true
        },
        "Service": {
          "Service": [
            {
              "Key": "DCUI",
              "Label": "Direct Console UI",
              "Required": false,
              "Uninstallable": false,
              "Running": true,
              "Ruleset": null,
              "Policy": "on",
              "SourcePackage": {
                "SourcePackageName": "esx-base",
                "Description": "This VIB contains all of the base functionality of vSphere ESXi."
              }
            },
            {
              "Key": "TSM",
              "Label": "ESXi Shell",
              "Required": false,
              "Uninstallable": false,
              "Running": true,
              "Ruleset": null,
              "Policy": "on",
              "SourcePackage": {
                "SourcePackageName": "esx-base",
                "Description": "This VIB contains all of the base functionality of vSphere ESXi."
              }
            },
            {
              "Key": "TSM-SSH",
              "Label": "SSH",
              "Required": false,
              "Uninstallable": false,
              "Running": true,
              "Ruleset": null,
              "Policy": "on",
              "SourcePackage": {
                "SourcePackageName": "esx-base",
                "Description": "This VIB contains all of the base functionality of vSphere ESXi."
              }
            },
            {
              "Key": "lbtd",
              "Label": "Load-Based Teaming Daemon",
              "Required": false,
              "Uninstallable": false,
              "Running": false,
              "Ruleset": null,
              "Policy": "on",
              "SourcePackage": {
                "SourcePackageName": "esx-base",
                "Description": "This VIB contains all of the base functionality of vSphere ESXi."
              }
            },
            {
              "Key": "lwsmd",
              "Label": "Active Directory Service",
              "Required": false,
              "Uninstallable": false,
              "Running": false,
              "Ruleset": null,
              "Policy": "off",
              "SourcePackage": {
                "SourcePackageName": "esx-base",
                "Description": "This VIB contains all of the base functionality of vSphere ESXi."
              }
            },
            {
              "Key": "ntpd",
              "Label": "NTP Daemon",
              "Required": false,
              "Uninstallable": false,
              "Running": false,
              "Ruleset": [
                "ntpClient"
              ],
              "Policy": "off",
              "SourcePackage": {
                "SourcePackageName": "esx-base",
                "Description": "This VIB contains all of the base functionality of vSphere ESXi."
              }
            },
            {
              "Key": "pcscd",
              "Label": "PC/SC Smart Card Daemon",
              "Required": false,
              "Uninstallable": false,
              "Running": false,
              "Ruleset": null,
              "Policy": "off",
              "SourcePackage": {
                "SourcePackageName": "esx-base",
                "Description": "This VIB contains all of the base functionality of vSphere ESXi."
              }
            },
            {
              "Key": "sfcbd-watchdog",
              "Label": "CIM Server",
              "Required": false,
              "Uninstallable": false,
              "Running": false,
              "Ruleset": [
                "CIMHttpServer",
                "CIMHttpsServer"
              ],
              "Policy": "on",
              "SourcePackage": {
                "SourcePackageName": "esx-base",
                "Description": "This VIB contains all of the base functionality of vSphere ESXi."
              }
            },
            {
              "Key": "snmpd",
              "Label": "SNMP Server",
              "Required": false,
              "Uninstallable": false,
              "Running": false,
              "Ruleset": [
                "snmp"
              ],
              "Policy": "on",
              "SourcePackage": {
                "SourcePackageName": "esx-base",
                "Description": "This VIB contains all of the base functionality of vSphere ESXi."
              }
            },
            {
              "Key": "vmsyslogd",
              "Label": "Syslog Server",
              "Required": true,
              "Uninstallable": false,
              "Running": true,
              "Ruleset": null,
              "Policy": "on",
              "SourcePackage": {
                "SourcePackageName": "esx-base",
                "Description": "This VIB contains all of the base functionality of vSphere ESXi."
              }
            },
            {
              "Key": "vpxa",
              "Label": "VMware vCenter Agent",
              "Required": false,
              "Uninstallable": false,
              "Running": false,
              "Ruleset": [
                "vpxHeartbeats"
              ],
              "Policy": "on",
              "SourcePackage": {
                "SourcePackageName": "esx-base",
                "Description": "This VIB contains all of the base functionality of vSphere ESXi."
              }
            },
            {
              "Key": "xorg",
              "Label": "X.Org Server",
              "Required": false,
              "Uninstallable": false,
              "Running": false,
              "Ruleset": null,
              "Policy": "on",
              "SourcePackage": {
                "SourcePackageName": "esx-xserver",
                "Description": "This VIB contains X Server used for virtual machine 3D hardware acceleration."
              }
            }
          ]
        },
        "Firewall": {
          "DefaultPolicy": {
            "IncomingBlocked": true,
            "OutgoingBlocked": true
          },
          "Ruleset": [
            {
              "Key": "CIMHttpServer",
              "Label": "CIM Server",
              "Required": false,
              "Rule": [
                {
                  "Port": 5988,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "sfcbd-watchdog",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "CIMHttpsServer",
              "Label": "CIM Secure Server",
              "Required": false,
              "Rule": [
                {
                  "Port": 5989,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "sfcbd-watchdog",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "CIMSLP",
              "Label": "CIM SLP",
              "Required": false,
              "Rule": [
                {
                  "Port": 427,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 427,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 427,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 427,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "DHCPv6",
              "Label": "DHCPv6",
              "Required": false,
              "Rule": [
                {
                  "Port": 547,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 546,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 547,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 546,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                }
              ],
              "Service": "",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "DVFilter",
              "Label": "DVFilter",
              "Required": false,
              "Rule": [
                {
                  "Port": 2222,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "DVSSync",
              "Label": "DVSSync",
              "Required": false,
              "Rule": [
                {
                  "Port": 8302,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 8301,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 8301,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 8302,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                }
              ],
              "Service": "",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "HBR",
              "Label": "HBR",
              "Required": false,
              "Rule": [
                {
                  "Port": 31031,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 44046,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "NFC",
              "Label": "NFC",
              "Required": false,
              "Rule": [
                {
                  "Port": 902,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 902,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "WOL",
              "Label": "WOL",
              "Required": false,
              "Rule": [
                {
                  "Port": 9,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                }
              ],
              "Service": "",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "activeDirectoryAll",
              "Label": "Active Directory All",
              "Required": false,
              "Rule": [
                {
                  "Port": 88,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 88,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 123,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 137,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 139,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 389,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 389,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 445,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 464,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 464,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 3268,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 7476,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 2020,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "cmmds",
              "Label": "vSAN Clustering Service",
              "Required": false,
              "Rule": [
                {
                  "Port": 12345,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 23451,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 12345,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 23451,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 12321,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 12321,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "dhcp",
              "Label": "DHCP Client",
              "Required": false,
              "Rule": [
                {
                  "Port": 68,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 68,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "src",
                  "Protocol": "udp"
                }
              ],
              "Service": "",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "dns",
              "Label": "DNS Client",
              "Required": false,
              "Rule": [
                {
                  "Port": 53,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 53,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "esxupdate",
              "Label": "esxupdate",
              "Required": false,
              "Rule": [
                {
                  "Port": 443,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "faultTolerance",
              "Label": "Fault Tolerance",
              "Required": false,
              "Rule": [
                {
                  "Port": 80,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 8300,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 8300,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "ftpClient",
              "Label": "FTP Client",
              "Required": false,
              "Rule": [
                {
                  "Port": 21,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 20,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "src",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "gdbserver",
              "Label": "gdbserver",
              "Required": false,
              "Rule": [
                {
                  "Port": 1000,
                  "EndPort": 9999,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 50000,
                  "EndPort": 50999,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "httpClient",
              "Label": "httpClient",
              "Required": false,
              "Rule": [
                {
                  "Port": 80,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 443,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "iSCSI",
              "Label": "Software iSCSI Client",
              "Required": false,
              "Rule": [
                {
                  "Port": 3260,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "iofiltervp",
              "Label": "iofiltervp",
              "Required": false,
              "Rule": [
                {
                  "Port": 9080,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "ipfam",
              "Label": "NSX Distributed Logical Router Service",
              "Required": false,
              "Rule": [
                {
                  "Port": 6999,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 6999,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "nfs41Client",
              "Label": "nfs41Client",
              "Required": false,
              "Rule": [
                {
                  "Port": 0,
                  "EndPort": 65535,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "nfsClient",
              "Label": "NFS Client",
              "Required": false,
              "Rule": [
                {
                  "Port": 0,
                  "EndPort": 65535,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "ntpClient",
              "Label": "NTP Client",
              "Required": false,
              "Rule": [
                {
                  "Port": 123,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                }
              ],
              "Service": "ntpd",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "pvrdma",
              "Label": "pvrdma",
              "Required": false,
              "Rule": [
                {
                  "Port": 28250,
                  "EndPort": 28761,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 28250,
                  "EndPort": 28761,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "rabbitmqproxy",
              "Label": "rabbitmqproxy",
              "Required": false,
              "Rule": [
                {
                  "Port": 5671,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "rdt",
              "Label": "vSAN Transport",
              "Required": false,
              "Rule": [
                {
                  "Port": 2233,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 2233,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "remoteSerialPort",
              "Label": "VM serial port connected over network",
              "Required": false,
              "Rule": [
                {
                  "Port": 0,
                  "EndPort": 65535,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 23,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 1024,
                  "EndPort": 65535,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "snmp",
              "Label": "SNMP Server",
              "Required": false,
              "Rule": [
                {
                  "Port": 161,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                }
              ],
              "Service": "snmpd",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "sshClient",
              "Label": "SSH Client",
              "Required": false,
              "Rule": [
                {
                  "Port": 22,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "sshServer",
              "Label": "SSH Server",
              "Required": true,
              "Rule": [
                {
                  "Port": 22,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "syslog",
              "Label": "syslog",
              "Required": false,
              "Rule": [
                {
                  "Port": 514,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 514,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 1514,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "updateManager",
              "Label": "vCenter Update Manager",
              "Required": false,
              "Rule": [
                {
                  "Port": 80,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 9000,
                  "EndPort": 9100,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "vMotion",
              "Label": "vMotion",
              "Required": false,
              "Rule": [
                {
                  "Port": 8000,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 8000,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "vSPC",
              "Label": "VM serial port connected to vSPC",
              "Required": false,
              "Rule": [
                {
                  "Port": 0,
                  "EndPort": 65535,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "vSphereClient",
              "Label": "vSphere Web Client",
              "Required": true,
              "Rule": [
                {
                  "Port": 902,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 443,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "vic-engine",
              "Label": "vic-engine",
              "Required": false,
              "Rule": [
                {
                  "Port": 2377,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "vit",
              "Label": "vit",
              "Required": false,
              "Rule": [
                {
                  "Port": 3260,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "vpxHeartbeats",
              "Label": "VMware vCenter Agent",
              "Required": false,
              "Rule": [
                {
                  "Port": 902,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                }
              ],
              "Service": "vpxa",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "vsanEncryption",
              "Label": "vsanEncryption",
              "Required": false,
              "Rule": [
                {
                  "Port": 0,
                  "EndPort": 65535,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "vsanhealth-unicasttest",
              "Label": "vsanhealth-unicasttest",
              "Required": false,
              "Rule": [
                {
                  "Port": 5201,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 5201,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "udp"
                },
                {
                  "Port": 5201,
                  "EndPort": 0,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                },
                {
                  "Port": 5201,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "vvold",
              "Label": "vvold",
              "Required": false,
              "Rule": [
                {
                  "Port": 0,
                  "EndPort": 65535,
                  "Direction": "outbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": false,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            },
            {
              "Key": "webAccess",
              "Label": "vSphere Web Access",
              "Required": false,
              "Rule": [
                {
                  "Port": 80,
                  "EndPort": 0,
                  "Direction": "inbound",
                  "PortType": "dst",
                  "Protocol": "tcp"
                }
              ],
              "Service": "",
              "Enabled": true,
              "AllowedHosts": {
                "IpAddress": null,
                "IpNetwork": null,
                "AllIp": true
              }
            }
          ]
        },
        "AutoStart": {
          "Defaults": {
            "Enabled": null,
            "StartDelay": 120,
            "StopDelay": 120,
            "WaitForHeartbeat": false,
            "StopAction": "PowerOff"
          },
          "PowerInfo": null
        },
        "ActiveDiagnosticPartition": {
          "StorageType": "directAttached",
          "DiagnosticType": "singleHost",
          "Slots": -15,
          "Id": {
            "DiskName": "t10.ATA_____VBOX_HARDDISK___________________________VB22cd9e3f2Df90775ba_",
            "Partition": 9
          }
        },
        "Option": [
          {
            "Key": "Annotations.WelcomeMessage",
            "Value": ""
          },
          {
            "Key": "BufferCache.FlushInterval",
            "Value": 30000
          },
          {
            "Key": "BufferCache.HardMaxDirty",
            "Value": 95
          },
          {
            "Key": "BufferCache.PerFileHardMaxDirty",
            "Value": 50
          },
          {
            "Key": "BufferCache.SoftMaxDirty",
            "Value": 15
          },
          {
            "Key": "CBRC.DCacheMemReserved",
            "Value": 400
          },
          {
            "Key": "CBRC.DCacheSize",
            "Value": 32768
          },
          {
            "Key": "CBRC.DigestJournalBootInterval",
            "Value": 10
          },
          {
            "Key": "CBRC.Enable",
            "Value": false
          },
          {
            "Key": "COW.COWMaxHeapSizeMB",
            "Value": 192
          },
          {
            "Key": "COW.COWMaxREPageCacheszMB",
            "Value": 256
          },
          {
            "Key": "COW.COWMinREPageCacheszMB",
            "Value": 0
          },
          {
            "Key": "COW.COWREPageCacheEviction",
            "Value": 1
          },
          {
            "Key": "Config.Defaults.security.host.ruissl",
            "Value": true
          },
          {
            "Key": "Config.Defaults.vGPU.consolidation",
            "Value": false
          },
          {
            "Key": "Config.Etc.issue",
            "Value": ""
          },
          {
            "Key": "Config.Etc.motd",
            "Value": "The time and date of this login have been sent to the system logs.\n\nWARNING:\n   All commands run on the ESXi shell are logged and may be included in\n   support bundles. Do not provide passwords directly on the command line.\n   Most tools can prompt for secrets or accept them from standard input.\n\n%1b[00mVMware offers supported, powerful system administration tools.  Please\nsee www.vmware.com/go/sysadmintools for details.\n\nThe ESXi Shell can be disabled by an administrative user. See the\nvSphere Security documentation for more information.\n"
          },
          {
            "Key": "Config.GlobalSettings.guest.commands.sharedPolicyRefCount",
            "Value": 0
          },
          {
            "Key": "Config.HostAgent.level[Hbrsvc].logLevel",
            "Value": ""
          },
          {
            "Key": "Config.HostAgent.level[Hostsvc].logLevel",
            "Value": ""
          },
          {
            "Key": "Config.HostAgent.level[Proxysvc].logLevel",
            "Value": ""
          },
          {
            "Key": "Config.HostAgent.level[Snmpsvc].logLevel",
            "Value": ""
          },
          {
            "Key": "Config.HostAgent.level[Statssvc].logLevel",
            "Value": ""
          },
          {
            "Key": "Config.HostAgent.level[Vcsvc].logLevel",
            "Value": ""
          },
          {
            "Key": "Config.HostAgent.level[Vimsvc].logLevel",
            "Value": ""
          },
          {
            "Key": "Config.HostAgent.level[Vmsvc].logLevel",
            "Value": ""
          },
          {
            "Key": "Config.HostAgent.log.level",
            "Value": "info"
          },
          {
            "Key": "Config.HostAgent.plugins.hostsvc.esxAdminsGroup",
            "Value": "ESX Admins"
          },
          {
            "Key": "Config.HostAgent.plugins.hostsvc.esxAdminsGroupAutoAdd",
            "Value": true
          },
          {
            "Key": "Config.HostAgent.plugins.hostsvc.esxAdminsGroupUpdateInterval",
            "Value": 1
          },
          {
            "Key": "Config.HostAgent.plugins.solo.enableMob",
            "Value": false
          },
          {
            "Key": "Config.HostAgent.plugins.solo.webServer.enableWebscriptLauncher",
            "Value": true
          },
          {
            "Key": "Config.HostAgent.plugins.vimsvc.authValidateInterval",
            "Value": 1440
          },
          {
            "Key": "Config.HostAgent.plugins.vimsvc.userSearch.maxResults",
            "Value": 100
          },
          {
            "Key": "Config.HostAgent.plugins.vimsvc.userSearch.maxTimeSeconds",
            "Value": 20
          },
          {
            "Key": "Config.HostAgent.plugins.vmsvc.enforceMaxRegisteredVms",
            "Value": true
          },
          {
            "Key": "Config.HostAgent.plugins.vmsvc.productLockerWatchInterval",
            "Value": 300
          },
          {
            "Key": "Config.HostAgent.ssl.keyStore.allowSelfSigned",
            "Value": false
          },
          {
            "Key": "Config.HostAgent.vmacore.soap.sessionTimeout",
            "Value": 30
          },
          {
            "Key": "Cpu.ActionLoadThreshold",
            "Value": 10
          },
          {
            "Key": "Cpu.AllowWideVsmp",
            "Value": 0
          },
          {
            "Key": "Cpu.BoundLagQuanta",
            "Value": 8
          },
          {
            "Key": "Cpu.CommRateThreshold",
            "Value": 500
          },
          {
            "Key": "Cpu.CoschedCostartThreshold",
            "Value": 2000
          },
          {
            "Key": "Cpu.CoschedCostopThreshold",
            "Value": 3000
          },
          {
            "Key": "Cpu.CoschedCrossCall",
            "Value": 1
          },
          {
            "Key": "Cpu.CoschedExclusiveAffinity",
            "Value": 0
          },
          {
            "Key": "Cpu.CoschedHandoffLLC",
            "Value": 1
          },
          {
            "Key": "Cpu.CoschedHandoffSkip",
            "Value": 10
          },
          {
            "Key": "Cpu.CoschedPollUsec",
            "Value": 1000
          },
          {
            "Key": "Cpu.CreditAgePeriod",
            "Value": 3000
          },
          {
            "Key": "Cpu.FairnessRebalancePcpus",
            "Value": 4
          },
          {
            "Key": "Cpu.HTRebalancePeriod",
            "Value": 5
          },
          {
            "Key": "Cpu.HTStolenAgeThreshold",
            "Value": 8
          },
          {
            "Key": "Cpu.HTWholeCoreThreshold",
            "Value": 800
          },
          {
            "Key": "Cpu.HostRebalancePeriod",
            "Value": 100
          },
          {
            "Key": "Cpu.L2RebalancePeriod",
            "Value": 10
          },
          {
            "Key": "Cpu.L3RebalancePeriod",
            "Value": 20
          },
          {
            "Key": "Cpu.LimitEnforcementThreshold",
            "Value": 200
          },
          {
            "Key": "Cpu.MaxSampleRateLg",
            "Value": 7
          },
          {
            "Key": "Cpu.MoveCurrentRunnerPcpus",
            "Value": 4
          },
          {
            "Key": "Cpu.NonTimerWakeupRate",
            "Value": 500
          },
          {
            "Key": "Cpu.PackageRebalancePeriod",
            "Value": 20
          },
          {
            "Key": "Cpu.PcpuMigrateIdlePcpus",
            "Value": 4
          },
          {
            "Key": "Cpu.Quantum",
            "Value": 200
          },
          {
            "Key": "Cpu.UseMwait",
            "Value": 2
          },
          {
            "Key": "Cpu.VMAdmitCheckPerVcpuMin",
            "Value": 1
          },
          {
            "Key": "Cpu.WakeupMigrateIdlePcpus",
            "Value": 4
          },
          {
            "Key": "DCUI.Access",
            "Value": "root"
          },
          {
            "Key": "DataMover.HardwareAcceleratedInit",
            "Value": 1
          },
          {
            "Key": "DataMover.HardwareAcceleratedMove",
            "Value": 1
          },
          {
            "Key": "DataMover.MaxHeapSize",
            "Value": 64
          },
          {
            "Key": "Digest.AlgoType",
            "Value": 1
          },
          {
            "Key": "Digest.BlockSize",
            "Value": 1
          },
          {
            "Key": "Digest.CollisionEnabled",
            "Value": 0
          },
          {
            "Key": "Digest.JournalCoverage",
            "Value": 8
          },
          {
            "Key": "Digest.UpdateOnClose",
            "Value": 0
          },
          {
            "Key": "DirentryCache.MaxDentryPerObj",
            "Value": 15000
          },
          {
            "Key": "Disk.AllowUsbClaimedAsSSD",
            "Value": 0
          },
          {
            "Key": "Disk.ApdTokenRetryCount",
            "Value": 25
          },
          {
            "Key": "Disk.AutoremoveOnPDL",
            "Value": 1
          },
          {
            "Key": "Disk.BandwidthCap",
            "Value": 4294967294
          },
          {
            "Key": "Disk.DelayOnBusy",
            "Value": 400
          },
          {
            "Key": "Disk.DeviceReclaimTime",
            "Value": 300
          },
          {
            "Key": "Disk.Disable4knSSD",
            "Value": 1
          },
          {
            "Key": "Disk.DisableVSCSIPollInBH",
            "Value": 1
          },
          {
            "Key": "Disk.DiskDelayPDLHelper",
            "Value": 10
          },
          {
            "Key": "Disk.DiskMaxIOSize",
            "Value": 32767
          },
          {
            "Key": "Disk.DiskReservationThreshold",
            "Value": 45
          },
          {
            "Key": "Disk.DiskRetryPeriod",
            "Value": 2000
          },
          {
            "Key": "Disk.DumpMaxRetries",
            "Value": 10
          },
          {
            "Key": "Disk.DumpPollDelay",
            "Value": 1000
          },
          {
            "Key": "Disk.DumpPollMaxRetries",
            "Value": 10000
          },
          {
            "Key": "Disk.EnableNaviReg",
            "Value": 1
          },
          {
            "Key": "Disk.FailDiskRegistration",
            "Value": 1
          },
          {
            "Key": "Disk.FastPathRestoreInterval",
            "Value": 100
          },
          {
            "Key": "Disk.IdleCredit",
            "Value": 32
          },
          {
            "Key": "Disk.MaxLUN",
            "Value": 1024
          },
          {
            "Key": "Disk.MaxNumIOIntervals",
            "Value": 1024
          },
          {
            "Key": "Disk.MaxResetLatency",
            "Value": 2000
          },
          {
            "Key": "Disk.NmpMaxCmdExtension",
            "Value": 0
          },
          {
            "Key": "Disk.PathEvalTime",
            "Value": 300
          },
          {
            "Key": "Disk.PreventVMFSOverwrite",
            "Value": 1
          },
          {
            "Key": "Disk.QFullSampleSize",
            "Value": 0
          },
          {
            "Key": "Disk.QFullThreshold",
            "Value": 8
          },
          {
            "Key": "Disk.ReqCallThreshold",
            "Value": 8
          },
          {
            "Key": "Disk.ResetLatency",
            "Value": 1000
          },
          {
            "Key": "Disk.ResetMaxRetries",
            "Value": 0
          },
          {
            "Key": "Disk.ResetOverdueLogPeriod",
            "Value": 60
          },
          {
            "Key": "Disk.ResetPeriod",
            "Value": 30
          },
          {
            "Key": "Disk.ResetThreadExpires",
            "Value": 1800
          },
          {
            "Key": "Disk.ResetThreadMax",
            "Value": 16
          },
          {
            "Key": "Disk.ResetThreadMin",
            "Value": 1
          },
          {
            "Key": "Disk.RetryUnitAttention",
            "Value": 1
          },
          {
            "Key": "Disk.ReturnCCForNoSpace",
            "Value": 0
          },
          {
            "Key": "Disk.SchedCostUnit",
            "Value": 32768
          },
          {
            "Key": "Disk.SchedQCleanupInterval",
            "Value": 300
          },
          {
            "Key": "Disk.SchedQControlSeqReqs",
            "Value": 128
          },
          {
            "Key": "Disk.SchedQControlVMSwitches",
            "Value": 6
          },
          {
            "Key": "Disk.SchedQPriorityPercentage",
            "Value": 80
          },
          {
            "Key": "Disk.SchedQuantum",
            "Value": 8
          },
          {
            "Key": "Disk.SchedReservationBurst",
            "Value": 1
          },
          {
            "Key": "Disk.SchedulerWithReservation",
            "Value": 1
          },
          {
            "Key": "Disk.SectorMaxDiff",
            "Value": 2000
          },
          {
            "Key": "Disk.SharesHigh",
            "Value": 2000
          },
          {
            "Key": "Disk.SharesLow",
            "Value": 500
          },
          {
            "Key": "Disk.SharesNormal",
            "Value": 1000
          },
          {
            "Key": "Disk.SllThrottleTime",
            "Value": 800
          },
          {
            "Key": "Disk.SupportSparseLUN",
            "Value": 1
          },
          {
            "Key": "Disk.ThroughputCap",
            "Value": 4294967294
          },
          {
            "Key": "Disk.UseDeviceReset",
            "Value": 1
          },
          {
            "Key": "Disk.UseIoPool",
            "Value": 0
          },
          {
            "Key": "Disk.UseLunReset",
            "Value": 1
          },
          {
            "Key": "Disk.UseReportLUN",
            "Value": 1
          },
          {
            "Key": "Disk.VSCSICoalesceCount",
            "Value": 1000
          },
          {
            "Key": "Disk.VSCSIEnablePreemption",
            "Value": 0
          },
          {
            "Key": "Disk.VSCSIPollPeriod",
            "Value": 1000
          },
          {
            "Key": "Disk.VSCSIResvCmdRetryInSecs",
            "Value": 1
          },
          {
            "Key": "Disk.VSCSIWriteSameBurstSize",
            "Value": 4
          },
          {
            "Key": "FSS.FSSLightWeightProbe",
            "Value": 1
          },
          {
            "Key": "FT.AckIntervalMax",
            "Value": 1000000
          },
          {
            "Key": "FT.AckIntervalMin",
            "Value": 0
          },
          {
            "Key": "FT.BackupConnectTimeout",
            "Value": 8000
          },
          {
            "Key": "FT.BackupExtraTimeout",
            "Value": 100
          },
          {
            "Key": "FT.BadExecLatency",
            "Value": 800
          },
          {
            "Key": "FT.BindToVmknic",
            "Value": 0
          },
          {
            "Key": "FT.ChargeVMXForFlush",
            "Value": 1
          },
          {
            "Key": "FT.CheckFCPathState",
            "Value": 1
          },
          {
            "Key": "FT.CheckForProgress",
            "Value": 0
          },
          {
            "Key": "FT.CoreDumpNoProgressMS",
            "Value": 0
          },
          {
            "Key": "FT.ExecLatencyKill",
            "Value": 0
          },
          {
            "Key": "FT.ExtraLogTimeout",
            "Value": 10000
          },
          {
            "Key": "FT.FTCptConcurrentSend",
            "Value": 1
          },
          {
            "Key": "FT.FTCptDelayCheckpoint",
            "Value": 2
          },
          {
            "Key": "FT.FTCptDiffCap",
            "Value": 100
          },
          {
            "Key": "FT.FTCptDiffThreads",
            "Value": 6
          },
          {
            "Key": "FT.FTCptDisableFailover",
            "Value": 0
          },
          {
            "Key": "FT.FTCptDiskWriteTimeout",
            "Value": 3000
          },
          {
            "Key": "FT.FTCptDontDelayPkts",
            "Value": 0
          },
          {
            "Key": "FT.FTCptDontSendPages",
            "Value": 0
          },
          {
            "Key": "FT.FTCptEpochList",
            "Value": "5,10,20,100"
          },
          {
            "Key": "FT.FTCptEpochSample",
            "Value": 1000
          },
          {
            "Key": "FT.FTCptEpochWait",
            "Value": 8000
          },
          {
            "Key": "FT.FTCptIORetryExtraInterval",
            "Value": 200
          },
          {
            "Key": "FT.FTCptIORetryInterval",
            "Value": 10
          },
          {
            "Key": "FT.FTCptIORetryTimes",
            "Value": 15
          },
          {
            "Key": "FT.FTCptLogTimeout",
            "Value": 8000
          },
          {
            "Key": "FT.FTCptMaxPktsDelay",
            "Value": 0
          },
          {
            "Key": "FT.FTCptMinInterval",
            "Value": 4
          },
          {
            "Key": "FT.FTCptNetDelayNoCpt",
            "Value": 0
          },
          {
            "Key": "FT.FTCptNumConnections",
            "Value": 2
          },
          {
            "Key": "FT.FTCptNumaIndex",
            "Value": 0
          },
          {
            "Key": "FT.FTCptPagePolicy",
            "Value": 65538
          },
          {
            "Key": "FT.FTCptPoweroff",
            "Value": 0
          },
          {
            "Key": "FT.FTCptRcvBufSize",
            "Value": 562140
          },
          {
            "Key": "FT.FTCptSndBufSize",
            "Value": 562140
          },
          {
            "Key": "FT.FTCptStartTimeout",
            "Value": 90000
          },
          {
            "Key": "FT.FTCptStatsInterval",
            "Value": 30
          },
          {
            "Key": "FT.FTCptThreadPolicy",
            "Value": 65536
          },
          {
            "Key": "FT.FTCptVcpuMinUsage",
            "Value": 40
          },
          {
            "Key": "FT.FTCptWaitOnSocket",
            "Value": 1
          },
          {
            "Key": "FT.FillAffinity",
            "Value": 1
          },
          {
            "Key": "FT.FillWorldlet",
            "Value": 1
          },
          {
            "Key": "FT.FlushReservationMax",
            "Value": 25
          },
          {
            "Key": "FT.FlushReservationMin",
            "Value": 5
          },
          {
            "Key": "FT.FlushSleep",
            "Value": 0
          },
          {
            "Key": "FT.FlushWorldlet",
            "Value": 1
          },
          {
            "Key": "FT.GlobalFlushWorld",
            "Value": 0
          },
          {
            "Key": "FT.GoodExecLatency",
            "Value": 200
          },
          {
            "Key": "FT.HeartbeatCount",
            "Value": 10
          },
          {
            "Key": "FT.HostTimeout",
            "Value": 2000
          },
          {
            "Key": "FT.IORetryExtraInterval",
            "Value": 200
          },
          {
            "Key": "FT.IORetryInterval",
            "Value": 10
          },
          {
            "Key": "FT.IORetryTimes",
            "Value": 15
          },
          {
            "Key": "FT.LogBufferStallSleep",
            "Value": 1
          },
          {
            "Key": "FT.LogTimeout",
            "Value": 8000
          },
          {
            "Key": "FT.LongFlushDebugMS",
            "Value": 500
          },
          {
            "Key": "FT.MaxFlushInterval",
            "Value": 0
          },
          {
            "Key": "FT.MinWriteSize",
            "Value": 0
          },
          {
            "Key": "FT.NoWaitOnSocket",
            "Value": 0
          },
          {
            "Key": "FT.PanicNoProgressMS",
            "Value": 0
          },
          {
            "Key": "FT.PrimaryConnectTimeout",
            "Value": 8000
          },
          {
            "Key": "FT.ShortFlushDebugMS",
            "Value": 100
          },
          {
            "Key": "FT.TCPNoDelayBackup",
            "Value": 1
          },
          {
            "Key": "FT.TCPNoDelayPrimary",
            "Value": 1
          },
          {
            "Key": "FT.TCPPersistTimer",
            "Value": 500
          },
          {
            "Key": "FT.TCPRcvBufSize",
            "Value": 131072
          },
          {
            "Key": "FT.TCPSndBufSize",
            "Value": 131072
          },
          {
            "Key": "FT.UseHostMonitor",
            "Value": 0
          },
          {
            "Key": "FT.Vmknic",
            "Value": ""
          },
          {
            "Key": "FT.XmitSyncQueueLen",
            "Value": 64
          },
          {
            "Key": "FT.adjDownInt",
            "Value": 10
          },
          {
            "Key": "FT.adjDownPct",
            "Value": 10
          },
          {
            "Key": "FT.adjUpInt",
            "Value": 200
          },
          {
            "Key": "FT.adjUpPct",
            "Value": 10
          },
          {
            "Key": "FT.execLatExtra",
            "Value": 500
          },
          {
            "Key": "FT.maxLowerBound",
            "Value": 20
          },
          {
            "Key": "FT.slowdownPctMax",
            "Value": 60
          },
          {
            "Key": "FT.slowdownTimeMax",
            "Value": 600
          },
          {
            "Key": "HBR.ChecksumIoSize",
            "Value": 8
          },
          {
            "Key": "HBR.ChecksumMaxIo",
            "Value": 8
          },
          {
            "Key": "HBR.ChecksumPerSlice",
            "Value": 2
          },
          {
            "Key": "HBR.ChecksumRegionSize",
            "Value": 256
          },
          {
            "Key": "HBR.ChecksumUseAllocInfo",
            "Value": 1
          },
          {
            "Key": "HBR.ChecksumUseChecksumInfo",
            "Value": 1
          },
          {
            "Key": "HBR.ChecksumZoneSize",
            "Value": 32768
          },
          {
            "Key": "HBR.CopySnapDiskMaxExtentCount",
            "Value": 16
          },
          {
            "Key": "HBR.CopySnapFidHashBuckets",
            "Value": 256
          },
          {
            "Key": "HBR.DemandlogCompletedHashBuckets",
            "Value": 8
          },
          {
            "Key": "HBR.DemandlogExtentHashBuckets",
            "Value": 512
          },
          {
            "Key": "HBR.DemandlogIoTimeoutSecs",
            "Value": 120
          },
          {
            "Key": "HBR.DemandlogReadRetries",
            "Value": 20
          },
          {
            "Key": "HBR.DemandlogRetryDelayMs",
            "Value": 10
          },
          {
            "Key": "HBR.DemandlogSendHashBuckets",
            "Value": 8
          },
          {
            "Key": "HBR.DemandlogTransferIoSize",
            "Value": 8
          },
          {
            "Key": "HBR.DemandlogTransferMaxCompletion",
            "Value": 512
          },
          {
            "Key": "HBR.DemandlogTransferMaxIo",
            "Value": 32
          },
          {
            "Key": "HBR.DemandlogTransferMaxNetwork",
            "Value": 64
          },
          {
            "Key": "HBR.DemandlogTransferPerSlice",
            "Value": 16
          },
          {
            "Key": "HBR.DemandlogWriteRetries",
            "Value": 20
          },
          {
            "Key": "HBR.DisableChecksumOffload",
            "Value": 0
          },
          {
            "Key": "HBR.DisconnectedEventDelayMs",
            "Value": 60000
          },
          {
            "Key": "HBR.ErrThrottleChecksumIO",
            "Value": 1
          },
          {
            "Key": "HBR.ErrThrottleDceRead",
            "Value": 1
          },
          {
            "Key": "HBR.HbrBitmapAllocTimeoutMS",
            "Value": 3000
          },
          {
            "Key": "HBR.HbrBitmapVMMaxStorageGB",
            "Value": 65536
          },
          {
            "Key": "HBR.HbrBitmapVMMinStorageGB",
            "Value": 500
          },
          {
            "Key": "HBR.HbrDemandLogIOPerVM",
            "Value": 64
          },
          {
            "Key": "HBR.HbrDisableNetCompression",
            "Value": 1
          },
          {
            "Key": "HBR.HbrLowerExtentBreakGB",
            "Value": 8192
          },
          {
            "Key": "HBR.HbrLowerExtentSizeKB",
            "Value": 16
          },
          {
            "Key": "HBR.HbrMaxExtentSizeKB",
            "Value": 64
          },
          {
            "Key": "HBR.HbrMaxGuestXferWhileDeltaMB",
            "Value": 1024
          },
          {
            "Key": "HBR.HbrMaxUnmapExtents",
            "Value": 10
          },
          {
            "Key": "HBR.HbrMaxUnmapsInFlight",
            "Value": 128
          },
          {
            "Key": "HBR.HbrMinExtentBreakGB",
            "Value": 2048
          },
          {
            "Key": "HBR.HbrMinExtentSizeKB",
            "Value": 8
          },
          {
            "Key": "HBR.HbrOptimizeFullSync",
            "Value": 1
          },
          {
            "Key": "HBR.HbrResourceHeapPerVMSizeKB",
            "Value": 128
          },
          {
            "Key": "HBR.HbrResourceHeapSizeMB",
            "Value": 3
          },
          {
            "Key": "HBR.HbrResourceHeapUtilization",
            "Value": 95
          },
          {
            "Key": "HBR.HbrResourceMaxCompletionContexts",
            "Value": 8192
          },
          {
            "Key": "HBR.HbrResourceMaxDiskContexts",
            "Value": 512
          },
          {
            "Key": "HBR.HbrRuntimeHeapMaxBaseMB",
            "Value": 1
          },
          {
            "Key": "HBR.HbrRuntimeHeapMinBaseMB",
            "Value": 1
          },
          {
            "Key": "HBR.HbrStaticHeapMaxBaseMB",
            "Value": 1
          },
          {
            "Key": "HBR.HbrStaticHeapMinBaseMB",
            "Value": 1
          },
          {
            "Key": "HBR.HbrUpperExtentBreakGB",
            "Value": 32768
          },
          {
            "Key": "HBR.HbrUpperExtentSizeKB",
            "Value": 32
          },
          {
            "Key": "HBR.HelperQueueMaxRequests",
            "Value": 8192
          },
          {
            "Key": "HBR.HelperQueueMaxWorlds",
            "Value": 8
          },
          {
            "Key": "HBR.LocalReadIoTimeoutSecs",
            "Value": 120
          },
          {
            "Key": "HBR.MigrateFlushTimerSecs",
            "Value": 3
          },
          {
            "Key": "HBR.NetworkUseCubic",
            "Value": 1
          },
          {
            "Key": "HBR.NetworkerRecvHashBuckets",
            "Value": 64
          },
          {
            "Key": "HBR.OpportunisticBlockListSize",
            "Value": 4000
          },
          {
            "Key": "HBR.ProgressReportIntervalMs",
            "Value": 5000
          },
          {
            "Key": "HBR.PsfIoTimeoutSecs",
            "Value": 300
          },
          {
            "Key": "HBR.ReconnectFailureDelaySecs",
            "Value": 10
          },
          {
            "Key": "HBR.ReconnectMaxDelaySecs",
            "Value": 90
          },
          {
            "Key": "HBR.ResourceServerHashBuckets",
            "Value": 8
          },
          {
            "Key": "HBR.RetryMaxDelaySecs",
            "Value": 60
          },
          {
            "Key": "HBR.RetryMinDelaySecs",
            "Value": 1
          },
          {
            "Key": "HBR.SyncTransferRetrySleepSecs",
            "Value": 5
          },
          {
            "Key": "HBR.TransferDiskMaxCompletion",
            "Value": 512
          },
          {
            "Key": "HBR.TransferDiskMaxIo",
            "Value": 32
          },
          {
            "Key": "HBR.TransferDiskMaxNetwork",
            "Value": 64
          },
          {
            "Key": "HBR.TransferDiskPerSlice",
            "Value": 16
          },
          {
            "Key": "HBR.TransferFileExtentSize",
            "Value": 8192
          },
          {
            "Key": "HBR.TransferMaxContExtents",
            "Value": 8
          },
          {
            "Key": "HBR.WireChecksum",
            "Value": 1
          },
          {
            "Key": "HBR.XferBitmapCheckIntervalSecs",
            "Value": 10
          },
          {
            "Key": "ISCSI.CloseIscsiConnOnTaskMgmtFailure",
            "Value": 1
          },
          {
            "Key": "ISCSI.MaxIoSizeKB",
            "Value": 128
          },
          {
            "Key": "Irq.BestVcpuRouting",
            "Value": 0
          },
          {
            "Key": "Irq.IRQActionAffinityWeight",
            "Value": 5
          },
          {
            "Key": "Irq.IRQAvoidExclusive",
            "Value": 1
          },
          {
            "Key": "Irq.IRQBHConflictWeight",
            "Value": 5
          },
          {
            "Key": "Irq.IRQRebalancePeriod",
            "Value": 50
          },
          {
            "Key": "Irq.IRQVcpuConflictWeight",
            "Value": 3
          },
          {
            "Key": "LPage.LPageAlwaysTryForNPT",
            "Value": 1
          },
          {
            "Key": "LPage.LPageDefragEnable",
            "Value": 1
          },
          {
            "Key": "LPage.LPageMarkLowNodes",
            "Value": 1
          },
          {
            "Key": "LPage.MaxSharedPages",
            "Value": 510
          },
          {
            "Key": "LPage.MaxSwappedPagesInitVal",
            "Value": 10
          },
          {
            "Key": "LPage.freePagesThresholdForRemote",
            "Value": 2048
          },
          {
            "Key": "LSOM.blkAttrCacheSizePercent",
            "Value": 0
          },
          {
            "Key": "LSOM.lsomPlogEnableDeadmanTimer",
            "Value": 1
          },
          {
            "Key": "Mem.AllocGuestLargePage",
            "Value": 1
          },
          {
            "Key": "Mem.CtlMaxPercent",
            "Value": 65
          },
          {
            "Key": "Mem.IdleTax",
            "Value": 75
          },
          {
            "Key": "Mem.IdleTaxType",
            "Value": 1
          },
          {
            "Key": "Mem.MemDefragClientsPerDir",
            "Value": 2
          },
          {
            "Key": "Mem.MemEagerZero",
            "Value": 0
          },
          {
            "Key": "Mem.MemMinFreePct",
            "Value": 0
          },
          {
            "Key": "Mem.MemZipEnable",
            "Value": 1
          },
          {
            "Key": "Mem.MemZipMaxAllocPct",
            "Value": 50
          },
          {
            "Key": "Mem.MemZipMaxPct",
            "Value": 10
          },
          {
            "Key": "Mem.SampleActivePctMin",
            "Value": 1
          },
          {
            "Key": "Mem.SampleDirtiedPctMin",
            "Value": 0
          },
          {
            "Key": "Mem.ShareForceSalting",
            "Value": 2
          },
          {
            "Key": "Mem.ShareRateMax",
            "Value": 1024
          },
          {
            "Key": "Mem.ShareScanGHz",
            "Value": 4
          },
          {
            "Key": "Mem.ShareScanTime",
            "Value": 60
          },
          {
            "Key": "Mem.VMOverheadGrowthLimit",
            "Value": 4294967295
          },
          {
            "Key": "Migrate.AutoBindVmknic",
            "Value": 1
          },
          {
            "Key": "Migrate.BindToVmknic",
            "Value": 3
          },
          {
            "Key": "Migrate.CptCacheMaxSizeMB",
            "Value": 544
          },
          {
            "Key": "Migrate.DebugChecksumMismatch",
            "Value": 0
          },
          {
            "Key": "Migrate.DetectZeroPages",
            "Value": 1
          },
          {
            "Key": "Migrate.DisableResumeDuringPageIn",
            "Value": 0
          },
          {
            "Key": "Migrate.DiskOpsChunkSize",
            "Value": 131072
          },
          {
            "Key": "Migrate.DiskOpsEnabled",
            "Value": 0
          },
          {
            "Key": "Migrate.DiskOpsMaxRetries",
            "Value": 20
          },
          {
            "Key": "Migrate.DiskOpsStreamChunks",
            "Value": 40
          },
          {
            "Key": "Migrate.Enabled",
            "Value": 1
          },
          {
            "Key": "Migrate.GetPageSysAlertThresholdMS",
            "Value": 10000
          },
          {
            "Key": "Migrate.LowBandwidthSysAlertThreshold",
            "Value": 0
          },
          {
            "Key": "Migrate.LowMemWaitSysAlertThresholdMS",
            "Value": 10000
          },
          {
            "Key": "Migrate.MigrateCpuMinPctDefault",
            "Value": 30
          },
          {
            "Key": "Migrate.MigrateCpuPctPerGb",
            "Value": 10
          },
          {
            "Key": "Migrate.MigrateCpuSharesHighPriority",
            "Value": 60000
          },
          {
            "Key": "Migrate.MigrateCpuSharesRegular",
            "Value": 30000
          },
          {
            "Key": "Migrate.MonActionWaitSysAlertThresholdMS",
            "Value": 2000
          },
          {
            "Key": "Migrate.NetExpectedLineRateMBps",
            "Value": 133
          },
          {
            "Key": "Migrate.NetLatencyModeThreshold",
            "Value": 4
          },
          {
            "Key": "Migrate.NetTimeout",
            "Value": 20
          },
          {
            "Key": "Migrate.OutstandingReadKBMax",
            "Value": 128
          },
          {
            "Key": "Migrate.PanicOnChecksumMismatch",
            "Value": 0
          },
          {
            "Key": "Migrate.PreCopyCountDelay",
            "Value": 10
          },
          {
            "Key": "Migrate.PreCopyMinProgressPerc",
            "Value": 130
          },
          {
            "Key": "Migrate.PreCopyPagesPerSend",
            "Value": 32
          },
          {
            "Key": "Migrate.PreCopySwitchoverTimeGoal",
            "Value": 500
          },
          {
            "Key": "Migrate.PreallocLPages",
            "Value": 1
          },
          {
            "Key": "Migrate.ProhibitInstantClone",
            "Value": 0
          },
          {
            "Key": "Migrate.RcvBufSize",
            "Value": 562540
          },
          {
            "Key": "Migrate.RdpiTransitionTimeMs",
            "Value": 1
          },
          {
            "Key": "Migrate.SdpsDynamicDelaySec",
            "Value": 30
          },
          {
            "Key": "Migrate.SdpsEnabled",
            "Value": 2
          },
          {
            "Key": "Migrate.SdpsTargetRate",
            "Value": 500
          },
          {
            "Key": "Migrate.SndBufSize",
            "Value": 562540
          },
          {
            "Key": "Migrate.TSMaster",
            "Value": 0
          },
          {
            "Key": "Migrate.TcpTsoDeferTx",
            "Value": 0
          },
          {
            "Key": "Migrate.TryToUseDefaultHeap",
            "Value": 1
          },
          {
            "Key": "Migrate.VASpaceReserveCount",
            "Value": 64
          },
          {
            "Key": "Migrate.VASpaceReserveSize",
            "Value": 2048
          },
          {
            "Key": "Migrate.VMotionLatencySensitivity",
            "Value": 1
          },
          {
            "Key": "Migrate.VMotionResolveSwapType",
            "Value": 1
          },
          {
            "Key": "Migrate.VMotionStreamDisable",
            "Value": 0
          },
          {
            "Key": "Migrate.VMotionStreamHelpers",
            "Value": 0
          },
          {
            "Key": "Migrate.Vmknic",
            "Value": ""
          },
          {
            "Key": "Misc.APDHandlingEnable",
            "Value": 1
          },
          {
            "Key": "Misc.APDTimeout",
            "Value": 140
          },
          {
            "Key": "Misc.BHTimeout",
            "Value": 0
          },
          {
            "Key": "Misc.BhTimeBound",
            "Value": 2000
          },
          {
            "Key": "Misc.BlueScreenTimeout",
            "Value": 0
          },
          {
            "Key": "Misc.ConsolePort",
            "Value": "none"
          },
          {
            "Key": "Misc.DebugBuddyEnable",
            "Value": 0
          },
          {
            "Key": "Misc.DebugLogToSerial",
            "Value": 0
          },
          {
            "Key": "Misc.DefaultHardwareVersion",
            "Value": ""
          },
          {
            "Key": "Misc.EnableHighDMA",
            "Value": 1
          },
          {
            "Key": "Misc.EnablePSPLatencyPolicy",
            "Value": 1
          },
          {
            "Key": "Misc.GDBPort",
            "Value": "none"
          },
          {
            "Key": "Misc.GuestLibAllowHostInfo",
            "Value": 0
          },
          {
            "Key": "Misc.HeapMgrGuardPages",
            "Value": 1
          },
          {
            "Key": "Misc.HeapPanicDestroyNonEmpty",
            "Value": 0
          },
          {
            "Key": "Misc.HeartbeatInterval",
            "Value": 1000
          },
          {
            "Key": "Misc.HeartbeatPanicTimeout",
            "Value": 14
          },
          {
            "Key": "Misc.HeartbeatTimeout",
            "Value": 7
          },
          {
            "Key": "Misc.HordeEnabled",
            "Value": 0
          },
          {
            "Key": "Misc.IntTimeout",
            "Value": 0
          },
          {
            "Key": "Misc.IoFilterWatchdogTimeout",
            "Value": 120
          },
          {
            "Key": "Misc.LogDumpShutdownTimeout",
            "Value": 180
          },
          {
            "Key": "Misc.LogPort",
            "Value": "none"
          },
          {
            "Key": "Misc.LogTimestampUptime",
            "Value": 0
          },
          {
            "Key": "Misc.LogToFile",
            "Value": 1
          },
          {
            "Key": "Misc.LogToSerial",
            "Value": 1
          },
          {
            "Key": "Misc.LogWldPrefix",
            "Value": 1
          },
          {
            "Key": "Misc.MCEMonitorInterval",
            "Value": 250
          },
          {
            "Key": "Misc.MinimalPanic",
            "Value": 0
          },
          {
            "Key": "Misc.NMILint1IntAction",
            "Value": 0
          },
          {
            "Key": "Misc.PSPDeactivateFlakyPath",
            "Value": 1
          },
          {
            "Key": "Misc.PanicLogToSerial",
            "Value": 0
          },
          {
            "Key": "Misc.PowerButton",
            "Value": 1
          },
          {
            "Key": "Misc.PowerOffEnable",
            "Value": 1
          },
          {
            "Key": "Misc.PreferredHostName",
            "Value": ""
          },
          {
            "Key": "Misc.ProcVerbose",
            "Value": ""
          },
          {
            "Key": "Misc.SIOControlFlag1",
            "Value": 0
          },
          {
            "Key": "Misc.SIOControlFlag2",
            "Value": 0
          },
          {
            "Key": "Misc.SIOControlLoglevel",
            "Value": 0
          },
          {
            "Key": "Misc.SIOControlOptions",
            "Value": ""
          },
          {
            "Key": "Misc.ScreenSaverDelay",
            "Value": 0
          },
          {
            "Key": "Misc.ShaperStatsEnabled",
            "Value": 1
          },
          {
            "Key": "Misc.ShellPort",
            "Value": "none"
          },
          {
            "Key": "Misc.TimerMaxHardPeriod",
            "Value": 500000
          },
          {
            "Key": "Misc.TimerTolerance",
            "Value": 2000
          },
          {
            "Key": "Misc.UsbArbitratorAutoStartDisabled",
            "Value": 0
          },
          {
            "Key": "Misc.UserDuctDynBufferSize",
            "Value": 16384
          },
          {
            "Key": "Misc.UserSocketUnixMaxBufferSize",
            "Value": 65536
          },
          {
            "Key": "Misc.WatchdogBacktrace",
            "Value": 0
          },
          {
            "Key": "Misc.WorldletActivationUS",
            "Value": 500
          },
          {
            "Key": "Misc.WorldletActivationsLimit",
            "Value": 8
          },
          {
            "Key": "Misc.WorldletGreedySampleMCycles",
            "Value": 10
          },
          {
            "Key": "Misc.WorldletGreedySampleRun",
            "Value": 256
          },
          {
            "Key": "Misc.WorldletIRQPenalty",
            "Value": 10
          },
          {
            "Key": "Misc.WorldletLoadThreshold",
            "Value": 90
          },
          {
            "Key": "Misc.WorldletLoadType",
            "Value": "medium"
          },
          {
            "Key": "Misc.WorldletLocalityBonus",
            "Value": 10
          },
          {
            "Key": "Misc.WorldletLoosePenalty",
            "Value": 30
          },
          {
            "Key": "Misc.WorldletMigOverheadLLC",
            "Value": 4
          },
          {
            "Key": "Misc.WorldletMigOverheadRemote",
            "Value": 16
          },
          {
            "Key": "Misc.WorldletPreemptOverhead",
            "Value": 30
          },
          {
            "Key": "Misc.WorldletRemoteActivateOverhead",
            "Value": 0
          },
          {
            "Key": "Misc.WorldletWorldOverheadLLC",
            "Value": 0
          },
          {
            "Key": "Misc.WorldletWorldOverheadRemote",
            "Value": 10
          },
          {
            "Key": "Misc.vmmDisableL1DFlush",
            "Value": 0
          },
          {
            "Key": "Misc.vsanWitnessVirtualAppliance",
            "Value": 0
          },
          {
            "Key": "Misc.DsNsMgrTimeout",
            "Value": 1200000
          },
          {
            "Key": "Misc.HostAgentUpdateLevel",
            "Value": "3"
          },
          {
            "Key": "Misc.MetadataUpdateTimeoutMsec",
            "Value": 30000
          },
          {
            "Key": "NFS.ApdStartCount",
            "Value": 3
          },
          {
            "Key": "NFS.DiskFileLockUpdateFreq",
            "Value": 10
          },
          {
            "Key": "NFS.HeartbeatDelta",
            "Value": 5
          },
          {
            "Key": "NFS.HeartbeatFrequency",
            "Value": 12
          },
          {
            "Key": "NFS.HeartbeatMaxFailures",
            "Value": 10
          },
          {
            "Key": "NFS.HeartbeatTimeout",
            "Value": 5
          },
          {
            "Key": "NFS.LockBreakTimeout",
            "Value": 10
          },
          {
            "Key": "NFS.LockRenewMaxFailureNumber",
            "Value": 3
          },
          {
            "Key": "NFS.LockSWMRTimeout",
            "Value": 10
          },
          {
            "Key": "NFS.LockSharedTimeout",
            "Value": 0
          },
          {
            "Key": "NFS.LockUpdateTimeout",
            "Value": 5
          },
          {
            "Key": "NFS.LogNfsStat3",
            "Value": 0
          },
          {
            "Key": "NFS.MaxQueueDepth",
            "Value": 4294967295
          },
          {
            "Key": "NFS.MaxVolumes",
            "Value": 8
          },
          {
            "Key": "NFS.ReceiveBufferSize",
            "Value": 1024
          },
          {
            "Key": "NFS.SendBufferSize",
            "Value": 1024
          },
          {
            "Key": "NFS.SyncRetries",
            "Value": 25
          },
          {
            "Key": "NFS.VolumeRemountFrequency",
            "Value": 30
          },
          {
            "Key": "NFS41.EOSDelay",
            "Value": 30
          },
          {
            "Key": "NFS41.IOTaskRetry",
            "Value": 25
          },
          {
            "Key": "NFS41.MaxRead",
            "Value": 4294967295
          },
          {
            "Key": "NFS41.MaxVolumes",
            "Value": 8
          },
          {
            "Key": "NFS41.MaxWrite",
            "Value": 4294967295
          },
          {
            "Key": "NFS41.MountTimeout",
            "Value": 30
          },
          {
            "Key": "NFS41.RecvBufSize",
            "Value": 1024
          },
          {
            "Key": "NFS41.SendBufSize",
            "Value": 1024
          },
          {
            "Key": "Net.AdvertisementDuration",
            "Value": 60
          },
          {
            "Key": "Net.AllowPT",
            "Value": 1
          },
          {
            "Key": "Net.BlockGuestBPDU",
            "Value": 0
          },
          {
            "Key": "Net.CoalesceDefaultOn",
            "Value": 1
          },
          {
            "Key": "Net.CoalesceFavorNoVmmVmkTx",
            "Value": 1
          },
          {
            "Key": "Net.CoalesceFineTimeoutCPU",
            "Value": 2
          },
          {
            "Key": "Net.CoalesceFineTxTimeout",
            "Value": 1000
          },
          {
            "Key": "Net.CoalesceFlexMrq",
            "Value": 1
          },
          {
            "Key": "Net.CoalesceLowRxRate",
            "Value": 4
          },
          {
            "Key": "Net.CoalesceLowTxRate",
            "Value": 4
          },
          {
            "Key": "Net.CoalesceMatchedQs",
            "Value": 1
          },
          {
            "Key": "Net.CoalesceMrqLt",
            "Value": 1
          },
          {
            "Key": "Net.CoalesceMrqMetricAllowTxOnly",
            "Value": 1
          },
          {
            "Key": "Net.CoalesceMrqMetricRxOnly",
            "Value": 0
          },
          {
            "Key": "Net.CoalesceMrqOverallStop",
            "Value": 0
          },
          {
            "Key": "Net.CoalesceMrqRatioMetric",
            "Value": 1
          },
          {
            "Key": "Net.CoalesceMrqTriggerReCalib",
            "Value": 1
          },
          {
            "Key": "Net.CoalesceMultiRxQCalib",
            "Value": 1
          },
          {
            "Key": "Net.CoalesceNoVmmVmkTx",
            "Value": 1
          },
          {
            "Key": "Net.CoalesceParams",
            "Value": ""
          },
          {
            "Key": "Net.CoalesceRBCRate",
            "Value": 4000
          },
          {
            "Key": "Net.CoalesceRxLtStopCalib",
            "Value": 0
          },
          {
            "Key": "Net.CoalesceRxQDepthCap",
            "Value": 40
          },
          {
            "Key": "Net.CoalesceScheme",
            "Value": "rbc"
          },
          {
            "Key": "Net.CoalesceTimeoutType",
            "Value": 2
          },
          {
            "Key": "Net.CoalesceTxAlwaysPoll",
            "Value": 1
          },
          {
            "Key": "Net.CoalesceTxQDepthCap",
            "Value": 40
          },
          {
            "Key": "Net.CoalesceTxTimeout",
            "Value": 4000
          },
          {
            "Key": "Net.DCBEnable",
            "Value": 1
          },
          {
            "Key": "Net.DVFilterBindIpAddress",
            "Value": ""
          },
          {
            "Key": "Net.DVFilterPriorityRdLockEnable",
            "Value": 1
          },
          {
            "Key": "Net.DVSLargeHeapMaxSize",
            "Value": 80
          },
          {
            "Key": "Net.DontOffloadInnerIPv6",
            "Value": 0
          },
          {
            "Key": "Net.E1000IntrCoalesce",
            "Value": 1
          },
          {
            "Key": "Net.E1000TxCopySize",
            "Value": 2048
          },
          {
            "Key": "Net.E1000TxZeroCopy",
            "Value": 1
          },
          {
            "Key": "Net.EnableDMASgCons",
            "Value": 1
          },
          {
            "Key": "Net.EnableOuterCsum",
            "Value": 1
          },
          {
            "Key": "Net.EnsMbufpoolMaxMBPerGB",
            "Value": 200
          },
          {
            "Key": "Net.EnsMbufpoolMinMBPerGB",
            "Value": 10
          },
          {
            "Key": "Net.EtherswitchAllowFastPath",
            "Value": 0
          },
          {
            "Key": "Net.EtherswitchHashSize",
            "Value": 1
          },
          {
            "Key": "Net.EtherswitchHeapMax",
            "Value": 512
          },
          {
            "Key": "Net.EtherswitchNumPerPCPUDispatchData",
            "Value": 3
          },
          {
            "Key": "Net.FollowHardwareMac",
            "Value": 0
          },
          {
            "Key": "Net.GuestIPHack",
            "Value": 0
          },
          {
            "Key": "Net.GuestTxCopyBreak",
            "Value": 64
          },
          {
            "Key": "Net.IGMPQueries",
            "Value": 2
          },
          {
            "Key": "Net.IGMPQueryInterval",
            "Value": 125
          },
          {
            "Key": "Net.IGMPRouterIP",
            "Value": "0.0.0.0"
          },
          {
            "Key": "Net.IGMPV3MaxSrcIPNum",
            "Value": 10
          },
          {
            "Key": "Net.IGMPVersion",
            "Value": 3
          },
          {
            "Key": "Net.LRODefBackoffPeriod",
            "Value": 8
          },
          {
            "Key": "Net.LRODefMaxLength",
            "Value": 65535
          },
          {
            "Key": "Net.LRODefThreshold",
            "Value": 4000
          },
          {
            "Key": "Net.LRODefUseRatioDenom",
            "Value": 3
          },
          {
            "Key": "Net.LRODefUseRatioNumer",
            "Value": 1
          },
          {
            "Key": "Net.LinkFlappingThreshold",
            "Value": 60
          },
          {
            "Key": "Net.LinkStatePollTimeout",
            "Value": 500
          },
          {
            "Key": "Net.MLDRouterIP",
            "Value": "FE80::FFFF:FFFF:FFFF:FFFF"
          },
          {
            "Key": "Net.MLDV2MaxSrcIPNum",
            "Value": 10
          },
          {
            "Key": "Net.MLDVersion",
            "Value": 2
          },
          {
            "Key": "Net.MaxBeaconVlans",
            "Value": 100
          },
          {
            "Key": "Net.MaxBeaconsAtOnce",
            "Value": 100
          },
          {
            "Key": "Net.MaxGlobalRxQueueCount",
            "Value": 100000
          },
          {
            "Key": "Net.MaxNetifTxQueueLen",
            "Value": 2000
          },
          {
            "Key": "Net.MaxPageInQueueLen",
            "Value": 75
          },
          {
            "Key": "Net.MaxPktRxListQueue",
            "Value": 3500
          },
          {
            "Key": "Net.MaxPortRxQueueLen",
            "Value": 80
          },
          {
            "Key": "Net.MinEtherLen",
            "Value": 60
          },
          {
            "Key": "Net.NcpLlcSap",
            "Value": 0
          },
          {
            "Key": "Net.NetBHRxStormThreshold",
            "Value": 320
          },
          {
            "Key": "Net.NetDebugRARPTimerInter",
            "Value": 30000
          },
          {
            "Key": "Net.NetDeferTxCompletion",
            "Value": 1
          },
          {
            "Key": "Net.NetDiscUpdateIntrvl",
            "Value": 300
          },
          {
            "Key": "Net.NetEnableSwCsumForLro",
            "Value": 1
          },
          {
            "Key": "Net.NetEsxfwPassOutboundGRE",
            "Value": 1
          },
          {
            "Key": "Net.NetInStressTest",
            "Value": 0
          },
          {
            "Key": "Net.NetLatencyAwareness",
            "Value": 1
          },
          {
            "Key": "Net.NetMaxRarpsPerInterval",
            "Value": 128
          },
          {
            "Key": "Net.NetNetqNumaIOCpuPinThreshold",
            "Value": 0
          },
          {
            "Key": "Net.NetNetqRxRebalRSSLoadThresholdPerc",
            "Value": 10
          },
          {
            "Key": "Net.NetNetqTxPackKpps",
            "Value": 300
          },
          {
            "Key": "Net.NetNetqTxUnpackKpps",
            "Value": 600
          },
          {
            "Key": "Net.NetNiocAllowOverCommit",
            "Value": 1
          },
          {
            "Key": "Net.NetPTMgrWakeupInterval",
            "Value": 6
          },
          {
            "Key": "Net.NetPktAllocTries",
            "Value": 5
          },
          {
            "Key": "Net.NetPktSlabFreePercentThreshold",
            "Value": 2
          },
          {
            "Key": "Net.NetPortFlushIterLimit",
            "Value": 2
          },
          {
            "Key": "Net.NetPortFlushPktLimit",
            "Value": 64
          },
          {
            "Key": "Net.NetPortTrackTxRace",
            "Value": 0
          },
          {
            "Key": "Net.NetRmDistMacFilter",
            "Value": 1
          },
          {
            "Key": "Net.NetRmDistSamplingRate",
            "Value": 0
          },
          {
            "Key": "Net.NetRxCopyInTx",
            "Value": 0
          },
          {
            "Key": "Net.NetSchedCoalesceTxUsecs",
            "Value": 33
          },
          {
            "Key": "Net.NetSchedDefaultResPoolSharesPct",
            "Value": 5
          },
          {
            "Key": "Net.NetSchedDefaultSchedName",
            "Value": "fifo"
          },
          {
            "Key": "Net.NetSchedECNEnabled",
            "Value": 1
          },
          {
            "Key": "Net.NetSchedECNThreshold",
            "Value": 70
          },
          {
            "Key": "Net.NetSchedHClkLeafQueueDepthPkt",
            "Value": 500
          },
          {
            "Key": "Net.NetSchedHClkMQ",
            "Value": 0
          },
          {
            "Key": "Net.NetSchedHClkMaxHwQueue",
            "Value": 2
          },
          {
            "Key": "Net.NetSchedHClkVnicMQ",
            "Value": 0
          },
          {
            "Key": "Net.NetSchedHeapMaxSizeMB",
            "Value": 64
          },
          {
            "Key": "Net.NetSchedInFlightMaxBytesDefault",
            "Value": 20000
          },
          {
            "Key": "Net.NetSchedInFlightMaxBytesInsane",
            "Value": 1500000
          },
          {
            "Key": "Net.NetSchedMaxPktSend",
            "Value": 256
          },
          {
            "Key": "Net.NetSchedQoSSchedName",
            "Value": "hclk"
          },
          {
            "Key": "Net.NetSchedSpareBasedShares",
            "Value": 1
          },
          {
            "Key": "Net.NetSendRARPOnPortEnablement",
            "Value": 1
          },
          {
            "Key": "Net.NetShaperQueuePerL3L4Flow",
            "Value": 1
          },
          {
            "Key": "Net.NetShaperQueueSizeMax",
            "Value": 500
          },
          {
            "Key": "Net.NetShaperQueueSizeMin",
            "Value": 10
          },
          {
            "Key": "Net.NetSplitRxMode",
            "Value": 1
          },
          {
            "Key": "Net.NetTraceEnable",
            "Value": 0
          },
          {
            "Key": "Net.NetTuneHostMode",
            "Value": "default"
          },
          {
            "Key": "Net.NetTuneInterval",
            "Value": 60
          },
          {
            "Key": "Net.NetTuneThreshold",
            "Value": "1n 2n 50"
          },
          {
            "Key": "Net.NetTxDontClusterSize",
            "Value": 0
          },
          {
            "Key": "Net.NetVMTxType",
            "Value": 2
          },
          {
            "Key": "Net.NetpollSwLRO",
            "Value": 1
          },
          {
            "Key": "Net.NoLocalCSum",
            "Value": 0
          },
          {
            "Key": "Net.NotifySwitch",
            "Value": 1
          },
          {
            "Key": "Net.PTSwitchingTimeout",
            "Value": 20000
          },
          {
            "Key": "Net.PVRDMAVmknic",
            "Value": ""
          },
          {
            "Key": "Net.PktagingDropPolicy",
            "Value": 0
          },
          {
            "Key": "Net.PortDisableTimeout",
            "Value": 5000
          },
          {
            "Key": "Net.ReversePathFwdCheck",
            "Value": 1
          },
          {
            "Key": "Net.ReversePathFwdCheckPromisc",
            "Value": 0
          },
          {
            "Key": "Net.TcpipCopySmallTx",
            "Value": 1
          },
          {
            "Key": "Net.TcpipDefLROEnabled",
            "Value": 1
          },
          {
            "Key": "Net.TcpipDefLROMaxLength",
            "Value": 32768
          },
          {
            "Key": "Net.TcpipDgramRateLimiting",
            "Value": 1
          },
          {
            "Key": "Net.TcpipEnableABC",
            "Value": 1
          },
          {
            "Key": "Net.TcpipEnableFlowtable",
            "Value": 1
          },
          {
            "Key": "Net.TcpipEnableSendScaling",
            "Value": 1
          },
          {
            "Key": "Net.TcpipHWLRONoDelayAck",
            "Value": 1
          },
          {
            "Key": "Net.TcpipHeapMax",
            "Value": 1024
          },
          {
            "Key": "Net.TcpipHeapSize",
            "Value": 0
          },
          {
            "Key": "Net.TcpipIGMPDefaultVersion",
            "Value": 3
          },
          {
            "Key": "Net.TcpipIGMPRejoinInterval",
            "Value": 60
          },
          {
            "Key": "Net.TcpipLODispatchQueueMaxLen",
            "Value": 128
          },
          {
            "Key": "Net.TcpipLRONoDelayAck",
            "Value": 1
          },
          {
            "Key": "Net.TcpipLogPackets",
            "Value": 0
          },
          {
            "Key": "Net.TcpipLogPacketsCount",
            "Value": 24570
          },
          {
            "Key": "Net.TcpipMaxNetstackInstances",
            "Value": 48
          },
          {
            "Key": "Net.TcpipNoBcopyRx",
            "Value": 1
          },
          {
            "Key": "Net.TcpipPendPktSocketFreeTimeout",
            "Value": 300
          },
          {
            "Key": "Net.TcpipRxDispatchQueueMaxLen",
            "Value": 2000
          },
          {
            "Key": "Net.TcpipRxDispatchQueues",
            "Value": 1
          },
          {
            "Key": "Net.TcpipRxDispatchQuota",
            "Value": 200
          },
          {
            "Key": "Net.TcpipRxVmknicWorldletAffinityType",
            "Value": 0
          },
          {
            "Key": "Net.TcpipTxDispatchQuota",
            "Value": 100
          },
          {
            "Key": "Net.TcpipTxqBackoffTimeoutMs",
            "Value": 70
          },
          {
            "Key": "Net.TcpipTxqMaxUsageThreshold",
            "Value": 80
          },
          {
            "Key": "Net.TeamPolicyUpDelay",
            "Value": 100
          },
          {
            "Key": "Net.TrafficFilterIpAddress",
            "Value": ""
          },
          {
            "Key": "Net.TsoDumpPkt",
            "Value": 0
          },
          {
            "Key": "Net.UplinkAbortDisconnectTimeout",
            "Value": 5000
          },
          {
            "Key": "Net.UplinkKillAsyncTimeout",
            "Value": 10000
          },
          {
            "Key": "Net.UplinkTxQueuesDispEnabled",
            "Value": 1
          },
          {
            "Key": "Net.UseHwCsumForIPv6Csum",
            "Value": 1
          },
          {
            "Key": "Net.UseHwIPv6Csum",
            "Value": 1
          },
          {
            "Key": "Net.UseHwTSO",
            "Value": 1
          },
          {
            "Key": "Net.UseHwTSO6",
            "Value": 1
          },
          {
            "Key": "Net.UseLegacyProc",
            "Value": 0
          },
          {
            "Key": "Net.UseProc",
            "Value": 0
          },
          {
            "Key": "Net.VLANMTUCheckMode",
            "Value": 1
          },
          {
            "Key": "Net.VmklnxLROEnabled",
            "Value": 0
          },
          {
            "Key": "Net.VmklnxLROMaxAggr",
            "Value": 6
          },
          {
            "Key": "Net.VmknicDoLroSplit",
            "Value": 0
          },
          {
            "Key": "Net.VmknicLroSplitBnd",
            "Value": 12
          },
          {
            "Key": "Net.Vmxnet2HwLRO",
            "Value": 1
          },
          {
            "Key": "Net.Vmxnet2PinRxBuf",
            "Value": 0
          },
          {
            "Key": "Net.Vmxnet2SwLRO",
            "Value": 1
          },
          {
            "Key": "Net.Vmxnet3HwLRO",
            "Value": 1
          },
          {
            "Key": "Net.Vmxnet3PageInBound",
            "Value": 32
          },
          {
            "Key": "Net.Vmxnet3RSSHashCache",
            "Value": 1
          },
          {
            "Key": "Net.Vmxnet3RxPollBound",
            "Value": 256
          },
          {
            "Key": "Net.Vmxnet3SwLRO",
            "Value": 1
          },
          {
            "Key": "Net.Vmxnet3WinIntrHints",
            "Value": 1
          },
          {
            "Key": "Net.Vmxnet3usePNICHash",
            "Value": 0
          },
          {
            "Key": "Net.VmxnetBiDirNeedsTsoTx",
            "Value": 1
          },
          {
            "Key": "Net.VmxnetBiDirNoTsoSplit",
            "Value": 1
          },
          {
            "Key": "Net.VmxnetCopyTxRunLimit",
            "Value": 16
          },
          {
            "Key": "Net.VmxnetDoLroSplit",
            "Value": 1
          },
          {
            "Key": "Net.VmxnetDoTsoSplit",
            "Value": 1
          },
          {
            "Key": "Net.VmxnetLROBackoffPeriod",
            "Value": 8
          },
          {
            "Key": "Net.VmxnetLROMaxLength",
            "Value": 32000
          },
          {
            "Key": "Net.VmxnetLROThreshold",
            "Value": 4000
          },
          {
            "Key": "Net.VmxnetLROUseRatioDenom",
            "Value": 3
          },
          {
            "Key": "Net.VmxnetLROUseRatioNumer",
            "Value": 2
          },
          {
            "Key": "Net.VmxnetLroSplitBnd",
            "Value": 64
          },
          {
            "Key": "Net.VmxnetPromDisableLro",
            "Value": 1
          },
          {
            "Key": "Net.VmxnetSwLROSL",
            "Value": 1
          },
          {
            "Key": "Net.VmxnetTsoSplitBnd",
            "Value": 12
          },
          {
            "Key": "Net.VmxnetTsoSplitSize",
            "Value": 17500
          },
          {
            "Key": "Net.VmxnetTxCopySize",
            "Value": 256
          },
          {
            "Key": "Net.VmxnetWinCopyTxRunLimit",
            "Value": 65535
          },
          {
            "Key": "Net.VmxnetWinUDPTxFullCopy",
            "Value": 1
          },
          {
            "Key": "Net.vNicNumDeferredReset",
            "Value": 12
          },
          {
            "Key": "Net.vNicTxPollBound",
            "Value": 192
          },
          {
            "Key": "Net.vmxnetThroughputWeight",
            "Value": 0
          },
          {
            "Key": "Net.IOControlPnicOptOut",
            "Value": ""
          },
          {
            "Key": "Nmp.NmpPReservationCmdRetryTime",
            "Value": 1
          },
          {
            "Key": "Nmp.NmpSatpAluaCmdRetryTime",
            "Value": 10
          },
          {
            "Key": "Numa.CoreCapRatioPct",
            "Value": 90
          },
          {
            "Key": "Numa.CostopSkewAdjust",
            "Value": 1
          },
          {
            "Key": "Numa.FollowCoresPerSocket",
            "Value": 0
          },
          {
            "Key": "Numa.LTermFairnessInterval",
            "Value": 5
          },
          {
            "Key": "Numa.LTermMigImbalThreshold",
            "Value": 10
          },
          {
            "Key": "Numa.LargeInterleave",
            "Value": 1
          },
          {
            "Key": "Numa.LatencyProbePeriod",
            "Value": 500
          },
          {
            "Key": "Numa.LocalityWeightActionAffinity",
            "Value": 130
          },
          {
            "Key": "Numa.LocalityWeightMem",
            "Value": 1
          },
          {
            "Key": "Numa.MigImbalanceThreshold",
            "Value": 10
          },
          {
            "Key": "Numa.MigPreventLTermThresh",
            "Value": 0
          },
          {
            "Key": "Numa.MigThrashThreshold",
            "Value": 50
          },
          {
            "Key": "Numa.MigThreshold",
            "Value": 2
          },
          {
            "Key": "Numa.MonMigEnable",
            "Value": 1
          },
          {
            "Key": "Numa.PageMigEnable",
            "Value": 1
          },
          {
            "Key": "Numa.PageMigLinearRun",
            "Value": 95
          },
          {
            "Key": "Numa.PageMigRandomRun",
            "Value": 5
          },
          {
            "Key": "Numa.PageMigRateMax",
            "Value": 8000
          },
          {
            "Key": "Numa.PreferHT",
            "Value": 0
          },
          {
            "Key": "Numa.RebalanceCoresNode",
            "Value": 2
          },
          {
            "Key": "Numa.RebalanceCoresTotal",
            "Value": 4
          },
          {
            "Key": "Numa.RebalanceEnable",
            "Value": 1
          },
          {
            "Key": "Numa.RebalancePeriod",
            "Value": 2000
          },
          {
            "Key": "Numa.SwapConsiderPeriod",
            "Value": 15
          },
          {
            "Key": "Numa.SwapInterval",
            "Value": 3
          },
          {
            "Key": "Numa.SwapLoadEnable",
            "Value": 1
          },
          {
            "Key": "Numa.SwapLocalityEnable",
            "Value": 1
          },
          {
            "Key": "Numa.SwapMigrateOnly",
            "Value": 2
          },
          {
            "Key": "Power.CStateMaxLatency",
            "Value": 500
          },
          {
            "Key": "Power.CStatePredictionCoef",
            "Value": 110479
          },
          {
            "Key": "Power.CStateResidencyCoef",
            "Value": 5
          },
          {
            "Key": "Power.ChargeMemoryPct",
            "Value": 20
          },
          {
            "Key": "Power.MaxCpuLoad",
            "Value": 60
          },
          {
            "Key": "Power.MaxFreqPct",
            "Value": 100
          },
          {
            "Key": "Power.MinFreqPct",
            "Value": 0
          },
          {
            "Key": "Power.PerfBias",
            "Value": 17
          },
          {
            "Key": "Power.PerfBiasEnable",
            "Value": 1
          },
          {
            "Key": "Power.TimerHz",
            "Value": 100
          },
          {
            "Key": "Power.UseCStates",
            "Value": 1
          },
          {
            "Key": "Power.UsePStates",
            "Value": 1
          },
          {
            "Key": "RdmFilter.HbaIsShared",
            "Value": true
          },
          {
            "Key": "ScratchConfig.ConfiguredScratchLocation",
            "Value": "/vmfs/volumes/60c4dd36-32ef1a2f-8d22-0800272601f9"
          },
          {
            "Key": "ScratchConfig.CurrentScratchLocation",
            "Value": "/vmfs/volumes/60c4dd36-32ef1a2f-8d22-0800272601f9"
          },
          {
            "Key": "Scsi.ChangeQErrSetting",
            "Value": 1
          },
          {
            "Key": "Scsi.CompareLUNNumber",
            "Value": 1
          },
          {
            "Key": "Scsi.EnableCmdSanityCheck",
            "Value": 0
          },
          {
            "Key": "Scsi.ExtendAPDCondition",
            "Value": 0
          },
          {
            "Key": "Scsi.FailVMIOonAPD",
            "Value": 0
          },
          {
            "Key": "Scsi.LogCmdErrors",
            "Value": 1
          },
          {
            "Key": "Scsi.LogCmdRCErrorsFreq",
            "Value": 0
          },
          {
            "Key": "Scsi.LogMPCmdErrors",
            "Value": 1
          },
          {
            "Key": "Scsi.LogScsiAborts",
            "Value": 0
          },
          {
            "Key": "Scsi.LunCleanupInterval",
            "Value": 7
          },
          {
            "Key": "Scsi.MaxReserveBacktrace",
            "Value": 0
          },
          {
            "Key": "Scsi.MaxReserveTime",
            "Value": 200
          },
          {
            "Key": "Scsi.MaxReserveTotalTime",
            "Value": 250
          },
          {
            "Key": "Scsi.PassthroughLocking",
            "Value": 1
          },
          {
            "Key": "Scsi.ReserveBacktrace",
            "Value": 0
          },
          {
            "Key": "Scsi.SCSIEnableDescToFixedConv",
            "Value": 1
          },
          {
            "Key": "Scsi.SCSIEnableIOLatencyMsgs",
            "Value": 0
          },
          {
            "Key": "Scsi.SCSIStrictSPCVersionChecksForPEs",
            "Value": 0
          },
          {
            "Key": "Scsi.SCSITimeout_ReabortTime",
            "Value": 5000
          },
          {
            "Key": "Scsi.SCSITimeout_ScanTime",
            "Value": 1000
          },
          {
            "Key": "Scsi.SCSIioTraceBufSizeMB",
            "Value": 1
          },
          {
            "Key": "Scsi.ScanOnDriverLoad",
            "Value": 1
          },
          {
            "Key": "Scsi.ScanSync",
            "Value": 0
          },
          {
            "Key": "Scsi.ScsiAllowDeviceSpinup",
            "Value": 1
          },
          {
            "Key": "Scsi.ScsiRestartStalledQueueLatency",
            "Value": 500
          },
          {
            "Key": "Scsi.ScsiTMHardTimeout",
            "Value": 120000
          },
          {
            "Key": "Scsi.ScsiVVolPESNRO",
            "Value": 128
          },
          {
            "Key": "Scsi.TimeoutTMThreadExpires",
            "Value": 1800
          },
          {
            "Key": "Scsi.TimeoutTMThreadLatency",
            "Value": 2000
          },
          {
            "Key": "Scsi.TimeoutTMThreadMax",
            "Value": 16
          },
          {
            "Key": "Scsi.TimeoutTMThreadMin",
            "Value": 1
          },
          {
            "Key": "Scsi.TimeoutTMThreadRetry",
            "Value": 2000
          },
          {
            "Key": "Scsi.TransFailLogPct",
            "Value": 20
          },
          {
            "Key": "Scsi.UseAdaptiveRetries",
            "Value": 1
          },
          {
            "Key": "Security.AccountLockFailures",
            "Value": 5
          },
          {
            "Key": "Security.AccountUnlockTime",
            "Value": 900
          },
          {
            "Key": "Security.PasswordHistory",
            "Value": 0
          },
          {
            "Key": "Security.PasswordMaxDays",
            "Value": 99999
          },
          {
            "Key": "Security.PasswordQualityControl",
            "Value": "retry=3 min=disabled,disabled,disabled,7,7"
          },
          {
            "Key": "SunRPC.MaxConnPerIP",
            "Value": 4
          },
          {
            "Key": "SunRPC.SendLowat",
            "Value": 25
          },
          {
            "Key": "SunRPC.WorldAffinity",
            "Value": 2
          },
          {
            "Key": "SvMotion.SvMotionAvgDisksPerVM",
            "Value": 8
          },
          {
            "Key": "Syslog.global.defaultRotate",
            "Value": 8
          },
          {
            "Key": "Syslog.global.defaultSize",
            "Value": 1024
          },
          {
            "Key": "Syslog.global.logCheckSSLCerts",
            "Value": true
          },
          {
            "Key": "Syslog.global.logDir",
            "Value": "[] /scratch/log"
          },
          {
            "Key": "Syslog.global.logDirUnique",
            "Value": false
          },
          {
            "Key": "Syslog.global.logHost",
            "Value": ""
          },
          {
            "Key": "Syslog.loggers.Xorg.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.Xorg.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.auth.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.auth.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.clomd.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.clomd.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.clusterAgent.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.clusterAgent.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.cmmdsTimeMachine.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.cmmdsTimeMachine.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.cmmdsTimeMachineDump.rotate",
            "Value": 20
          },
          {
            "Key": "Syslog.loggers.cmmdsTimeMachineDump.size",
            "Value": 10240
          },
          {
            "Key": "Syslog.loggers.ddecomd.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.ddecomd.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.dhclient.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.dhclient.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.epd.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.epd.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.esxupdate.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.esxupdate.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.fdm.rotate",
            "Value": 10
          },
          {
            "Key": "Syslog.loggers.fdm.size",
            "Value": 5120
          },
          {
            "Key": "Syslog.loggers.hbragent.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.hbragent.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.hostd-probe.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.hostd-probe.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.hostd.rotate",
            "Value": 10
          },
          {
            "Key": "Syslog.loggers.hostd.size",
            "Value": 10240
          },
          {
            "Key": "Syslog.loggers.hostdCgiServer.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.hostdCgiServer.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.hostprofiletrace.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.hostprofiletrace.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.iofiltervpd.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.iofiltervpd.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.lacp.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.lacp.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.loadESX.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.loadESX.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.nfcd.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.nfcd.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.osfsd.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.osfsd.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.rabbitmqproxy.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.rabbitmqproxy.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.rhttpproxy.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.rhttpproxy.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.sdrsInjector.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.sdrsInjector.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.shell.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.shell.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.sockrelay.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.sockrelay.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.storageRM.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.storageRM.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.swapobjd.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.swapobjd.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.syslog.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.syslog.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.upitd.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.upitd.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.usb.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.usb.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.vdfs-proxy.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.vdfs-proxy.size",
            "Value": 10240
          },
          {
            "Key": "Syslog.loggers.vdfs-server.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.vdfs-server.size",
            "Value": 10240
          },
          {
            "Key": "Syslog.loggers.vitd.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.vitd.size",
            "Value": 10240
          },
          {
            "Key": "Syslog.loggers.vmauthd.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.vmauthd.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.vmkdevmgr.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.vmkdevmgr.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.vmkernel.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.vmkernel.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.vmkeventd.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.vmkeventd.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.vmksummary.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.vmksummary.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.vmkwarning.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.vmkwarning.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.vobd.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.vobd.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.vprobe.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.vprobe.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.vpxa.rotate",
            "Value": 20
          },
          {
            "Key": "Syslog.loggers.vpxa.size",
            "Value": 5120
          },
          {
            "Key": "Syslog.loggers.vsandpd.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.vsandpd.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.vsanmgmt.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.vsanmgmt.size",
            "Value": 10240
          },
          {
            "Key": "Syslog.loggers.vsansystem.rotate",
            "Value": 10
          },
          {
            "Key": "Syslog.loggers.vsansystem.size",
            "Value": 10240
          },
          {
            "Key": "Syslog.loggers.vsantraceUrgent.rotate",
            "Value": 8
          },
          {
            "Key": "Syslog.loggers.vsantraceUrgent.size",
            "Value": 1024
          },
          {
            "Key": "Syslog.loggers.vvold.rotate",
            "Value": 16
          },
          {
            "Key": "Syslog.loggers.vvold.size",
            "Value": 8192
          },
          {
            "Key": "USB.quirks",
            "Value": ""
          },
          {
            "Key": "User.PTEDisableNX",
            "Value": 0
          },
          {
            "Key": "User.ReaddirRetries",
            "Value": 10
          },
          {
            "Key": "UserVars.ActiveDirectoryPreferredDomainControllers",
            "Value": ""
          },
          {
            "Key": "UserVars.ActiveDirectoryVerifyCAMCertificate",
            "Value": 1
          },
          {
            "Key": "UserVars.DcuiTimeOut",
            "Value": 600
          },
          {
            "Key": "UserVars.ESXiShellInteractiveTimeOut",
            "Value": 0
          },
          {
            "Key": "UserVars.ESXiShellTimeOut",
            "Value": 0
          },
          {
            "Key": "UserVars.ESXiVPsAllowedCiphers",
            "Value": "!aNULL:kECDH+AESGCM:ECDH+AESGCM:RSA+AESGCM:kECDH+AES:ECDH+AES:RSA+AES"
          },
          {
            "Key": "UserVars.ESXiVPsDisabledProtocols",
            "Value": "sslv3,tlsv1,tlsv1.1"
          },
          {
            "Key": "UserVars.EsximageNetRateLimit",
            "Value": 0
          },
          {
            "Key": "UserVars.EsximageNetRetries",
            "Value": 10
          },
          {
            "Key": "UserVars.EsximageNetTimeout",
            "Value": 60
          },
          {
            "Key": "UserVars.HardwareHealthIgnoredSensors",
            "Value": ""
          },
          {
            "Key": "UserVars.HardwareHealthSyncTime",
            "Value": 360
          },
          {
            "Key": "UserVars.HostClientCEIPOptIn",
            "Value": 2
          },
          {
            "Key": "UserVars.HostClientDefaultConsole",
            "Value": "webmks"
          },
          {
            "Key": "UserVars.HostClientEnableMOTDNotification",
            "Value": 1
          },
          {
            "Key": "UserVars.HostClientEnableVisualEffects",
            "Value": 1
          },
          {
            "Key": "UserVars.HostClientSessionTimeout",
            "Value": 900
          },
          {
            "Key": "UserVars.HostClientShowOnlyRecentObjects",
            "Value": 1
          },
          {
            "Key": "UserVars.HostClientWelcomeMessage",
            "Value": "Welcome to {{hostname}}"
          },
          {
            "Key": "UserVars.HostdStatsstoreRamdiskSize",
            "Value": 0
          },
          {
            "Key": "UserVars.ProductLockerLocation",
            "Value": "/locker/packages/vmtoolsRepo/"
          },
          {
            "Key": "UserVars.SuppressCoredumpWarning",
            "Value": 0
          },
          {
            "Key": "UserVars.SuppressHyperthreadWarning",
            "Value": 0
          },
          {
            "Key": "UserVars.SuppressShellWarning",
            "Value": 0
          },
          {
            "Key": "UserVars.ToolsRamdisk",
            "Value": 0
          },
          {
            "Key": "VFLASH.CacheStatsEnable",
            "Value": 1
          },
          {
            "Key": "VFLASH.CacheStatsFromVFC",
            "Value": 1
          },
          {
            "Key": "VFLASH.MaxCacheFileSizeMB",
            "Value": 409600
          },
          {
            "Key": "VFLASH.MaxDiskFileSizeGB",
            "Value": 16384
          },
          {
            "Key": "VFLASH.MaxHeapSizeMB",
            "Value": 32
          },
          {
            "Key": "VFLASH.MaxResourceGBForVmCache",
            "Value": 2048
          },
          {
            "Key": "VFLASH.ResourceUsageThreshold",
            "Value": 80
          },
          {
            "Key": "VMFS.UnresolvedVolumeLiveCheck",
            "Value": true
          },
          {
            "Key": "VMFS3.EnableBlockDelete",
            "Value": 0
          },
          {
            "Key": "VMFS3.FailVolumeOpenIfAPD",
            "Value": 0
          },
          {
            "Key": "VMFS3.GBLAllowMW",
            "Value": 0
          },
          {
            "Key": "VMFS3.HardwareAcceleratedLocking",
            "Value": 1
          },
          {
            "Key": "VMFS3.LFBCSlabSizeMaxMB",
            "Value": 8
          },
          {
            "Key": "VMFS3.MaxAddressableSpaceTB",
            "Value": 32
          },
          {
            "Key": "VMFS3.MaxHeapSizeMB",
            "Value": 256
          },
          {
            "Key": "VMFS3.MaxextendedTxnsUsingfs3Heap",
            "Value": 20
          },
          {
            "Key": "VMFS3.MinAddressableSpaceTB",
            "Value": 0
          },
          {
            "Key": "VMFS3.OpenWithoutJournal",
            "Value": 1
          },
          {
            "Key": "VMFS3.PBCapMissRatioIntervalSec",
            "Value": 60
          },
          {
            "Key": "VMFS3.StAtExclLockEnd",
            "Value": 0
          },
          {
            "Key": "VMFS3.UseATSForHBOnVMFS5",
            "Value": 1
          },
          {
            "Key": "VMkernel.Boot.autoCreateDumpFile",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.autoPartition",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.autoPartitionCreateUSBCoreDumpPartition",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.autoPartitionDiskDumpPartitionSize",
            "Value": 2560
          },
          {
            "Key": "VMkernel.Boot.bootDeviceRescanTimeout",
            "Value": 1
          },
          {
            "Key": "VMkernel.Boot.busSpeedMayVary",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.cacheFlushImmOnAllHalt",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.checkCPUIDLimit",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.checkPages",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.com1_baud",
            "Value": 115200
          },
          {
            "Key": "VMkernel.Boot.com2_baud",
            "Value": 115200
          },
          {
            "Key": "VMkernel.Boot.coresPerPkg",
            "Value": 0
          },
          {
            "Key": "VMkernel.Boot.debugBreak",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.debugLogToSerial",
            "Value": 2
          },
          {
            "Key": "VMkernel.Boot.disableACSCheck",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.disableCFOH",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.disableHwrng",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.diskDumpSlotSize",
            "Value": 0
          },
          {
            "Key": "VMkernel.Boot.dmaEngineExposeIdentityMapping",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.dmaMapperPolicy",
            "Value": "disabled"
          },
          {
            "Key": "VMkernel.Boot.enableACSCheckForRP",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.execInstalledOnly",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.fakePMemPct",
            "Value": 0
          },
          {
            "Key": "VMkernel.Boot.fsCheck",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.gdbPort",
            "Value": "default"
          },
          {
            "Key": "VMkernel.Boot.heapCheckTimerInterval",
            "Value": 10
          },
          {
            "Key": "VMkernel.Boot.heapFreeOwnerCheck",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.heapFreePoisonByte",
            "Value": 255
          },
          {
            "Key": "VMkernel.Boot.heapMetaPoisonByte",
            "Value": 90
          },
          {
            "Key": "VMkernel.Boot.heapMetadataProtect",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.heapMgr2mbTotalVASpaceGB",
            "Value": 5
          },
          {
            "Key": "VMkernel.Boot.heapMgrTotalVASpaceGB",
            "Value": 2048
          },
          {
            "Key": "VMkernel.Boot.heapPoisonFreeMem",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.heapPoisonTimerChecks",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.hyperthreading",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.hyperthreadingMitigation",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.hyperthreadingMitigationIntraVM",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.ignoreMsrFaults",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.intrBalancingEnabled",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.ioAbilityChecks",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.iovDisableIR",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.ipmiEnabled",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.isPerFileSchedModelActive",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.leaveWakeGPEsDisabled",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.logPort",
            "Value": "default"
          },
          {
            "Key": "VMkernel.Boot.maxLogEntries",
            "Value": 0
          },
          {
            "Key": "VMkernel.Boot.maxPCPUS",
            "Value": 1024
          },
          {
            "Key": "VMkernel.Boot.maxPCPUsNUMAInterleaving",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.maxVMs",
            "Value": 0
          },
          {
            "Key": "VMkernel.Boot.memCheckEveryWord",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.memLowReservedMB",
            "Value": 0
          },
          {
            "Key": "VMkernel.Boot.memmapMaxEarlyPoisonMemMB",
            "Value": 65536
          },
          {
            "Key": "VMkernel.Boot.memmapMaxPhysicalMemMB",
            "Value": 0
          },
          {
            "Key": "VMkernel.Boot.memmapMaxRAMMB",
            "Value": 33585088
          },
          {
            "Key": "VMkernel.Boot.microcodeUpdate",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.microcodeUpdateForce",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.netCoalesceTimerHdlrPcpu",
            "Value": 0
          },
          {
            "Key": "VMkernel.Boot.netGPHeapMaxMBPerGB",
            "Value": 4
          },
          {
            "Key": "VMkernel.Boot.netMaxPktsToProcess",
            "Value": 64
          },
          {
            "Key": "VMkernel.Boot.netMaxPktsToRelease",
            "Value": 128
          },
          {
            "Key": "VMkernel.Boot.netNetqueueEnabled",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.netNetqueueMaxFiltersPerUplink",
            "Value": 4096
          },
          {
            "Key": "VMkernel.Boot.netNetqueueMaxLearnedFilters",
            "Value": 9216
          },
          {
            "Key": "VMkernel.Boot.netNetqueueMaxStaticFilters",
            "Value": 10240
          },
          {
            "Key": "VMkernel.Boot.netPagePoolLimitCap",
            "Value": 98304
          },
          {
            "Key": "VMkernel.Boot.netPagePoolLimitPerGB",
            "Value": 5120
          },
          {
            "Key": "VMkernel.Boot.netPagePoolResvCap",
            "Value": 0
          },
          {
            "Key": "VMkernel.Boot.netPagePoolResvPerGB",
            "Value": 0
          },
          {
            "Key": "VMkernel.Boot.netPktHeapMaxMBPerGB",
            "Value": 6
          },
          {
            "Key": "VMkernel.Boot.netPktHeapMinMBPerGB",
            "Value": 0
          },
          {
            "Key": "VMkernel.Boot.netPktPoolMaxMBPerGB",
            "Value": 75
          },
          {
            "Key": "VMkernel.Boot.netPktPoolMinMBPerGB",
            "Value": 0
          },
          {
            "Key": "VMkernel.Boot.netPreemptionEnabled",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.nmiAction",
            "Value": 0
          },
          {
            "Key": "VMkernel.Boot.numSpareCoresPerLLC",
            "Value": 0
          },
          {
            "Key": "VMkernel.Boot.numaLatencyRemoteThresholdPct",
            "Value": 10
          },
          {
            "Key": "VMkernel.Boot.overrideDuplicateImageDetection",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.pciBarAllocPolicy",
            "Value": 1
          },
          {
            "Key": "VMkernel.Boot.pcipDisablePciErrReporting",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.poisonMarker",
            "Value": -6148914691236517206
          },
          {
            "Key": "VMkernel.Boot.poisonPagePool",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.preferVmklinux",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.preventFreePageMapping",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.rdmaRoceIPBasedGidGeneration",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.rtcEnableEFI",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.rtcEnableLegacy",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.rtcEnableTAD",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.scrubIgnoredPages",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.scrubMemoryAfterModuleLoad",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.serialUntrusted",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.skipPartitioningSsds",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.storageHeapMaxSize",
            "Value": 0
          },
          {
            "Key": "VMkernel.Boot.storageHeapMinSize",
            "Value": 0
          },
          {
            "Key": "VMkernel.Boot.storageMaxDevices",
            "Value": 1024
          },
          {
            "Key": "VMkernel.Boot.storageMaxPaths",
            "Value": 4096
          },
          {
            "Key": "VMkernel.Boot.storageMaxVMsPerDevice",
            "Value": 32
          },
          {
            "Key": "VMkernel.Boot.terminateVMOnPDL",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.timerEnableACPI",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.timerEnableHPET",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.timerEnableTSC",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.timerForceTSC",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.tscSpeedMayVary",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.tty1Port",
            "Value": "default"
          },
          {
            "Key": "VMkernel.Boot.tty2Port",
            "Value": "default"
          },
          {
            "Key": "VMkernel.Boot.updateBusIRQ",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.useNUMAInfo",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.useReliableMem",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.useSLIT",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.vmkATKeyboard",
            "Value": false
          },
          {
            "Key": "VMkernel.Boot.vmkacEnable",
            "Value": 1
          },
          {
            "Key": "VMkernel.Boot.vtdSuperPages",
            "Value": true
          },
          {
            "Key": "VMkernel.Boot.x2ApicPreferred",
            "Value": false
          },
          {
            "Key": "VSAN-iSCSI.iscsiPingTimeout",
            "Value": 5
          },
          {
            "Key": "VSAN.AutoRestoreDecomState",
            "Value": 1
          },
          {
            "Key": "VSAN.AutoTerminateGhostVm",
            "Value": 1
          },
          {
            "Key": "VSAN.ClomForceProvisionPlacements",
            "Value": 0
          },
          {
            "Key": "VSAN.ClomMaxComponentSizeGB",
            "Value": 255
          },
          {
            "Key": "VSAN.ClomMaxDiskUsageRepairComps",
            "Value": 95
          },
          {
            "Key": "VSAN.ClomRebalanceThreshold",
            "Value": 80
          },
          {
            "Key": "VSAN.DedupScope",
            "Value": 0
          },
          {
            "Key": "VSAN.DefaultHostDecommissionMode",
            "Value": "ensureAccessibility"
          },
          {
            "Key": "VSAN.DomBriefIoTraces",
            "Value": 0
          },
          {
            "Key": "VSAN.DomFullIoTraces",
            "Value": 0
          },
          {
            "Key": "VSAN.DomLongOpTraceMS",
            "Value": 1000
          },
          {
            "Key": "VSAN.DomLongOpUrgentTraceMS",
            "Value": 10000
          },
          {
            "Key": "VSAN.ObjectScrubsPerYear",
            "Value": 1
          },
          {
            "Key": "VSAN.PerTraceBandwidthLimit",
            "Value": 0
          },
          {
            "Key": "VSAN.PerTraceBandwidthLimitPeriodMs",
            "Value": 10000
          },
          {
            "Key": "VSAN.PerTraceMaxRolloverPeriods",
            "Value": 360
          },
          {
            "Key": "VSAN.TraceEnableCmmds",
            "Value": 1
          },
          {
            "Key": "VSAN.TraceEnableDom",
            "Value": 1
          },
          {
            "Key": "VSAN.TraceEnableDomIo",
            "Value": 0
          },
          {
            "Key": "VSAN.TraceEnableLchk",
            "Value": 1
          },
          {
            "Key": "VSAN.TraceEnableLsom",
            "Value": 1
          },
          {
            "Key": "VSAN.TraceEnableLsomIo",
            "Value": 0
          },
          {
            "Key": "VSAN.TraceEnablePlog",
            "Value": 1
          },
          {
            "Key": "VSAN.TraceEnableRdt",
            "Value": 1
          },
          {
            "Key": "VSAN.TraceEnableSsdLog",
            "Value": 1
          },
          {
            "Key": "VSAN.TraceEnableVirsto",
            "Value": 1
          },
          {
            "Key": "VSAN.TraceEnableVirstoIo",
            "Value": 0
          },
          {
            "Key": "VSAN.TraceEnableVsanSparse",
            "Value": 1
          },
          {
            "Key": "VSAN.TraceEnableVsanSparseIO",
            "Value": 0
          },
          {
            "Key": "VSAN.TraceEnableVsanSparseVerbose",
            "Value": 0
          },
          {
            "Key": "VSAN.TraceGlobalBandwidthLimit",
            "Value": 0
          },
          {
            "Key": "VSAN.TraceGlobalBandwidthLimitPeriodMs",
            "Value": 10000
          },
          {
            "Key": "VSAN.TraceGlobalMaxRolloverPeriods",
            "Value": 360
          },
          {
            "Key": "VSAN.TracesPerErrorBandwidthLimit",
            "Value": 1000
          },
          {
            "Key": "VSAN.TracesPerErrorBandwidthLimitPeriodMs",
            "Value": 10000
          },
          {
            "Key": "VSAN.TracesPerErrorMaxRolloverPeriods",
            "Value": 60
          },
          {
            "Key": "VSAN.VsanSparseCacheOverEvict",
            "Value": 5
          },
          {
            "Key": "VSAN.VsanSparseCacheThreshold",
            "Value": 1024
          },
          {
            "Key": "VSAN.VsanSparseEnabled",
            "Value": 1
          },
          {
            "Key": "VSAN.VsanSparseHeapSize",
            "Value": 65536
          },
          {
            "Key": "VSAN.VsanSparseMaxExtentsPrefetch",
            "Value": 64
          },
          {
            "Key": "VSAN.VsanSparseParallelLookup",
            "Value": 1
          },
          {
            "Key": "VSAN.VsanSparseRetainCacheOnSnapshots",
            "Value": 1
          },
          {
            "Key": "VSAN.VsanSparseRetainCacheTTL",
            "Value": 20
          },
          {
            "Key": "VSAN.VsanSparseSpeculativePrefetch",
            "Value": 4194304
          },
          {
            "Key": "Virsto.DiskFormatVersion",
            "Value": 10
          },
          {
            "Key": "Virsto.Enabled",
            "Value": 1
          },
          {
            "Key": "Virsto.FlusherRegistryThreshold",
            "Value": 95
          },
          {
            "Key": "Virsto.GweFetchExtentsFactor",
            "Value": 3
          },
          {
            "Key": "Virsto.InstanceHeapLimit",
            "Value": 130
          },
          {
            "Key": "Virsto.MapBlocksFlushThreshold",
            "Value": 90
          },
          {
            "Key": "Virsto.MapBlocksMin",
            "Value": 16384
          },
          {
            "Key": "Virsto.MaxMFRetryCount",
            "Value": 3
          },
          {
            "Key": "Virsto.MsecBeforeMetaFlush",
            "Value": 10000
          },
          {
            "Key": "Virsto.RecordsPerFormatWrite",
            "Value": 16
          },
          {
            "Key": "Virsto.SharedHeapLimit",
            "Value": 4
          },
          {
            "Key": "XvMotion.VMFSOptimizations",
            "Value": 1
          }
        ],
        "OptionDef": [
          {
            "Label": "Welcome message for the Direct Console User Interface (DCUI)",
            "Summary": "A welcome message in the initial screen of the Direct Console User Interface (DCUI). Use an empty string to reset to the default message.",
            "Key": "Annotations.WelcomeMessage",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": ""
            }
          },
          {
            "Label": "Flush interval",
            "Summary": "Flush at this interval (milliseconds)",
            "Key": "BufferCache.FlushInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 3600000,
              "DefaultValue": 30000
            }
          },
          {
            "Label": "Maximum dirty buffers",
            "Summary": "Block writers if this many buffers are dirty (percent)",
            "Key": "BufferCache.HardMaxDirty",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 95
            }
          },
          {
            "Label": "PerFileHardMaxDirty",
            "Summary": "Block writers if this many buffers of a given file are dirtied (percent)",
            "Key": "BufferCache.PerFileHardMaxDirty",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 50
            }
          },
          {
            "Label": "Soft maximum dirty buffers",
            "Summary": "Flush immediately if this many buffers are dirty (percent)",
            "Key": "BufferCache.SoftMaxDirty",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 15
            }
          },
          {
            "Label": "DCacheMemReserved",
            "Summary": "Memory consumed by CBRC Data Cache (in MB)",
            "Key": "CBRC.DCacheMemReserved",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 32768,
              "DefaultValue": 400
            }
          },
          {
            "Label": "DCacheSize",
            "Summary": "Size of CBRC Data Cache in MB. This cannot be changed if CBRC.Enable is set to 1.",
            "Key": "CBRC.DCacheSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 32768,
              "Max": 32768,
              "DefaultValue": 32768
            }
          },
          {
            "Label": "DigestJournalBootInterval",
            "Summary": "Interval (in minutes) for which Digest Journal is temporarily disabled to avoid interfering with the boot process",
            "Key": "CBRC.DigestJournalBootInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000,
              "DefaultValue": 10
            }
          },
          {
            "Label": "Enable",
            "Summary": "Enable Content Based Read Cache",
            "Key": "CBRC.Enable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "COWMaxHeapSizeMB",
            "Summary": "Maximum size (in MB) to which the COW heap is allowed to grow",
            "Key": "COW.COWMaxHeapSizeMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 192,
              "Max": 256,
              "DefaultValue": 192
            }
          },
          {
            "Label": "COWMaxREPageCacheszMB",
            "Summary": "Maximum size (in MB) of VMFSSparse metadata cache size before cache eviction kicks in",
            "Key": "COW.COWMaxREPageCacheszMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 32,
              "Max": 512,
              "DefaultValue": 256
            }
          },
          {
            "Label": "COWMinREPageCacheszMB",
            "Summary": "Minimum size (in MB) of VMFSSparse metadata cache size. Valid when cache eviction is enabled.",
            "Key": "COW.COWMinREPageCacheszMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 512,
              "DefaultValue": 0
            }
          },
          {
            "Label": "COWREPageCacheEviction",
            "Summary": "VMFSSparse metadata cache eviction: 0 - disabled, 1 enabled",
            "Key": "COW.COWREPageCacheEviction",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "security.host.ruissl",
            "Summary": "Require SSL to be used when communicating with the host over port 902.",
            "Key": "Config.Defaults.security.host.ruissl",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "vGPU.consolidation",
            "Summary": "Assignment policy to place shared passthru graphics VMs on same physical GPU until full.",
            "Key": "Config.Defaults.vGPU.consolidation",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "issue",
            "Summary": "Contents of /etc/issue",
            "Key": "Config.Etc.issue",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": ""
            }
          },
          {
            "Label": "motd",
            "Summary": "Contents of /etc/motd",
            "Key": "Config.Etc.motd",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": ""
            }
          },
          {
            "Label": "guest.commands.sharedPolicyRefCount",
            "Summary": "Reference count to enable guest operations.",
            "Key": "Config.GlobalSettings.guest.commands.sharedPolicyRefCount",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2147483647,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Hbrsvc Logging level",
            "Summary": "Logging level for the loggers related to HBR services.",
            "Key": "Config.HostAgent.level[Hbrsvc].logLevel",
            "OptionType": {
              "ValueIsReadonly": false,
              "ChoiceInfo": [
                {
                  "Label": "default to the global log level.",
                  "Summary": "default to the global log level.",
                  "Key": ""
                },
                {
                  "Label": "None",
                  "Summary": "None",
                  "Key": "none"
                },
                {
                  "Label": "Quiet",
                  "Summary": "Quiet",
                  "Key": "quiet"
                },
                {
                  "Label": "Panic",
                  "Summary": "Panic",
                  "Key": "panic"
                },
                {
                  "Label": "Error",
                  "Summary": "Error",
                  "Key": "error"
                },
                {
                  "Label": "Warning",
                  "Summary": "Warning",
                  "Key": "warning"
                },
                {
                  "Label": "Info",
                  "Summary": "Info",
                  "Key": "info"
                },
                {
                  "Label": "Verbose",
                  "Summary": "Verbose",
                  "Key": "verbose"
                },
                {
                  "Label": "Trivia",
                  "Summary": "Trivia",
                  "Key": "trivia"
                }
              ],
              "DefaultIndex": 0
            }
          },
          {
            "Label": "Hostsvc Logging level",
            "Summary": "Logging level for the loggers related to Host services.",
            "Key": "Config.HostAgent.level[Hostsvc].logLevel",
            "OptionType": {
              "ValueIsReadonly": false,
              "ChoiceInfo": [
                {
                  "Label": "default to the global log level.",
                  "Summary": "default to the global log level.",
                  "Key": ""
                },
                {
                  "Label": "None",
                  "Summary": "None",
                  "Key": "none"
                },
                {
                  "Label": "Quiet",
                  "Summary": "Quiet",
                  "Key": "quiet"
                },
                {
                  "Label": "Panic",
                  "Summary": "Panic",
                  "Key": "panic"
                },
                {
                  "Label": "Error",
                  "Summary": "Error",
                  "Key": "error"
                },
                {
                  "Label": "Warning",
                  "Summary": "Warning",
                  "Key": "warning"
                },
                {
                  "Label": "Info",
                  "Summary": "Info",
                  "Key": "info"
                },
                {
                  "Label": "Verbose",
                  "Summary": "Verbose",
                  "Key": "verbose"
                },
                {
                  "Label": "Trivia",
                  "Summary": "Trivia",
                  "Key": "trivia"
                }
              ],
              "DefaultIndex": 0
            }
          },
          {
            "Label": "Proxysvc Logging level",
            "Summary": "Logging level for the loggers related to proxy services.",
            "Key": "Config.HostAgent.level[Proxysvc].logLevel",
            "OptionType": {
              "ValueIsReadonly": false,
              "ChoiceInfo": [
                {
                  "Label": "default to the global log level.",
                  "Summary": "default to the global log level.",
                  "Key": ""
                },
                {
                  "Label": "None",
                  "Summary": "None",
                  "Key": "none"
                },
                {
                  "Label": "Quiet",
                  "Summary": "Quiet",
                  "Key": "quiet"
                },
                {
                  "Label": "Panic",
                  "Summary": "Panic",
                  "Key": "panic"
                },
                {
                  "Label": "Error",
                  "Summary": "Error",
                  "Key": "error"
                },
                {
                  "Label": "Warning",
                  "Summary": "Warning",
                  "Key": "warning"
                },
                {
                  "Label": "Info",
                  "Summary": "Info",
                  "Key": "info"
                },
                {
                  "Label": "Verbose",
                  "Summary": "Verbose",
                  "Key": "verbose"
                },
                {
                  "Label": "Trivia",
                  "Summary": "Trivia",
                  "Key": "trivia"
                }
              ],
              "DefaultIndex": 0
            }
          },
          {
            "Label": "Snmpsvc Logging level",
            "Summary": "Logging level for the loggers related to SNMP services.",
            "Key": "Config.HostAgent.level[Snmpsvc].logLevel",
            "OptionType": {
              "ValueIsReadonly": false,
              "ChoiceInfo": [
                {
                  "Label": "default to the global log level.",
                  "Summary": "default to the global log level.",
                  "Key": ""
                },
                {
                  "Label": "None",
                  "Summary": "None",
                  "Key": "none"
                },
                {
                  "Label": "Quiet",
                  "Summary": "Quiet",
                  "Key": "quiet"
                },
                {
                  "Label": "Panic",
                  "Summary": "Panic",
                  "Key": "panic"
                },
                {
                  "Label": "Error",
                  "Summary": "Error",
                  "Key": "error"
                },
                {
                  "Label": "Warning",
                  "Summary": "Warning",
                  "Key": "warning"
                },
                {
                  "Label": "Info",
                  "Summary": "Info",
                  "Key": "info"
                },
                {
                  "Label": "Verbose",
                  "Summary": "Verbose",
                  "Key": "verbose"
                },
                {
                  "Label": "Trivia",
                  "Summary": "Trivia",
                  "Key": "trivia"
                }
              ],
              "DefaultIndex": 0
            }
          },
          {
            "Label": "Statssvc Logging level",
            "Summary": "Logging level for the loggers related to Stats services.",
            "Key": "Config.HostAgent.level[Statssvc].logLevel",
            "OptionType": {
              "ValueIsReadonly": false,
              "ChoiceInfo": [
                {
                  "Label": "default to the global log level.",
                  "Summary": "default to the global log level.",
                  "Key": ""
                },
                {
                  "Label": "None",
                  "Summary": "None",
                  "Key": "none"
                },
                {
                  "Label": "Quiet",
                  "Summary": "Quiet",
                  "Key": "quiet"
                },
                {
                  "Label": "Panic",
                  "Summary": "Panic",
                  "Key": "panic"
                },
                {
                  "Label": "Error",
                  "Summary": "Error",
                  "Key": "error"
                },
                {
                  "Label": "Warning",
                  "Summary": "Warning",
                  "Key": "warning"
                },
                {
                  "Label": "Info",
                  "Summary": "Info",
                  "Key": "info"
                },
                {
                  "Label": "Verbose",
                  "Summary": "Verbose",
                  "Key": "verbose"
                },
                {
                  "Label": "Trivia",
                  "Summary": "Trivia",
                  "Key": "trivia"
                }
              ],
              "DefaultIndex": 0
            }
          },
          {
            "Label": "Vcsvc Logging level",
            "Summary": "Logging level for the loggres related to VC services.",
            "Key": "Config.HostAgent.level[Vcsvc].logLevel",
            "OptionType": {
              "ValueIsReadonly": false,
              "ChoiceInfo": [
                {
                  "Label": "default to the global log level.",
                  "Summary": "default to the global log level.",
                  "Key": ""
                },
                {
                  "Label": "None",
                  "Summary": "None",
                  "Key": "none"
                },
                {
                  "Label": "Quiet",
                  "Summary": "Quiet",
                  "Key": "quiet"
                },
                {
                  "Label": "Panic",
                  "Summary": "Panic",
                  "Key": "panic"
                },
                {
                  "Label": "Error",
                  "Summary": "Error",
                  "Key": "error"
                },
                {
                  "Label": "Warning",
                  "Summary": "Warning",
                  "Key": "warning"
                },
                {
                  "Label": "Info",
                  "Summary": "Info",
                  "Key": "info"
                },
                {
                  "Label": "Verbose",
                  "Summary": "Verbose",
                  "Key": "verbose"
                },
                {
                  "Label": "Trivia",
                  "Summary": "Trivia",
                  "Key": "trivia"
                }
              ],
              "DefaultIndex": 0
            }
          },
          {
            "Label": "Vimsvc Logging level",
            "Summary": "Logging level for the loggers related to Virtual infrastructure management services.",
            "Key": "Config.HostAgent.level[Vimsvc].logLevel",
            "OptionType": {
              "ValueIsReadonly": false,
              "ChoiceInfo": [
                {
                  "Label": "default to the global log level.",
                  "Summary": "default to the global log level.",
                  "Key": ""
                },
                {
                  "Label": "None",
                  "Summary": "None",
                  "Key": "none"
                },
                {
                  "Label": "Quiet",
                  "Summary": "Quiet",
                  "Key": "quiet"
                },
                {
                  "Label": "Panic",
                  "Summary": "Panic",
                  "Key": "panic"
                },
                {
                  "Label": "Error",
                  "Summary": "Error",
                  "Key": "error"
                },
                {
                  "Label": "Warning",
                  "Summary": "Warning",
                  "Key": "warning"
                },
                {
                  "Label": "Info",
                  "Summary": "Info",
                  "Key": "info"
                },
                {
                  "Label": "Verbose",
                  "Summary": "Verbose",
                  "Key": "verbose"
                },
                {
                  "Label": "Trivia",
                  "Summary": "Trivia",
                  "Key": "trivia"
                }
              ],
              "DefaultIndex": 0
            }
          },
          {
            "Label": "Vmsvc logging level",
            "Summary": "Logging level for the loggers related to VM services.",
            "Key": "Config.HostAgent.level[Vmsvc].logLevel",
            "OptionType": {
              "ValueIsReadonly": false,
              "ChoiceInfo": [
                {
                  "Label": "default to the global log level.",
                  "Summary": "default to the global log level.",
                  "Key": ""
                },
                {
                  "Label": "None",
                  "Summary": "None",
                  "Key": "none"
                },
                {
                  "Label": "Quiet",
                  "Summary": "Quiet",
                  "Key": "quiet"
                },
                {
                  "Label": "Panic",
                  "Summary": "Panic",
                  "Key": "panic"
                },
                {
                  "Label": "Error",
                  "Summary": "Error",
                  "Key": "error"
                },
                {
                  "Label": "Warning",
                  "Summary": "Warning",
                  "Key": "warning"
                },
                {
                  "Label": "Info",
                  "Summary": "Info",
                  "Key": "info"
                },
                {
                  "Label": "Verbose",
                  "Summary": "Verbose",
                  "Key": "verbose"
                },
                {
                  "Label": "Trivia",
                  "Summary": "Trivia",
                  "Key": "trivia"
                }
              ],
              "DefaultIndex": 0
            }
          },
          {
            "Label": "log.level",
            "Summary": "Controls the global log level.",
            "Key": "Config.HostAgent.log.level",
            "OptionType": {
              "ValueIsReadonly": false,
              "ChoiceInfo": [
                {
                  "Label": "None",
                  "Summary": "None",
                  "Key": "none"
                },
                {
                  "Label": "Quiet",
                  "Summary": "Quiet",
                  "Key": "quiet"
                },
                {
                  "Label": "Panic",
                  "Summary": "Panic",
                  "Key": "panic"
                },
                {
                  "Label": "Error",
                  "Summary": "Error",
                  "Key": "error"
                },
                {
                  "Label": "Warning",
                  "Summary": "Warning",
                  "Key": "warning"
                },
                {
                  "Label": "Info",
                  "Summary": "Info",
                  "Key": "info"
                },
                {
                  "Label": "Verbose",
                  "Summary": "Verbose",
                  "Key": "verbose"
                },
                {
                  "Label": "Trivia",
                  "Summary": "Trivia",
                  "Key": "trivia"
                }
              ],
              "DefaultIndex": 5
            }
          },
          {
            "Label": "plugins.hostsvc.esxAdminsGroup",
            "Summary": "Active Directory group name that is automatically granted administrator privileges on the ESX.",
            "Key": "Config.HostAgent.plugins.hostsvc.esxAdminsGroup",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "ESX Admins",
              "ValidCharacters": ""
            }
          },
          {
            "Label": "plugins.hostsvc.esxAdminsGroupAutoAdd",
            "Summary": "Controls whether the group specified by 'esxAdminsGroup' is automatically granted administrator permission.",
            "Key": "Config.HostAgent.plugins.hostsvc.esxAdminsGroupAutoAdd",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "plugins.hostsvc.esxAdminsGroupUpdateInterval",
            "Summary": "Interval between checks for whether the group specified by 'esxAdminsGroup' has appeared in Active Directory, in minutes.",
            "Key": "Config.HostAgent.plugins.hostsvc.esxAdminsGroupUpdateInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 30,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Debug Managed Object Browser enabled",
            "Summary": "Enables or disables the Debug Managed Object Browser for the ESXi host.",
            "Key": "Config.HostAgent.plugins.solo.enableMob",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "Enable webscript launcher page",
            "Summary": "Controls the availability of webscript launcher page.",
            "Key": "Config.HostAgent.plugins.solo.webServer.enableWebscriptLauncher",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "plugins.vimsvc.authValidateInterval",
            "Summary": "Number of minutes between each validation of all known users and groups - set to zero to disable validation.",
            "Key": "Config.HostAgent.plugins.vimsvc.authValidateInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2147483647,
              "DefaultValue": 1440
            }
          },
          {
            "Label": "plugins.vimsvc.userSearch.maxResults",
            "Summary": "Maximum number of users and groups to display in the Add Permissions dialog - set to zero to disable the limit.",
            "Key": "Config.HostAgent.plugins.vimsvc.userSearch.maxResults",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2147483647,
              "DefaultValue": 100
            }
          },
          {
            "Label": "plugins.vimsvc.userSearch.maxTimeSeconds",
            "Summary": "Number of seconds to wait for a search for users and groups to return results - set to zero to disable the timeout.",
            "Key": "Config.HostAgent.plugins.vimsvc.userSearch.maxTimeSeconds",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 180,
              "DefaultValue": 20
            }
          },
          {
            "Label": "Limit the number of registered virtual machines",
            "Summary": "Enables enforcing the limitation on the number of registered virtual machines in the inventory.",
            "Key": "Config.HostAgent.plugins.vmsvc.enforceMaxRegisteredVms",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": false,
              "DefaultValue": true
            }
          },
          {
            "Label": "plugins.vmsvc.productLockerWatchIntervalSeconds",
            "Summary": "Interval between checks for whether the product locker files are changed, in seconds. Set to -1 to disable checks.",
            "Key": "Config.HostAgent.plugins.vmsvc.productLockerWatchInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": -1,
              "Max": 86400,
              "DefaultValue": 300
            }
          },
          {
            "Label": "ssl.keyStore.allowSelfSigned",
            "Summary": "Allow self-signed certificates to be added to the host CA store.",
            "Key": "Config.HostAgent.ssl.keyStore.allowSelfSigned",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "vmacore.soap.sessionTimeout",
            "Summary": "Number of minutes until a VIM API session times out due to inactivity - set to zero to disable timeout. Applies to new sessions only.",
            "Key": "Config.HostAgent.vmacore.soap.sessionTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2147483647,
              "DefaultValue": 30
            }
          },
          {
            "Label": "ActionLoadThreshold",
            "Summary": "load threshold below which vcpu affinity migration is performed, 0 to disable",
            "Key": "Cpu.ActionLoadThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 10
            }
          },
          {
            "Label": "AllowWideVsmp",
            "Summary": "Allow VMs with more VCPUs than host PCPUs, 0 to disable",
            "Key": "Cpu.AllowWideVsmp",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Bound lag quanta",
            "Summary": "Number of global quanta before bound lag",
            "Key": "Cpu.BoundLagQuanta",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 100,
              "DefaultValue": 8
            }
          },
          {
            "Label": "CommRateThreshold",
            "Summary": "threshold for inter-sched-context rate above which the contexts are considered to be related (in num/sec)",
            "Key": "Cpu.CommRateThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 50000,
              "DefaultValue": 500
            }
          },
          {
            "Label": "CoschedCostartThreshold",
            "Summary": "costart threshold in usec, costart threshold should be less than costopThreshold",
            "Key": "Cpu.CoschedCostartThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100000,
              "DefaultValue": 2000
            }
          },
          {
            "Label": "CoschedCostopThreshold",
            "Summary": "maximum skew between vcpus in usec, 0 to disable",
            "Key": "Cpu.CoschedCostopThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100000,
              "DefaultValue": 3000
            }
          },
          {
            "Label": "CoschedCrossCall",
            "Summary": "0: disable cosched on crosscall; 1: enable cosched on crosscall",
            "Key": "Cpu.CoschedCrossCall",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CoschedExclusiveAffinity",
            "Summary": "1 to enable coscheduling for vcpus with exclusive affinity, 0 to disable",
            "Key": "Cpu.CoschedExclusiveAffinity",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "CoschedHandoffLLC",
            "Summary": "0: handoff by switching pcpu; 1: handoff to LLC if possible",
            "Key": "Cpu.CoschedHandoffLLC",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CoschedHandoffSkip",
            "Summary": "only skip handoff if ready time is smaller than this threshold, in usec, 0 to allow skip always",
            "Key": "Cpu.CoschedHandoffSkip",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000,
              "DefaultValue": 10
            }
          },
          {
            "Label": "CoschedPollUsec",
            "Summary": "interval between coscheduling skew checks, in usec",
            "Key": "Cpu.CoschedPollUsec",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1000,
              "Max": 5000,
              "DefaultValue": 1000
            }
          },
          {
            "Label": "Credit age period",
            "Summary": "Period in milliseconds",
            "Key": "Cpu.CreditAgePeriod",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1000,
              "Max": 10000,
              "DefaultValue": 3000
            }
          },
          {
            "Label": "FairnessRebalancePcpus",
            "Summary": "max number of PCPUs to be considered when doing fairness rebalance, 0 to disable",
            "Key": "Cpu.FairnessRebalancePcpus",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 32,
              "DefaultValue": 4
            }
          },
          {
            "Label": "HTRebalancePeriod",
            "Summary": "average milliseconds between opportunities for a pcpu to migrate vcpus from within a core",
            "Key": "Cpu.HTRebalancePeriod",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 5000,
              "DefaultValue": 5
            }
          },
          {
            "Label": "HTStolenAgeThreshold",
            "Summary": "the amount of htStolen time a vcpu can keep without being aged (in seconds)",
            "Key": "Cpu.HTStolenAgeThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 500,
              "DefaultValue": 8
            }
          },
          {
            "Label": "HTWholeCoreThreshold",
            "Summary": "a vcpu with vtime falling behind by this threshold (in ms) is eligible to use the whole core (HT only), 0 to disable (may violate resource settings)",
            "Key": "Cpu.HTWholeCoreThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 5000,
              "DefaultValue": 800
            }
          },
          {
            "Label": "HostRebalancePeriod",
            "Summary": "average milliseconds between opportunities for a pcpu to migrate vcpus from within the whole system, 0 to disable",
            "Key": "Cpu.HostRebalancePeriod",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 5000,
              "DefaultValue": 100
            }
          },
          {
            "Label": "L2RebalancePeriod",
            "Summary": "average milliseconds between opportunities for a pcpu to migrate vcpus from within the shared L2 cache, 0 to disable",
            "Key": "Cpu.L2RebalancePeriod",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 5000,
              "DefaultValue": 10
            }
          },
          {
            "Label": "L3RebalancePeriod",
            "Summary": "average milliseconds between opportunities for a pcpu to migrate vcpus from within the shared L3 cache, 0 to disable",
            "Key": "Cpu.L3RebalancePeriod",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 5000,
              "DefaultValue": 20
            }
          },
          {
            "Label": "LimitEnforcementThreshold",
            "Summary": "Only allows low-vtime children ro run when a group/VM's vtimeLimit is smaller than the global virtual time by less than this threshold (in ms), 0 to disable",
            "Key": "Cpu.LimitEnforcementThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000,
              "DefaultValue": 200
            }
          },
          {
            "Label": "MaxSampleRateLg",
            "Summary": "Sampling system services at most (2^MaxSampleRateLg) times a second",
            "Key": "Cpu.MaxSampleRateLg",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 15,
              "DefaultValue": 7
            }
          },
          {
            "Label": "MoveCurrentRunnerPcpus",
            "Summary": "max number of PCPUs to be considered when searching for runner move opportunity, 0 to disable",
            "Key": "Cpu.MoveCurrentRunnerPcpus",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 32,
              "DefaultValue": 4
            }
          },
          {
            "Label": "NonTimerWakeupRate",
            "Summary": "Disable P state if the running vcpu's non-timer wakeup rate is higher than this threshold, 0 to disable",
            "Key": "Cpu.NonTimerWakeupRate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 5000,
              "DefaultValue": 500
            }
          },
          {
            "Label": "PackageRebalancePeriod",
            "Summary": "average milliseconds between opportunities for a pcpu to migrate vcpus from within a package, 0 to disable",
            "Key": "Cpu.PackageRebalancePeriod",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 5000,
              "DefaultValue": 20
            }
          },
          {
            "Label": "PcpuMigrateIdlePcpus",
            "Summary": "max number of PCPUs to be considered when doing pcpu idle rebalance, 0 to disable",
            "Key": "Cpu.PcpuMigrateIdlePcpus",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 32,
              "DefaultValue": 4
            }
          },
          {
            "Label": "Quantum",
            "Summary": "Quantum in milliseconds",
            "Key": "Cpu.Quantum",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 1000,
              "DefaultValue": 200
            }
          },
          {
            "Label": "UseMwait",
            "Summary": "use MWAIT vs. HLT in the idle loop; 0: use HLT, 1: use MWAIT if possible, 2: choose by cpu type",
            "Key": "Cpu.UseMwait",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 3,
              "DefaultValue": 2
            }
          },
          {
            "Label": "Enable admission check per virtual CPU",
            "Summary": "Perform additional admission control check that per virtual CPU virtual machine minimum does not exceed the speed of a single physical CPU",
            "Key": "Cpu.VMAdmitCheckPerVcpuMin",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "WakeupMigrateIdlePcpus",
            "Summary": "max number of PCPUs to be considered when doing wakeup idle rebalance, 0 to disable",
            "Key": "Cpu.WakeupMigrateIdlePcpus",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 32,
              "DefaultValue": 4
            }
          },
          {
            "Label": "DCUI Access",
            "Summary": "One or more comma-separated local users which are granted unconditional access to DCUI, even if they don't have administrator role on the host.",
            "Key": "DCUI.Access",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "root",
              "ValidCharacters": ""
            }
          },
          {
            "Label": "HardwareAcceleratedInit",
            "Summary": "Enable hardware accelerated VMFS data initialization (requires compliant hardware)",
            "Key": "DataMover.HardwareAcceleratedInit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "HardwareAcceleratedMove",
            "Summary": "Enable hardware accelerated VMFS data movement (requires compliant hardware)",
            "Key": "DataMover.HardwareAcceleratedMove",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "MaxHeapSize",
            "Summary": "Maximum size of the heap in MB used for data movement",
            "Key": "DataMover.MaxHeapSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 16,
              "Max": 256,
              "DefaultValue": 64
            }
          },
          {
            "Label": "AlgoType",
            "Summary": "Digest Crypto Hash Type (1=SHA-1, 2=SHA-256).",
            "Key": "Digest.AlgoType",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 2,
              "DefaultValue": 1
            }
          },
          {
            "Label": "BlockSize",
            "Summary": "Blocksize in the original VMDK to compute crypto hash codes. In pages of 4K size. Value needs to be power of 2.",
            "Key": "Digest.BlockSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 2,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CollisionEnabled",
            "Summary": "Enable collision detection (0=disabled, 1=enabled)",
            "Key": "Digest.CollisionEnabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "JournalCoverage",
            "Summary": "Journal coverage in MB. Value needs to be power of 2.",
            "Key": "Digest.JournalCoverage",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 32,
              "DefaultValue": 8
            }
          },
          {
            "Label": "UpdateOnClose",
            "Summary": "Enable crypto-hash updates on DiskLib_Close (0=disabled, 1=enabled).",
            "Key": "Digest.UpdateOnClose",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "MaxDentryPerObj",
            "Summary": "Maximum directory entries cached per directory",
            "Key": "DirentryCache.MaxDentryPerObj",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 60,
              "Max": 30000,
              "DefaultValue": 15000
            }
          },
          {
            "Label": "AllowUsbClaimedAsSSD",
            "Summary": "Permit claim rules to mark USB disks as SSD",
            "Key": "Disk.AllowUsbClaimedAsSSD",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "APD Token Retry Count",
            "Summary": "APD Token Retry Count",
            "Key": "Disk.ApdTokenRetryCount",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 99999,
              "DefaultValue": 25
            }
          },
          {
            "Label": "AutoremoveOnPDL",
            "Summary": "Autoremove paths to a disk that is in PDL (Permanent Device Loss)",
            "Key": "Disk.AutoremoveOnPDL",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "BandwidthCap",
            "Summary": "cap on disk bandwidth (KB/s) usage",
            "Key": "Disk.BandwidthCap",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 4294967294,
              "DefaultValue": 4294967294
            }
          },
          {
            "Label": "Delay on busy",
            "Summary": "Delay in milliseconds for completion of commands with a BUSY status",
            "Key": "Disk.DelayOnBusy",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 5000,
              "DefaultValue": 400
            }
          },
          {
            "Label": "DeviceReclaimTime",
            "Summary": "The number of seconds between device re-claim attempts",
            "Key": "Disk.DeviceReclaimTime",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 31536000,
              "DefaultValue": 300
            }
          },
          {
            "Label": "Disable 4kn SSDs",
            "Summary": "Disable use of 4kn SSDs",
            "Key": "Disk.Disable4knSSD",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Disable VSCSI_Poll in bottom half",
            "Summary": "Disable VSCSI_Poll in bottom half. Set to 1 to disable.",
            "Key": "Disk.DisableVSCSIPollInBH",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "DiskDelayPDLHelper",
            "Summary": "Delay PDL helper in secs",
            "Key": "Disk.DiskDelayPDLHelper",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 300,
              "DefaultValue": 10
            }
          },
          {
            "Label": "DiskMaxIOSize",
            "Summary": "Max Disk READ/WRITE I/O size before splitting (in KB)",
            "Key": "Disk.DiskMaxIOSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 32,
              "Max": 32767,
              "DefaultValue": 32767
            }
          },
          {
            "Label": "DiskReservationThreshold",
            "Summary": "Time window within which refcounted reservations on a device are permitted (in msec)",
            "Key": "Disk.DiskReservationThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 3000,
              "DefaultValue": 45
            }
          },
          {
            "Label": "DiskRetryPeriod",
            "Summary": "Retry period in milliseconds for a command with retry status",
            "Key": "Disk.DiskRetryPeriod",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 500,
              "Max": 50000,
              "DefaultValue": 2000
            }
          },
          {
            "Label": "DumpMaxRetries",
            "Summary": "Max number of I/O retries during disk dump",
            "Key": "Disk.DumpMaxRetries",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000,
              "DefaultValue": 10
            }
          },
          {
            "Label": "DumpPollDelay",
            "Summary": "Number of microseconds to wait between polls during a disk dump.",
            "Key": "Disk.DumpPollDelay",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 100000,
              "DefaultValue": 1000
            }
          },
          {
            "Label": "DumpPollMaxRetries",
            "Summary": "Max number of device poll retries during disk dump",
            "Key": "Disk.DumpPollMaxRetries",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 100000,
              "DefaultValue": 10000
            }
          },
          {
            "Label": "EnableNaviReg",
            "Summary": "Enable automatic NaviAgent registration with EMC CLARiiON and Invista",
            "Key": "Disk.EnableNaviReg",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "FailDiskRegistration",
            "Summary": "Fail device registration if disk has only standby paths and supports only implicit asymmetric logical unit access.",
            "Key": "Disk.FailDiskRegistration",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Fast-path latency monitoring interval",
            "Summary": "Time interval (in msec) to monitor the IO latency to evaluate eligibility for fast-path in PSA.",
            "Key": "Disk.FastPathRestoreInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 1000,
              "DefaultValue": 100
            }
          },
          {
            "Label": "IdleCredit",
            "Summary": "Amount of idle credit that a virtual machine can gain for I/O requests",
            "Key": "Disk.IdleCredit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 512,
              "DefaultValue": 32
            }
          },
          {
            "Label": "Maximum LUN",
            "Summary": "Only LUNs with LUN ID values below this value will be scanned",
            "Key": "Disk.MaxLUN",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 16384,
              "DefaultValue": 1024
            }
          },
          {
            "Label": "Maximum number of IO intervals",
            "Summary": "Maximum number of IO intervals per device that can be stored in order to detect overlapping IOs to 4Kn disks",
            "Key": "Disk.MaxNumIOIntervals",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 64,
              "Max": 65535,
              "DefaultValue": 1024
            }
          },
          {
            "Label": "Maximum reset latency",
            "Summary": "Delay in milliseconds before logging warnings and spawning new reset worlds if a reset is overdue or taking too long",
            "Key": "Disk.MaxResetLatency",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 500,
              "Max": 600000,
              "DefaultValue": 2000
            }
          },
          {
            "Label": "Maximum commands in NMP",
            "Summary": "Increase the maximum number of commands to be processed at once in NMP",
            "Key": "Disk.NmpMaxCmdExtension",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000000000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Path evaluation time",
            "Summary": "The number of seconds between FC path evaluations",
            "Key": "Disk.PathEvalTime",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 31536000,
              "DefaultValue": 300
            }
          },
          {
            "Label": "PreventVMFSOverwrite",
            "Summary": "Prevent overwriting VMFS partitions",
            "Key": "Disk.PreventVMFSOverwrite",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "QFullSampleSize",
            "Summary": "I/O samples to monitor for detecting non-transient queue full condition. Should be nonzero to enable queue depth throttling.",
            "Key": "Disk.QFullSampleSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 64,
              "DefaultValue": 0
            }
          },
          {
            "Label": "QFullThreshold",
            "Summary": "BUSY or QFULL threshold, upon which LUN queue depth will be throttled. Should be \u003c= QFullSampleSize if throttling is enabled.",
            "Key": "Disk.QFullThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 16,
              "DefaultValue": 8
            }
          },
          {
            "Label": "ReqCallThreshold",
            "Summary": "Threshold in number of pending requests before calling into vmkernel to process the requests",
            "Key": "Disk.ReqCallThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 129,
              "DefaultValue": 8
            }
          },
          {
            "Label": "Reset latency",
            "Summary": "Delay in milliseconds between reset thread wakeups",
            "Key": "Disk.ResetLatency",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 600000,
              "DefaultValue": 1000
            }
          },
          {
            "Label": "Reset maximum retries",
            "Summary": "Maximum number of bus reset retries. Set to 0 for no limit.",
            "Key": "Disk.ResetMaxRetries",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Reset overdue log period",
            "Summary": "Delay in seconds between logs of overdue reset",
            "Key": "Disk.ResetOverdueLogPeriod",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 86400,
              "DefaultValue": 60
            }
          },
          {
            "Label": "Reset period",
            "Summary": "Delay in seconds between bus resets retries",
            "Key": "Disk.ResetPeriod",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 3600,
              "DefaultValue": 30
            }
          },
          {
            "Label": "Reset thread expires",
            "Summary": "Life in seconds of an inactive reset handle thread",
            "Key": "Disk.ResetThreadExpires",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 86400,
              "DefaultValue": 1800
            }
          },
          {
            "Label": "Reset thread maximum",
            "Summary": "Maximum number of reset handler threads",
            "Key": "Disk.ResetThreadMax",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 16,
              "DefaultValue": 16
            }
          },
          {
            "Label": "Reset thread minimum",
            "Summary": "Minimum number of reset handler threads",
            "Key": "Disk.ResetThreadMin",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 16,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Retry unit attention",
            "Summary": "Retry all SCSI commands that return a unit attention error",
            "Key": "Disk.RetryUnitAttention",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "ReturnCCForNoSpace",
            "Summary": "Return CC 0x7/0x27/0x7 in the event where a backing datastore has run out of space as opposed to posting a monitor event to halt the VM",
            "Key": "Disk.ReturnCCForNoSpace",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "SchedCostUnit",
            "Summary": "IO Scheduler block size for accounting",
            "Key": "Disk.SchedCostUnit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 4096,
              "Max": 262144,
              "DefaultValue": 32768
            }
          },
          {
            "Label": "SchedQ cleanup interval",
            "Summary": "Time interval (in secs) to cleanup per device unused schedQ list (default = 5 minutes).",
            "Key": "Disk.SchedQCleanupInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 31536000,
              "DefaultValue": 300
            }
          },
          {
            "Label": "Scheduled QControl sequence requests",
            "Summary": "Number of consecutive requests from a virtual machine required to raise the outstanding commands to maximum",
            "Key": "Disk.SchedQControlSeqReqs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2048,
              "DefaultValue": 128
            }
          },
          {
            "Label": "Scheduled QControl VM switches",
            "Summary": "Number of switches between commands issued by different virtual machines required to reduce outstanding commands to CONFIG_DISK_CIF",
            "Key": "Disk.SchedQControlVMSwitches",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2048,
              "DefaultValue": 6
            }
          },
          {
            "Label": "SchedQPriorityPercentage",
            "Summary": "Percentage of priority commands to serve from priority queue",
            "Key": "Disk.SchedQPriorityPercentage",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 100,
              "DefaultValue": 80
            }
          },
          {
            "Label": "Scheduled quantum",
            "Summary": "Number of consecutive requests from one world",
            "Key": "Disk.SchedQuantum",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 64,
              "DefaultValue": 8
            }
          },
          {
            "Label": "SchedReservationBurst",
            "Summary": "Permit I/O bursts in mclock scheduler with reservations.",
            "Key": "Disk.SchedReservationBurst",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "SchedulerWithReservation",
            "Summary": "Disk I/O scheduler (0:default 1:mclock)",
            "Key": "Disk.SchedulerWithReservation",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Sector maximum difference",
            "Summary": "Distance in sectors at which the disk BW schedules affinity stops",
            "Key": "Disk.SectorMaxDiff",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2000000,
              "DefaultValue": 2000
            }
          },
          {
            "Label": "Shares high",
            "Summary": "Shares for high disk priority",
            "Key": "Disk.SharesHigh",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 10000,
              "DefaultValue": 2000
            }
          },
          {
            "Label": "Shares low",
            "Summary": "Shares for low disk priority",
            "Key": "Disk.SharesLow",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 10000,
              "DefaultValue": 500
            }
          },
          {
            "Label": "Shares normal",
            "Summary": "Shares for normal disk priority",
            "Key": "Disk.SharesNormal",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 10000,
              "DefaultValue": 1000
            }
          },
          {
            "Label": "SllThrottleTime",
            "Summary": "Time (in msecs) I/Os to SLLs will be throttled in the event of a TASK_SET_FULL being received.",
            "Key": "Disk.SllThrottleTime",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 25,
              "Max": 3000,
              "DefaultValue": 800
            }
          },
          {
            "Label": "Support sparse LUN",
            "Summary": "Support for sparse LUNs if set to one",
            "Key": "Disk.SupportSparseLUN",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "ThroughputCap",
            "Summary": "Cap on disk throughput (IO/s) usage",
            "Key": "Disk.ThroughputCap",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 4294967294,
              "DefaultValue": 4294967294
            }
          },
          {
            "Label": "Use device reset",
            "Summary": "Use device reset (instead of bus reset) to reset a SCSI device",
            "Key": "Disk.UseDeviceReset",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Enable PSA deferred work pools",
            "Summary": "A bitmask: 0x1 - adapter submission pool, 0x2 - device completion pool, 0x4 - device queueing pool.",
            "Key": "Disk.UseIoPool",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 7,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Use LUN reset",
            "Summary": "Use LUN reset (instead of device.bus reset) to reset a SCSI device",
            "Key": "Disk.UseLunReset",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Use report LUN",
            "Summary": "Use the REPORT LUN command to increase scanning speed for devices",
            "Key": "Disk.UseReportLUN",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VSCSI Coalesce Count",
            "Summary": "Frequency with which the coalesce callback is called.",
            "Key": "Disk.VSCSICoalesceCount",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100000,
              "DefaultValue": 1000
            }
          },
          {
            "Label": "VSCSIEnablePreemption",
            "Summary": "Enable VSCSI Preemption.",
            "Key": "Disk.VSCSIEnablePreemption",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VSCSI Poll Period",
            "Summary": "Time in microseconds between VSCSI polls.",
            "Key": "Disk.VSCSIPollPeriod",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 99999999,
              "DefaultValue": 1000
            }
          },
          {
            "Label": "VSCSIResvCmdRetryInSecs",
            "Summary": "Time (in secs) to retry on transient errors for Reservation commands for MSCS CAB configs.",
            "Key": "Disk.VSCSIResvCmdRetryInSecs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 60,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VSCSIWriteSameBurstSize",
            "Summary": "Max number of split IOs per write same request.",
            "Key": "Disk.VSCSIWriteSameBurstSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 4,
              "DefaultValue": 4
            }
          },
          {
            "Label": "FSSLightWeightProbe",
            "Summary": "Enable light-weight efficient probe of ESX supported datastores",
            "Key": "FSS.FSSLightWeightProbe",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "AckIntervalMax",
            "Summary": "Maximum time in microseconds between acks",
            "Key": "FT.AckIntervalMax",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1000000,
              "Max": 4000000,
              "DefaultValue": 1000000
            }
          },
          {
            "Label": "AckIntervalMin",
            "Summary": "Minimum time in microseconds between acks",
            "Key": "FT.AckIntervalMin",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "BackupConnectTimeout",
            "Summary": "Time in milliseconds to wait for the backup to connect",
            "Key": "FT.BackupConnectTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 30000,
              "DefaultValue": 8000
            }
          },
          {
            "Label": "BackupExtraTimeout",
            "Summary": "Extra milliseconds before backup declares primary dead",
            "Key": "FT.BackupExtraTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 30000,
              "DefaultValue": 100
            }
          },
          {
            "Label": "BadExecLatency",
            "Summary": "Latency considered bad execution latency(ms)",
            "Key": "FT.BadExecLatency",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000,
              "DefaultValue": 800
            }
          },
          {
            "Label": "BindToVmknic",
            "Summary": "Bind the FT socket to a specific vmknic",
            "Key": "FT.BindToVmknic",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "ChargeVMXForFlush",
            "Summary": "Charge the VMX world for log flushing instead of the global flush world",
            "Key": "FT.ChargeVMXForFlush",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CheckFCPathState",
            "Summary": "Check storage path state",
            "Key": "FT.CheckFCPathState",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CheckForProgress",
            "Summary": "Check for progress on the backup and/or primary",
            "Key": "FT.CheckForProgress",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "CoreDumpNoProgressMS",
            "Summary": "Milliseconds before live coredump vmm and vmx if progress isn't made",
            "Key": "FT.CoreDumpNoProgressMS",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "ExecLatencyKill",
            "Summary": "Time to wait for good latency before killing a slow backup(ms)",
            "Key": "FT.ExecLatencyKill",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "ExtraLogTimeout",
            "Summary": "Time in milliseconds for temporary increase in timeout",
            "Key": "FT.ExtraLogTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 500,
              "Max": 300000,
              "DefaultValue": 10000
            }
          },
          {
            "Label": "FTCptConcurrentSend",
            "Summary": "Send non-diff pages in parallel with diffing rather than after",
            "Key": "FT.FTCptConcurrentSend",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "FTCptDelayCheckpoint",
            "Summary": "Delay checkpoint if no network packet waiting",
            "Key": "FT.FTCptDelayCheckpoint",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2,
              "DefaultValue": 2
            }
          },
          {
            "Label": "FTCptDiffCap",
            "Summary": "Max percent pages via diffs (EXPERIMENTAL, failover will not work if not 100)",
            "Key": "FT.FTCptDiffCap",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 100
            }
          },
          {
            "Label": "FTCptDiffThreads",
            "Summary": "Number of threads for diffing pages",
            "Key": "FT.FTCptDiffThreads",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 16,
              "DefaultValue": 6
            }
          },
          {
            "Label": "FTCptDisableFailover",
            "Summary": "Disable failovers (testing only)",
            "Key": "FT.FTCptDisableFailover",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 6,
              "DefaultValue": 0
            }
          },
          {
            "Label": "FTCptDiskWriteTimeout",
            "Summary": "Time in milliseconds for backup site to wait for a disk I/O to complete",
            "Key": "FT.FTCptDiskWriteTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 60000,
              "DefaultValue": 3000
            }
          },
          {
            "Label": "FTCptDontDelayPkts",
            "Summary": "Do not delay network packets - for testing only",
            "Key": "FT.FTCptDontDelayPkts",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "FTCptDontSendPages",
            "Summary": "Do not send over modified pages - for testing only",
            "Key": "FT.FTCptDontSendPages",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "FTCptEpochList",
            "Summary": "List of potential epochs to try in order of increasing value",
            "Key": "FT.FTCptEpochList",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "5,10,20,100",
              "ValidCharacters": "0123456789,"
            }
          },
          {
            "Label": "FTCptEpochSample",
            "Summary": "Single epoch sampling time in ms",
            "Key": "FT.FTCptEpochSample",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 60000,
              "DefaultValue": 1000
            }
          },
          {
            "Label": "FTCptEpochWait",
            "Summary": "Wait in ms after epoch sampling",
            "Key": "FT.FTCptEpochWait",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 4294967295,
              "DefaultValue": 8000
            }
          },
          {
            "Label": "FTCptIORetryExtraInterval",
            "Summary": "Extra sleep interval (in ms) between retries on disk I/O error",
            "Key": "FT.FTCptIORetryExtraInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 2000,
              "DefaultValue": 200
            }
          },
          {
            "Label": "FTCptIORetryInterval",
            "Summary": "Sleep interval (in ms) between retries on disk I/O error",
            "Key": "FT.FTCptIORetryInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 2000,
              "DefaultValue": 10
            }
          },
          {
            "Label": "FTCptIORetryTimes",
            "Summary": "Maximum retries on disk I/O error",
            "Key": "FT.FTCptIORetryTimes",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 5,
              "Max": 50,
              "DefaultValue": 15
            }
          },
          {
            "Label": "FTCptLogTimeout",
            "Summary": "Time in milliseconds to wait for FT log entries (read or write)",
            "Key": "FT.FTCptLogTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 500,
              "Max": 30000,
              "DefaultValue": 8000
            }
          },
          {
            "Label": "FTCptMaxPktsDelay",
            "Summary": "Max number of packets in the delayed queue before forcing a checkpoint",
            "Key": "FT.FTCptMaxPktsDelay",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "FTCptMinInterval",
            "Summary": "Time in milliseconds to wait between two forced checkpoints",
            "Key": "FT.FTCptMinInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 2,
              "Max": 1000,
              "DefaultValue": 4
            }
          },
          {
            "Label": "FTCptNetDelayNoCpt",
            "Summary": "Delay to impose on virtual machine network output in ms",
            "Key": "FT.FTCptNetDelayNoCpt",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 5000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "FTCptNumConnections",
            "Summary": "# of data connections to use for page sending",
            "Key": "FT.FTCptNumConnections",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 4,
              "DefaultValue": 2
            }
          },
          {
            "Label": "FTCptNumaIndex",
            "Summary": "Index dirty pages by NUMA node.  Workers will use the index to prefer local work.",
            "Key": "FT.FTCptNumaIndex",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "FTCptPagePolicy",
            "Summary": "Page placement policy for side data.  \u003c 2^16: Use a specific node mask, 0x10000: Put pages anywhere, 0x10001: Match nodes with virtual machine, 0x10002: Avoid matching nodes with virtual machine",
            "Key": "FT.FTCptPagePolicy",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 65538,
              "DefaultValue": 65538
            }
          },
          {
            "Label": "FTCptPoweroff",
            "Summary": "Power off the Primary VM after the next checkpoint",
            "Key": "FT.FTCptPoweroff",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 3,
              "DefaultValue": 0
            }
          },
          {
            "Label": "FTCptRcvBufSize",
            "Summary": "TCP receive buffer size for the backup",
            "Key": "FT.FTCptRcvBufSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 131080,
              "Max": 16777216,
              "DefaultValue": 562140
            }
          },
          {
            "Label": "FTCptSndBufSize",
            "Summary": "TCP send buffer size for the Primary VM",
            "Key": "FT.FTCptSndBufSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 131080,
              "Max": 16777216,
              "DefaultValue": 562140
            }
          },
          {
            "Label": "FTCptStartTimeout",
            "Summary": "Time in milliseconds for backup site to wait for data for the next checkpoint",
            "Key": "FT.FTCptStartTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1000,
              "Max": 90000,
              "DefaultValue": 90000
            }
          },
          {
            "Label": "FTCptStatsInterval",
            "Summary": "Time in milliseconds between stat printing",
            "Key": "FT.FTCptStatsInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 600,
              "DefaultValue": 30
            }
          },
          {
            "Label": "FTCptThreadPolicy",
            "Summary": "Policy for placing helper threads.  \u003c 2^16: Use a fixed NUMA node mask, 0x10000: Put threads anywhere, 0x10001: Match threads to a dynamic node index",
            "Key": "FT.FTCptThreadPolicy",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 65537,
              "DefaultValue": 65536
            }
          },
          {
            "Label": "FTCptVcpuMinUsage",
            "Summary": "VCPU usage in percentage below which the VM will be considered for forced checkpoint",
            "Key": "FT.FTCptVcpuMinUsage",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 40
            }
          },
          {
            "Label": "FTCptWaitOnSocket",
            "Summary": "Wait when socket is empty",
            "Key": "FT.FTCptWaitOnSocket",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "FillAffinity",
            "Summary": "Set the fill world/worldlet affinity to the tcp worldlet",
            "Key": "FT.FillAffinity",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "FillWorldlet",
            "Summary": "Fill the buffer from a worldlet",
            "Key": "FT.FillWorldlet",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "FlushReservationMax",
            "Summary": "The maximum amount of CPU to reserve for the async flush world",
            "Key": "FT.FlushReservationMax",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 25
            }
          },
          {
            "Label": "FlushReservationMin",
            "Summary": "The minimum amount of CPU to reserve for the async flush world",
            "Key": "FT.FlushReservationMin",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 5
            }
          },
          {
            "Label": "FlushSleep",
            "Summary": "Sleep time in milliseconds when nothing to flush",
            "Key": "FT.FlushSleep",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "FlushWorldlet",
            "Summary": "Flush the buffer from a worldlet",
            "Key": "FT.FlushWorldlet",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "GlobalFlushWorld",
            "Summary": "Use a global async flush world",
            "Key": "FT.GlobalFlushWorld",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "GoodExecLatency",
            "Summary": "Latency considered good execution latency(ms)",
            "Key": "FT.GoodExecLatency",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000,
              "DefaultValue": 200
            }
          },
          {
            "Label": "HeartbeatCount",
            "Summary": "Number of failed heartbeats before declare dead.",
            "Key": "FT.HeartbeatCount",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 5,
              "Max": 20,
              "DefaultValue": 10
            }
          },
          {
            "Label": "HostTimeout",
            "Summary": "Time in milliseconds before declare FT host dead",
            "Key": "FT.HostTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 30000,
              "DefaultValue": 2000
            }
          },
          {
            "Label": "IORetryExtraInterval",
            "Summary": "Sleep extra interval (msec) between I/O reissuing on I/O error",
            "Key": "FT.IORetryExtraInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 2000,
              "DefaultValue": 200
            }
          },
          {
            "Label": "IORetryInterval",
            "Summary": "Sleep interval (msec) between I/O reissuing on I/O error",
            "Key": "FT.IORetryInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 2000,
              "DefaultValue": 10
            }
          },
          {
            "Label": "IORetryTimes",
            "Summary": "Maximum disk I/O reissuing times on I/O error",
            "Key": "FT.IORetryTimes",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 5,
              "Max": 50,
              "DefaultValue": 15
            }
          },
          {
            "Label": "LogBufferStallSleep",
            "Summary": "Sleep time in milliseconds when log buffer is full on the primary",
            "Key": "FT.LogBufferStallSleep",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100000,
              "DefaultValue": 1
            }
          },
          {
            "Label": "LogTimeout",
            "Summary": "Time in milliseconds to wait on log socket",
            "Key": "FT.LogTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 500,
              "Max": 30000,
              "DefaultValue": 8000
            }
          },
          {
            "Label": "LongFlushDebugMS",
            "Summary": "Long millisecond interval for debugging flushing delays",
            "Key": "FT.LongFlushDebugMS",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 5000,
              "DefaultValue": 500
            }
          },
          {
            "Label": "MaxFlushInterval",
            "Summary": "Maximum amount of microseconds to wait before writing to flush socket",
            "Key": "FT.MaxFlushInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "MinWriteSize",
            "Summary": "Minimum amount of data to write to flush socket",
            "Key": "FT.MinWriteSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 65536,
              "DefaultValue": 0
            }
          },
          {
            "Label": "NoWaitOnSocket",
            "Summary": "Don't wait on socket",
            "Key": "FT.NoWaitOnSocket",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "PanicNoProgressMS",
            "Summary": "Milliseconds before panic if progress isn't made",
            "Key": "FT.PanicNoProgressMS",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "PrimaryConnectTimeout",
            "Summary": "Time in milliseconds to wait when connecting to the primary",
            "Key": "FT.PrimaryConnectTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 30000,
              "DefaultValue": 8000
            }
          },
          {
            "Label": "ShortFlushDebugMS",
            "Summary": "Short millisecond interval for debugging flushing delays",
            "Key": "FT.ShortFlushDebugMS",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 5000,
              "DefaultValue": 100
            }
          },
          {
            "Label": "TCPNoDelayBackup",
            "Summary": "Set TCP_NODELAY on the socket on the backup",
            "Key": "FT.TCPNoDelayBackup",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TCPNoDelayPrimary",
            "Summary": "Set TCP_NODELAY on the socket on the primary",
            "Key": "FT.TCPNoDelayPrimary",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TCPPersistTimer",
            "Summary": "TCP persist timer time in milliseconds",
            "Key": "FT.TCPPersistTimer",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 500,
              "Max": 5000,
              "DefaultValue": 500
            }
          },
          {
            "Label": "TCPRcvBufSize",
            "Summary": "TCP receive buffer size for the backup",
            "Key": "FT.TCPRcvBufSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 16384,
              "Max": 1000000,
              "DefaultValue": 131072
            }
          },
          {
            "Label": "TCPSndBufSize",
            "Summary": "TCP send buffer size for the primary",
            "Key": "FT.TCPSndBufSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 16384,
              "Max": 1000000,
              "DefaultValue": 131072
            }
          },
          {
            "Label": "UseHostMonitor",
            "Summary": "Use the host monitor to ping the other host",
            "Key": "FT.UseHostMonitor",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Vmknic",
            "Summary": "Vmknic for FT vmkernel VNIC",
            "Key": "FT.Vmknic",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "XmitSyncQueueLen",
            "Summary": "Length of the Tx synchronization queue",
            "Key": "FT.XmitSyncQueueLen",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 32768,
              "DefaultValue": 64
            }
          },
          {
            "Label": "adjDownInt",
            "Summary": "CPU adjust down interval in seconds",
            "Key": "FT.adjDownInt",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 3,
              "Max": 30,
              "DefaultValue": 10
            }
          },
          {
            "Label": "adjDownPct",
            "Summary": "CPU slowdown adjustment pct parameter ",
            "Key": "FT.adjDownPct",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 30,
              "DefaultValue": 10
            }
          },
          {
            "Label": "adjUpInt",
            "Summary": "CPU adjust up interval in seconds",
            "Key": "FT.adjUpInt",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 5,
              "Max": 12000,
              "DefaultValue": 200
            }
          },
          {
            "Label": "adjUpPct",
            "Summary": "CPU speedup adjustment pct parameter ",
            "Key": "FT.adjUpPct",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 30,
              "DefaultValue": 10
            }
          },
          {
            "Label": "execLatExtra",
            "Summary": "Extra execution latency allowed in msec",
            "Key": "FT.execLatExtra",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 3000,
              "DefaultValue": 500
            }
          },
          {
            "Label": "maxLowerBound",
            "Summary": "CPU minimum alloc.max allowed in pct ",
            "Key": "FT.maxLowerBound",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 5,
              "Max": 50,
              "DefaultValue": 20
            }
          },
          {
            "Label": "slowdownPctMax",
            "Summary": "Maximum primary vm CPU slowdown pct",
            "Key": "FT.slowdownPctMax",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 90,
              "DefaultValue": 60
            }
          },
          {
            "Label": "slowdownTimeMax",
            "Summary": "Maximum primary vm CPU slowdown duration in seconds",
            "Key": "FT.slowdownTimeMax",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 9000,
              "DefaultValue": 600
            }
          },
          {
            "Label": "ChecksumIoSize",
            "Summary": "Size in blocks of a checksum read I/O",
            "Key": "HBR.ChecksumIoSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 1024,
              "DefaultValue": 8
            }
          },
          {
            "Label": "ChecksumMaxIo",
            "Summary": "Maximum number of I/O chunks read in parallel for checksum",
            "Key": "HBR.ChecksumMaxIo",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 256,
              "DefaultValue": 8
            }
          },
          {
            "Label": "ChecksumPerSlice",
            "Summary": "Maximum number of I/O chunks we read in each slice for checksum",
            "Key": "HBR.ChecksumPerSlice",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 64,
              "DefaultValue": 2
            }
          },
          {
            "Label": "ChecksumRegionSize",
            "Summary": "Size in blocks of one checksum region, corresponding to one network request",
            "Key": "HBR.ChecksumRegionSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 4096,
              "DefaultValue": 256
            }
          },
          {
            "Label": "ChecksumUseAllocInfo",
            "Summary": "Use disk allocation info to help speed up the checksum process",
            "Key": "HBR.ChecksumUseAllocInfo",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "ChecksumUseChecksumInfo",
            "Summary": "Use disk checksum info to help speed up transfering valid blocks of data.",
            "Key": "HBR.ChecksumUseChecksumInfo",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "ChecksumZoneSize",
            "Summary": "Size in regions of one checksum zone for which allocation information will be cached",
            "Key": "HBR.ChecksumZoneSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 32,
              "Max": 1048576,
              "DefaultValue": 32768
            }
          },
          {
            "Label": "CopySnapDiskMaxExtentCount",
            "Summary": "Number of extents for a single snapshot disk that can be copied in parallel",
            "Key": "HBR.CopySnapDiskMaxExtentCount",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 16
            }
          },
          {
            "Label": "CopySnapFidHashBuckets",
            "Summary": "Number of hash buckets to use to track the snapshot disks open to copy to demand log.",
            "Key": "HBR.CopySnapFidHashBuckets",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 1024,
              "DefaultValue": 256
            }
          },
          {
            "Label": "DemandlogCompletedHashBuckets",
            "Summary": "Number of hash buckets to use to track extents that have been written to the demand log.",
            "Key": "HBR.DemandlogCompletedHashBuckets",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 32,
              "DefaultValue": 8
            }
          },
          {
            "Label": "DemandlogExtentHashBuckets",
            "Summary": "Number of hash buckets to use to track extents that haven't been read.",
            "Key": "HBR.DemandlogExtentHashBuckets",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 65536,
              "DefaultValue": 512
            }
          },
          {
            "Label": "DemandlogIoTimeoutSecs",
            "Summary": "Timeout for IOs for demand log operations.",
            "Key": "HBR.DemandlogIoTimeoutSecs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000,
              "DefaultValue": 120
            }
          },
          {
            "Label": "DemandlogReadRetries",
            "Summary": "Number of times to retry an internal read (for the demand log) before aborting the delta.",
            "Key": "HBR.DemandlogReadRetries",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000,
              "DefaultValue": 20
            }
          },
          {
            "Label": "DemandlogRetryDelayMs",
            "Summary": "Delay in milliseconds for retrying a demand log write.",
            "Key": "HBR.DemandlogRetryDelayMs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100000,
              "DefaultValue": 10
            }
          },
          {
            "Label": "DemandlogSendHashBuckets",
            "Summary": "No longer used.",
            "Key": "HBR.DemandlogSendHashBuckets",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 32,
              "DefaultValue": 8
            }
          },
          {
            "Label": "DemandlogTransferIoSize",
            "Summary": "Size in blocks of a demandlog transfer read I/O",
            "Key": "HBR.DemandlogTransferIoSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 16,
              "DefaultValue": 8
            }
          },
          {
            "Label": "DemandlogTransferMaxCompletion",
            "Summary": "Maximum number of demandlog chunks that are allowed in completion phase per disk",
            "Key": "HBR.DemandlogTransferMaxCompletion",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 512,
              "DefaultValue": 512
            }
          },
          {
            "Label": "DemandlogTransferMaxIo",
            "Summary": "Maximum number of demandlog transfer I/O chunks issued in parallel",
            "Key": "HBR.DemandlogTransferMaxIo",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 32
            }
          },
          {
            "Label": "DemandlogTransferMaxNetwork",
            "Summary": "Maximum number of demandlog chunks transferred in parallel",
            "Key": "HBR.DemandlogTransferMaxNetwork",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 256,
              "DefaultValue": 64
            }
          },
          {
            "Label": "DemandlogTransferPerSlice",
            "Summary": "Maximum number of demandlog chunks issued per slice",
            "Key": "HBR.DemandlogTransferPerSlice",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 16
            }
          },
          {
            "Label": "DemandlogWriteRetries",
            "Summary": "Number of times to retry a demand log write before aborting the delta.",
            "Key": "HBR.DemandlogWriteRetries",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000,
              "DefaultValue": 20
            }
          },
          {
            "Label": "DisableChecksumOffload",
            "Summary": "Disable the use of SSE CRC32 instruction",
            "Key": "HBR.DisableChecksumOffload",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "DisconnectedEventDelayMs",
            "Summary": "Time to wait (while attempting reconnection) before posting a 'no connection to VR server' event",
            "Key": "HBR.DisconnectedEventDelayMs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 600000,
              "DefaultValue": 60000
            }
          },
          {
            "Label": "ErrThrottleChecksumIO",
            "Summary": "Throttle Checksum I/O errors.",
            "Key": "HBR.ErrThrottleChecksumIO",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "ErrThrottleDceRead",
            "Summary": "Throttle DCE Read errors.",
            "Key": "HBR.ErrThrottleDceRead",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "BitmapAllocTimeoutMS",
            "Summary": "A timeout in MS for how long we will wait to allocate a bitmap",
            "Key": "HBR.HbrBitmapAllocTimeoutMS",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000,
              "DefaultValue": 3000
            }
          },
          {
            "Label": "BitmapVMMaxStorageGB",
            "Summary": "An estimation of the maximum storage allocated per VM in gigabytes",
            "Key": "HBR.HbrBitmapVMMaxStorageGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 131072,
              "DefaultValue": 65536
            }
          },
          {
            "Label": "BitmapVMMinStorageGB",
            "Summary": "An estimation of the minimum storage allocated per VM in gigabytes",
            "Key": "HBR.HbrBitmapVMMinStorageGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 131072,
              "DefaultValue": 500
            }
          },
          {
            "Label": "DemandLogIOPerVM",
            "Summary": "Number of concurrent demand log copies per VM",
            "Key": "HBR.HbrDemandLogIOPerVM",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 64
            }
          },
          {
            "Label": "HbrDisableNetCompression",
            "Summary": "Disable end-to-end network compression.",
            "Key": "HBR.HbrDisableNetCompression",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "HbrLowerExtentBreakTB",
            "Summary": "Disks with capacity between the min extent break and this number of gigabytes will have the lower extent size.",
            "Key": "HBR.HbrLowerExtentBreakGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 65536,
              "DefaultValue": 8192
            }
          },
          {
            "Label": "HbrLowerExtentSizeKB",
            "Summary": "Lower extent size used for disks in kilobytes.",
            "Key": "HBR.HbrLowerExtentSizeKB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 16
            }
          },
          {
            "Label": "HbrMaxExtentSizeKB",
            "Summary": "Maximum extent size in kilobytes. Used for disks with capacity over the upper extent break.",
            "Key": "HBR.HbrMaxExtentSizeKB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 64
            }
          },
          {
            "Label": "HbrMaxGuestXferWhileDeltaMB",
            "Summary": "Maximum single SCSI command transfer size (in megabytes) that will be tolerated while a delta is taking place.",
            "Key": "HBR.HbrMaxGuestXferWhileDeltaMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 256,
              "Max": 3072,
              "DefaultValue": 1024
            }
          },
          {
            "Label": "HbrMaxUnmapExtents",
            "Summary": "Maximum expected number of extents for SCSI UNMAP commands.",
            "Key": "HBR.HbrMaxUnmapExtents",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100000,
              "DefaultValue": 10
            }
          },
          {
            "Label": "HbrMaxUnmapsInFlight",
            "Summary": "Maximum expected number of SCSI UNMAP commands in flight on a single disk.",
            "Key": "HBR.HbrMaxUnmapsInFlight",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1048576,
              "DefaultValue": 128
            }
          },
          {
            "Label": "HbrMinExtentBreakGB",
            "Summary": "Disks with capacity under this number of gigabytes will have the min extent size.",
            "Key": "HBR.HbrMinExtentBreakGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 65536,
              "DefaultValue": 2048
            }
          },
          {
            "Label": "HbrMinExtentSizeKB",
            "Summary": "Minimum extent size used for disks in kilobytes.",
            "Key": "HBR.HbrMinExtentSizeKB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 8
            }
          },
          {
            "Label": "HbrOptimizeFullSync",
            "Summary": "Skip transfer of changed blocks during full sync to avoid sending them twice between the full sync and the subsequent delta.",
            "Key": "HBR.HbrOptimizeFullSync",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "ResourceHeapPerVMSizeKB",
            "Summary": "The additional amount of memory in KB per VM to add to the resource heap size",
            "Key": "HBR.HbrResourceHeapPerVMSizeKB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4096,
              "DefaultValue": 128
            }
          },
          {
            "Label": "ResourceHeapSizeMB",
            "Summary": "A base value in MB for the size of the resource heap",
            "Key": "HBR.HbrResourceHeapSizeMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 2,
              "Max": 256,
              "DefaultValue": 3
            }
          },
          {
            "Label": "ResourceHeapUtilization",
            "Summary": "Expected usable percentage of the resource heap (minus overheads, fragmentation)",
            "Key": "HBR.HbrResourceHeapUtilization",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 200,
              "DefaultValue": 95
            }
          },
          {
            "Label": "HbrResourceMaxCompletionContexts",
            "Summary": "The maximum number of resource contexts allowed in completion phase for all VMs.",
            "Key": "HBR.HbrResourceMaxCompletionContexts",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 16,
              "Max": 16384,
              "DefaultValue": 8192
            }
          },
          {
            "Label": "HbrResourceMaxDiskContexts",
            "Summary": "The maximum number of resource contexts allowed in disk phase for all VMs.",
            "Key": "HBR.HbrResourceMaxDiskContexts",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 16,
              "Max": 8192,
              "DefaultValue": 512
            }
          },
          {
            "Label": "RuntimeHeapMaxBaseMB",
            "Summary": "A base value in MB for the maximum size of the runtime heap",
            "Key": "HBR.HbrRuntimeHeapMaxBaseMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 1
            }
          },
          {
            "Label": "RuntimeHeapMinBaseMB",
            "Summary": "A base value in MB for the minimum size of the runtime heap",
            "Key": "HBR.HbrRuntimeHeapMinBaseMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 1
            }
          },
          {
            "Label": "StaticHeapMaxBaseMB",
            "Summary": "A base value in MB for the maximum size of the static heap",
            "Key": "HBR.HbrStaticHeapMaxBaseMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 1
            }
          },
          {
            "Label": "StaticHeapMinBaseMB",
            "Summary": "A base value in MB for the minimum size of the static heap",
            "Key": "HBR.HbrStaticHeapMinBaseMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 1
            }
          },
          {
            "Label": "HbrUpperExtentBreakTB",
            "Summary": "Disks with capacity between the lower extent break and this number of gigabytes will have the upper extent size.",
            "Key": "HBR.HbrUpperExtentBreakGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 65536,
              "DefaultValue": 32768
            }
          },
          {
            "Label": "HbrUpperExtentSizeKB",
            "Summary": "Upper extent size used for disks in kilobytes.",
            "Key": "HBR.HbrUpperExtentSizeKB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 32
            }
          },
          {
            "Label": "HelperQueueMaxRequests",
            "Summary": "Maximum number of helper requests the helper queue can support",
            "Key": "HBR.HelperQueueMaxRequests",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 128,
              "Max": 16384,
              "DefaultValue": 8192
            }
          },
          {
            "Label": "HelperQueueMaxWorlds",
            "Summary": "Maximum number of world processing helper queue requests",
            "Key": "HBR.HelperQueueMaxWorlds",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 16,
              "DefaultValue": 8
            }
          },
          {
            "Label": "LocalReadIoTimeoutSecs",
            "Summary": "Timeout for IOs for dce local reads.",
            "Key": "HBR.LocalReadIoTimeoutSecs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000,
              "DefaultValue": 120
            }
          },
          {
            "Label": "MigrateFlushTimerSecs",
            "Summary": "Time between attempts to flush the state to the persistent file during migration.",
            "Key": "HBR.MigrateFlushTimerSecs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 600,
              "DefaultValue": 3
            }
          },
          {
            "Label": "NetworkUseCubic",
            "Summary": "Use the cubic TCP congestion algorithm for HBR sockets",
            "Key": "HBR.NetworkUseCubic",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "NetworkerRecvHashBuckets",
            "Summary": "Number of hash buckets to use to track commands waiting to receive a response.",
            "Key": "HBR.NetworkerRecvHashBuckets",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 65536,
              "DefaultValue": 64
            }
          },
          {
            "Label": "OpportunisticBlockListSize",
            "Summary": "Number of blocks to keep around in a LRU list for opportunistic replication",
            "Key": "HBR.OpportunisticBlockListSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 500,
              "Max": 10000,
              "DefaultValue": 4000
            }
          },
          {
            "Label": "ProgressReportIntervalMs",
            "Summary": "Interval between per-disk progress updates to hostd",
            "Key": "HBR.ProgressReportIntervalMs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1000,
              "Max": 600000,
              "DefaultValue": 5000
            }
          },
          {
            "Label": "PsfIoTimeoutSecs",
            "Summary": "Timeout for IOs for persistent state file/demand log metadata.",
            "Key": "HBR.PsfIoTimeoutSecs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000,
              "DefaultValue": 300
            }
          },
          {
            "Label": "ReconnectFailureDelaySecs",
            "Summary": "Additional delay in seconds added per reconnection failure for a session",
            "Key": "HBR.ReconnectFailureDelaySecs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 300,
              "DefaultValue": 10
            }
          },
          {
            "Label": "ReconnectMaxDelaySecs",
            "Summary": "Maximum delay in seconds between reconnection attempts for a session",
            "Key": "HBR.ReconnectMaxDelaySecs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 30,
              "Max": 300,
              "DefaultValue": 90
            }
          },
          {
            "Label": "ResourceServerHashBuckets",
            "Summary": "Number of hash buckets to use to track remote HBR servers.",
            "Key": "HBR.ResourceServerHashBuckets",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 65536,
              "DefaultValue": 8
            }
          },
          {
            "Label": "RetryMaxDelaySecs",
            "Summary": "Maximum server request retry delay in seconds (for non-fatal errors)",
            "Key": "HBR.RetryMaxDelaySecs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 120,
              "DefaultValue": 60
            }
          },
          {
            "Label": "RetryMinDelaySecs",
            "Summary": "Minimum server request retry delay in seconds (for non-fatal errors)",
            "Key": "HBR.RetryMinDelaySecs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 60,
              "DefaultValue": 1
            }
          },
          {
            "Label": "SyncTransferRetrySleepSecs",
            "Summary": "Time in seconds to wait after a failure before retrying a sync operation.",
            "Key": "HBR.SyncTransferRetrySleepSecs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 600,
              "DefaultValue": 5
            }
          },
          {
            "Label": "TransferDiskMaxCompletion",
            "Summary": "Maximum number of blocks that are allowed in the completion phase per disk",
            "Key": "HBR.TransferDiskMaxCompletion",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 512,
              "DefaultValue": 512
            }
          },
          {
            "Label": "TransferDiskMaxIo",
            "Summary": "Maximum number of blocks that will be read in parallel",
            "Key": "HBR.TransferDiskMaxIo",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 32
            }
          },
          {
            "Label": "TransferDiskMaxNetwork",
            "Summary": "Maximum number of blocks that will be transferred in parallel",
            "Key": "HBR.TransferDiskMaxNetwork",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 256,
              "DefaultValue": 64
            }
          },
          {
            "Label": "TransferDiskPerSlice",
            "Summary": "Maximum number of blocks that will be read in each slice",
            "Key": "HBR.TransferDiskPerSlice",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 16
            }
          },
          {
            "Label": "TransferFileExtentSize",
            "Summary": "Chunk size for config file transfers",
            "Key": "HBR.TransferFileExtentSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 512,
              "Max": 1048576,
              "DefaultValue": 8192
            }
          },
          {
            "Label": "TransferMaxContExtents",
            "Summary": "Maximum number of contiguous extents that will be coalesced into a single update",
            "Key": "HBR.TransferMaxContExtents",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 8
            }
          },
          {
            "Label": "WireChecksum",
            "Summary": "Use wire checksums",
            "Key": "HBR.WireChecksum",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "XferBitmapCheckIntervalSecs",
            "Summary": "Time in seconds to wait before checking the transfer bitmap for availability of dirty blocks.",
            "Key": "HBR.XferBitmapCheckIntervalSecs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 600,
              "DefaultValue": 10
            }
          },
          {
            "Label": "CloseIscsiConnOnTaskMgmtFailure",
            "Summary": "Close iSCSI connection on task management failure",
            "Key": "ISCSI.CloseIscsiConnOnTaskMgmtFailure",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Maximum I/O Size in KB",
            "Summary": "The maximum I/O size of Software iSCSI in KB. Requires rebooting the host.",
            "Key": "ISCSI.MaxIoSizeKB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 128,
              "Max": 512,
              "DefaultValue": 128
            }
          },
          {
            "Label": "Best virtual CPU routing",
            "Summary": "IRQ best virtual CPU routing",
            "Key": "Irq.BestVcpuRouting",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "IRQActionAffinityWeight",
            "Summary": "relative weight for action-vcpu affinity",
            "Key": "Irq.IRQActionAffinityWeight",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10,
              "DefaultValue": 5
            }
          },
          {
            "Label": "IRQAvoidExclusive",
            "Summary": "Avoid placing interrupts on physical CPUs with exclusive affinity set.",
            "Key": "Irq.IRQAvoidExclusive",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "IRQBHConflictWeight",
            "Summary": "relative weight for irq/BH conflict",
            "Key": "Irq.IRQBHConflictWeight",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10,
              "DefaultValue": 5
            }
          },
          {
            "Label": "IRQ rebalance period",
            "Summary": "Time in milliseconds between attempts to rebalance interrupts",
            "Key": "Irq.IRQRebalancePeriod",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 20000,
              "DefaultValue": 50
            }
          },
          {
            "Label": "IRQVcpuConflictWeight",
            "Summary": "relative weight for irq/vcpu conflict",
            "Key": "Irq.IRQVcpuConflictWeight",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 10,
              "DefaultValue": 3
            }
          },
          {
            "Label": "LPageAlwaysTryForNPT",
            "Summary": "Enable always try to alloc large page for NPT",
            "Key": "LPage.LPageAlwaysTryForNPT",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "LPageDefragEnable",
            "Summary": "Enable large page defragmentation",
            "Key": "LPage.LPageDefragEnable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "LPageMarkLowNodes",
            "Summary": "Enable marking of nodes with low large pages free",
            "Key": "LPage.LPageMarkLowNodes",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "MaxSharedPages",
            "Summary": "Maximum number of shared pages in a 2MB region that may be broken to back the region with a large page",
            "Key": "LPage.MaxSharedPages",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 512,
              "DefaultValue": 510
            }
          },
          {
            "Label": "MaxSwappedPagesInitVal",
            "Summary": "Initial value for maximum number of swapped pages in a 2MB region that may be read to back the region with a large page",
            "Key": "LPage.MaxSwappedPagesInitVal",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 512,
              "DefaultValue": 10
            }
          },
          {
            "Label": "freePagesThresholdForRemote",
            "Summary": "Maximum number of free small pages on local nodes to allow remote lpages",
            "Key": "LPage.freePagesThresholdForRemote",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 2048
            }
          },
          {
            "Label": "blkAttrCacheSizePercent",
            "Summary": "BlkAttr cache size percent growth rate with peak value (100%) caching almost all blkattrs for the diskgroup.",
            "Key": "LSOM.blkAttrCacheSizePercent",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "lsomPlogEnableDeadmanTimer",
            "Summary": "To trigger a panic if an I/O is stuck for a long time. (0:disabled 1:enabled)",
            "Key": "LSOM.lsomPlogEnableDeadmanTimer",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "AllocGuestLargePage",
            "Summary": "Enable large page backing of guest memory",
            "Key": "Mem.AllocGuestLargePage",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Vmmemctl maximum percent",
            "Summary": "Vmmemctl limit as percentage of virtual machine maximum size",
            "Key": "Mem.CtlMaxPercent",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 99,
              "DefaultValue": 65
            }
          },
          {
            "Label": "Idle tax",
            "Summary": "Idle memory tax rate",
            "Key": "Mem.IdleTax",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 99,
              "DefaultValue": 75
            }
          },
          {
            "Label": "IdleTaxType",
            "Summary": "idle tax type. 0=flat, 1=variable",
            "Key": "Mem.IdleTaxType",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "MemDefragClientsPerDir",
            "Summary": "Clients that are allowed to defrags per directory.",
            "Key": "Mem.MemDefragClientsPerDir",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 512,
              "DefaultValue": 2
            }
          },
          {
            "Label": "MemEagerZero",
            "Summary": "Zero out userworld and guest memory pages immediately after free",
            "Key": "Mem.MemEagerZero",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "MemMinFreePct",
            "Summary": "Percentage of host memory to reserve for accelerating memory allocations when free memory is low, 0 for automatic",
            "Key": "Mem.MemMinFreePct",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 50,
              "DefaultValue": 0
            }
          },
          {
            "Label": "MemZipEnable",
            "Summary": "Enable the memory compression cache",
            "Key": "Mem.MemZipEnable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "MemZipMaxAllocPct",
            "Summary": "Sets the maximum size for the compression cache as a percentage of allocated VM memory size",
            "Key": "Mem.MemZipMaxAllocPct",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 5,
              "Max": 100,
              "DefaultValue": 50
            }
          },
          {
            "Label": "MemZipMaxPct",
            "Summary": "Sets the maximum target size for the compression cache as a percentage of VM size",
            "Key": "Mem.MemZipMaxPct",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 5,
              "Max": 100,
              "DefaultValue": 10
            }
          },
          {
            "Label": "SampleActivePctMin",
            "Summary": "lower bound for sampled active memory",
            "Key": "Mem.SampleActivePctMin",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10,
              "DefaultValue": 1
            }
          },
          {
            "Label": "SampleDirtiedPctMin",
            "Summary": "lower bound for sampled active dirtied memory",
            "Key": "Mem.SampleDirtiedPctMin",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Share force salting",
            "Summary": "PShare salting lest you limit the transparent page sharing only between a set of VMs. PShare salting is controlled by the VMX sched.mem.pshare.salt option. The PShare salting option has three states: 0 - no salting or isolation between VMs; 1 - VMs that have the sched.mem.pshare.salt option set are able to share memory with VMs with the same salt; 2 - VMs that do not have the sched.mem.pshare.salt option set cannot share memory with any other VM, page sharing is possible only inside the VM. If the  sched.mem.pshare.salt is present but empty, the VM gest its own unique salt.",
            "Key": "Mem.ShareForceSalting",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2,
              "DefaultValue": 2
            }
          },
          {
            "Label": "ShareRateMax",
            "Summary": "per-VM upper limit on pshare scan rate in pages/sec. (0 to disable)",
            "Key": "Mem.ShareRateMax",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 32768,
              "DefaultValue": 1024
            }
          },
          {
            "Label": "ShareScanGHz",
            "Summary": "max page scan rate in MB/sec per GHz of host CPU, 0 to disable",
            "Key": "Mem.ShareScanGHz",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 32,
              "DefaultValue": 4
            }
          },
          {
            "Label": "ShareScanTime",
            "Summary": "desired time in minutes to scan entire VM",
            "Key": "Mem.ShareScanTime",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 600,
              "DefaultValue": 60
            }
          },
          {
            "Label": "VMOverheadGrowthLimit",
            "Summary": "Default limit (in MB) on VM overhead memory growth. Valid values are 0 to maximum memory supported and (uint32)-1 which means \"unlimited\".",
            "Key": "Mem.VMOverheadGrowthLimit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 4294967295
            }
          },
          {
            "Label": "AutoBindVmknic",
            "Summary": "Autobind vMotion socket to the vmknic where data comes from. Used only for the vMotion server side, which is the vMotion listening and receiving side",
            "Key": "Migrate.AutoBindVmknic",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "BindToVmknic",
            "Summary": "Bind the vMotion socket to a specific vmknic.  0 for never, 1 to bind only with FT, 2 to bind with FT or for multi-vmknic support, 3 to always bind",
            "Key": "Migrate.BindToVmknic",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 3,
              "DefaultValue": 3
            }
          },
          {
            "Label": "CptCacheMaxSizeMB",
            "Summary": "Maximum checkpoint cache size in MB",
            "Key": "Migrate.CptCacheMaxSizeMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 8,
              "Max": 1024,
              "DefaultValue": 544
            }
          },
          {
            "Label": "DebugChecksumMismatch",
            "Summary": "Debug checksum mismatch.",
            "Key": "Migrate.DebugChecksumMismatch",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "DetectZeroPages",
            "Summary": "Whether vMotion should detect zero pages during page transmission",
            "Key": "Migrate.DetectZeroPages",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "DisableResumeDuringPageIn",
            "Summary": "Disable observation of resume during page-in flag, always completing page-in before VM resume",
            "Key": "Migrate.DisableResumeDuringPageIn",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "DiskOpsChunkSize",
            "Summary": "Size of each stream chunk in bytes (will be rounded up to multiple of block size)",
            "Key": "Migrate.DiskOpsChunkSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 8096,
              "Max": 1000000,
              "DefaultValue": 131072
            }
          },
          {
            "Label": "DiskOpsEnabled",
            "Summary": "Enables VMotion disk-backed operation",
            "Key": "Migrate.DiskOpsEnabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "DiskOpsMaxRetries",
            "Summary": "Maximum number of times to retry a diskOp IO upon encountering a reservation conflict",
            "Key": "Migrate.DiskOpsMaxRetries",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000,
              "DefaultValue": 20
            }
          },
          {
            "Label": "DiskOpsStreamChunks",
            "Summary": "Stream Chunks to allocate on disk",
            "Key": "Migrate.DiskOpsStreamChunks",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 1024,
              "DefaultValue": 40
            }
          },
          {
            "Label": "Enabled",
            "Summary": "Enable hot migration support",
            "Key": "Migrate.Enabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "GetPageSysAlertThresholdMS",
            "Summary": "Threshold in milliseconds for the source host to prepare a page for transmission above which a SysAlert is triggered",
            "Key": "Migrate.GetPageSysAlertThresholdMS",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1000,
              "Max": 100000,
              "DefaultValue": 10000
            }
          },
          {
            "Label": "LowBandwidthSysAlertThreshold",
            "Summary": "Threshold in KB/s for VMotion bandwidth below which a SysAlert is triggered",
            "Key": "Migrate.LowBandwidthSysAlertThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "LowMemWaitSysAlertThresholdMS",
            "Summary": "Threshold in milliseconds for the dest host to leave the low-memory state above which a SysAlert is triggered",
            "Key": "Migrate.LowMemWaitSysAlertThresholdMS",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1000,
              "Max": 100000,
              "DefaultValue": 10000
            }
          },
          {
            "Label": "MigrateCpuMinPctDefault",
            "Summary": "Desired default shared CPU reservation (in %) for VMotions",
            "Key": "Migrate.MigrateCpuMinPctDefault",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 400,
              "DefaultValue": 30
            }
          },
          {
            "Label": "MigrateCpuPctPerGb",
            "Summary": "Desired per Gbit shared CPU reservation (in %) for VMotions",
            "Key": "Migrate.MigrateCpuPctPerGb",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 10
            }
          },
          {
            "Label": "MigrateCpuSharesHighPriority",
            "Summary": "CPU shares for a high priority VMotion",
            "Key": "Migrate.MigrateCpuSharesHighPriority",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 100000,
              "DefaultValue": 60000
            }
          },
          {
            "Label": "MigrateCpuSharesRegular",
            "Summary": "CPU shares for a regular VMotion",
            "Key": "Migrate.MigrateCpuSharesRegular",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 100000,
              "DefaultValue": 30000
            }
          },
          {
            "Label": "MonActionWaitSysAlertThresholdMS",
            "Summary": "Threshold in milliseconds for the monitor to process a pre-copy action after which a SysAlert is triggered",
            "Key": "Migrate.MonActionWaitSysAlertThresholdMS",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 100000,
              "DefaultValue": 2000
            }
          },
          {
            "Label": "NetExpectedLineRateMBps",
            "Summary": "Expected network throughput, in MBps, for bandwidth-delay calculation",
            "Key": "Migrate.NetExpectedLineRateMBps",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 2000,
              "DefaultValue": 133
            }
          },
          {
            "Label": "NetLatencyModeThreshold",
            "Summary": "Lowest possible round-trip time, in ms, before vMotion must operate in latency-aware mode.",
            "Key": "Migrate.NetLatencyModeThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 1000,
              "DefaultValue": 4
            }
          },
          {
            "Label": "Net time-out",
            "Summary": "Time-out for migration network operations",
            "Key": "Migrate.NetTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 3600,
              "DefaultValue": 20
            }
          },
          {
            "Label": "OutstandingReadKBMax",
            "Summary": "Maximum socket-backed mbuf bytes vMotion will allow to be outstanding while drained concurrently with reads, 0 for unlimited",
            "Key": "Migrate.OutstandingReadKBMax",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2048,
              "DefaultValue": 128
            }
          },
          {
            "Label": "PanicOnChecksumMismatch",
            "Summary": "1 for world panic, 2 for vmkernel panic",
            "Key": "Migrate.PanicOnChecksumMismatch",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2,
              "DefaultValue": 0
            }
          },
          {
            "Label": "PreCopyCountDelay",
            "Summary": "Delay preCopy next action every n action posts",
            "Key": "Migrate.PreCopyCountDelay",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000,
              "DefaultValue": 10
            }
          },
          {
            "Label": "PreCopyMinProgressPerc",
            "Summary": "Minimum allowed transmit to dirty page ratio per pre-copy iteration",
            "Key": "Migrate.PreCopyMinProgressPerc",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 1000,
              "DefaultValue": 130
            }
          },
          {
            "Label": "PreCopyPagesPerSend",
            "Summary": "Maximum number of pages to send per precopy transmit",
            "Key": "Migrate.PreCopyPagesPerSend",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 1024,
              "DefaultValue": 32
            }
          },
          {
            "Label": "PreCopySwitchoverTimeGoal",
            "Summary": "Goal time in milliseconds to send changed pages after pre-copy completes",
            "Key": "Migrate.PreCopySwitchoverTimeGoal",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 100000,
              "DefaultValue": 500
            }
          },
          {
            "Label": "PreallocLPages",
            "Summary": "Attempt to prealloc destination pages via large page allocation",
            "Key": "Migrate.PreallocLPages",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "ProhibitInstantClone",
            "Summary": "Prohibit instant clone from a VM",
            "Key": "Migrate.ProhibitInstantClone",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "RcvBufSize",
            "Summary": "TCP receive buffer size for the destination",
            "Key": "Migrate.RcvBufSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 65536,
              "Max": 1000000,
              "DefaultValue": 562540
            }
          },
          {
            "Label": "RdpiTransitionTimeMs",
            "Summary": "Milliseconds to wait for page-in completion before resuming, if rdpi is enabled",
            "Key": "Migrate.RdpiTransitionTimeMs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 300000,
              "DefaultValue": 1
            }
          },
          {
            "Label": "SdpsDynamicDelaySec",
            "Summary": "Delay, in seconds, between polling when considering enabling SDPS in the first preCopy iteration.",
            "Key": "Migrate.SdpsDynamicDelaySec",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000,
              "DefaultValue": 30
            }
          },
          {
            "Label": "SdpsEnabled",
            "Summary": "Stuns VMotion source in small increments during precopy, 0=disabled, 1=always enabled, 2=dynamic",
            "Key": "Migrate.SdpsEnabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2,
              "DefaultValue": 2
            }
          },
          {
            "Label": "SdpsTargetRate",
            "Summary": "Percent by which transmit should be made to exceed dirty",
            "Key": "Migrate.SdpsTargetRate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 10000,
              "DefaultValue": 500
            }
          },
          {
            "Label": "SndBufSize",
            "Summary": "TCP send buffer size for the source",
            "Key": "Migrate.SndBufSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 65536,
              "Max": 1000000,
              "DefaultValue": 562540
            }
          },
          {
            "Label": "TS Master",
            "Summary": "Pseudo-synchronize clocks for migration to and from this machine",
            "Key": "Migrate.TSMaster",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "TcpTsoDeferTx",
            "Summary": "Use TCP tso defer optimization for transmit",
            "Key": "Migrate.TcpTsoDeferTx",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "TryToUseDefaultHeap",
            "Summary": "Attempt use the default migration heap when beginning new migrations",
            "Key": "Migrate.TryToUseDefaultHeap",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VASpaceReserveCount",
            "Summary": "Number of migrations to reserve virtual address space for at module load time",
            "Key": "Migrate.VASpaceReserveCount",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 2,
              "Max": 64,
              "DefaultValue": 64
            }
          },
          {
            "Label": "VASpaceReserveSize",
            "Summary": "Megabytes of virtual address space to reserve per migration at module load time",
            "Key": "Migrate.VASpaceReserveSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 64,
              "Max": 2048,
              "DefaultValue": 2048
            }
          },
          {
            "Label": "VMotionLatencySensitivity",
            "Summary": "Make vMotion helper worlds latency sensitive, avoid transmit delays.",
            "Key": "Migrate.VMotionLatencySensitivity",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VMotionResolveSwapType",
            "Summary": "Attempt to resolve swap type during VMotion initialization",
            "Key": "Migrate.VMotionResolveSwapType",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VMotionStreamDisable",
            "Summary": "Pretend to not support streams",
            "Key": "Migrate.VMotionStreamDisable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VMotionStreamHelpers",
            "Summary": "Number of helpers to allocate for VMotion streams",
            "Key": "Migrate.VMotionStreamHelpers",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 32,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Vmknic",
            "Summary": "vmknic for vMotion vmkernel VNIC",
            "Key": "Migrate.Vmknic",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "APD Handling Enable",
            "Summary": "Enable Storage APD Handling.",
            "Key": "Misc.APDHandlingEnable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "APD Timeout",
            "Summary": "Number of seconds a device can be in APD before failing User World I/O.",
            "Key": "Misc.APDTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 20,
              "Max": 999999,
              "DefaultValue": 140
            }
          },
          {
            "Label": "BH time-out",
            "Summary": "Time-out for bottom-half handlers in milliseconds",
            "Key": "Misc.BHTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 5000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "BhTimeBound",
            "Summary": "Time bound on BH checking in us (microseconds).",
            "Key": "Misc.BhTimeBound",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 500000,
              "DefaultValue": 2000
            }
          },
          {
            "Label": "Blue screen time-out",
            "Summary": "Time-out in seconds. Set to 0 for no time-out.",
            "Key": "Misc.BlueScreenTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 65535,
              "DefaultValue": 0
            }
          },
          {
            "Label": "ConsolePort",
            "Summary": "Name of serial port to use for visor console (COM1, COM2)",
            "Key": "Misc.ConsolePort",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "none",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "Enable debug buddy",
            "Summary": "Enable buddy debugging. Set to 0 to disable.",
            "Key": "Misc.DebugBuddyEnable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "DebugLogToSerial",
            "Summary": "Send vmkernel LOG messages to the serial port",
            "Key": "Misc.DebugLogToSerial",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "DefaultHardwareVersion",
            "Summary": "Default virtual machine compatibility",
            "Key": "Misc.DefaultHardwareVersion",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "Enable high DMA",
            "Summary": "Enable DMA above 4GB",
            "Key": "Misc.EnableHighDMA",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "EnablePSPLatencyPolicy",
            "Summary": "Enable latency based sub-policy of Round-robin path selection plugin",
            "Key": "Misc.EnablePSPLatencyPolicy",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "GDBPort",
            "Summary": "Name of serial port to use for GDB debugging (COM1, COM2)",
            "Key": "Misc.GDBPort",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "none",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "GuestLibAllowHostInfo",
            "Summary": "Allow guest to read host-level metrics",
            "Key": "Misc.GuestLibAllowHostInfo",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "HeapMgrGuardPages",
            "Summary": "Number of guard pages to insert between heap VA regions",
            "Key": "Misc.HeapMgrGuardPages",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 512,
              "DefaultValue": 1
            }
          },
          {
            "Label": "HeapPanicDestroyNonEmpty",
            "Summary": "Panic when a non-empty heap gets destroyed",
            "Key": "Misc.HeapPanicDestroyNonEmpty",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Heartbeat interval",
            "Summary": "Interval to check CPU lockups",
            "Key": "Misc.HeartbeatInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 86400000,
              "DefaultValue": 1000
            }
          },
          {
            "Label": "Panic time-out",
            "Summary": "Interval in seconds after which to panic if no heartbeats received",
            "Key": "Misc.HeartbeatPanicTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 86400,
              "DefaultValue": 14
            }
          },
          {
            "Label": "Heartbeat time-out",
            "Summary": "Time-out for sending NMI to the locked CPU",
            "Key": "Misc.HeartbeatTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 86400,
              "DefaultValue": 7
            }
          },
          {
            "Label": "HordeEnabled",
            "Summary": "Enables horde mode",
            "Key": "Misc.HordeEnabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Interrupt time-out",
            "Summary": "Time-out for interrupt handlers in milliseconds",
            "Key": "Misc.IntTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 5000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "I/O Filter watchdog timeout",
            "Summary": "Timeout for the I/O filter watchdog in seconds. 0 means the watchdog is disabled. 120 seconds is the minimum timeout value.",
            "Key": "Misc.IoFilterWatchdogTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 3600,
              "DefaultValue": 120
            }
          },
          {
            "Label": "LogDumpShutdownTimeout",
            "Summary": "The maximum amount of time during shutdown that the kernel logger will spend dumping logs from the log buffer",
            "Key": "Misc.LogDumpShutdownTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1800,
              "DefaultValue": 180
            }
          },
          {
            "Label": "LogPort",
            "Summary": "Name of serial port to use for logging (COM1, COM2)",
            "Key": "Misc.LogPort",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "none",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "LogTimestampUptime",
            "Summary": "Use uptime rather than UTC for vmkernel log",
            "Key": "Misc.LogTimestampUptime",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Log to file",
            "Summary": "Send VMkernel log messages to /var/log/vmkernel",
            "Key": "Misc.LogToFile",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Log to serial",
            "Summary": "Send VMkernel log messages to the serial port",
            "Key": "Misc.LogToSerial",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Log world prefix",
            "Summary": "Include running world on every log statement",
            "Key": "Misc.LogWldPrefix",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "MCEMonitorInterval",
            "Summary": "Interval[0 - 0x7fffffff ms] to check Machine Check Errors(0=never)",
            "Key": "Misc.MCEMonitorInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2147483647,
              "DefaultValue": 250
            }
          },
          {
            "Label": "Minimal panic",
            "Summary": "Do not attempt core dump after purple screen",
            "Key": "Misc.MinimalPanic",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "NMILint1IntAction",
            "Summary": "Change how a hardware generated NMI should be handled (1=enter debugger, 2=panic)",
            "Key": "Misc.NMILint1IntAction",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 3,
              "DefaultValue": 0
            }
          },
          {
            "Label": "PSPDeactivateFlakyPath",
            "Summary": "Deactivate flaky path if IOs are failing with HOST ERROR",
            "Key": "Misc.PSPDeactivateFlakyPath",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Panic Logs to serial",
            "Summary": "Send Panic log messages to the serial port",
            "Key": "Misc.PanicLogToSerial",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Power button",
            "Summary": "Action to take on a momentary press of the soft power button (0=ignore, 1=request graceful system shutdown and power-off)",
            "Key": "Misc.PowerButton",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Power-off enable",
            "Summary": "Action to take on system power-off request (0=halt only, 1=power off)",
            "Key": "Misc.PowerOffEnable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "PreferredHostName",
            "Summary": "Preferred Host name",
            "Key": "Misc.PreferredHostName",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "Proc verbose",
            "Summary": "Option unused",
            "Key": "Misc.ProcVerbose",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "SIOControlFlag1",
            "Summary": "Storage I/O Control Internal Flag",
            "Key": "Misc.SIOControlFlag1",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2147483647,
              "DefaultValue": 0
            }
          },
          {
            "Label": "SIOControlFlag2",
            "Summary": "Storage I/O Control Internal Flag",
            "Key": "Misc.SIOControlFlag2",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2147483647,
              "DefaultValue": 0
            }
          },
          {
            "Label": "SIOControlLoglevel",
            "Summary": "Storage I/O Control Log Level",
            "Key": "Misc.SIOControlLoglevel",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 7,
              "DefaultValue": 0
            }
          },
          {
            "Label": "SIOControlOptions",
            "Summary": "Storage I/O Control Options",
            "Key": "Misc.SIOControlOptions",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "ScreenSaverDelay",
            "Summary": "Delay in minutes before screensaver kicks in",
            "Key": "Misc.ScreenSaverDelay",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 60,
              "DefaultValue": 0
            }
          },
          {
            "Label": "ShaperStatsEnabled",
            "Summary": "Enable stats in shaper module",
            "Key": "Misc.ShaperStatsEnabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "ShellPort",
            "Summary": "Name of serial port to use for visor shell (COM1, COM2)",
            "Key": "Misc.ShellPort",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "none",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "Maximum hard timer period",
            "Summary": "Maximum hard timer interrupt period in microseconds",
            "Key": "Misc.TimerMaxHardPeriod",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 10000000,
              "DefaultValue": 500000
            }
          },
          {
            "Label": "TimerTolerance",
            "Summary": "Default timer lateness tolerance in microseconds",
            "Key": "Misc.TimerTolerance",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 1000000,
              "DefaultValue": 2000
            }
          },
          {
            "Label": "USB Arbitrator auto start disable",
            "Summary": "Disable automatic start of USB Arbitrator.  If set USB passthrough will not be available until USB arbitrator is started at the command line.",
            "Key": "Misc.UsbArbitratorAutoStartDisabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "UserDuctDynBufferSize",
            "Summary": "Max buffer size for UW duct (bytes)",
            "Key": "Misc.UserDuctDynBufferSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 32768,
              "DefaultValue": 16384
            }
          },
          {
            "Label": "UserSocketUnixMaxBufferSize",
            "Summary": "Max buffer size for UW unix domain sockets (bytes)",
            "Key": "Misc.UserSocketUnixMaxBufferSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 131072,
              "DefaultValue": 65536
            }
          },
          {
            "Label": "Watchdog backtrace",
            "Summary": "Backtrace on every Nth watchdog",
            "Key": "Misc.WatchdogBacktrace",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10,
              "DefaultValue": 0
            }
          },
          {
            "Label": "WorldletActivationUS",
            "Summary": "Number of microseconds a worldlet may execute prior to being asked to yield.",
            "Key": "Misc.WorldletActivationUS",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 10000,
              "DefaultValue": 500
            }
          },
          {
            "Label": "WorldletActivationsLimit",
            "Summary": "Number of worldlet activations permissible in each interruption (0==unlimited).",
            "Key": "Misc.WorldletActivationsLimit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000,
              "DefaultValue": 8
            }
          },
          {
            "Label": "WorldletGreedySampleMCycles",
            "Summary": "million cycles between greedy samples, controls greedy sampling rate",
            "Key": "Misc.WorldletGreedySampleMCycles",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 1000,
              "DefaultValue": 10
            }
          },
          {
            "Label": "WorldletGreedySampleRun",
            "Summary": "number of runs between greedy samples, controls greedy sampling rate",
            "Key": "Misc.WorldletGreedySampleRun",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 16,
              "Max": 4096,
              "DefaultValue": 256
            }
          },
          {
            "Label": "WorldletIRQPenalty",
            "Summary": "constant load penalty for worldlets driven by interrupts",
            "Key": "Misc.WorldletIRQPenalty",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 200,
              "DefaultValue": 10
            }
          },
          {
            "Label": "WorldletLoadThreshold",
            "Summary": "Worldlet load/score level below which a placement is deemed desirable",
            "Key": "Misc.WorldletLoadThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 200,
              "DefaultValue": 90
            }
          },
          {
            "Label": "WorldletLoadType",
            "Summary": "Worldlet load type",
            "Key": "Misc.WorldletLoadType",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "medium",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "WorldletLocalityBonus",
            "Summary": "load score bonus for keeping a worldlet on it's last PCPU/LLC",
            "Key": "Misc.WorldletLocalityBonus",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 200,
              "DefaultValue": 10
            }
          },
          {
            "Label": "WorldletLoosePenalty",
            "Summary": "load penalty for leaving LOOSE affinity topology level",
            "Key": "Misc.WorldletLoosePenalty",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 200,
              "DefaultValue": 30
            }
          },
          {
            "Label": "WorldletMigOverheadLLC",
            "Summary": "intra-cache worldlet migration overhead, default = 4/1024=0.39%",
            "Key": "Misc.WorldletMigOverheadLLC",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1024,
              "DefaultValue": 4
            }
          },
          {
            "Label": "WorldletMigOverheadRemote",
            "Summary": "inter-cache worldlet migration overhead, default = 16/1024=1.56%",
            "Key": "Misc.WorldletMigOverheadRemote",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1024,
              "DefaultValue": 16
            }
          },
          {
            "Label": "WorldletPreemptOverhead",
            "Summary": "overhead if worldlet preempts a running no-idle world, in percentage",
            "Key": "Misc.WorldletPreemptOverhead",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 30
            }
          },
          {
            "Label": "WorldletRemoteActivateOverhead",
            "Summary": "remote activate overhead, in cycles",
            "Key": "Misc.WorldletRemoteActivateOverhead",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 50000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "WorldletWorldOverheadLLC",
            "Summary": "overhead if worldlet is on the same cache as its affinity world, in cycles",
            "Key": "Misc.WorldletWorldOverheadLLC",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 50000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "WorldletWorldOverheadRemote",
            "Summary": "overhead if worldlet is on different cache as its affinity world, in percentage",
            "Key": "Misc.WorldletWorldOverheadRemote",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 10
            }
          },
          {
            "Label": "VmmDisableL1DFlush",
            "Summary": "Disable L1D flush on VM entry",
            "Key": "Misc.vmmDisableL1DFlush",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "vsanWitnessVirtualAppliance",
            "Summary": "Indicates a vSAN witness host running in a Virtual Appliance. VM services (create/register/power on) are blocked",
            "Key": "Misc.vsanWitnessVirtualAppliance",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "DatastoreNamespaceManager Timeout",
            "Summary": "Timeout for DatastoreNamespaceManager operations in milliseconds",
            "Key": "Misc.DsNsMgrTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10000,
              "Max": 1800000,
              "DefaultValue": 1200000
            }
          },
          {
            "Label": "Misc.HostAgentUpdateLevel",
            "Summary": "The update level of Host Agent.",
            "Key": "Misc.HostAgentUpdateLevel",
            "OptionType": {
              "ValueIsReadonly": true,
              "DefaultValue": "",
              "ValidCharacters": ""
            }
          },
          {
            "Label": "VM MetadataManager Timeout",
            "Summary": "Timeout for VM MetadataManager operations in milliseconds",
            "Key": "Misc.MetadataUpdateTimeoutMsec",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 600000,
              "DefaultValue": 30000
            }
          },
          {
            "Label": "APD start count",
            "Summary": "Number of sequential heartbeat failures after which APD start event is triggered",
            "Key": "NFS.ApdStartCount",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 5,
              "DefaultValue": 3
            }
          },
          {
            "Label": "Disk file lock update frequency",
            "Summary": "Time (in seconds) between updates to a disk lock file",
            "Key": "NFS.DiskFileLockUpdateFreq",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 8,
              "Max": 3600,
              "DefaultValue": 10
            }
          },
          {
            "Label": "Heartbeat delta",
            "Summary": "Time in seconds since the last successful update before a heartbeat is sent",
            "Key": "NFS.HeartbeatDelta",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 3,
              "Max": 30,
              "DefaultValue": 5
            }
          },
          {
            "Label": "Heartbeat frequency",
            "Summary": "Time in seconds between heartbeats",
            "Key": "NFS.HeartbeatFrequency",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 5,
              "Max": 86400,
              "DefaultValue": 12
            }
          },
          {
            "Label": "Maximum heartbeat failures",
            "Summary": "Number of sequential failures before a volume is marked down",
            "Key": "NFS.HeartbeatMaxFailures",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 6,
              "Max": 10,
              "DefaultValue": 10
            }
          },
          {
            "Label": "Heartbeat time-out",
            "Summary": "Time in seconds before an outstanding heartbeat is stopped",
            "Key": "NFS.HeartbeatTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 3,
              "Max": 30,
              "DefaultValue": 5
            }
          },
          {
            "Label": "LockBreakTimeout",
            "Summary": "Time (in seconds) to wait to check for expired races when breaking lock",
            "Key": "NFS.LockBreakTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 3600,
              "DefaultValue": 10
            }
          },
          {
            "Label": "Maximum number of lock renew failures",
            "Summary": "Number of update failures before a disk file lock is declared stale",
            "Key": "NFS.LockRenewMaxFailureNumber",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 100,
              "DefaultValue": 3
            }
          },
          {
            "Label": "LockSWMRTimeout",
            "Summary": "Time (in seconds) to wait to check for expired races when acquiring SWMR lock",
            "Key": "NFS.LockSWMRTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 3600,
              "DefaultValue": 10
            }
          },
          {
            "Label": "LockSharedTimeout",
            "Summary": "Time (in seconds) to wait to check for expired races when sharing (read) lock",
            "Key": "NFS.LockSharedTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 3600,
              "DefaultValue": 0
            }
          },
          {
            "Label": "LockUpdateTimeout",
            "Summary": "Time (in seconds) before we abort an outstanding lock update",
            "Key": "NFS.LockUpdateTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 8,
              "DefaultValue": 5
            }
          },
          {
            "Label": "LogNfsStat3",
            "Summary": "Log nfsstat3 code",
            "Key": "NFS.LogNfsStat3",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "MaxQueueDepth",
            "Summary": "Maximum per-Volume queue depth",
            "Key": "NFS.MaxQueueDepth",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 4294967295,
              "DefaultValue": 4294967295
            }
          },
          {
            "Label": "Maximum NFS v3 volumes count",
            "Summary": "Maximum number of mounted NFS v3 volumes",
            "Key": "NFS.MaxVolumes",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 8,
              "Max": 256,
              "DefaultValue": 8
            }
          },
          {
            "Label": "Socket receive buffer size",
            "Summary": "Default size of socket receive buffer (KB)",
            "Key": "NFS.ReceiveBufferSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 64,
              "Max": 5120,
              "DefaultValue": 1024
            }
          },
          {
            "Label": "Socket send buffer size",
            "Summary": "Default size of socket send buffer (KB)",
            "Key": "NFS.SendBufferSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 32,
              "Max": 5120,
              "DefaultValue": 1024
            }
          },
          {
            "Label": "SyncRetries",
            "Summary": "Number of retries before synchronous IO fails (10 seconds per retry)",
            "Key": "NFS.SyncRetries",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 3,
              "Max": 360,
              "DefaultValue": 25
            }
          },
          {
            "Label": "Remount volume frequency",
            "Summary": "Time in seconds before attempting to remount a volume",
            "Key": "NFS.VolumeRemountFrequency",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 30,
              "Max": 3600,
              "DefaultValue": 30
            }
          },
          {
            "Label": "EOSDelay",
            "Summary": "Request EOS safety delay in seconds",
            "Key": "NFS41.EOSDelay",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 90,
              "DefaultValue": 30
            }
          },
          {
            "Label": "IOTaskRetry",
            "Summary": "Synchronous I/O task number of retries",
            "Key": "NFS41.IOTaskRetry",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 100,
              "DefaultValue": 25
            }
          },
          {
            "Label": "MaxRead",
            "Summary": "Maximum read transfer size in bytes (use the smaller of this value and the server advertised value)",
            "Key": "NFS41.MaxRead",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 256,
              "Max": 4294967295,
              "DefaultValue": 4294967295
            }
          },
          {
            "Label": "Maximum NFS v4.1 volumes count",
            "Summary": "Maximum number of mounted NFS v4.1 volumes",
            "Key": "NFS41.MaxVolumes",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 8,
              "Max": 256,
              "DefaultValue": 8
            }
          },
          {
            "Label": "MaxWrite",
            "Summary": "Maximum write transfer size in bytes (use the smaller of this value and the server advertised value)",
            "Key": "NFS41.MaxWrite",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 256,
              "Max": 4294967295,
              "DefaultValue": 4294967295
            }
          },
          {
            "Label": "MountTimeout",
            "Summary": "Mount timeout in seconds",
            "Key": "NFS41.MountTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 60,
              "DefaultValue": 30
            }
          },
          {
            "Label": "RecvBufSize",
            "Summary": "Socket receive buffer size in kilobytes (using default if set to zero)",
            "Key": "NFS41.RecvBufSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2048,
              "DefaultValue": 1024
            }
          },
          {
            "Label": "SendBufSize",
            "Summary": "Socket send buffer size in kilobytes (using default if set to zero)",
            "Key": "NFS41.SendBufSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2048,
              "DefaultValue": 1024
            }
          },
          {
            "Label": "AdvertisementDuration",
            "Summary": "duration of RARP advertisements",
            "Key": "Net.AdvertisementDuration",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 255,
              "DefaultValue": 60
            }
          },
          {
            "Label": "AllowPT",
            "Summary": "Whether to enable UPT/NPA",
            "Key": "Net.AllowPT",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "BlockGuestBPDU",
            "Summary": "Block guest sourced BPDU frames",
            "Key": "Net.BlockGuestBPDU",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Coalesce default on",
            "Summary": "Dynamic coalescing on by default",
            "Key": "Net.CoalesceDefaultOn",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CoalesceFavorNoVmmVmkTx",
            "Summary": "Favor disabling all vmm-\u003evmk tx transitions; boost its score by factor of this/64.",
            "Key": "Net.CoalesceFavorNoVmmVmkTx",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 64,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CoalesceFineTimeoutCPU",
            "Summary": "Set which CPU the fine timer will run on",
            "Key": "Net.CoalesceFineTimeoutCPU",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 512,
              "DefaultValue": 2
            }
          },
          {
            "Label": "CoalesceFineTxTimeout",
            "Summary": "set the fine coalesce timeout in microseconds",
            "Key": "Net.CoalesceFineTxTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 4294967295,
              "DefaultValue": 1000
            }
          },
          {
            "Label": "CoalesceFlexMrq",
            "Summary": "Whether to dynamically switch on/off multiRxQCalib.",
            "Key": "Net.CoalesceFlexMrq",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CoalesceLowRxRate",
            "Summary": "No Rx coalescing calibration when the number of pkts Rx per timeout is lower than this number.",
            "Key": "Net.CoalesceLowRxRate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 256,
              "DefaultValue": 4
            }
          },
          {
            "Label": "CoalesceLowTxRate",
            "Summary": "No tx coalescing calibration when the number of pkts tx per timeout is lower than this number.",
            "Key": "Net.CoalesceLowTxRate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 256,
              "DefaultValue": 4
            }
          },
          {
            "Label": "CoalesceMatchedQs",
            "Summary": "Whether to use matched TxRxQ-pairs mode when applicable.",
            "Key": "Net.CoalesceMatchedQs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CoalesceMrqLt",
            "Summary": "Whether to set a RxQ's coalesce to zero based on per-RxQ Low Traffic.",
            "Key": "Net.CoalesceMrqLt",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CoalesceMrqMetricAllowTxOnly",
            "Summary": "Whether to allow's individual RxQ's perf metric to be tx pkt cnt only; if not, it will be tx + rx, or rx only.",
            "Key": "Net.CoalesceMrqMetricAllowTxOnly",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CoalesceMrqMetricRxOnly",
            "Summary": "Whether to force individual RxQ's perf metric to be rx pkt cnt only.",
            "Key": "Net.CoalesceMrqMetricRxOnly",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "CoalesceMrqOverallStop",
            "Summary": "Whether to use overall performance to stop RxQ Calib.",
            "Key": "Net.CoalesceMrqOverallStop",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "CoalesceMrqRatioMetric",
            "Summary": "Whether Tx perf score is attributed to RxQ according to rxPktCnt ratio.",
            "Key": "Net.CoalesceMrqRatioMetric",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CoalesceMrqTriggerReCalib",
            "Summary": "Whether to let individual RxQ's perf change trigger re-calib.",
            "Key": "Net.CoalesceMrqTriggerReCalib",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CoalesceMultiRxQCalib",
            "Summary": "When not in matched TxRxQ-pairs mode, whether to uses separate RxQ Calib.",
            "Key": "Net.CoalesceMultiRxQCalib",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CoalesceNoVmmVmkTx",
            "Summary": "Whether to try disable all vmm-\u003evmk tx transitions.",
            "Key": "Net.CoalesceNoVmmVmkTx",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CoalesceParams",
            "Summary": "Set parameters for the default virtual NIC coalescing scheme.",
            "Key": "Net.CoalesceParams",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "CoalesceRBCRate",
            "Summary": "Target event rate for RateBasedCoalescing",
            "Key": "Net.CoalesceRBCRate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 100000,
              "DefaultValue": 4000
            }
          },
          {
            "Label": "CoalesceRxLtStopCalib",
            "Summary": "Whether Rx Low Traffic stops Rx calibration.",
            "Key": "Net.CoalesceRxLtStopCalib",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "CoalesceRxQDepthCap",
            "Summary": "Cap of Rx coalescing size.",
            "Key": "Net.CoalesceRxQDepthCap",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 80,
              "DefaultValue": 40
            }
          },
          {
            "Label": "CoalesceScheme",
            "Summary": "Set the default virtual NIC coalescing scheme.",
            "Key": "Net.CoalesceScheme",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "rbc",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "CoalesceTimeoutType",
            "Summary": "set the coalesce timeout type: fine(1 ms by default) or coarse (4 ms by default)",
            "Key": "Net.CoalesceTimeoutType",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 2,
              "DefaultValue": 2
            }
          },
          {
            "Label": "CoalesceTxAlwaysPoll",
            "Summary": "Whether always poll Tx at coalesce timeout handler.",
            "Key": "Net.CoalesceTxAlwaysPoll",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CoalesceTxQDepthCap",
            "Summary": "Cap of Tx coalescing size.",
            "Key": "Net.CoalesceTxQDepthCap",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 80,
              "DefaultValue": 40
            }
          },
          {
            "Label": "Coalesce Tx time-out",
            "Summary": "Set the coalesce time-out in microseconds",
            "Key": "Net.CoalesceTxTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 4294967295,
              "DefaultValue": 4000
            }
          },
          {
            "Label": "DCBEnable",
            "Summary": "Enable DCB for FCoE",
            "Key": "Net.DCBEnable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "DVFilterBindIpAddress",
            "Summary": "DVFilter internal communication endpoint",
            "Key": "Net.DVFilterBindIpAddress",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "DVFilterPriorityRdLockEnable",
            "Summary": "Use priority locking in dvfilter to read lock portsets",
            "Key": "Net.DVFilterPriorityRdLockEnable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "DVSLargeHeapMaxSize",
            "Summary": "Max size for the dvsLargeHeap",
            "Key": "Net.DVSLargeHeapMaxSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 64,
              "Max": 256,
              "DefaultValue": 80
            }
          },
          {
            "Label": "DontOffloadInnerIPv6",
            "Summary": "Don't offload inner IPv6 CSO/TSO packets to physical NICs",
            "Key": "Net.DontOffloadInnerIPv6",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "E1000IntrCoalesce",
            "Summary": "Whether to enable interrupt coalescing for e1000 vNIC.",
            "Key": "Net.E1000IntrCoalesce",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "E1000TxCopySize",
            "Summary": "e1000 tx less than or equal to this will be fully copied with no need for completion.",
            "Key": "Net.E1000TxCopySize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 2048
            }
          },
          {
            "Label": "E1000TxZeroCopy",
            "Summary": "Use tx zero copy for non-tso packets for e1000.",
            "Key": "Net.E1000TxZeroCopy",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "EnableDMASgCons",
            "Summary": "When non-zero, enable the DMA SG constraints support in uplink layer",
            "Key": "Net.EnableDMASgCons",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "EnableOuterCsum",
            "Summary": "Enable uplink layer outer checksumming",
            "Key": "Net.EnableOuterCsum",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "EnsMbufpoolMaxMBPerGB",
            "Summary": "Maximum MB of the ENS slab memory to be allocated per GB of physical memory",
            "Key": "Net.EnsMbufpoolMaxMBPerGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 500,
              "DefaultValue": 200
            }
          },
          {
            "Label": "EnsMbufpoolMinMBPerGB",
            "Summary": "Minimum MB of the ENS slab memory to be allocated per GB of physical memory",
            "Key": "Net.EnsMbufpoolMinMBPerGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 500,
              "DefaultValue": 10
            }
          },
          {
            "Label": "Etherswitch fast path",
            "Summary": "Allow Etherswitch fast path",
            "Key": "Net.EtherswitchAllowFastPath",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Ethernet switch hash size",
            "Summary": "Number of ports on the etherswitch x 2^N is the size of the hash table for looking up MACs",
            "Key": "Net.EtherswitchHashSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4,
              "DefaultValue": 1
            }
          },
          {
            "Label": "EtherswitchHeapMax",
            "Summary": "The maximum size (in Megabytes) to which the etherswitch heap can grow. (REQUIRES REBOOT!)",
            "Key": "Net.EtherswitchHeapMax",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 86,
              "Max": 2047,
              "DefaultValue": 512
            }
          },
          {
            "Label": "Etherswitch per-pCPU dispatch data number",
            "Summary": "The dispatch data number in the etherswitch per-pCPU dispatch data cache. (REQUIRES REBOOT!)",
            "Key": "Net.EtherswitchNumPerPCPUDispatchData",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 3,
              "Max": 63,
              "DefaultValue": 3
            }
          },
          {
            "Label": "FollowHardwareMac",
            "Summary": "If set to 1, the management interface MAC address will update whenever the hardware MAC address changes.",
            "Key": "Net.FollowHardwareMac",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "GuestIPHack",
            "Summary": "Enable guest arp inspection IOChain to get IP",
            "Key": "Net.GuestIPHack",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Guest Tx copy break",
            "Summary": "Transmits smaller than this will be copied rather than mapped",
            "Key": "Net.GuestTxCopyBreak",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 60,
              "Max": 4294967295,
              "DefaultValue": 64
            }
          },
          {
            "Label": "IGMPQueries",
            "Summary": "Number of IGMP Queries to send during after VMotion/Teaming failover",
            "Key": "Net.IGMPQueries",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2,
              "DefaultValue": 2
            }
          },
          {
            "Label": "IGMPQueryInterval",
            "Summary": "Interval(in seconds) for IGMP/MLD general query in multicast snooping",
            "Key": "Net.IGMPQueryInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 32,
              "Max": 3600,
              "DefaultValue": 125
            }
          },
          {
            "Label": "IGMPRouterIP",
            "Summary": "Router IP Address for IGMP (generally not necessary to set this)",
            "Key": "Net.IGMPRouterIP",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "0.0.0.0",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "IGMPV3MaxSrcIPNum",
            "Summary": "Max per-group srouce IP number for IGMP V3",
            "Key": "Net.IGMPV3MaxSrcIPNum",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 32,
              "DefaultValue": 10
            }
          },
          {
            "Label": "IGMPVersion",
            "Summary": "IGMP Version (2 or 3)",
            "Key": "Net.IGMPVersion",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 2,
              "Max": 3,
              "DefaultValue": 3
            }
          },
          {
            "Label": "LRODefBackoffPeriod",
            "Summary": "After adaptive LRO decided not to do LRO, how many intervals to wait before trying again.",
            "Key": "Net.LRODefBackoffPeriod",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 65535,
              "DefaultValue": 8
            }
          },
          {
            "Label": "LRODefMaxLength",
            "Summary": "LRO default max length",
            "Key": "Net.LRODefMaxLength",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 65535,
              "DefaultValue": 65535
            }
          },
          {
            "Label": "LRODefThreshold",
            "Summary": "After this # packets, evaluate whether to continue SW LRO",
            "Key": "Net.LRODefThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 65535,
              "DefaultValue": 4000
            }
          },
          {
            "Label": "LRODefUseRatioDenom",
            "Summary": "If SW LRO reduce pkt count to be smaller than ratio, continue to do LRO. Denominator of ratio.",
            "Key": "Net.LRODefUseRatioDenom",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 255,
              "DefaultValue": 3
            }
          },
          {
            "Label": "LRODefUseRatioNumer",
            "Summary": "If SW LRO reduce pkt count to be smaller than ratio, continue to do LRO. Numerator of ratio.",
            "Key": "Net.LRODefUseRatioNumer",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 255,
              "DefaultValue": 1
            }
          },
          {
            "Label": "LinkFlappingThreshold",
            "Summary": "Max number of link down events per minute before considering a link unstable (0 to deactivate)",
            "Key": "Net.LinkFlappingThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 65535,
              "DefaultValue": 60
            }
          },
          {
            "Label": "LinkStatePollTimeout",
            "Summary": "Link State poll timer period in milliseconds.",
            "Key": "Net.LinkStatePollTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 90000,
              "DefaultValue": 500
            }
          },
          {
            "Label": "MLDRouterIP",
            "Summary": "Router IPv6 Address for MLD (generally not necessary to set this)",
            "Key": "Net.MLDRouterIP",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "FE80::FFFF:FFFF:FFFF:FFFF",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "MLDV2MaxSrcIPNum",
            "Summary": "Max per-group srouce IP number for MLD V2",
            "Key": "Net.MLDV2MaxSrcIPNum",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 32,
              "DefaultValue": 10
            }
          },
          {
            "Label": "MLDVersion",
            "Summary": "MLD Version (1 or 2)",
            "Key": "Net.MLDVersion",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 2,
              "DefaultValue": 2
            }
          },
          {
            "Label": "MaxBeaconVlans",
            "Summary": "maximum number of VLANs to probe with beacons.",
            "Key": "Net.MaxBeaconVlans",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4096,
              "DefaultValue": 100
            }
          },
          {
            "Label": "Maximum simultaneous beacons",
            "Summary": "Maximum number of beacons to send in one beacon cycle",
            "Key": "Net.MaxBeaconsAtOnce",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4096,
              "DefaultValue": 100
            }
          },
          {
            "Label": "MaxGlobalRxQueueCount",
            "Summary": "Global max length of rx queues for all virtual ports on a ESX host that support queueing",
            "Key": "Net.MaxGlobalRxQueueCount",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 100000
            }
          },
          {
            "Label": "Maximum net Tx queue length",
            "Summary": "Maximum length of the Tx queue for the physical NICs",
            "Key": "Net.MaxNetifTxQueueLen",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 10000,
              "DefaultValue": 2000
            }
          },
          {
            "Label": "Maximum page in queue length",
            "Summary": "Maximum number of paging requests to queue for guest DMA",
            "Key": "Net.MaxPageInQueueLen",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 500,
              "DefaultValue": 75
            }
          },
          {
            "Label": "MaxPktRxListQueue",
            "Summary": "Maximum packet we can queue in rxList",
            "Key": "Net.MaxPktRxListQueue",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 200000,
              "DefaultValue": 3500
            }
          },
          {
            "Label": "Maximum port Rx queue length",
            "Summary": "Maximum length of the rx queue for virtual ports whose clients support queuing",
            "Key": "Net.MaxPortRxQueueLen",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 500,
              "DefaultValue": 80
            }
          },
          {
            "Label": "MinEtherLen",
            "Summary": "Minimum size ethernet frame to transmit",
            "Key": "Net.MinEtherLen",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 60,
              "Max": 1518,
              "DefaultValue": 60
            }
          },
          {
            "Label": "NcpLlcSap",
            "Summary": "beacon/color NCP messages created with this SAP (DSAP/SSAP)",
            "Key": "Net.NcpLlcSap",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 255,
              "DefaultValue": 0
            }
          },
          {
            "Label": "NetBHRxStormThreshold",
            "Summary": "Declare Rx Storm after this number of consecutive rx pkt drops during queuing in NetBH rxList.",
            "Key": "Net.NetBHRxStormThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 200000,
              "DefaultValue": 320
            }
          },
          {
            "Label": "NetDebugRARPTimerInter",
            "Summary": "RARP timer will be triggered at this interval.",
            "Key": "Net.NetDebugRARPTimerInter",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 16777215,
              "DefaultValue": 30000
            }
          },
          {
            "Label": "NetDeferTxCompletion",
            "Summary": "Whether to defer tx completion to tx worldlet/world.",
            "Key": "Net.NetDeferTxCompletion",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "NetDiscUpdateIntrvl",
            "Summary": "Set interval (in milliseconds) of networking discovery to update the VLAN information",
            "Key": "Net.NetDiscUpdateIntrvl",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 4294967295,
              "DefaultValue": 300
            }
          },
          {
            "Label": "NetEnableSwCsumForLro",
            "Summary": "Whether enable software checksum for LRO",
            "Key": "Net.NetEnableSwCsumForLro",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "NetEsxfwPassOutboundGRE",
            "Summary": "Whether outbound GRE traffic is passed by ESXi firewall",
            "Key": "Net.NetEsxfwPassOutboundGRE",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "NetInStressTest",
            "Summary": "If set to 1, suppress certain logs to avoid log spew.",
            "Key": "Net.NetInStressTest",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "NetLatencyAwareness",
            "Summary": "Whether to check virtual machine's latency settings or not for vmxnet2/3",
            "Key": "Net.NetLatencyAwareness",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "NetMaxRarpsPerInterval",
            "Summary": "Max number of RARPs dispatched per timer callback.",
            "Key": "Net.NetMaxRarpsPerInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 4294967295,
              "DefaultValue": 128
            }
          },
          {
            "Label": "NetNetqNumaIOCpuPinThreshold",
            "Summary": "CPU threshold for pinning device queues in NUMA I/O",
            "Key": "Net.NetNetqNumaIOCpuPinThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 60,
              "DefaultValue": 0
            }
          },
          {
            "Label": "NetNetqRxRebalRSSLoadThresholdPerc",
            "Summary": "Threshold percentage to rebalance RSS(Receive Side Scaling) queue",
            "Key": "Net.NetNetqRxRebalRSSLoadThresholdPerc",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 10
            }
          },
          {
            "Label": "NetNetqTxPackKpps",
            "Summary": "Max TX queue load (in thousand packet per second) to allow packing on the corresponding RX queue",
            "Key": "Net.NetNetqTxPackKpps",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 10000,
              "DefaultValue": 300
            }
          },
          {
            "Label": "NetNetqTxUnpackKpps",
            "Summary": "Threshold (in thousand packet per second) for TX queue load to trigger unpacking of the corresponding RX queue",
            "Key": "Net.NetNetqTxUnpackKpps",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 10000,
              "DefaultValue": 600
            }
          },
          {
            "Label": "NetNiocAllowOverCommit",
            "Summary": "Whether allow NIOC overcommit when a vNIC is in connected state for DVS",
            "Key": "Net.NetNiocAllowOverCommit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "NetPTMgrWakeupInterval",
            "Summary": "How often the PTMgr will wakeup and trigger the UPT mode switch in second.",
            "Key": "Net.NetPTMgrWakeupInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 5,
              "Max": 10,
              "DefaultValue": 6
            }
          },
          {
            "Label": "Packet allocation retry count",
            "Summary": "Number of attempts for allocating packet within the page",
            "Key": "Net.NetPktAllocTries",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 10,
              "DefaultValue": 5
            }
          },
          {
            "Label": "NetPktSlabFreePercentThreshold",
            "Summary": "Percent of free network memory pool, below which an event is reported.",
            "Key": "Net.NetPktSlabFreePercentThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 2,
              "Max": 50,
              "DefaultValue": 2
            }
          },
          {
            "Label": "NetPortFlushIterLimit",
            "Summary": "when input is serialized, this bounds the number of times a thread flushes the deferred list.",
            "Key": "Net.NetPortFlushIterLimit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 127,
              "DefaultValue": 2
            }
          },
          {
            "Label": "NetPortFlushPktLimit",
            "Summary": "when input is serialized, this bounds the number of pkts a thread flushes from the deferred list.",
            "Key": "Net.NetPortFlushPktLimit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 255,
              "DefaultValue": 64
            }
          },
          {
            "Label": "NetPortTrackTxRace",
            "Summary": "if enabled(1), collect statistics on potential tx race between concurrent threads.",
            "Key": "Net.NetPortTrackTxRace",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "NetRmDistMacFilter",
            "Summary": "Activate/Deactivate the MAC filter on distributed NetRM",
            "Key": "Net.NetRmDistMacFilter",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "NetRmDistSamplingRate",
            "Summary": "Sampling rate on distributed NetRM",
            "Key": "Net.NetRmDistSamplingRate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "NetRxCopyInTx",
            "Summary": "Whether to enable rx copy in tx worldlet/world.",
            "Key": "Net.NetRxCopyInTx",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "NetSchedCoalesceTxUsecs",
            "Summary": "Maximum number of microseconds the device can delay tx completions",
            "Key": "Net.NetSchedCoalesceTxUsecs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 125,
              "DefaultValue": 33
            }
          },
          {
            "Label": "NetSchedDefaultResPoolSharesPct",
            "Summary": "Percent share given to unclassified traffic",
            "Key": "Net.NetSchedDefaultResPoolSharesPct",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 99,
              "DefaultValue": 5
            }
          },
          {
            "Label": "NetSchedDefaultSchedName",
            "Summary": "Default networking packet scheduler",
            "Key": "Net.NetSchedDefaultSchedName",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "fifo",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "NetSchedECNEnabled",
            "Summary": "Enable net scheduler to send congestion notification to switch ports.",
            "Key": "Net.NetSchedECNEnabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "NetSchedECNThreshold",
            "Summary": "Percentage queue utilization at which net scheduler will start sending congestion notifications to switch ports.",
            "Key": "Net.NetSchedECNThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 70
            }
          },
          {
            "Label": "NetSchedHClkLeafQueueDepthPkt",
            "Summary": "Maximum number of packets each HClk leaf node can hold",
            "Key": "Net.NetSchedHClkLeafQueueDepthPkt",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 10000,
              "DefaultValue": 500
            }
          },
          {
            "Label": "NetSchedHClkMQ",
            "Summary": "Enable multiple hardware queue for hclk netsched",
            "Key": "Net.NetSchedHClkMQ",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "NetSchedHClkMaxHwQueue",
            "Summary": "Maximum number hardware queue that HClock can use",
            "Key": "Net.NetSchedHClkMaxHwQueue",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 2
            }
          },
          {
            "Label": "NetSchedHClkVnicMQ",
            "Summary": "Enable multiple VNIC queue for hardware send queue selection",
            "Key": "Net.NetSchedHClkVnicMQ",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "NetSchedHeapMaxSizeMB",
            "Summary": "Size of the netsched subsystem heap in MB",
            "Key": "Net.NetSchedHeapMaxSizeMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 96,
              "DefaultValue": 64
            }
          },
          {
            "Label": "NetSchedInFlightMaxBytesDefault",
            "Summary": "Number of bytes fed to the nic for nics with a normal (1Gbps) linkspeed",
            "Key": "Net.NetSchedInFlightMaxBytesDefault",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1500,
              "Max": 1500000,
              "DefaultValue": 20000
            }
          },
          {
            "Label": "NetSchedInFlightMaxBytesInsane",
            "Summary": "Number of bytes fed to the nic for nics that don't support tx completion coalescing",
            "Key": "Net.NetSchedInFlightMaxBytesInsane",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1500,
              "Max": 1500000,
              "DefaultValue": 1500000
            }
          },
          {
            "Label": "NetSchedMaxPktSend",
            "Summary": "Maximum number of packets that we can send to the driver at a time",
            "Key": "Net.NetSchedMaxPktSend",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 4096,
              "DefaultValue": 256
            }
          },
          {
            "Label": "NetSchedQoSSchedName",
            "Summary": "Default networking packet scheduler for QoS",
            "Key": "Net.NetSchedQoSSchedName",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "hclk",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "NetSchedSpareBasedShares",
            "Summary": "Enable shares scheduling only on spare bandwidth. Don't bill while doing reservation scheduling",
            "Key": "Net.NetSchedSpareBasedShares",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "NetSendRARPOnPortEnablement",
            "Summary": "Ensure one RARP is sent immediately when a port is enabled",
            "Key": "Net.NetSendRARPOnPortEnablement",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "NetShaperQueuePerL3L4Flow",
            "Summary": "Enable queuing per L3/L4 flow hashing",
            "Key": "Net.NetShaperQueuePerL3L4Flow",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "NetShaperQueueSizeMax",
            "Summary": "Maximum shaper queue size",
            "Key": "Net.NetShaperQueueSizeMax",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 5000,
              "DefaultValue": 500
            }
          },
          {
            "Label": "NetShaperQueueSizeMin",
            "Summary": "Minimum shaper queue size",
            "Key": "Net.NetShaperQueueSizeMin",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 500,
              "DefaultValue": 10
            }
          },
          {
            "Label": "NetSplitRxMode",
            "Summary": "Whether to enable automatic splitRxMode",
            "Key": "Net.NetSplitRxMode",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "NetTraceEnable",
            "Summary": "Enable virtual network tracing",
            "Key": "Net.NetTraceEnable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "NetTuneHostMode",
            "Summary": "Tuning mode for networking",
            "Key": "Net.NetTuneHostMode",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "default",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "NetTuneInterval",
            "Summary": "Tuning interval in seconds",
            "Key": "Net.NetTuneInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 3600,
              "DefaultValue": 60
            }
          },
          {
            "Label": "NetTuneThreshold",
            "Summary": "Threshold for activating a tuned mode",
            "Key": "Net.NetTuneThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "1n 2n 50",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "Net Tx do not cluster size",
            "Summary": "Transmits smaller than this will not be subject to clustering.coalescing",
            "Key": "Net.NetTxDontClusterSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 8192,
              "DefaultValue": 0
            }
          },
          {
            "Label": "NetVMTxType",
            "Summary": "Worldlet/world for asynchronous Tx for net devices. 1 for one tx context per vNIC. 2 for one tx context per VM",
            "Key": "Net.NetVMTxType",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 3,
              "DefaultValue": 2
            }
          },
          {
            "Label": "NetpollSwLRO",
            "Summary": "Whether to perform SW LRO on pkts in netPoll",
            "Key": "Net.NetpollSwLRO",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "No local checksum",
            "Summary": "Do not checksum local tx.rx frames",
            "Key": "Net.NoLocalCSum",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Notify switch",
            "Summary": "Broadcasts an arp request on net handle enable",
            "Key": "Net.NotifySwitch",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "PTSwitchingTimeout",
            "Summary": "Timeout (in ms) when asking the VMX/guest to switch in/out of passthru",
            "Key": "Net.PTSwitchingTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 60000,
              "DefaultValue": 20000
            }
          },
          {
            "Label": "PVRDMAVmknic",
            "Summary": "Vmknic for PVRDMA",
            "Key": "Net.PVRDMAVmknic",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "PktagingDropPolicy",
            "Summary": "Dropping policy for vmxnet3 rx burst queue. 1 for PKTAGING_TAIL_DROP. 2 for PKTAGING_RED",
            "Key": "Net.PktagingDropPolicy",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Port disable time-out",
            "Summary": "Maximum time to wait for ports to complete I/O before disabling",
            "Key": "Net.PortDisableTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2147483647,
              "DefaultValue": 5000
            }
          },
          {
            "Label": "ReversePathFwdCheck",
            "Summary": "Block the multicast/broadcast packets that come back from physical switches in a teamed environment",
            "Key": "Net.ReversePathFwdCheck",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "ReversePathFwdCheckPromisc",
            "Summary": "Block duplicate packet in a teamed environment when the virtual switch is set to Promiscuous mode.",
            "Key": "Net.ReversePathFwdCheckPromisc",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "TcpipCopySmallTx",
            "Summary": "Copy and tx complete small packets for tcp tx",
            "Key": "Net.TcpipCopySmallTx",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TcpipDefLROEnabled",
            "Summary": "LRO enabled for TCP/IP",
            "Key": "Net.TcpipDefLROEnabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TcpipDefLROMaxLength",
            "Summary": "LRO default max length for TCP/IP",
            "Key": "Net.TcpipDefLROMaxLength",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 65535,
              "DefaultValue": 32768
            }
          },
          {
            "Label": "TcpipDgramRateLimiting",
            "Summary": "Enable Tx rate limiting for UDP sockets",
            "Key": "Net.TcpipDgramRateLimiting",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TcpipEnableABC",
            "Summary": "Enable Appropriate Byte Counting for TCP (RFC 3465)",
            "Key": "Net.TcpipEnableABC",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TcpipEnableFlowtable",
            "Summary": "Enable route caching through the use of flowtable",
            "Key": "Net.TcpipEnableFlowtable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TcpipEnableSendScaling",
            "Summary": "Enable Send-Side Scaling (requires RSS)",
            "Key": "Net.TcpipEnableSendScaling",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TcpipHWLRONoDelayAck",
            "Summary": "Delayed ack timer not armed for Hardware LRO (socket option needs to be set in addition).",
            "Key": "Net.TcpipHWLRONoDelayAck",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TcpipHeapMax",
            "Summary": "Max megabytes the tcpip module heap can grow to. (REQUIRES REBOOT!)",
            "Key": "Net.TcpipHeapMax",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 32,
              "Max": 3070,
              "DefaultValue": 1024
            }
          },
          {
            "Label": "TCP/IP heap size",
            "Summary": "Size of the TCP/IP module heap in megabytes (requires reboot)",
            "Key": "Net.TcpipHeapSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 32,
              "DefaultValue": 0
            }
          },
          {
            "Label": "TcpipIGMPDefaultVersion",
            "Summary": "Default version of IGMP, in the absence of a querier",
            "Key": "Net.TcpipIGMPDefaultVersion",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 3,
              "DefaultValue": 3
            }
          },
          {
            "Label": "TcpipIGMPRejoinInterval",
            "Summary": "Delay in seconds between automatic IGMP rejoins when no querier is present",
            "Key": "Net.TcpipIGMPRejoinInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 3600,
              "DefaultValue": 60
            }
          },
          {
            "Label": "TcpipLODispatchQueueMaxLen",
            "Summary": "Max # of pkts queued into the per-protocol queue used for dispatching loopback traffic (REQUIRES REBOOT!)",
            "Key": "Net.TcpipLODispatchQueueMaxLen",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 50,
              "Max": 512,
              "DefaultValue": 128
            }
          },
          {
            "Label": "TcpipLRONoDelayAck",
            "Summary": "Delayed ack timer not armed for LRO",
            "Key": "Net.TcpipLRONoDelayAck",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TcpipLogPackets",
            "Summary": "Turns on packet logging for a vmknic on debug builds, in a circular \u0026 in-memory buffer (Takes effect during vmknic creation time)",
            "Key": "Net.TcpipLogPackets",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "TcpipLogPacketsCount",
            "Summary": "Number of packets to log in the in-memory logger. 24570 packets take up about 1.2 MB, and Tx \u0026 Rx use separate buffers. (Takes effect during vmknic creation time)",
            "Key": "Net.TcpipLogPacketsCount",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1024,
              "Max": 49140,
              "DefaultValue": 24570
            }
          },
          {
            "Label": "Maximum number of TCP/IP stack instances",
            "Summary": "The maximum number of TCP/IP stack instances that can exist concurrently. If  you increase this number, you must also increase the TcpipHeapSize by about 2.5 MB per instance. Requires rebooting the host.",
            "Key": "Net.TcpipMaxNetstackInstances",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 48,
              "Max": 513,
              "DefaultValue": 48
            }
          },
          {
            "Label": "TcpipNoBcopyRx",
            "Summary": "Avoid bcopy in tcp rx",
            "Key": "Net.TcpipNoBcopyRx",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TcpipPendPktSocketFreeTimeout",
            "Summary": "Time Delay in seconds, for freeing UDP sockets that have pending packets for Tx completion",
            "Key": "Net.TcpipPendPktSocketFreeTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 300,
              "DefaultValue": 300
            }
          },
          {
            "Label": "TcpipRxDispatchQueueMaxLen",
            "Summary": "Max # of pkts queued into a tcpip vmknic by an execution context (applied when vmknic is created)",
            "Key": "Net.TcpipRxDispatchQueueMaxLen",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 20000,
              "DefaultValue": 2000
            }
          },
          {
            "Label": "TcpipRxDispatchQueues",
            "Summary": "Max # of dispatch queues used for RX. For low memory systems, this should be minimum value(REQUIRES REBOOT!)",
            "Key": "Net.TcpipRxDispatchQueues",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 8,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TcpipRxDispatchQuota",
            "Summary": "Max # of pkts dispatched into the tcpip stack by an execution context",
            "Key": "Net.TcpipRxDispatchQuota",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 1000,
              "DefaultValue": 200
            }
          },
          {
            "Label": "TcpipRxVmknicWorldletAffinityType",
            "Summary": "Use exact affinity for tcpip input worldlet (Takes hold at port creation time)",
            "Key": "Net.TcpipRxVmknicWorldletAffinityType",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "TcpipTxDispatchQuota",
            "Summary": "Max # of pkts dispatched from the tcpip stack by an execution context",
            "Key": "Net.TcpipTxDispatchQuota",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 1000,
              "DefaultValue": 100
            }
          },
          {
            "Label": "TcpipTxqBackoffTimeoutMs",
            "Summary": "Duration (in milli seconds) for which backoff is effective when the tx queue has reached the NET_TCPIP_TXQ_MAX_USAGE_THRESHOLD",
            "Key": "Net.TcpipTxqBackoffTimeoutMs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 20,
              "Max": 1000,
              "DefaultValue": 70
            }
          },
          {
            "Label": "TcpipTxqMaxUsageThreshold",
            "Summary": "Tx queue usage threshold in percent at which to start throttling",
            "Key": "Net.TcpipTxqMaxUsageThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 50,
              "Max": 100,
              "DefaultValue": 80
            }
          },
          {
            "Label": "TeamPolicyUpDelay",
            "Summary": "Delay (ms) before considering an `uplink up' event relevant",
            "Key": "Net.TeamPolicyUpDelay",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 600000,
              "DefaultValue": 100
            }
          },
          {
            "Label": "TrafficFilterIpAddress",
            "Summary": "Alternate DVFilter internal communication endpoint",
            "Key": "Net.TrafficFilterIpAddress",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "TsoDumpPkt",
            "Summary": "detailed dump of every \u003cn\u003e pkts",
            "Key": "Net.TsoDumpPkt",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2147483647,
              "DefaultValue": 0
            }
          },
          {
            "Label": "UplinkAbortDisconnectTimeout",
            "Summary": "Timeout (ms) waiting for pending calls to finish when disconnecting.",
            "Key": "Net.UplinkAbortDisconnectTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 6000000,
              "DefaultValue": 5000
            }
          },
          {
            "Label": "UplinkKillAsyncTimeout",
            "Summary": "Timeout (ms) waiting for async when world is killed.",
            "Key": "Net.UplinkKillAsyncTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 6000000,
              "DefaultValue": 10000
            }
          },
          {
            "Label": "UplinkTxQueuesDispEnabled",
            "Summary": "Enables dispatching of port traffic on multiple tx queues",
            "Key": "Net.UplinkTxQueuesDispEnabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "UseHwCsumForIPv6Csum",
            "Summary": "When non-zero, use pNIC HW_CSUM, if available, as IPv6 csum offload",
            "Key": "Net.UseHwCsumForIPv6Csum",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "UseHwIPv6Csum",
            "Summary": "When non-zero, use pNIC HW IPv6 csum offload if available",
            "Key": "Net.UseHwIPv6Csum",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "UseHwTSO",
            "Summary": "When non-zero, use pNIC HW TSO offload if available",
            "Key": "Net.UseHwTSO",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "UseHwTSO6",
            "Summary": "When non-zero, use pNIC HW IPv6 TSO offload if available",
            "Key": "Net.UseHwTSO6",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Populate legacy entries",
            "Summary": "Enable or disable populating legacy entries in /proc/vmware/net",
            "Key": "Net.UseLegacyProc",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Use proc",
            "Summary": "Populate /proc/vmware/net",
            "Key": "Net.UseProc",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VLANMTUCheckMode",
            "Summary": "Set the unicast/broadcast checking mode in VLAN MTU check",
            "Key": "Net.VLANMTUCheckMode",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VmklnxLROEnabled",
            "Summary": "LRO enabled in vmklinux",
            "Key": "Net.VmklnxLROEnabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VmklnxLROMaxAggr",
            "Summary": "LRO max aggr in vmklinux",
            "Key": "Net.VmklnxLROMaxAggr",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 24,
              "DefaultValue": 6
            }
          },
          {
            "Label": "VmknicDoLroSplit",
            "Summary": "whether for vmknic LRO avoids aggregating all pkts into a single (\u003e 2 ms) large pkt.",
            "Key": "Net.VmknicDoLroSplit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VmknicLroSplitBnd",
            "Summary": "when VmknicDoLroSplit is non-zero, while pktList is larger than this number, lroSplit is not done.",
            "Key": "Net.VmknicLroSplitBnd",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 2,
              "Max": 255,
              "DefaultValue": 12
            }
          },
          {
            "Label": "Vmxnet2HwLRO",
            "Summary": "Whether to perform HW LRO on pkts going to a LPD capable vmxnet2",
            "Key": "Net.Vmxnet2HwLRO",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Vmxnet2PinRxBuf",
            "Summary": "Pin RX buffers for vmxnet2 clients (windows guest only)",
            "Key": "Net.Vmxnet2PinRxBuf",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Vmxnet2SwLRO",
            "Summary": "Whether to perform SW LRO on pkts going to a LPD capable vmxnet2",
            "Key": "Net.Vmxnet2SwLRO",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Vmxnet3HwLRO",
            "Summary": "Whether to enable HW LRO on pkts going to a LPD capable vmxnet3",
            "Key": "Net.Vmxnet3HwLRO",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Vmxnet3PageInBound",
            "Summary": "max # pageIn requests to handle per helper call for vmxnet3.",
            "Key": "Net.Vmxnet3PageInBound",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 1024,
              "DefaultValue": 32
            }
          },
          {
            "Label": "Vmxnet3RSSHashCache",
            "Summary": "Enable RSS hash cache.",
            "Key": "Net.Vmxnet3RSSHashCache",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Vmxnet3RxPollBound",
            "Summary": "max # pkts to receive per timeout for vmxnet3.",
            "Key": "Net.Vmxnet3RxPollBound",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4096,
              "DefaultValue": 256
            }
          },
          {
            "Label": "Vmxnet3SwLRO",
            "Summary": "Whether to perform SW LRO on pkts going to a LPD capable vmxnet3",
            "Key": "Net.Vmxnet3SwLRO",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Vmxnet3WinIntrHints",
            "Summary": "whether intr hints are used for Windows vmxnet3.",
            "Key": "Net.Vmxnet3WinIntrHints",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Vmxnet3usePNICHash",
            "Summary": "Reuse pnic computed RSS hash.",
            "Key": "Net.Vmxnet3usePNICHash",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VmxnetBiDirNeedsTsoTx",
            "Summary": "Need to see Tso Tx to qualify for tsoSplit bidirectional traffic condition.",
            "Key": "Net.VmxnetBiDirNeedsTsoTx",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VmxnetBiDirNoTsoSplit",
            "Summary": "For bidirectional traffic, don't do tsoSplit.",
            "Key": "Net.VmxnetBiDirNoTsoSplit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VmxnetCopyTxRunLimit",
            "Summary": "non-Win vmxnet2/3 tx will have at most so many fully copied tx's in a row (65536 and larger means never).",
            "Key": "Net.VmxnetCopyTxRunLimit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 16
            }
          },
          {
            "Label": "VmxnetDoLroSplit",
            "Summary": "whether for vmxnet LRO avoids aggregating all pkts into a single (\u003e 2 mss) large pkt.",
            "Key": "Net.VmxnetDoLroSplit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VmxnetDoTsoSplit",
            "Summary": "whether to split TSO pkts before LPD; 1: halved; 3: VmxnetTsoSplitSize; 2: hybrid.",
            "Key": "Net.VmxnetDoTsoSplit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 3,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VmxnetLROBackoffPeriod",
            "Summary": "After adaptive LRO decided not to do LRO, how many intervals to wait before trying again.",
            "Key": "Net.VmxnetLROBackoffPeriod",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 65535,
              "DefaultValue": 8
            }
          },
          {
            "Label": "VmxnetLROMaxLength",
            "Summary": "LRO default max length for TCP/IP",
            "Key": "Net.VmxnetLROMaxLength",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 65535,
              "DefaultValue": 32000
            }
          },
          {
            "Label": "VmxnetLROThreshold",
            "Summary": "After this # packets, evaluate whether to continue SW LRO",
            "Key": "Net.VmxnetLROThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 65535,
              "DefaultValue": 4000
            }
          },
          {
            "Label": "VmxnetLROUseRatioDenom",
            "Summary": "If SW LRO reduce pkt count to be smaller than ratio, continue to do LRO. Denominator of ratio.",
            "Key": "Net.VmxnetLROUseRatioDenom",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 255,
              "DefaultValue": 3
            }
          },
          {
            "Label": "VmxnetLROUseRatioNumer",
            "Summary": "If SW LRO reduce pkt count to be smaller than ratio, continue to do LRO. Numerator of ratio.",
            "Key": "Net.VmxnetLROUseRatioNumer",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 255,
              "DefaultValue": 2
            }
          },
          {
            "Label": "VmxnetLroSplitBnd",
            "Summary": "when VmxnetDoLroSplit is non-zero, while pktList is larger than this number, lroSplit is not done.",
            "Key": "Net.VmxnetLroSplitBnd",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 2,
              "Max": 255,
              "DefaultValue": 64
            }
          },
          {
            "Label": "VmxnetPromDisableLro",
            "Summary": "Whether to disable SW LRO when vNIC goes into promiscuous mode.",
            "Key": "Net.VmxnetPromDisableLro",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VmxnetSwLROSL",
            "Summary": "Whether to use ShortLived for vmxnet SW LRO",
            "Key": "Net.VmxnetSwLROSL",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VmxnetTsoSplitBnd",
            "Summary": "when VmxnetDoTsoSplit is 1 or 2, use targetSize as the tsoSplit threshold if the overall pkt list already has this number of pkts.",
            "Key": "Net.VmxnetTsoSplitBnd",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 2,
              "Max": 255,
              "DefaultValue": 12
            }
          },
          {
            "Label": "VmxnetTsoSplitSize",
            "Summary": "split (byte) size; only used if DoTsoSplit \u003e= 2.",
            "Key": "Net.VmxnetTsoSplitSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1500,
              "Max": 65535,
              "DefaultValue": 17500
            }
          },
          {
            "Label": "VmxnetTxCopySize",
            "Summary": "vmxnet tx \u003c= than this will be fully copied with no need for completion.",
            "Key": "Net.VmxnetTxCopySize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 256
            }
          },
          {
            "Label": "VmxnetWinCopyTxRunLimit",
            "Summary": "Win vmxnet2/3 tx will have at most so many fully copied tx's in a row (65536 and larger means never).",
            "Key": "Net.VmxnetWinCopyTxRunLimit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 65535
            }
          },
          {
            "Label": "VmxnetWinUDPTxFullCopy",
            "Summary": "whether Windows vmxnet UDP tx is fullCopy.",
            "Key": "Net.VmxnetWinUDPTxFullCopy",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2,
              "DefaultValue": 1
            }
          },
          {
            "Label": "vNicNumDeferredReset",
            "Summary": "max # normalPkts per poll.",
            "Key": "Net.vNicNumDeferredReset",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4096,
              "DefaultValue": 12
            }
          },
          {
            "Label": "vNicTxPollBound",
            "Summary": "max # normalPkts per poll.",
            "Key": "Net.vNicTxPollBound",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4096,
              "DefaultValue": 192
            }
          },
          {
            "Label": "vmxnetThroughputWeight",
            "Summary": "How far to favor throughput in vmxnet behavior.",
            "Key": "Net.vmxnetThroughputWeight",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 255,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Network I/O control physical NIC opt out option",
            "Summary": "List of physical NICs to opt out of network I/O control",
            "Key": "Net.IOControlPnicOptOut",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": ""
            }
          },
          {
            "Label": "NmpPReservationCmdRetryTime",
            "Summary": "Time (in secs) to retry on transient errors for Persistent reservation commands for MSCS CAB configs",
            "Key": "Nmp.NmpPReservationCmdRetryTime",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 300,
              "DefaultValue": 1
            }
          },
          {
            "Label": "NmpSatpAluaCmdRetryTime",
            "Summary": "Time (in secs) to retry on transient errors",
            "Key": "Nmp.NmpSatpAluaCmdRetryTime",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 50,
              "DefaultValue": 10
            }
          },
          {
            "Label": "CoreCapRatioPct",
            "Summary": "The capacity of a core in percent. When exceeded, locality migration is rejected. Set to 0 to disable this check",
            "Key": "Numa.CoreCapRatioPct",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 90
            }
          },
          {
            "Label": "CostopSkewAdjust",
            "Summary": "way to handle costop skew, 0:do nothing, 1:interleave vcpus among clients, 2:allow vcpus on all nodes",
            "Key": "Numa.CostopSkewAdjust",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2,
              "DefaultValue": 1
            }
          },
          {
            "Label": "FollowCoresPerSocket",
            "Summary": "1: if the vNUMA topology should strickly follow core-per-socket config, 0: to remove the restriction",
            "Key": "Numa.FollowCoresPerSocket",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "LTermFairnessInterval",
            "Summary": "duration of long term fairness interval in terms of NUMA rebalance period, 0 indicates that long term fairness is disabled",
            "Key": "Numa.LTermFairnessInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000,
              "DefaultValue": 5
            }
          },
          {
            "Label": "LTermMigImbalThreshold",
            "Summary": "imbalance in long term owed, in percentage, required to trigger migration for long term fairness",
            "Key": "Numa.LTermMigImbalThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 10
            }
          },
          {
            "Label": "LargeInterleave",
            "Summary": "Always use large page interleaving; 0 to disable.",
            "Key": "Numa.LargeInterleave",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Dynamic latency probe period",
            "Summary": "Time (in ms) in between dynamic latency probing",
            "Key": "Numa.LatencyProbePeriod",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 100000,
              "DefaultValue": 500
            }
          },
          {
            "Label": "LocalityWeightActionAffinity",
            "Summary": "Benefit of improving action affinity by 1.",
            "Key": "Numa.LocalityWeightActionAffinity",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 200,
              "DefaultValue": 130
            }
          },
          {
            "Label": "LocalityWeightMem",
            "Summary": "Benefit of improving memory locality by 1 pct.",
            "Key": "Numa.LocalityWeightMem",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Migration imbalance threshold",
            "Summary": "Minimum percent load imbalance between nodes to trigger migration",
            "Key": "Numa.MigImbalanceThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 200,
              "DefaultValue": 10
            }
          },
          {
            "Label": "MigPreventLTermThresh",
            "Summary": "long term owed for a VM, in percentage, above which NUMA migration and swaps are disabled for that VM",
            "Key": "Numa.MigPreventLTermThresh",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Migration thrash threshold",
            "Summary": "Maximum post-migration load imbalance as a percentage of pre-migration imbalance to prevent thrashing",
            "Key": "Numa.MigThrashThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 50
            }
          },
          {
            "Label": "Minimum load improvement for single migration",
            "Summary": "Minimum percent load balance improvement to allow single migration.swap",
            "Key": "Numa.MigThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 2
            }
          },
          {
            "Label": "Monitor migration enable",
            "Summary": "Allow NUMASched monitor migrations. Set to 1 to enable; set to 0 to disable.",
            "Key": "Numa.MonMigEnable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Page migration enable",
            "Summary": "Permit NUMASched to manipulate page migration. Set to 1 to enable; set to 0 to disable.",
            "Key": "Numa.PageMigEnable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "PageMigLinearRun",
            "Summary": "page migration candidates for linear scan, 0 to disable",
            "Key": "Numa.PageMigLinearRun",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000,
              "DefaultValue": 95
            }
          },
          {
            "Label": "PageMigRandomRun",
            "Summary": "page migration candidates for random scan, 0 to disable",
            "Key": "Numa.PageMigRandomRun",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000,
              "DefaultValue": 5
            }
          },
          {
            "Label": "PageMigRateMax",
            "Summary": "max page migration rate",
            "Key": "Numa.PageMigRateMax",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 250000,
              "DefaultValue": 8000
            }
          },
          {
            "Label": "PreferHT",
            "Summary": "Prefer using HyperThreads on the same NUMA node over full cores on multiple nodes; 0 to disable.",
            "Key": "Numa.PreferHT",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Rebalancer core count",
            "Summary": "Minimum number of cores per node required to enable NUMA rebalancer",
            "Key": "Numa.RebalanceCoresNode",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 8,
              "DefaultValue": 2
            }
          },
          {
            "Label": "Rebalancer total host core count",
            "Summary": "Minimum number of total host cores required to enable NUMA rebalancer",
            "Key": "Numa.RebalanceCoresTotal",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 32,
              "DefaultValue": 4
            }
          },
          {
            "Label": "Enable rebalancer",
            "Summary": "Enable or disable NUMA scheduler rebalancer",
            "Key": "Numa.RebalanceEnable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Rebalance period",
            "Summary": "Frequency of NUMA node rebalancing in milliseconds",
            "Key": "Numa.RebalancePeriod",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 100,
              "Max": 60000,
              "DefaultValue": 2000
            }
          },
          {
            "Label": "SwapConsiderPeriod",
            "Summary": "time between reconsidering a client for swap, in units of NUMA rebalance period",
            "Key": "Numa.SwapConsiderPeriod",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000,
              "DefaultValue": 15
            }
          },
          {
            "Label": "SwapInterval",
            "Summary": "frequency of NUMA node swap rebalancing, in units of NUMA rebalance period",
            "Key": "Numa.SwapInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 10000,
              "DefaultValue": 3
            }
          },
          {
            "Label": "Enable virtual machine swaps for CPU",
            "Summary": "Enable or disable virtual machine swaps across nodes to balance CPU load",
            "Key": "Numa.SwapLoadEnable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Enable virtual machine swaps for memory",
            "Summary": "Enable or disable virtual machine swaps across nodes to improve memory locality",
            "Key": "Numa.SwapLocalityEnable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "SwapMigrateOnly",
            "Summary": "frequency of NUMA VM migration only considerations, in units of NUMA rebalance period, 0 to disable pure migrations",
            "Key": "Numa.SwapMigrateOnly",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 2
            }
          },
          {
            "Label": "CStateMaxLatency",
            "Summary": "In Custom policy, avoid a C-state whose latency is larger than this value (us)",
            "Key": "Power.CStateMaxLatency",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000000,
              "DefaultValue": 500
            }
          },
          {
            "Label": "CStatePredictionCoef",
            "Summary": "In Custom policy, predict non-timer wakeup with error probability p, where CStatePredictionCoef = -ln(1 - p) * (1 \u003c\u003c 20)",
            "Key": "Power.CStatePredictionCoef",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2000000,
              "DefaultValue": 110479
            }
          },
          {
            "Label": "CStateResidencyCoef",
            "Summary": "In Custom policy, avoid a C-state whose latency * CStateResidencyCoef \u003e predicted residence time",
            "Key": "Power.CStateResidencyCoef",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000000,
              "DefaultValue": 5
            }
          },
          {
            "Label": "ChargeMemoryPct",
            "Summary": "Percentage of idle power consumed by memory",
            "Key": "Power.ChargeMemoryPct",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 20
            }
          },
          {
            "Label": "MaxCpuLoad",
            "Summary": "In Custom policy, CPU utilization threshold below which CPU frequency can be dynamically adjusted",
            "Key": "Power.MaxCpuLoad",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 60
            }
          },
          {
            "Label": "MaxFreqPct",
            "Summary": "In Custom policy, do not use P-states faster than the given percentage of full CPU speed, rounded up to the next available P-state",
            "Key": "Power.MaxFreqPct",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 100
            }
          },
          {
            "Label": "MinFreqPct",
            "Summary": "In Custom policy, do not use P-states slower than the given percentage of full CPU speed",
            "Key": "Power.MinFreqPct",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "PerfBias",
            "Summary": "In Custom policy, Performance Energy Bias Hint (Intel-only), where 0-15 specify the exact value, while 16-18 choose an automatically determined value from a preset policy: 16=Low Power, 17=Balanced, 18=High Performance",
            "Key": "Power.PerfBias",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 18,
              "DefaultValue": 17
            }
          },
          {
            "Label": "PerfBiasEnable",
            "Summary": "Use Performance Energy Bias Hint",
            "Key": "Power.PerfBiasEnable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TimerHz",
            "Summary": "In Custom policy, dynamic power management timer sampling rate",
            "Key": "Power.TimerHz",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 1000,
              "DefaultValue": 100
            }
          },
          {
            "Label": "UseCStates",
            "Summary": "In Custom policy, use ACPI C-states when processor is idle",
            "Key": "Power.UseCStates",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "UsePStates",
            "Summary": "In Custom policy, use ACPI P-states to save power when processor is busy",
            "Key": "Power.UsePStates",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "RdmFilter.HbaIsShared",
            "Summary": "Allow local disks to be RDM candidates for Virtual Machines",
            "Key": "RdmFilter.HbaIsShared",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "ScratchConfig.ConfiguredScratchLocation",
            "Summary": "The directory configured to be used for scratch space. Changes will take effect on next reboot.",
            "Key": "ScratchConfig.ConfiguredScratchLocation",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": ""
            }
          },
          {
            "Label": "ScratchConfig.CurrentScratchLocation",
            "Summary": "The directory currently being used for scratch space.",
            "Key": "ScratchConfig.CurrentScratchLocation",
            "OptionType": {
              "ValueIsReadonly": true,
              "DefaultValue": "",
              "ValidCharacters": ""
            }
          },
          {
            "Label": "ChangeQErrSetting",
            "Summary": "Change the QErr value of devices to 0x0.",
            "Key": "Scsi.ChangeQErrSetting",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CompareLUNNumber",
            "Summary": "Consider LUN number when determining LUN identity.",
            "Key": "Scsi.CompareLUNNumber",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "EnableCmdSanityCheck",
            "Summary": "Enable Scsi command basic sanity checks. This option can crash the system if Scsi Command signature mismatches",
            "Key": "Scsi.EnableCmdSanityCheck",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "ExtendAPDCondition",
            "Summary": "Trigger APD condition when paths are in unavailable states",
            "Key": "Scsi.ExtendAPDCondition",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "FailVMIOonAPD",
            "Summary": "Fast fail virtual machine I/Os on APD Timeout.",
            "Key": "Scsi.FailVMIOonAPD",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "LogCmdErrors",
            "Summary": "Log SCSI Device command errors.",
            "Key": "Scsi.LogCmdErrors",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "LogCmdRCErrorsFreq",
            "Summary": "Number of consecutive RC errors to be seen before logging SCSI Device command.",
            "Key": "Scsi.LogCmdRCErrorsFreq",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 0
            }
          },
          {
            "Label": "LogMPCmdErrors",
            "Summary": "Log SCSI Multi-path plugin command errors.",
            "Key": "Scsi.LogMPCmdErrors",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "LogScsiAborts",
            "Summary": "Log SCSI abort errors and success.",
            "Key": "Scsi.LogScsiAborts",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "LunCleanupInterval",
            "Summary": "Timeout for purging stale LUNs. Any LUN which was seen more than the configured number of days ago, will be deleted by the daily cleanup operation",
            "Key": "Scsi.LunCleanupInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 100,
              "DefaultValue": 7
            }
          },
          {
            "Label": "MaxReserveBacktrace",
            "Summary": "Log a backtrace if caller exceeds SCSI_MAX_RESERVE_TIME or SCSI_MAX_RESERVE_TOTALTIME (debug only)",
            "Key": "Scsi.MaxReserveBacktrace",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Maximum reserve time",
            "Summary": "Maximum time (in milliseconds) a system-initiated reservation is normally held",
            "Key": "Scsi.MaxReserveTime",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 50,
              "Max": 500,
              "DefaultValue": 200
            }
          },
          {
            "Label": "MaxReserveTotalTime",
            "Summary": "Maximum time (in msecs) a system-initiated reservation normally takes from issue to release (debug only)",
            "Key": "Scsi.MaxReserveTotalTime",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 50,
              "Max": 500,
              "DefaultValue": 250
            }
          },
          {
            "Label": "Passthrough locking",
            "Summary": "SCSI passthrough locking",
            "Key": "Scsi.PassthroughLocking",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "ReserveBacktrace",
            "Summary": "Log a backtrace for all Reserve/Release pairs (debug only)",
            "Key": "Scsi.ReserveBacktrace",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "SCSIEnableDescToFixedConv",
            "Summary": "Enable or disable conversion of descriptor format sense to fixed for older plugins",
            "Key": "Scsi.SCSIEnableDescToFixedConv",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "SCSIEnableIOLatencyMsgs",
            "Summary": "Enable or disable storage latency-related error messages from PSA",
            "Key": "Scsi.SCSIEnableIOLatencyMsgs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "SCSIStrictSPCVersionChecksForPEs",
            "Summary": "Only LUNs with version \u003e= SCSI_ANSI_SCSI3_SPC4 can be PEs",
            "Key": "Scsi.SCSIStrictSPCVersionChecksForPEs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "SCSI time-out re-stop time",
            "Summary": "Delay (in milliseconds) after a stop due to time-out before the stop is re-issued",
            "Key": "Scsi.SCSITimeout_ReabortTime",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 99999999,
              "DefaultValue": 5000
            }
          },
          {
            "Label": "SCSI time-out scan time",
            "Summary": "Time (in milliseconds) to sleep between checking for timed-out asynchronous I/O",
            "Key": "Scsi.SCSITimeout_ScanTime",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 99999999,
              "DefaultValue": 1000
            }
          },
          {
            "Label": "SCSIioTraceBufSizeMB",
            "Summary": "Logchannel buffer size for per device IO tracing in MB",
            "Key": "Scsi.SCSIioTraceBufSizeMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 2,
              "DefaultValue": 1
            }
          },
          {
            "Label": "ScanOnDriverLoad",
            "Summary": "Scan new SCSI buses on device driver load",
            "Key": "Scsi.ScanOnDriverLoad",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "ScanSync",
            "Summary": "Force LU scanning operations to be synchronous if set.",
            "Key": "Scsi.ScanSync",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "ScsiAllowDeviceSpinup",
            "Summary": "Allow device spin up, if device is in spun down state.",
            "Key": "Scsi.ScsiAllowDeviceSpinup",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "ScsiRestartStalledQueueLatency",
            "Summary": "Delay in ms before restarting a stalled queue ",
            "Key": "Scsi.ScsiRestartStalledQueueLatency",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 60000,
              "DefaultValue": 500
            }
          },
          {
            "Label": "ScsiTMHardTimeout",
            "Summary": "Timeout in milliseconds before signalling upper layers of wedged I/O (0 = Signalling disabled).",
            "Key": "Scsi.ScsiTMHardTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 600000,
              "DefaultValue": 120000
            }
          },
          {
            "Label": "ScsiVVolPESNRO",
            "Summary": "Default schedNumReqOutstanding value for a PE LUN.",
            "Key": "Scsi.ScsiVVolPESNRO",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 32,
              "Max": 4096,
              "DefaultValue": 128
            }
          },
          {
            "Label": "TimeoutTMThreadExpires",
            "Summary": "Life in seconds of timeout task mgmt handler thread",
            "Key": "Scsi.TimeoutTMThreadExpires",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 86400,
              "DefaultValue": 1800
            }
          },
          {
            "Label": "TimeoutTMThreadLatency",
            "Summary": "Delay in ms before waking up new task mgmt thread ",
            "Key": "Scsi.TimeoutTMThreadLatency",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 600000,
              "DefaultValue": 2000
            }
          },
          {
            "Label": "TimeoutTMThreadMax",
            "Summary": "Max number of timeout task-mgmt handler threads",
            "Key": "Scsi.TimeoutTMThreadMax",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 16,
              "DefaultValue": 16
            }
          },
          {
            "Label": "TimeoutTMThreadMin",
            "Summary": "Min number of timeout task-mgmt handler threads",
            "Key": "Scsi.TimeoutTMThreadMin",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 16,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TimeoutTMThreadRetry",
            "Summary": "Delay in milliseconds before retrying taskmgmt which failed or for which the I/O did not complete",
            "Key": "Scsi.TimeoutTMThreadRetry",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1000,
              "Max": 10000,
              "DefaultValue": 2000
            }
          },
          {
            "Label": "TransFailLogPct",
            "Summary": "Percent of Transient failures seen on Scsi Device that will be logged.",
            "Key": "Scsi.TransFailLogPct",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 20
            }
          },
          {
            "Label": "UseAdaptiveRetries",
            "Summary": "Use adaptive retries for transient errors.",
            "Key": "Scsi.UseAdaptiveRetries",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Account lock failures",
            "Summary": "Maximum allowed failed login attempts before locking out a user's account. Zero disables account locking.",
            "Key": "Security.AccountLockFailures",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 5
            }
          },
          {
            "Label": "Account unlock time",
            "Summary": "Duration in seconds to lock out a user's account after exceeding the maximum allowed failed login attempts.",
            "Key": "Security.AccountUnlockTime",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 3600,
              "DefaultValue": 900
            }
          },
          {
            "Label": "Password history",
            "Summary": "Number of passwords to remember for each user. Zero disables password history.",
            "Key": "Security.PasswordHistory",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Password maximum age",
            "Summary": "Maximum number of days between password changes. Affects all existing and new users.",
            "Key": "Security.PasswordMaxDays",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 99999,
              "DefaultValue": 99999
            }
          },
          {
            "Label": "Password quality control",
            "Summary": "Raw options for pam_passwdqc PAM module. This value is used as is in PAM's configuration file, so use with caution.",
            "Key": "Security.PasswordQualityControl",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "retry=3 min=disabled,disabled,disabled,7,7",
              "ValidCharacters": ""
            }
          },
          {
            "Label": "MaxConnPerIP",
            "Summary": "Max number of TCP/IP connection per IP",
            "Key": "SunRPC.MaxConnPerIP",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 4
            }
          },
          {
            "Label": "SendLowat",
            "Summary": "Send buffer lowat (%)",
            "Key": "SunRPC.SendLowat",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 100,
              "DefaultValue": 25
            }
          },
          {
            "Label": "WorldAffinity",
            "Summary": "TX Affinity: 0 - Disabled, 1 - Issuing world, 2 - Exact RX world, 3 - Loose RX world",
            "Key": "SunRPC.WorldAffinity",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2,
              "DefaultValue": 2
            }
          },
          {
            "Label": "SvMotionAvgDisksPerVM",
            "Summary": "Initial Storage vMotion Heap Size is proportional to this setting",
            "Key": "SvMotion.SvMotionAvgDisksPerVM",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 4,
              "Max": 1024,
              "DefaultValue": 8
            }
          },
          {
            "Label": "Syslog.global.defaultRotate",
            "Summary": "Default number of rotated logs to keep. Reset to default on zero.",
            "Key": "Syslog.global.defaultRotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Syslog.global.defaultSize",
            "Summary": "Default size of logs before rotation, in KiB. Reset to default on zero.",
            "Key": "Syslog.global.defaultSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10240,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Syslog.global.logCheckSSLCerts",
            "Summary": "Enforce checking of SSL certificates when logging to a remote host.",
            "Key": "Syslog.global.logCheckSSLCerts",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "Syslog.global.logDir",
            "Summary": "Datastore path of directory to output logs to. Reset to default on null. Example: [datastoreName]/logdir",
            "Key": "Syslog.global.logDir",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": ""
            }
          },
          {
            "Label": "Syslog.global.logDirUnique",
            "Summary": "Place logs in a unique subdirectory of logdir, based on hostname.",
            "Key": "Syslog.global.logDirUnique",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "Syslog.global.logHost",
            "Summary": "The remote host to output logs to. Reset to default on null. Multiple hosts are supported and must be separated with comma (,). Example: udp://hostName1:514, hostName2, ssl://hostName3:1514",
            "Key": "Syslog.global.logHost",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": ""
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.Xorg.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.Xorg.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.auth.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.auth.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.clomd.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.clomd.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.clusterAgent.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.clusterAgent.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.cmmdsTimeMachine.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.cmmdsTimeMachine.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.cmmdsTimeMachineDump.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.cmmdsTimeMachineDump.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.ddecomd.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.ddecomd.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.dhclient.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.dhclient.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.epd.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.epd.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.esxupdate.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.esxupdate.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.fdm.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.fdm.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.hbragent.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.hbragent.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.hostd-probe.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.hostd-probe.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.hostd.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.hostd.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.hostdCgiServer.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.hostdCgiServer.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.hostprofiletrace.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.hostprofiletrace.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.iofiltervpd.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.iofiltervpd.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.lacp.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.lacp.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.loadESX.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.loadESX.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.nfcd.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.nfcd.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.osfsd.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.osfsd.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.rabbitmqproxy.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.rabbitmqproxy.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.rhttpproxy.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.rhttpproxy.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.sdrsInjector.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.sdrsInjector.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.shell.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.shell.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.sockrelay.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.sockrelay.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.storageRM.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.storageRM.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.swapobjd.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.swapobjd.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.syslog.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.syslog.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.upitd.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.upitd.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.usb.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.usb.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.vdfs-proxy.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.vdfs-proxy.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.vdfs-server.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.vdfs-server.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.vitd.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.vitd.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.vmauthd.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.vmauthd.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.vmkdevmgr.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.vmkdevmgr.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.vmkernel.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.vmkernel.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.vmkeventd.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.vmkeventd.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.vmksummary.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.vmksummary.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.vmkwarning.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.vmkwarning.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.vobd.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.vobd.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.vprobe.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.vprobe.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.vpxa.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.vpxa.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.vsandpd.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.vsandpd.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.vsanmgmt.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.vsanmgmt.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.vsansystem.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.vsansystem.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.vsantraceUrgent.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.vsantraceUrgent.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger rotations",
            "Summary": "Number of rotated logs to keep for this logger. Reset to default on zero.",
            "Key": "Syslog.loggers.vvold.rotate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Logger size",
            "Summary": "Set size of logs before rotation for this logger, in KiB. Reset to default on zero.",
            "Key": "Syslog.loggers.vvold.size",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 102400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "USB.quirks",
            "Summary": "Additional USB device quirks to be added to the vmkusb native driver default quirks.  Device quirks are software workarounds for hardware errata.  String format is \u003cvid\u003e:\u003cpid\u003e:\u003clow rev\u003e:\u003chigh rev\u003e:\u003cquirk name\u003e, with colon delimted fields.  vid and pid are the \\\"usb id\\\" numbers for the USB device vendor and product, respectively (and e.g., 0x0e0f for vendor VMware), and low and high rev are the revisions (inclusive) between which to apply the quirk (e.g., 0 and 0xffff).  Quirk name is a string constant starting with UQ_.  Contact VMware support for a full list of valid quirk names.",
            "Key": "USB.quirks",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "PTEDisableNX",
            "Summary": "Disables the use of NX bit in user pagetable entries",
            "Key": "User.PTEDisableNX",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Read directory retries",
            "Summary": "Maximum number of retries when reading entries from directories",
            "Key": "User.ReaddirRetries",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 20,
              "DefaultValue": 10
            }
          },
          {
            "Label": "ActiveDirectoryPreferredDomainControllers",
            "Summary": "Preferred Domain Controllers for Active Directory",
            "Key": "UserVars.ActiveDirectoryPreferredDomainControllers",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": "*"
            }
          },
          {
            "Label": "ActiveDirectoryVerifyCAMCertificate",
            "Summary": "Enable or disable verification of SSL certificate for vSphere Authentication Proxy server",
            "Key": "UserVars.ActiveDirectoryVerifyCAMCertificate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Direct Console User Interface (DCUI) timeout",
            "Summary": "An idle time in seconds before DCUI is automatically logged out. Setting 0 disables the timeout.",
            "Key": "UserVars.DcuiTimeOut",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 86400,
              "DefaultValue": 600
            }
          },
          {
            "Label": "ESXiShellInteractiveTimeOut",
            "Summary": "Idle time before an interactive shell is automatically logged out (in seconds, 0 disables).  Takes effect only for newly logged in sessions.",
            "Key": "UserVars.ESXiShellInteractiveTimeOut",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 86400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "ESXiShellTimeOut",
            "Summary": "Time before automatically disabling local and remote shell access (in seconds, 0 disables).  Takes effect after the services are restarted.",
            "Key": "UserVars.ESXiShellTimeOut",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 86400,
              "DefaultValue": 0
            }
          },
          {
            "Label": "ESXiVPsAllowedCiphers",
            "Summary": "ESXi VPs allowed ciphers. List of allowed ciphers to be used by the VPs. Must be in the form accepted by the SSL_CTX_set_cipher_list API.",
            "Key": "UserVars.ESXiVPsAllowedCiphers",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "!aNULL:kECDH+AESGCM:ECDH+AESGCM:RSA+AESGCM:kECDH+AES:ECDH+AES:RSA+AES",
              "ValidCharacters": "*"
            }
          },
          {
            "Label": "ESXiVPsDisabledProtocols",
            "Summary": "ESXi VPs disabled protocols. Choices are sslv3, tlsv1, tlsv1.1, tlsv1.2. By default sslv3 is disabled. If no protocol is specified, all protocols are enabled.",
            "Key": "UserVars.ESXiVPsDisabledProtocols",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "sslv3,tlsv1,tlsv1.1",
              "ValidCharacters": "*"
            }
          },
          {
            "Label": "EsximageNetRateLimit",
            "Summary": "Set the maximum rate, in bytes/sec, for downloading VIBs (0=no limit)",
            "Key": "UserVars.EsximageNetRateLimit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2147483647,
              "DefaultValue": 0
            }
          },
          {
            "Label": "EsximageNetRetries",
            "Summary": "Set the number of times to retry in case of failure while downloading VIBs",
            "Key": "UserVars.EsximageNetRetries",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1024,
              "DefaultValue": 10
            }
          },
          {
            "Label": "EsximageNetTimeout",
            "Summary": "Set the timeout in seconds for downloading VIBs (0=no timeout)",
            "Key": "UserVars.EsximageNetTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1024,
              "DefaultValue": 60
            }
          },
          {
            "Label": "Hardware Health Ignored Sensors",
            "Summary": "List of comma-seperated sensor ID's to ignore for alarm generation.",
            "Key": "UserVars.HardwareHealthIgnoredSensors",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "",
              "ValidCharacters": "*"
            }
          },
          {
            "Label": "Hardware Health Sync Interval",
            "Summary": "Interval in minutes for periodic synchronization of hardware sensor state with VC alarm state. Setting 0 disables synchronization.",
            "Key": "UserVars.HardwareHealthSyncTime",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 43200,
              "DefaultValue": 360
            }
          },
          {
            "Label": "Host Client CEIP opt-in state",
            "Summary": "Whether or not to opt-in for the Customer Experience Improvement Program in Host Client, 0 for ask, 1 for yes, 2 for no",
            "Key": "UserVars.HostClientCEIPOptIn",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 2,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Default console type when clicking on screen shot",
            "Summary": "The default console to use when clicking on a VM screen shot in Host Client, webmks or vmrc",
            "Key": "UserVars.HostClientDefaultConsole",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "webmks",
              "ValidCharacters": "*"
            }
          },
          {
            "Label": "Show Host Client MOTD notification",
            "Summary": "Whether or not to enable MOTD notification on login for Host Client",
            "Key": "UserVars.HostClientEnableMOTDNotification",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Enable Host Client Visual Effects",
            "Summary": "Whether or not to enable visual effects for Host Client",
            "Key": "UserVars.HostClientEnableVisualEffects",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Host Client session timeout",
            "Summary": "Default timeout for Host Client sessions in seconds",
            "Key": "UserVars.HostClientSessionTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 7200,
              "DefaultValue": 900
            }
          },
          {
            "Label": "Show recent objects in Host Client",
            "Summary": "Whether or not to show only recent objects in Host Client",
            "Key": "UserVars.HostClientShowOnlyRecentObjects",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Welcome message displayed on login in Host Client",
            "Summary": "The welcome message that is displayed when a user logs into the Host Client",
            "Key": "UserVars.HostClientWelcomeMessage",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "Welcome to {{hostname}}",
              "ValidCharacters": "*"
            }
          },
          {
            "Label": "HostdStatsstoreRamdiskSize",
            "Summary": "Explicitly specify size for the ramdisk hostd uses to store stats (in megabytes, 0 default). Takes effect after hostd service is restarted.",
            "Key": "UserVars.HostdStatsstoreRamdiskSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1024,
              "DefaultValue": 0
            }
          },
          {
            "Label": "ProductLockerLocation",
            "Summary": "Path to VMware Tools and vSphere Client repository",
            "Key": "UserVars.ProductLockerLocation",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "/locker/packages/vmtoolsRepo/",
              "ValidCharacters": "*"
            }
          },
          {
            "Label": "SuppressCoredumpWarning",
            "Summary": "Do not show warning for disabled or unconfigured coredump target",
            "Key": "UserVars.SuppressCoredumpWarning",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "SuppressHyperthreadWarning",
            "Summary": "Do not show warning for potential security vulnerability due to hyperthreading",
            "Key": "UserVars.SuppressHyperthreadWarning",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "SuppressShellWarning",
            "Summary": "Do not show warning for enabled local and remote shell access",
            "Key": "UserVars.SuppressShellWarning",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "ToolsRamdisk",
            "Summary": "Use VMware Tools repository from /tools ramdisk.",
            "Key": "UserVars.ToolsRamdisk",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "CacheStatsEnable",
            "Summary": "Vitual Flash Read Cache statistics enable ?",
            "Key": "VFLASH.CacheStatsEnable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "CacheStatsFromVFC",
            "Summary": "Use cache statistics from virtual Flash Read Cache module ?",
            "Key": "VFLASH.CacheStatsFromVFC",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "MaxCacheFileSizeMB",
            "Summary": "Maximum file size (in MB) of virtual Flash Read supported",
            "Key": "VFLASH.MaxCacheFileSizeMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 4,
              "Max": 409600,
              "DefaultValue": 409600
            }
          },
          {
            "Label": "MaxDiskFileSizeGB",
            "Summary": "Maximum supported disk size (in GB) with virtual Flash Read Cache configuration",
            "Key": "VFLASH.MaxDiskFileSizeGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 16384,
              "DefaultValue": 16384
            }
          },
          {
            "Label": "MaxHeapSizeMB",
            "Summary": "Maximum size (in MB) to which the virtual flash heap is allowed to grow",
            "Key": "VFLASH.MaxHeapSizeMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 16,
              "Max": 128,
              "DefaultValue": 32
            }
          },
          {
            "Label": "MaxResourceGBForVmCache",
            "Summary": "Maximum supported virtual flash resource (in GB) to be allocated for VM caches",
            "Key": "VFLASH.MaxResourceGBForVmCache",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 500,
              "Max": 65536,
              "DefaultValue": 2048
            }
          },
          {
            "Label": "ResourceUsageThreshold",
            "Summary": "Threshold (in percentage) of virtual flash resource usage",
            "Key": "VFLASH.ResourceUsageThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 99,
              "DefaultValue": 80
            }
          },
          {
            "Label": "VMFS.UnresolvedVolumeLiveCheck",
            "Summary": "Enable/disable liveliness check during unresolved volume query.",
            "Key": "VMFS.UnresolvedVolumeLiveCheck",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "EnableBlockDelete",
            "Summary": "Enable VMFS block delete when UNMAP is issued from guest OS",
            "Key": "VMFS3.EnableBlockDelete",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "FailVolumeOpenIfAPD",
            "Summary": "Fail VMFS volume open operation if the underlying device is deemed to be under an all-paths-down condition",
            "Key": "VMFS3.FailVolumeOpenIfAPD",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Allow multi-writer Generation Based Locking",
            "Summary": "Enable support for more than 8 hosts using multi-writer mode locks on virtual-disks",
            "Key": "VMFS3.GBLAllowMW",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "HardwareAcceleratedLocking",
            "Summary": "Enable hardware accelerated VMFS locking (requires compliant hardware). Please see http://kb.vmware.com/kb/2094604 before disabling this option",
            "Key": "VMFS3.HardwareAcceleratedLocking",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "LFBCSlabSizeMax",
            "Summary": "Maximum size (in MB) to which the VMFS affinity manager cluster cache is allowed to grow.",
            "Key": "VMFS3.LFBCSlabSizeMaxMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 32,
              "DefaultValue": 8
            }
          },
          {
            "Label": "MaxAddressableSpaceTB",
            "Summary": "Maximum size of all open files that VMFS cache will support before eviction mechanisms kick in",
            "Key": "VMFS3.MaxAddressableSpaceTB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 32,
              "Max": 128,
              "DefaultValue": 32
            }
          },
          {
            "Label": "MaxHeapSizeMB",
            "Summary": "Maximum size (in MB) to which the VMFS heap is allowed to grow",
            "Key": "VMFS3.MaxHeapSizeMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 16,
              "Max": 256,
              "DefaultValue": 256
            }
          },
          {
            "Label": "MaxextendedTxnsUsingfs3Heap",
            "Summary": "Maximum number of extended transactions for which log space can be allocated from VMFS3 heap when the extendedTxnRegion is full",
            "Key": "VMFS3.MaxextendedTxnsUsingfs3Heap",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 20,
              "Max": 40,
              "DefaultValue": 20
            }
          },
          {
            "Label": "MinAddressableSpaceTB",
            "Summary": "Minimum size of all open files that VMFS cache will support (guaranteed)",
            "Key": "VMFS3.MinAddressableSpaceTB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10,
              "DefaultValue": 0
            }
          },
          {
            "Label": "OpenWithoutJournal",
            "Summary": "Open file system when out of space for journal allocation, allowing reads and no meta-data updates",
            "Key": "VMFS3.OpenWithoutJournal",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "PBCapMissRatioIntervalSec",
            "Summary": "Frequency (in seconds) that the Capacity Miss Ratio is computed for the VMFS Pointer Block cache.",
            "Key": "VMFS3.PBCapMissRatioIntervalSec",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 1800,
              "DefaultValue": 60
            }
          },
          {
            "Label": "StAtExclLockEnd",
            "Summary": " Generate Back Trace in FS3_EndIOExclusive",
            "Key": "VMFS3.StAtExclLockEnd",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "Use ATS For HB On VMFS5",
            "Summary": "Use ATS for Heartbeat on ATS supported VMFS5 volumes",
            "Key": "VMFS3.UseATSForHBOnVMFS5",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VMkernel.Boot.autoCreateDumpFile",
            "Summary": "If enabled and if no suitable dump partition or dump file exists, create a dump file.",
            "Key": "VMkernel.Boot.autoCreateDumpFile",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.autoPartition",
            "Summary": "Enable/Disable auto-partitioning of empty local disks.",
            "Key": "VMkernel.Boot.autoPartition",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.autoPartitionCreateUSBCoreDumpPartition",
            "Summary": "Enable/Disable auto-partitioning of core dump partition for USB boot devices. Requires that autoPartition is set to TRUE as well.",
            "Key": "VMkernel.Boot.autoPartitionCreateUSBCoreDumpPartition",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.autoPartitionDiskDumpPartitionSize",
            "Summary": "Disk dump partition size in MB that gets configured during the auto-partition process.",
            "Key": "VMkernel.Boot.autoPartitionDiskDumpPartitionSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 2560
            }
          },
          {
            "Label": "VMkernel.Boot.bootDeviceRescanTimeout",
            "Summary": "Boot device rescan timeout (in minutes).",
            "Key": "VMkernel.Boot.bootDeviceRescanTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 255,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VMkernel.Boot.busSpeedMayVary",
            "Summary": "Allow different APIC timer speeds on different CPUs.",
            "Key": "VMkernel.Boot.busSpeedMayVary",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.cacheFlushImmOnAllHalt",
            "Summary": "Flush caches immediately if all cores sharing LLC halt (AMD only)",
            "Key": "VMkernel.Boot.cacheFlushImmOnAllHalt",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.checkCPUIDLimit",
            "Summary": "Refuse to run on CPUID limited cpus.",
            "Key": "VMkernel.Boot.checkCPUIDLimit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.checkPages",
            "Summary": "Check that free and free-lpage-pool pages are not corrupted.",
            "Key": "VMkernel.Boot.checkPages",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.com1_baud",
            "Summary": "Baud rate for COM1.",
            "Key": "VMkernel.Boot.com1_baud",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 115200
            }
          },
          {
            "Label": "VMkernel.Boot.com2_baud",
            "Summary": "Baud rate for COM2.",
            "Key": "VMkernel.Boot.com2_baud",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 115200
            }
          },
          {
            "Label": "VMkernel.Boot.coresPerPkg",
            "Summary": "Number of cores per package that vmkernel should use",
            "Key": "VMkernel.Boot.coresPerPkg",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 255,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VMkernel.Boot.debugBreak",
            "Summary": "Break into debugger during vmkernel initialization.",
            "Key": "VMkernel.Boot.debugBreak",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.debugLogToSerial",
            "Summary": "0=Serial debug logging off, 1=Serial debug logging on, 2=Defer to config option DebugLogToSerial.",
            "Key": "VMkernel.Boot.debugLogToSerial",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 2
            }
          },
          {
            "Label": "VMkernel.Boot.disableACSCheck",
            "Summary": "Bypass ACS capability checks on all PCIE devices",
            "Key": "VMkernel.Boot.disableACSCheck",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.disableCFOH",
            "Summary": "Disable Cache Flush on Halt",
            "Key": "VMkernel.Boot.disableCFOH",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.disableHwrng",
            "Summary": "Disable hardware random number generator (RDRAND, RDSEED)",
            "Key": "VMkernel.Boot.disableHwrng",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.diskDumpSlotSize",
            "Summary": "Disk dump slot size in MB. 0 = automatically sized, otherwise requsted size \u003e= 100 MB.",
            "Key": "VMkernel.Boot.diskDumpSlotSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VMkernel.Boot.dmaEngineExposeIdentityMapping",
            "Summary": "Whether to expose whether DMA engines do identity mapping.",
            "Key": "VMkernel.Boot.dmaEngineExposeIdentityMapping",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.dmaMapperPolicy",
            "Summary": "DMA mapping policy to use.",
            "Key": "VMkernel.Boot.dmaMapperPolicy",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "disabled",
              "ValidCharacters": ""
            }
          },
          {
            "Label": "VMkernel.Boot.enableACSCheckForRP",
            "Summary": "Enable ACS capability checks for Root Port",
            "Key": "VMkernel.Boot.enableACSCheckForRP",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.execInstalledOnly",
            "Summary": "Execute only those files that have been installed via a vib package and have not been modified.",
            "Key": "VMkernel.Boot.execInstalledOnly",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMKernel.Boot.fakePMemPct",
            "Summary": "Amount of fake persistent memory (in pct of all volatile memory)",
            "Key": "VMkernel.Boot.fakePMemPct",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 255,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VMkernel.Boot.fsCheck",
            "Summary": "Run filesystem checks on system partitions.",
            "Key": "VMkernel.Boot.fsCheck",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.gdbPort",
            "Summary": "gdb port; com1 or com2",
            "Key": "VMkernel.Boot.gdbPort",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "default",
              "ValidCharacters": ""
            }
          },
          {
            "Label": "VMkernel.Boot.heapCheckTimerInterval",
            "Summary": "Interval in seconds between heap timer checks",
            "Key": "VMkernel.Boot.heapCheckTimerInterval",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 10
            }
          },
          {
            "Label": "VMkernel.Boot.heapFreeOwnerCheck",
            "Summary": "Check heap ownership on free operations",
            "Key": "VMkernel.Boot.heapFreeOwnerCheck",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.heapFreePoisonByte",
            "Summary": "Byte pattern used to poison freed memory",
            "Key": "VMkernel.Boot.heapFreePoisonByte",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 255,
              "DefaultValue": 255
            }
          },
          {
            "Label": "VMkernel.Boot.heapMetaPoisonByte",
            "Summary": "Byte pattern used to poison red zones for allocations",
            "Key": "VMkernel.Boot.heapMetaPoisonByte",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 255,
              "DefaultValue": 90
            }
          },
          {
            "Label": "VMkernel.Boot.heapMetadataProtect",
            "Summary": "Use poisoned red zones to protect against under/overruns",
            "Key": "VMkernel.Boot.heapMetadataProtect",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.heapMgrTotalVASpaceGB2MB",
            "Summary": "VA space (in GB) that is allocated by heap manager for 2m page heaps",
            "Key": "VMkernel.Boot.heapMgr2mbTotalVASpaceGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 5
            }
          },
          {
            "Label": "VMkernel.Boot.heapMgrTotalVASpaceGB",
            "Summary": "VA space (in GB) that is allocated by heap manager for ordinary heaps",
            "Key": "VMkernel.Boot.heapMgrTotalVASpaceGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 2048
            }
          },
          {
            "Label": "VMkernel.Boot.heapPoisonFreeMem",
            "Summary": "Poison free memory to catch use after free bugs",
            "Key": "VMkernel.Boot.heapPoisonFreeMem",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.heapPoisonTimerChecks",
            "Summary": "Check heap poisoned areas for corruption on regular intervals",
            "Key": "VMkernel.Boot.heapPoisonTimerChecks",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.hyperthreading",
            "Summary": "Enable hyperthreading if available.",
            "Key": "VMkernel.Boot.hyperthreading",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.hyperthreadingMitigation",
            "Summary": "Restrict the simultaneous use of logical processors from the same hyperthreaded core as necessary to mitigate a security vulnerability.",
            "Key": "VMkernel.Boot.hyperthreadingMitigation",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.hyperthreadingMitigationIntraVM",
            "Summary": "Restrict the simultaneous use of logical processors from the same hyperthreaded core as necessary to mitigate a security vulnerability within a single VM.",
            "Key": "VMkernel.Boot.hyperthreadingMitigationIntraVM",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.ignoreMsrFaults",
            "Summary": "Ignore general protection faults as a result of rdmsr and wrmsr instructions",
            "Key": "VMkernel.Boot.ignoreMsrFaults",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.intrBalancingEnabled",
            "Summary": "Indicates if interrupt balancing is enabled.",
            "Key": "VMkernel.Boot.intrBalancingEnabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.ioAbilityChecks",
            "Summary": "Enforce checking of whether regions can be DMA mapped.",
            "Key": "VMkernel.Boot.ioAbilityChecks",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.iovDisableIR",
            "Summary": "Disable Interrupt Remapping in the IOMMU.",
            "Key": "VMkernel.Boot.iovDisableIR",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.ipmiEnabled",
            "Summary": "Enable IPMI",
            "Key": "VMkernel.Boot.ipmiEnabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.isPerFileSchedModelActive",
            "Summary": "Enable per file scheduling model on this host",
            "Key": "VMkernel.Boot.isPerFileSchedModelActive",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.leaveWakeGPEsDisabled",
            "Summary": "Disallow a wake GPE from also being a runtime GPE",
            "Key": "VMkernel.Boot.leaveWakeGPEsDisabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.logPort",
            "Summary": "Serial port to enable for logging; com1 or com2",
            "Key": "VMkernel.Boot.logPort",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "default",
              "ValidCharacters": ""
            }
          },
          {
            "Label": "VMkernel.Boot.maxLogEntries",
            "Summary": "Size of the kernel log buffer in 256-byte lines. 0=Use default. Min is 1024. Max is 32768.",
            "Key": "VMkernel.Boot.maxLogEntries",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VMkernel.Boot.maxPCPUS",
            "Summary": "Number of PCPUs vmkernel should use.",
            "Key": "VMkernel.Boot.maxPCPUS",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 1024
            }
          },
          {
            "Label": "VMkernel.Boot.maxPCPUsNUMAInterleaving",
            "Summary": "Enable NUMA-node interleaving of enabled PCPUs.",
            "Key": "VMkernel.Boot.maxPCPUsNUMAInterleaving",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.maxVMs",
            "Summary": "Max number of VMs VMKernel should support. 0 == determine at runtime",
            "Key": "VMkernel.Boot.maxVMs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VMkernel.Boot.memCheckEveryWord",
            "Summary": "Check every single word when checking mem.",
            "Key": "VMkernel.Boot.memCheckEveryWord",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.memLowReservedMB",
            "Summary": "Amount of low memory (\u003c 4 GB) which gets reserved. 0 == determine at runtime",
            "Key": "VMkernel.Boot.memLowReservedMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VMkernel.Boot.memmapMaxEarlyPoisonMemMB",
            "Summary": "Memory that should be poisoned during early initialization.",
            "Key": "VMkernel.Boot.memmapMaxEarlyPoisonMemMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 65536
            }
          },
          {
            "Label": "VMkernel.Boot.memmapMaxPhysicalMemMB",
            "Summary": "Maximum physical memory (in MB) addressable by kernel.",
            "Key": "VMkernel.Boot.memmapMaxPhysicalMemMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": -9223372036854775808,
              "Max": 9223372036854775807,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VMkernel.Boot.memmapMaxRAMMB",
            "Summary": "Maximum conventional memory (RAM) supported on the system. Additional RAM above this limit will not be used by the system.",
            "Key": "VMkernel.Boot.memmapMaxRAMMB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 33585088
            }
          },
          {
            "Label": "VMkernel.Boot.microcodeUpdate",
            "Summary": "Update microcode from boot module if available",
            "Key": "VMkernel.Boot.microcodeUpdate",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.microcodeUpdateForce",
            "Summary": "Disable check that microcode update is newer than installed microcode and that both are released versions",
            "Key": "VMkernel.Boot.microcodeUpdateForce",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.netCoalesceTimerHdlrPcpu",
            "Summary": "Pcpu that coalesce timeout handler runs on.",
            "Key": "VMkernel.Boot.netCoalesceTimerHdlrPcpu",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VMkernel.Boot.netGPHeapMaxMBPerGB",
            "Summary": "Maximum MB of the general purpose networking heap to be allocated per GB of physical memory.",
            "Key": "VMkernel.Boot.netGPHeapMaxMBPerGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 4
            }
          },
          {
            "Label": "VMkernel.Boot.netMaxPktsToProcess",
            "Summary": "Maximum number of packets to process in each invocation packet processing routine",
            "Key": "VMkernel.Boot.netMaxPktsToProcess",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 64
            }
          },
          {
            "Label": "VMkernel.Boot.netMaxPktsToRelease",
            "Summary": "Maximum number of packets to release in each invocation packet releasing routine",
            "Key": "VMkernel.Boot.netMaxPktsToRelease",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 128
            }
          },
          {
            "Label": "VMkernel.Boot.netNetqueueEnabled",
            "Summary": "Enable/Disable NetQueue support.",
            "Key": "VMkernel.Boot.netNetqueueEnabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.netNetqueueMaxFiltersPerUplink",
            "Summary": "Maximum number of netqueue filters for Uplink. Maximum value for this setting is 32768. Requires REBOOT.",
            "Key": "VMkernel.Boot.netNetqueueMaxFiltersPerUplink",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 4096
            }
          },
          {
            "Label": "VMkernel.Boot.netNetqueueMaxLearnedFilters",
            "Summary": "Maximum number of hostwide netqueue learned filters. Maximum value for this setting is 16384. Requires REBOOT.",
            "Key": "VMkernel.Boot.netNetqueueMaxLearnedFilters",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 9216
            }
          },
          {
            "Label": "VMkernel.Boot.netNetqueueMaxStaticFilters",
            "Summary": "Maximum number of hostwide netqueue static filters. Maximum value for this setting is 16384. Requires REBOOT.",
            "Key": "VMkernel.Boot.netNetqueueMaxStaticFilters",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 10240
            }
          },
          {
            "Label": "VMkernel.Boot.netPagePoolLimitCap",
            "Summary": "Maximum number of pages period for the packet page pool.",
            "Key": "VMkernel.Boot.netPagePoolLimitCap",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 98304
            }
          },
          {
            "Label": "VMkernel.Boot.netPagePoolLimitPerGB",
            "Summary": "Maximum number of pages for the packet page pool per gigabyte.",
            "Key": "VMkernel.Boot.netPagePoolLimitPerGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 5120
            }
          },
          {
            "Label": "VMkernel.Boot.netPagePoolResvCap",
            "Summary": "Maximum number of pages to reserve for the packet page pool.",
            "Key": "VMkernel.Boot.netPagePoolResvCap",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VMkernel.Boot.netPagePoolResvPerGB",
            "Summary": "Number of pages to reserve for the packet page pool per gigabyte.",
            "Key": "VMkernel.Boot.netPagePoolResvPerGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VMkernel.Boot.netPktHeapMaxMBPerGB",
            "Summary": "Maximum MB of low-memory packet heap to be allocated per GB of physical memory. Maximum value for this setting is 512.",
            "Key": "VMkernel.Boot.netPktHeapMaxMBPerGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 6
            }
          },
          {
            "Label": "VMkernel.Boot.netPktHeapMinMBPerGB",
            "Summary": "Minimum MB of low-memory packet heap to be allocated per GB of physical memory. Maximum value for this setting is 512.",
            "Key": "VMkernel.Boot.netPktHeapMinMBPerGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VMkernel.Boot.netPktPoolMaxMBPerGB",
            "Summary": "Maximum MB of networking packet buffer pool to be allocated per GB of physical memory. Maximum value for this setting is 512. Net packet pool maximum per system will be at least 656MB.",
            "Key": "VMkernel.Boot.netPktPoolMaxMBPerGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 75
            }
          },
          {
            "Label": "VMkernel.Boot.netPktPoolMinMBPerGB",
            "Summary": "Minimum MB of networking packet buffer pool to be allocated per GB of physical memory (multiple of 24)",
            "Key": "VMkernel.Boot.netPktPoolMinMBPerGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VMkernel.Boot.netPreemptionEnabled",
            "Summary": "Enable/disable preemption support in overall networking area",
            "Key": "VMkernel.Boot.netPreemptionEnabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.nmiAction",
            "Summary": "Action on hardware generated NMI: 0=default (panic, unless changed by advanced config option), 1=enter debugger, 2=panic, 3=log and ignore (not recommended)",
            "Key": "VMkernel.Boot.nmiAction",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VMkernel.Boot.numSpareCoresPerLLC",
            "Summary": "Number of spare cores reserved at boot time per last-level cache (LLC)",
            "Key": "VMkernel.Boot.numSpareCoresPerLLC",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VMkernel.Boot.numaLatencyRemoteThresholdPct",
            "Summary": "Maximum measured memory access latency difference (in percent units) between 2 pairs of NUMA nodes for the pairs to be considered equidistant",
            "Key": "VMkernel.Boot.numaLatencyRemoteThresholdPct",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 10
            }
          },
          {
            "Label": "VMkernel.Boot.overrideDuplicateImageDetection",
            "Summary": "Override duplicate ESXi image detection",
            "Key": "VMkernel.Boot.overrideDuplicateImageDetection",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.pciBarAllocPolicy",
            "Summary": "PCI BAR allocation policy; 0=first-fit, 1=smallest-fit",
            "Key": "VMkernel.Boot.pciBarAllocPolicy",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VMkernel.Boot.pcipDisablePciErrReporting",
            "Summary": "Disable error reporting for PCI passthru devices.",
            "Key": "VMkernel.Boot.pcipDisablePciErrReporting",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.poisonMarker",
            "Summary": "Value used to poison memmap pages",
            "Key": "VMkernel.Boot.poisonMarker",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": -9223372036854775808,
              "Max": 9223372036854775807,
              "DefaultValue": -6148914691236517206
            }
          },
          {
            "Label": "VMkernel.Boot.poisonPagePool",
            "Summary": "Poison memmap pages",
            "Key": "VMkernel.Boot.poisonPagePool",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.preferVmklinux",
            "Summary": "Boost priority of vmklinux drivers to trump native drivers.",
            "Key": "VMkernel.Boot.preferVmklinux",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.preventFreePageMapping",
            "Summary": "Prevent mapping of free pages",
            "Key": "VMkernel.Boot.preventFreePageMapping",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.rdmaRoceIPBasedGidGeneration",
            "Summary": "Use the new IP-based GID generation for RoCE",
            "Key": "VMkernel.Boot.rdmaRoceIPBasedGidGeneration",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.rtcEnableEFI",
            "Summary": "Enable UEFI Runtime Services as real time clock",
            "Key": "VMkernel.Boot.rtcEnableEFI",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.rtcEnableLegacy",
            "Summary": "Enable legacy CMOS device as real time clock",
            "Key": "VMkernel.Boot.rtcEnableLegacy",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.rtcEnableTAD",
            "Summary": "Enable ACPI 5.0 Time and Alarm Device as real time clock",
            "Key": "VMkernel.Boot.rtcEnableTAD",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.scrubIgnoredPages",
            "Summary": "Check pages that were ignored due to vmbIgnoreStartMPN and vmbIgnoreNumMPNs boot options.",
            "Key": "VMkernel.Boot.scrubIgnoredPages",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.scrubMemoryAfterModuleLoad",
            "Summary": "Check all memory after each module load. This causes booting to be very slow.",
            "Key": "VMkernel.Boot.scrubMemoryAfterModuleLoad",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.serialUntrusted",
            "Summary": "Disable serial port if UART misbehaves.",
            "Key": "VMkernel.Boot.serialUntrusted",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.skipPartitioningSsds",
            "Summary": "If enabled, skip auto-partitioning of empty local SSDs.",
            "Key": "VMkernel.Boot.skipPartitioningSsds",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.storageHeapMaxSize",
            "Summary": "Maximum size in bytes of the main storage heap",
            "Key": "VMkernel.Boot.storageHeapMaxSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VMkernel.Boot.storageHeapMinSize",
            "Summary": "Minimum size in bytes of the main storage heap",
            "Key": "VMkernel.Boot.storageHeapMinSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 0
            }
          },
          {
            "Label": "VMkernel.Boot.storageMaxDevices",
            "Summary": "Maximum number of supported SCSI devices",
            "Key": "VMkernel.Boot.storageMaxDevices",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 1024
            }
          },
          {
            "Label": "VMkernel.Boot.storageMaxPaths",
            "Summary": "Maximum number of supported SCSI paths",
            "Key": "VMkernel.Boot.storageMaxPaths",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 4096
            }
          },
          {
            "Label": "VMkernel.Boot.storageMaxVMsPerDevice",
            "Summary": "Maximum number of VMs expected to use a given device",
            "Key": "VMkernel.Boot.storageMaxVMsPerDevice",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 32
            }
          },
          {
            "Label": "VMkernel.Boot.terminateVMOnPDL",
            "Summary": "Terminate virtual machine on permanent loss of storage",
            "Key": "VMkernel.Boot.terminateVMOnPDL",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.timerEnableACPI",
            "Summary": "Enable ACPI PM timer as system reference timer.",
            "Key": "VMkernel.Boot.timerEnableACPI",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.timerEnableHPET",
            "Summary": "Enable HPET as system reference timer.",
            "Key": "VMkernel.Boot.timerEnableHPET",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.timerEnableTSC",
            "Summary": "Enable TSC as system reference timer.",
            "Key": "VMkernel.Boot.timerEnableTSC",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.timerForceTSC",
            "Summary": "Always use TSC as system reference timer.",
            "Key": "VMkernel.Boot.timerForceTSC",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.tscSpeedMayVary",
            "Summary": "Allow different TSC speeds on different CPUs.",
            "Key": "VMkernel.Boot.tscSpeedMayVary",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.tty1Port",
            "Summary": "TTY1 port; com1, com2 ... to enable",
            "Key": "VMkernel.Boot.tty1Port",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "default",
              "ValidCharacters": ""
            }
          },
          {
            "Label": "VMkernel.Boot.tty2Port",
            "Summary": "TTY2 port; com1, com2 ... to enable",
            "Key": "VMkernel.Boot.tty2Port",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "default",
              "ValidCharacters": ""
            }
          },
          {
            "Label": "VMkernel.Boot.updateBusIRQ",
            "Summary": "Allow adding/removing busIRQ information",
            "Key": "VMkernel.Boot.updateBusIRQ",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.useNUMAInfo",
            "Summary": "Enable/disable NUMA-ness",
            "Key": "VMkernel.Boot.useNUMAInfo",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.useReliableMem",
            "Summary": "System is aware of reliable memory.",
            "Key": "VMkernel.Boot.useReliableMem",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.useSLIT",
            "Summary": "Use NUMA latency information from the SLIT table if available",
            "Key": "VMkernel.Boot.useSLIT",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.vmkATKeyboard",
            "Summary": "Enable VMkernel AT keyboard driver.",
            "Key": "VMkernel.Boot.vmkATKeyboard",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "VMkernel.Boot.vmkacEnable",
            "Summary": "0=Disabled, 1=Enforcing, 2=Do not enforce, just warn",
            "Key": "VMkernel.Boot.vmkacEnable",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 255,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VMkernel.Boot.vtdSuperPages",
            "Summary": "Enables support for VT-d Super Pages (aka 2MB, 1GB large pages); super-page usage is dependent on this option as well as hardware support.",
            "Key": "VMkernel.Boot.vtdSuperPages",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": true
            }
          },
          {
            "Label": "VMkernel.Boot.x2ApicPreferred",
            "Summary": "Use x2APIC if available, even with less than 256 cpus.",
            "Key": "VMkernel.Boot.x2ApicPreferred",
            "OptionType": {
              "ValueIsReadonly": false,
              "Supported": true,
              "DefaultValue": false
            }
          },
          {
            "Label": "iscsiPingTimeout",
            "Summary": "Interval between ping (NOP-Out) requests, in seconds",
            "Key": "VSAN-iSCSI.iscsiPingTimeout",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 60,
              "DefaultValue": 5
            }
          },
          {
            "Label": "AutoRestoreDecomState",
            "Summary": "Whether to restore vSAN node decommission state automatically during vSAN refresh",
            "Key": "VSAN.AutoRestoreDecomState",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "AutoTerminateGhostVm",
            "Summary": "Automatically terminate ghost VM(s) during network partition",
            "Key": "VSAN.AutoTerminateGhostVm",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "ClomForceProvisionPlacements",
            "Summary": "Add Force provisioning flag for all new object placement requests",
            "Key": "VSAN.ClomForceProvisionPlacements",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "ClomMaxComponentSizeGB",
            "Summary": "Maximum component size used for new placements",
            "Key": "VSAN.ClomMaxComponentSizeGB",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 10,
              "Max": 255,
              "DefaultValue": 255
            }
          },
          {
            "Label": "ClomMaxDiskUsageRepairComps",
            "Summary": "Percentage disk fullness after which no new repair components can be placed on a given disk",
            "Key": "VSAN.ClomMaxDiskUsageRepairComps",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 95
            }
          },
          {
            "Label": "ClomRebalanceThreshold",
            "Summary": "Percentage disk fullness after which rebalancing is triggered",
            "Key": "VSAN.ClomRebalanceThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 80
            }
          },
          {
            "Label": "Deduplication Scope",
            "Summary": "The default deduplication scope for in all-flash disk group",
            "Key": "VSAN.DedupScope",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 3,
              "DefaultValue": 0
            }
          },
          {
            "Label": "DefaultHostDecommissionMode",
            "Summary": "Default host decommission mode for a given node",
            "Key": "VSAN.DefaultHostDecommissionMode",
            "OptionType": {
              "ValueIsReadonly": false,
              "DefaultValue": "ensureAccessibility",
              "ValidCharacters": "**"
            }
          },
          {
            "Label": "DomBriefIoTraces",
            "Summary": "Enables a brief set of per-IO DOM traces for debugging",
            "Key": "VSAN.DomBriefIoTraces",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "DomFullIoTraces",
            "Summary": "Enables the full set of per-IO DOM traces for debugging",
            "Key": "VSAN.DomFullIoTraces",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "DomLongOpTraceMS",
            "Summary": "Trace ops that take more than the specified value in milliseconds",
            "Key": "VSAN.DomLongOpTraceMS",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 600000,
              "DefaultValue": 1000
            }
          },
          {
            "Label": "DomLongOpUrgentTraceMS",
            "Summary": "Urgent trace ops that take more than the specified value in milliseconds",
            "Key": "VSAN.DomLongOpUrgentTraceMS",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 600000,
              "DefaultValue": 10000
            }
          },
          {
            "Label": "ObjectScrubsPerYear",
            "Summary": "Option to set the scrubbing rate as scrubs-per-year",
            "Key": "VSAN.ObjectScrubsPerYear",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 525600,
              "DefaultValue": 1
            }
          },
          {
            "Label": "PerTraceBandwidthLimit",
            "Summary": "Max number of traces per second (0 to disable limits)",
            "Key": "VSAN.PerTraceBandwidthLimit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "PerTraceBandwidthLimitPeriodMs",
            "Summary": "Add BANDWIDTH_LIMIT * PERIOD_MS tokens (traces) every PERIOD_MS.",
            "Key": "VSAN.PerTraceBandwidthLimitPeriodMs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000000,
              "DefaultValue": 10000
            }
          },
          {
            "Label": "PerTraceMaxRolloverPeriods",
            "Summary": "Maximum number of periods where unused bandwidth can accumulate",
            "Key": "VSAN.PerTraceMaxRolloverPeriods",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000000,
              "DefaultValue": 360
            }
          },
          {
            "Label": "TraceEnableCmmds",
            "Summary": "Enables tracing for vSAN CMMDS and CMMDSResolver components",
            "Key": "VSAN.TraceEnableCmmds",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TraceEnableDom",
            "Summary": "Enables tracing for vSAN DOM component",
            "Key": "VSAN.TraceEnableDom",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TraceEnableDomIo",
            "Summary": "Enables tracing for vSAN DOMIO component",
            "Key": "VSAN.TraceEnableDomIo",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "TraceEnableLchk",
            "Summary": "Enables LCHK tracing",
            "Key": "VSAN.TraceEnableLchk",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TraceEnableLsom",
            "Summary": "Enables tracing for vSAN LSOM component",
            "Key": "VSAN.TraceEnableLsom",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TraceEnableLsomIo",
            "Summary": "Enables IO tracing for vSAN LSOM component",
            "Key": "VSAN.TraceEnableLsomIo",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "TraceEnablePlog",
            "Summary": "Enables tracing for vSAN PLOG component",
            "Key": "VSAN.TraceEnablePlog",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TraceEnableRdt",
            "Summary": "Enables tracing for vSAN RDT component",
            "Key": "VSAN.TraceEnableRdt",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TraceEnableSsdLog",
            "Summary": "Enables tracing for vSAN SSDLOG component",
            "Key": "VSAN.TraceEnableSsdLog",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TraceEnableVirsto",
            "Summary": "Enables tracing for vSAN Virsto component",
            "Key": "VSAN.TraceEnableVirsto",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TraceEnableVirstoIo",
            "Summary": "Enables IO tracing for vSAN Virsto component",
            "Key": "VSAN.TraceEnableVirstoIo",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "TraceEnableVsanSparse",
            "Summary": "Enables tracing for VsanSparse FDS operations",
            "Key": "VSAN.TraceEnableVsanSparse",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "TraceEnableVsanSparseIO",
            "Summary": "Enables tracing for VsanSparse I/O operations",
            "Key": "VSAN.TraceEnableVsanSparseIO",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "TraceEnableVsanSparseVerbose",
            "Summary": "Enables tracing for details of VsanSparse I/O operations",
            "Key": "VSAN.TraceEnableVsanSparseVerbose",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 0
            }
          },
          {
            "Label": "TraceGlobalBandwidthLimit",
            "Summary": "Max number of traces per second (0 to disable limits)",
            "Key": "VSAN.TraceGlobalBandwidthLimit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000000,
              "DefaultValue": 0
            }
          },
          {
            "Label": "TraceGlobalBandwidthLimitPeriodMs",
            "Summary": "Add BANDWIDTH_LIMIT * PERIOD_MS tokens (traces) every PERIOD_MS.",
            "Key": "VSAN.TraceGlobalBandwidthLimitPeriodMs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000000,
              "DefaultValue": 10000
            }
          },
          {
            "Label": "TraceGlobalMaxRolloverPeriods",
            "Summary": "Maximum number of periods where unused bandwidth can accumulate",
            "Key": "VSAN.TraceGlobalMaxRolloverPeriods",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000000,
              "DefaultValue": 360
            }
          },
          {
            "Label": "TracesPerErrorBandwidthLimit",
            "Summary": "Maximum number of traces per second during specific error conditions (0 to disable limits)",
            "Key": "VSAN.TracesPerErrorBandwidthLimit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000000,
              "DefaultValue": 1000
            }
          },
          {
            "Label": "TracesPerErrorBandwidthLimitPeriodMs",
            "Summary": "Add BANDWIDTH_LIMIT * PERIOD_MS tokens (traces) every PERIOD_MS",
            "Key": "VSAN.TracesPerErrorBandwidthLimitPeriodMs",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 10000000,
              "DefaultValue": 10000
            }
          },
          {
            "Label": "TracesPerErrorMaxRolloverPeriods",
            "Summary": "Maximum number of periods where unused bandwidth can accumulate",
            "Key": "VSAN.TracesPerErrorMaxRolloverPeriods",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1000000,
              "DefaultValue": 60
            }
          },
          {
            "Label": "VsanSparseCacheOverEvict",
            "Summary": "Percentage of VsanSparseCacheThreshold to add to eviction",
            "Key": "VSAN.VsanSparseCacheOverEvict",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 100,
              "DefaultValue": 5
            }
          },
          {
            "Label": "VsanSparseCacheThreshold",
            "Summary": "Maximum VsanSparse cache size, in cache entries",
            "Key": "VSAN.VsanSparseCacheThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 1024
            }
          },
          {
            "Label": "VsanSparseEnabled",
            "Summary": "Enable auto-creation of vsanSparse instead of vmfsSparse redologs, for vSAN 2.0 datastore only.",
            "Key": "VSAN.VsanSparseEnabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VsanSparseHeapSize",
            "Summary": "Maximum heap size for VsanSparse snapshot consolidation buffers (in KiB)",
            "Key": "VSAN.VsanSparseHeapSize",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 256,
              "Max": 131072,
              "DefaultValue": 65536
            }
          },
          {
            "Label": "VsanSparseMaxExtentsPrefetch",
            "Summary": "Maximum number of extents to fetch during interrogation",
            "Key": "VSAN.VsanSparseMaxExtentsPrefetch",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 128,
              "DefaultValue": 64
            }
          },
          {
            "Label": "VsanSparseParallelLookup",
            "Summary": "Request written extent data from each layer in parallel",
            "Key": "VSAN.VsanSparseParallelLookup",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VsanSparseRetainCacheOnSnapshots",
            "Summary": "Try to retain VsanSparse in-memory cache content when taking VM snapshots",
            "Key": "VSAN.VsanSparseRetainCacheOnSnapshots",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "VsanSparseRetainCacheTTL",
            "Summary": "Maximum time to retain VsanSparse in-memory cache content between snapshots, in seconds",
            "Key": "VSAN.VsanSparseRetainCacheTTL",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 3600,
              "DefaultValue": 20
            }
          },
          {
            "Label": "VsanSparseSpeculativePrefetch",
            "Summary": "Number of bytes to add to each extent interrogation request",
            "Key": "VSAN.VsanSparseSpeculativePrefetch",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 4194304
            }
          },
          {
            "Label": "Virsto Disk Format version",
            "Summary": "Virsto Disk Format version",
            "Key": "Virsto.DiskFormatVersion",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 2,
              "Max": 10,
              "DefaultValue": 10
            }
          },
          {
            "Label": "Enabled",
            "Summary": "Use Virsto format for new disks",
            "Key": "Virsto.Enabled",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          },
          {
            "Label": "Flusher Registry space threshold",
            "Summary": "Flusher registry data size threshold (percentage) when metadata flush is forced",
            "Key": "Virsto.FlusherRegistryThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 50,
              "Max": 100,
              "DefaultValue": 95
            }
          },
          {
            "Label": "Control GWE on-disk extents fetching",
            "Summary": "Multiplier controlling how many on-disk extents fetched based on GWE request size",
            "Key": "Virsto.GweFetchExtentsFactor",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 10,
              "DefaultValue": 3
            }
          },
          {
            "Label": "Instance heap limit",
            "Summary": "Heap limit for each Virsto instance (disk) in MB",
            "Key": "Virsto.InstanceHeapLimit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 128,
              "Max": 4095,
              "DefaultValue": 130
            }
          },
          {
            "Label": "Map block cache flush threshold",
            "Summary": "A threshold for dirty entries of map block cache when metadata flush is forced (in count of 4KB blocks)",
            "Key": "Virsto.MapBlocksFlushThreshold",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 50,
              "Max": 100,
              "DefaultValue": 90
            }
          },
          {
            "Label": "Map block cache minimum",
            "Summary": "Map block cache minimum for each Virsto instance (in count of 4KB blocks)",
            "Key": "Virsto.MapBlocksMin",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 16,
              "Max": 4294967295,
              "DefaultValue": 16384
            }
          },
          {
            "Label": "Virsto MF retry count",
            "Summary": "How many times Virsto metadata flusher retries in case of a transient error.",
            "Key": "Virsto.MaxMFRetryCount",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 4294967295,
              "DefaultValue": 3
            }
          },
          {
            "Label": "Msec before metadata flush",
            "Summary": "Force Virsto metadata flush after this many msec",
            "Key": "Virsto.MsecBeforeMetaFlush",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 86400000,
              "DefaultValue": 10000
            }
          },
          {
            "Label": "Records per format write",
            "Summary": "Number of (LSAR) records written per format write operation",
            "Key": "Virsto.RecordsPerFormatWrite",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 1,
              "Max": 128,
              "DefaultValue": 16
            }
          },
          {
            "Label": "Shared heap limit",
            "Summary": "Shared heap limit for Virsto module in MB",
            "Key": "Virsto.SharedHeapLimit",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 2,
              "Max": 4095,
              "DefaultValue": 4
            }
          },
          {
            "Label": "VMFSOptimizations",
            "Summary": "Enable VMFS-specific IO optimizations",
            "Key": "XvMotion.VMFSOptimizations",
            "OptionType": {
              "ValueIsReadonly": false,
              "Min": 0,
              "Max": 1,
              "DefaultValue": 1
            }
          }
        ],
        "DatastorePrincipal": "root",
        "LocalSwapDatastore": null,
        "SystemSwapConfiguration": {
          "Option": [
            {
              "Key": 0
            },
            {
              "Key": 1
            }
          ]
        },
        "SystemResources": null,
        "DateTimeInfo": {
          "TimeZone": {
            "Key": "UTC",
            "Name": "UTC",
            "Description": "UTC",
            "GmtOffset": 0
          },
          "SystemClockProtocol": "",
          "NtpConfig": {
            "Server": null,
            "ConfigFile": [
              "restrict default nomodify notrap nopeer noquery",
              "restrict 127.0.0.1",
              "driftfile /etc/ntp.drift"
            ]
          }
        },
        "Flags": {
          "BackgroundSnapshotsEnabled": null
        },
        "AdminDisabled": null,
        "LockdownMode": "lockdownDisabled",
        "Ipmi": null,
        "SslThumbprintInfo": null,
        "SslThumbprintData": null,
        "Certificate": "MTA=",
        "PciPassthruInfo": [
          {
            "Id": "0000:00:00.0",
            "DependentDevice": "",
            "PassthruEnabled": false,
            "PassthruCapable": false,
            "PassthruActive": false
          },
          {
            "Id": "0000:00:01.0",
            "DependentDevice": "",
            "PassthruEnabled": false,
            "PassthruCapable": false,
            "PassthruActive": false
          },
          {
            "Id": "0000:00:01.1",
            "DependentDevice": "",
            "PassthruEnabled": false,
            "PassthruCapable": false,
            "PassthruActive": false
          },
          {
            "Id": "0000:00:02.0",
            "DependentDevice": "",
            "PassthruEnabled": false,
            "PassthruCapable": false,
            "PassthruActive": false
          },
          {
            "Id": "0000:00:03.0",
            "DependentDevice": "",
            "PassthruEnabled": false,
            "PassthruCapable": false,
            "PassthruActive": false
          },
          {
            "Id": "0000:00:04.0",
            "DependentDevice": "",
            "PassthruEnabled": false,
            "PassthruCapable": false,
            "PassthruActive": false
          },
          {
            "Id": "0000:00:05.0",
            "DependentDevice": "",
            "PassthruEnabled": false,
            "PassthruCapable": false,
            "PassthruActive": false
          },
          {
            "Id": "0000:00:06.0",
            "DependentDevice": "",
            "PassthruEnabled": false,
            "PassthruCapable": false,
            "PassthruActive": false
          },
          {
            "Id": "0000:00:07.0",
            "DependentDevice": "",
            "PassthruEnabled": false,
            "PassthruCapable": false,
            "PassthruActive": false
          },
          {
            "Id": "0000:00:08.0",
            "DependentDevice": "",
            "PassthruEnabled": false,
            "PassthruCapable": false,
            "PassthruActive": false
          }
        ],
        "AuthenticationManagerInfo": {
          "AuthConfig": [
            {
              "Enabled": true
            },
            {
              "Enabled": false,
              "JoinedDomain": "",
              "TrustedDomain": null,
              "DomainMembershipStatus": "",
              "SmartCardAuthenticationEnabled": false
            }
          ]
        },
        "FeatureVersion": null,
        "PowerSystemCapability": {
          "AvailablePolicy": [
            {
              "Key": 1,
              "Name": "PowerPolicy.static.name",
              "ShortName": "static",
              "Description": "PowerPolicy.static.description"
            },
            {
              "Key": 2,
              "Name": "PowerPolicy.dynamic.name",
              "ShortName": "dynamic",
              "Description": "PowerPolicy.dynamic.description"
            },
            {
              "Key": 3,
              "Name": "PowerPolicy.low.name",
              "ShortName": "low",
              "Description": "PowerPolicy.low.description"
            },
            {
              "Key": 4,
              "Name": "PowerPolicy.custom.name",
              "ShortName": "custom",
              "Description": "PowerPolicy.custom.description"
            }
          ]
        },
        "PowerSystemInfo": {
          "CurrentPolicy": {
            "Key": 2,
            "Name": "PowerPolicy.dynamic.name",
            "ShortName": "dynamic",
            "Description": "PowerPolicy.dynamic.description"
          }
        },
        "CacheConfigurationInfo": null,
        "WakeOnLanCapable": false,
        "FeatureCapability": [
          {
            "Key": "cpuid.3DNOW",
            "FeatureName": "cpuid.3DNOW",
            "Value": "0"
          },
          {
            "Key": "cpuid.3DNOWPLUS",
            "FeatureName": "cpuid.3DNOWPLUS",
            "Value": "0"
          },
          {
            "Key": "cpuid.3DNPREFETCH",
            "FeatureName": "cpuid.3DNPREFETCH",
            "Value": "1"
          },
          {
            "Key": "cpuid.ABM",
            "FeatureName": "cpuid.ABM",
            "Value": "1"
          },
          {
            "Key": "cpuid.ADX",
            "FeatureName": "cpuid.ADX",
            "Value": "0"
          },
          {
            "Key": "cpuid.AES",
            "FeatureName": "cpuid.AES",
            "Value": "1"
          },
          {
            "Key": "cpuid.AMD",
            "FeatureName": "cpuid.AMD",
            "Value": "0"
          },
          {
            "Key": "cpuid.AVX",
            "FeatureName": "cpuid.AVX",
            "Value": "1"
          },
          {
            "Key": "cpuid.AVX2",
            "FeatureName": "cpuid.AVX2",
            "Value": "1"
          },
          {
            "Key": "cpuid.AVX512BW",
            "FeatureName": "cpuid.AVX512BW",
            "Value": "0"
          },
          {
            "Key": "cpuid.AVX512CD",
            "FeatureName": "cpuid.AVX512CD",
            "Value": "0"
          },
          {
            "Key": "cpuid.AVX512DQ",
            "FeatureName": "cpuid.AVX512DQ",
            "Value": "0"
          },
          {
            "Key": "cpuid.AVX512ER",
            "FeatureName": "cpuid.AVX512ER",
            "Value": "0"
          },
          {
            "Key": "cpuid.AVX512F",
            "FeatureName": "cpuid.AVX512F",
            "Value": "0"
          },
          {
            "Key": "cpuid.AVX512PF",
            "FeatureName": "cpuid.AVX512PF",
            "Value": "0"
          },
          {
            "Key": "cpuid.AVX512VL",
            "FeatureName": "cpuid.AVX512VL",
            "Value": "0"
          },
          {
            "Key": "cpuid.BMI1",
            "FeatureName": "cpuid.BMI1",
            "Value": "0"
          },
          {
            "Key": "cpuid.BMI2",
            "FeatureName": "cpuid.BMI2",
            "Value": "0"
          },
          {
            "Key": "cpuid.CLFLUSHOPT",
            "FeatureName": "cpuid.CLFLUSHOPT",
            "Value": "1"
          },
          {
            "Key": "cpuid.CLWB",
            "FeatureName": "cpuid.CLWB",
            "Value": "0"
          },
          {
            "Key": "cpuid.CLZERO",
            "FeatureName": "cpuid.CLZERO",
            "Value": "0"
          },
          {
            "Key": "cpuid.CMPXCHG16B",
            "FeatureName": "cpuid.CMPXCHG16B",
            "Value": "0"
          },
          {
            "Key": "cpuid.CR8AVAIL",
            "FeatureName": "cpuid.CR8AVAIL",
            "Value": "0"
          },
          {
            "Key": "cpuid.Cyrix",
            "FeatureName": "cpuid.Cyrix",
            "Value": "0"
          },
          {
            "Key": "cpuid.DS",
            "FeatureName": "cpuid.DS",
            "Value": "1"
          },
          {
            "Key": "cpuid.ENFSTRG",
            "FeatureName": "cpuid.ENFSTRG",
            "Value": "0"
          },
          {
            "Key": "cpuid.EXTAPICSPC",
            "FeatureName": "cpuid.EXTAPICSPC",
            "Value": "0"
          },
          {
            "Key": "cpuid.F16C",
            "FeatureName": "cpuid.F16C",
            "Value": "0"
          },
          {
            "Key": "cpuid.FCMD",
            "FeatureName": "cpuid.FCMD",
            "Value": "1"
          },
          {
            "Key": "cpuid.FFXSR",
            "FeatureName": "cpuid.FFXSR",
            "Value": "0"
          },
          {
            "Key": "cpuid.FMA",
            "FeatureName": "cpuid.FMA",
            "Value": "0"
          },
          {
            "Key": "cpuid.FMA4",
            "FeatureName": "cpuid.FMA4",
            "Value": "0"
          },
          {
            "Key": "cpuid.FSGSBASE",
            "FeatureName": "cpuid.FSGSBASE",
            "Value": "1"
          },
          {
            "Key": "cpuid.HLE",
            "FeatureName": "cpuid.HLE",
            "Value": "0"
          },
          {
            "Key": "cpuid.Hygon",
            "FeatureName": "cpuid.Hygon",
            "Value": "0"
          },
          {
            "Key": "cpuid.IBPB",
            "FeatureName": "cpuid.IBPB",
            "Value": "0"
          },
          {
            "Key": "cpuid.IBRS",
            "FeatureName": "cpuid.IBRS",
            "Value": "0"
          },
          {
            "Key": "cpuid.INVPCID",
            "FeatureName": "cpuid.INVPCID",
            "Value": "1"
          },
          {
            "Key": "cpuid.Intel",
            "FeatureName": "cpuid.Intel",
            "Value": "1"
          },
          {
            "Key": "cpuid.LAHF64",
            "FeatureName": "cpuid.LAHF64",
            "Value": "0"
          },
          {
            "Key": "cpuid.LM",
            "FeatureName": "cpuid.LM",
            "Value": "0"
          },
          {
            "Key": "cpuid.MDCLEAR",
            "FeatureName": "cpuid.MDCLEAR",
            "Value": "1"
          },
          {
            "Key": "cpuid.MISALIGNED_SSE",
            "FeatureName": "cpuid.MISALIGNED_SSE",
            "Value": "0"
          },
          {
            "Key": "cpuid.MMXEXT",
            "FeatureName": "cpuid.MMXEXT",
            "Value": "0"
          },
          {
            "Key": "cpuid.MOVBE",
            "FeatureName": "cpuid.MOVBE",
            "Value": "1"
          },
          {
            "Key": "cpuid.MPX",
            "FeatureName": "cpuid.MPX",
            "Value": "0"
          },
          {
            "Key": "cpuid.MWAIT",
            "FeatureName": "cpuid.MWAIT",
            "Value": "0"
          },
          {
            "Key": "cpuid.NX",
            "FeatureName": "cpuid.NX",
            "Value": "1"
          },
          {
            "Key": "cpuid.PCID",
            "FeatureName": "cpuid.PCID",
            "Value": "1"
          },
          {
            "Key": "cpuid.PCLMULQDQ",
            "FeatureName": "cpuid.PCLMULQDQ",
            "Value": "1"
          },
          {
            "Key": "cpuid.PDPE1GB",
            "FeatureName": "cpuid.PDPE1GB",
            "Value": "0"
          },
          {
            "Key": "cpuid.PKU",
            "FeatureName": "cpuid.PKU",
            "Value": "0"
          },
          {
            "Key": "cpuid.POPCNT",
            "FeatureName": "cpuid.POPCNT",
            "Value": "1"
          },
          {
            "Key": "cpuid.PREFETCHWT1",
            "FeatureName": "cpuid.PREFETCHWT1",
            "Value": "0"
          },
          {
            "Key": "cpuid.PSN",
            "FeatureName": "cpuid.PSN",
            "Value": "0"
          },
          {
            "Key": "cpuid.RDRAND",
            "FeatureName": "cpuid.RDRAND",
            "Value": "1"
          },
          {
            "Key": "cpuid.RDSEED",
            "FeatureName": "cpuid.RDSEED",
            "Value": "1"
          },
          {
            "Key": "cpuid.RDTSCP",
            "FeatureName": "cpuid.RDTSCP",
            "Value": "1"
          },
          {
            "Key": "cpuid.RTM",
            "FeatureName": "cpuid.RTM",
            "Value": "0"
          },
          {
            "Key": "cpuid.SHA",
            "FeatureName": "cpuid.SHA",
            "Value": "0"
          },
          {
            "Key": "cpuid.SMAP",
            "FeatureName": "cpuid.SMAP",
            "Value": "0"
          },
          {
            "Key": "cpuid.SMEP",
            "FeatureName": "cpuid.SMEP",
            "Value": "0"
          },
          {
            "Key": "cpuid.SS",
            "FeatureName": "cpuid.SS",
            "Value": "0"
          },
          {
            "Key": "cpuid.SSBD",
            "FeatureName": "cpuid.SSBD",
            "Value": "0"
          },
          {
            "Key": "cpuid.SSE3",
            "FeatureName": "cpuid.SSE3",
            "Value": "1"
          },
          {
            "Key": "cpuid.SSE41",
            "FeatureName": "cpuid.SSE41",
            "Value": "1"
          },
          {
            "Key": "cpuid.SSE42",
            "FeatureName": "cpuid.SSE42",
            "Value": "1"
          },
          {
            "Key": "cpuid.SSE4A",
            "FeatureName": "cpuid.SSE4A",
            "Value": "0"
          },
          {
            "Key": "cpuid.SSSE3",
            "FeatureName": "cpuid.SSSE3",
            "Value": "1"
          },
          {
            "Key": "cpuid.STIBP",
            "FeatureName": "cpuid.STIBP",
            "Value": "0"
          },
          {
            "Key": "cpuid.SVM",
            "FeatureName": "cpuid.SVM",
            "Value": "0"
          },
          {
            "Key": "cpuid.SVM_DECODE_ASSISTS",
            "FeatureName": "cpuid.SVM_DECODE_ASSISTS",
            "Value": "0"
          },
          {
            "Key": "cpuid.SVM_FLUSH_BY_ASID",
            "FeatureName": "cpuid.SVM_FLUSH_BY_ASID",
            "Value": "0"
          },
          {
            "Key": "cpuid.SVM_NPT",
            "FeatureName": "cpuid.SVM_NPT",
            "Value": "0"
          },
          {
            "Key": "cpuid.SVM_NRIP",
            "FeatureName": "cpuid.SVM_NRIP",
            "Value": "0"
          },
          {
            "Key": "cpuid.SVM_VMCB_CLEAN",
            "FeatureName": "cpuid.SVM_VMCB_CLEAN",
            "Value": "0"
          },
          {
            "Key": "cpuid.TBM",
            "FeatureName": "cpuid.TBM",
            "Value": "0"
          },
          {
            "Key": "cpuid.VIA",
            "FeatureName": "cpuid.VIA",
            "Value": "0"
          },
          {
            "Key": "cpuid.VMX",
            "FeatureName": "cpuid.VMX",
            "Value": "0"
          },
          {
            "Key": "cpuid.XCR0_MASTER_BNDCSR",
            "FeatureName": "cpuid.XCR0_MASTER_BNDCSR",
            "Value": "0"
          },
          {
            "Key": "cpuid.XCR0_MASTER_BNDREGS",
            "FeatureName": "cpuid.XCR0_MASTER_BNDREGS",
            "Value": "0"
          },
          {
            "Key": "cpuid.XCR0_MASTER_HI16_ZMM",
            "FeatureName": "cpuid.XCR0_MASTER_HI16_ZMM",
            "Value": "0"
          },
          {
            "Key": "cpuid.XCR0_MASTER_OPMASK",
            "FeatureName": "cpuid.XCR0_MASTER_OPMASK",
            "Value": "0"
          },
          {
            "Key": "cpuid.XCR0_MASTER_PKRU",
            "FeatureName": "cpuid.XCR0_MASTER_PKRU",
            "Value": "0"
          },
          {
            "Key": "cpuid.XCR0_MASTER_SSE",
            "FeatureName": "cpuid.XCR0_MASTER_SSE",
            "Value": "1"
          },
          {
            "Key": "cpuid.XCR0_MASTER_YMM_H",
            "FeatureName": "cpuid.XCR0_MASTER_YMM_H",
            "Value": "1"
          },
          {
            "Key": "cpuid.XCR0_MASTER_ZMM_H",
            "FeatureName": "cpuid.XCR0_MASTER_ZMM_H",
            "Value": "0"
          },
          {
            "Key": "cpuid.XOP",
            "FeatureName": "cpuid.XOP",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVE",
            "FeatureName": "cpuid.XSAVE",
            "Value": "1"
          },
          {
            "Key": "cpuid.XSAVEC",
            "FeatureName": "cpuid.XSAVEC",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVEOPT",
            "FeatureName": "cpuid.XSAVEOPT",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVES",
            "FeatureName": "cpuid.XSAVES",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVE_BNDCSR_ALIGN",
            "FeatureName": "cpuid.XSAVE_BNDCSR_ALIGN",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVE_BNDREGS_ALIGN",
            "FeatureName": "cpuid.XSAVE_BNDREGS_ALIGN",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVE_HI16_ZMM_ALIGN",
            "FeatureName": "cpuid.XSAVE_HI16_ZMM_ALIGN",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVE_OPMASK_ALIGN",
            "FeatureName": "cpuid.XSAVE_OPMASK_ALIGN",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVE_PKRU_ALIGN",
            "FeatureName": "cpuid.XSAVE_PKRU_ALIGN",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVE_YMM_ALIGN",
            "FeatureName": "cpuid.XSAVE_YMM_ALIGN",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVE_ZMM_H_ALIGN",
            "FeatureName": "cpuid.XSAVE_ZMM_H_ALIGN",
            "Value": "0"
          }
        ],
        "MaskedFeatureCapability": [
          {
            "Key": "cpuid.3DNOW",
            "FeatureName": "cpuid.3DNOW",
            "Value": "0"
          },
          {
            "Key": "cpuid.3DNOWPLUS",
            "FeatureName": "cpuid.3DNOWPLUS",
            "Value": "0"
          },
          {
            "Key": "cpuid.3DNPREFETCH",
            "FeatureName": "cpuid.3DNPREFETCH",
            "Value": "1"
          },
          {
            "Key": "cpuid.ABM",
            "FeatureName": "cpuid.ABM",
            "Value": "1"
          },
          {
            "Key": "cpuid.ADX",
            "FeatureName": "cpuid.ADX",
            "Value": "0"
          },
          {
            "Key": "cpuid.AES",
            "FeatureName": "cpuid.AES",
            "Value": "1"
          },
          {
            "Key": "cpuid.AMD",
            "FeatureName": "cpuid.AMD",
            "Value": "0"
          },
          {
            "Key": "cpuid.AVX",
            "FeatureName": "cpuid.AVX",
            "Value": "1"
          },
          {
            "Key": "cpuid.AVX2",
            "FeatureName": "cpuid.AVX2",
            "Value": "1"
          },
          {
            "Key": "cpuid.AVX512BW",
            "FeatureName": "cpuid.AVX512BW",
            "Value": "0"
          },
          {
            "Key": "cpuid.AVX512CD",
            "FeatureName": "cpuid.AVX512CD",
            "Value": "0"
          },
          {
            "Key": "cpuid.AVX512DQ",
            "FeatureName": "cpuid.AVX512DQ",
            "Value": "0"
          },
          {
            "Key": "cpuid.AVX512ER",
            "FeatureName": "cpuid.AVX512ER",
            "Value": "0"
          },
          {
            "Key": "cpuid.AVX512F",
            "FeatureName": "cpuid.AVX512F",
            "Value": "0"
          },
          {
            "Key": "cpuid.AVX512PF",
            "FeatureName": "cpuid.AVX512PF",
            "Value": "0"
          },
          {
            "Key": "cpuid.AVX512VL",
            "FeatureName": "cpuid.AVX512VL",
            "Value": "0"
          },
          {
            "Key": "cpuid.BMI1",
            "FeatureName": "cpuid.BMI1",
            "Value": "0"
          },
          {
            "Key": "cpuid.BMI2",
            "FeatureName": "cpuid.BMI2",
            "Value": "0"
          },
          {
            "Key": "cpuid.CLFLUSHOPT",
            "FeatureName": "cpuid.CLFLUSHOPT",
            "Value": "1"
          },
          {
            "Key": "cpuid.CLWB",
            "FeatureName": "cpuid.CLWB",
            "Value": "0"
          },
          {
            "Key": "cpuid.CLZERO",
            "FeatureName": "cpuid.CLZERO",
            "Value": "0"
          },
          {
            "Key": "cpuid.CMPXCHG16B",
            "FeatureName": "cpuid.CMPXCHG16B",
            "Value": "0"
          },
          {
            "Key": "cpuid.CR8AVAIL",
            "FeatureName": "cpuid.CR8AVAIL",
            "Value": "0"
          },
          {
            "Key": "cpuid.Cyrix",
            "FeatureName": "cpuid.Cyrix",
            "Value": "0"
          },
          {
            "Key": "cpuid.DS",
            "FeatureName": "cpuid.DS",
            "Value": "1"
          },
          {
            "Key": "cpuid.ENFSTRG",
            "FeatureName": "cpuid.ENFSTRG",
            "Value": "0"
          },
          {
            "Key": "cpuid.EXTAPICSPC",
            "FeatureName": "cpuid.EXTAPICSPC",
            "Value": "0"
          },
          {
            "Key": "cpuid.F16C",
            "FeatureName": "cpuid.F16C",
            "Value": "0"
          },
          {
            "Key": "cpuid.FCMD",
            "FeatureName": "cpuid.FCMD",
            "Value": "1"
          },
          {
            "Key": "cpuid.FFXSR",
            "FeatureName": "cpuid.FFXSR",
            "Value": "0"
          },
          {
            "Key": "cpuid.FMA",
            "FeatureName": "cpuid.FMA",
            "Value": "0"
          },
          {
            "Key": "cpuid.FMA4",
            "FeatureName": "cpuid.FMA4",
            "Value": "0"
          },
          {
            "Key": "cpuid.FSGSBASE",
            "FeatureName": "cpuid.FSGSBASE",
            "Value": "1"
          },
          {
            "Key": "cpuid.HLE",
            "FeatureName": "cpuid.HLE",
            "Value": "0"
          },
          {
            "Key": "cpuid.Hygon",
            "FeatureName": "cpuid.Hygon",
            "Value": "0"
          },
          {
            "Key": "cpuid.IBPB",
            "FeatureName": "cpuid.IBPB",
            "Value": "0"
          },
          {
            "Key": "cpuid.IBRS",
            "FeatureName": "cpuid.IBRS",
            "Value": "0"
          },
          {
            "Key": "cpuid.INVPCID",
            "FeatureName": "cpuid.INVPCID",
            "Value": "1"
          },
          {
            "Key": "cpuid.Intel",
            "FeatureName": "cpuid.Intel",
            "Value": "1"
          },
          {
            "Key": "cpuid.LAHF64",
            "FeatureName": "cpuid.LAHF64",
            "Value": "0"
          },
          {
            "Key": "cpuid.LM",
            "FeatureName": "cpuid.LM",
            "Value": "0"
          },
          {
            "Key": "cpuid.MDCLEAR",
            "FeatureName": "cpuid.MDCLEAR",
            "Value": "1"
          },
          {
            "Key": "cpuid.MISALIGNED_SSE",
            "FeatureName": "cpuid.MISALIGNED_SSE",
            "Value": "0"
          },
          {
            "Key": "cpuid.MMXEXT",
            "FeatureName": "cpuid.MMXEXT",
            "Value": "0"
          },
          {
            "Key": "cpuid.MOVBE",
            "FeatureName": "cpuid.MOVBE",
            "Value": "1"
          },
          {
            "Key": "cpuid.MPX",
            "FeatureName": "cpuid.MPX",
            "Value": "0"
          },
          {
            "Key": "cpuid.MWAIT",
            "FeatureName": "cpuid.MWAIT",
            "Value": "0"
          },
          {
            "Key": "cpuid.NX",
            "FeatureName": "cpuid.NX",
            "Value": "1"
          },
          {
            "Key": "cpuid.PCID",
            "FeatureName": "cpuid.PCID",
            "Value": "1"
          },
          {
            "Key": "cpuid.PCLMULQDQ",
            "FeatureName": "cpuid.PCLMULQDQ",
            "Value": "1"
          },
          {
            "Key": "cpuid.PDPE1GB",
            "FeatureName": "cpuid.PDPE1GB",
            "Value": "0"
          },
          {
            "Key": "cpuid.PKU",
            "FeatureName": "cpuid.PKU",
            "Value": "0"
          },
          {
            "Key": "cpuid.POPCNT",
            "FeatureName": "cpuid.POPCNT",
            "Value": "1"
          },
          {
            "Key": "cpuid.PREFETCHWT1",
            "FeatureName": "cpuid.PREFETCHWT1",
            "Value": "0"
          },
          {
            "Key": "cpuid.PSN",
            "FeatureName": "cpuid.PSN",
            "Value": "0"
          },
          {
            "Key": "cpuid.RDRAND",
            "FeatureName": "cpuid.RDRAND",
            "Value": "1"
          },
          {
            "Key": "cpuid.RDSEED",
            "FeatureName": "cpuid.RDSEED",
            "Value": "1"
          },
          {
            "Key": "cpuid.RDTSCP",
            "FeatureName": "cpuid.RDTSCP",
            "Value": "1"
          },
          {
            "Key": "cpuid.RTM",
            "FeatureName": "cpuid.RTM",
            "Value": "0"
          },
          {
            "Key": "cpuid.SHA",
            "FeatureName": "cpuid.SHA",
            "Value": "0"
          },
          {
            "Key": "cpuid.SMAP",
            "FeatureName": "cpuid.SMAP",
            "Value": "0"
          },
          {
            "Key": "cpuid.SMEP",
            "FeatureName": "cpuid.SMEP",
            "Value": "0"
          },
          {
            "Key": "cpuid.SS",
            "FeatureName": "cpuid.SS",
            "Value": "0"
          },
          {
            "Key": "cpuid.SSBD",
            "FeatureName": "cpuid.SSBD",
            "Value": "0"
          },
          {
            "Key": "cpuid.SSE3",
            "FeatureName": "cpuid.SSE3",
            "Value": "1"
          },
          {
            "Key": "cpuid.SSE41",
            "FeatureName": "cpuid.SSE41",
            "Value": "1"
          },
          {
            "Key": "cpuid.SSE42",
            "FeatureName": "cpuid.SSE42",
            "Value": "1"
          },
          {
            "Key": "cpuid.SSE4A",
            "FeatureName": "cpuid.SSE4A",
            "Value": "0"
          },
          {
            "Key": "cpuid.SSSE3",
            "FeatureName": "cpuid.SSSE3",
            "Value": "1"
          },
          {
            "Key": "cpuid.STIBP",
            "FeatureName": "cpuid.STIBP",
            "Value": "0"
          },
          {
            "Key": "cpuid.SVM",
            "FeatureName": "cpuid.SVM",
            "Value": "0"
          },
          {
            "Key": "cpuid.SVM_DECODE_ASSISTS",
            "FeatureName": "cpuid.SVM_DECODE_ASSISTS",
            "Value": "0"
          },
          {
            "Key": "cpuid.SVM_FLUSH_BY_ASID",
            "FeatureName": "cpuid.SVM_FLUSH_BY_ASID",
            "Value": "0"
          },
          {
            "Key": "cpuid.SVM_NPT",
            "FeatureName": "cpuid.SVM_NPT",
            "Value": "0"
          },
          {
            "Key": "cpuid.SVM_NRIP",
            "FeatureName": "cpuid.SVM_NRIP",
            "Value": "0"
          },
          {
            "Key": "cpuid.SVM_VMCB_CLEAN",
            "FeatureName": "cpuid.SVM_VMCB_CLEAN",
            "Value": "0"
          },
          {
            "Key": "cpuid.TBM",
            "FeatureName": "cpuid.TBM",
            "Value": "0"
          },
          {
            "Key": "cpuid.VIA",
            "FeatureName": "cpuid.VIA",
            "Value": "0"
          },
          {
            "Key": "cpuid.VMX",
            "FeatureName": "cpuid.VMX",
            "Value": "0"
          },
          {
            "Key": "cpuid.XCR0_MASTER_BNDCSR",
            "FeatureName": "cpuid.XCR0_MASTER_BNDCSR",
            "Value": "0"
          },
          {
            "Key": "cpuid.XCR0_MASTER_BNDREGS",
            "FeatureName": "cpuid.XCR0_MASTER_BNDREGS",
            "Value": "0"
          },
          {
            "Key": "cpuid.XCR0_MASTER_HI16_ZMM",
            "FeatureName": "cpuid.XCR0_MASTER_HI16_ZMM",
            "Value": "0"
          },
          {
            "Key": "cpuid.XCR0_MASTER_OPMASK",
            "FeatureName": "cpuid.XCR0_MASTER_OPMASK",
            "Value": "0"
          },
          {
            "Key": "cpuid.XCR0_MASTER_PKRU",
            "FeatureName": "cpuid.XCR0_MASTER_PKRU",
            "Value": "0"
          },
          {
            "Key": "cpuid.XCR0_MASTER_SSE",
            "FeatureName": "cpuid.XCR0_MASTER_SSE",
            "Value": "1"
          },
          {
            "Key": "cpuid.XCR0_MASTER_YMM_H",
            "FeatureName": "cpuid.XCR0_MASTER_YMM_H",
            "Value": "1"
          },
          {
            "Key": "cpuid.XCR0_MASTER_ZMM_H",
            "FeatureName": "cpuid.XCR0_MASTER_ZMM_H",
            "Value": "0"
          },
          {
            "Key": "cpuid.XOP",
            "FeatureName": "cpuid.XOP",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVE",
            "FeatureName": "cpuid.XSAVE",
            "Value": "1"
          },
          {
            "Key": "cpuid.XSAVEC",
            "FeatureName": "cpuid.XSAVEC",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVEOPT",
            "FeatureName": "cpuid.XSAVEOPT",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVES",
            "FeatureName": "cpuid.XSAVES",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVE_BNDCSR_ALIGN",
            "FeatureName": "cpuid.XSAVE_BNDCSR_ALIGN",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVE_BNDREGS_ALIGN",
            "FeatureName": "cpuid.XSAVE_BNDREGS_ALIGN",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVE_HI16_ZMM_ALIGN",
            "FeatureName": "cpuid.XSAVE_HI16_ZMM_ALIGN",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVE_OPMASK_ALIGN",
            "FeatureName": "cpuid.XSAVE_OPMASK_ALIGN",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVE_PKRU_ALIGN",
            "FeatureName": "cpuid.XSAVE_PKRU_ALIGN",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVE_YMM_ALIGN",
            "FeatureName": "cpuid.XSAVE_YMM_ALIGN",
            "Value": "0"
          },
          {
            "Key": "cpuid.XSAVE_ZMM_H_ALIGN",
            "FeatureName": "cpuid.XSAVE_ZMM_H_ALIGN",
            "Value": "0"
          }
        ],
        "VFlashConfigInfo": {
          "VFlashResourceConfigInfo": null,
          "VFlashCacheConfigInfo": {
            "VFlashModuleConfigOption": [
              {
                "VFlashModule": "vfc",
                "VFlashModuleVersion": "1.0.0.0",
                "MinSupportedModuleVersion": "1.0.0.0",
                "CacheConsistencyType": {
                  "ValueIsReadonly": true,
                  "ChoiceInfo": [
                    {
                      "Label": "strong",
                      "Summary": "Cache data consistency is guaranteed after a crash",
                      "Key": "strong"
                    }
                  ],
                  "DefaultIndex": 0
                },
                "CacheMode": {
                  "ValueIsReadonly": true,
                  "ChoiceInfo": [
                    {
                      "Label": "write_thru",
                      "Summary": "Writes to the cache cause writes to the underlying storage",
                      "Key": "write_thru"
                    }
                  ],
                  "DefaultIndex": 0
                },
                "BlockSizeInKBOption": {
                  "ValueIsReadonly": null,
                  "Min": 4,
                  "Max": 1024,
                  "DefaultValue": 8
                },
                "ReservationInMBOption": {
                  "ValueIsReadonly": null,
                  "Min": 4,
                  "Max": 409600,
                  "DefaultValue": 4
                },
                "MaxDiskSizeInKB": 17179869184
              }
            ],
            "DefaultVFlashModule": "vfc",
            "SwapCacheReservationInGB": 0
          }
        },
        "VsanHostConfig": {
          "Enabled": false,
          "HostSystem": {
            "Type": "HostSystem",
            "Value": "ha-host"
          },
          "ClusterInfo": {
            "Uuid": "",
            "NodeUuid": ""
          },
          "StorageInfo": {
            "AutoClaimStorage": false,
            "DiskMapping": null,
            "DiskMapInfo": null,
            "ChecksumEnabled": null
          },
          "NetworkInfo": {
            "Port": null
          },
          "FaultDomainInfo": {
            "Name": ""
          }
        },
        "DomainList": null,
        "ScriptCheckSum": "UmdoQm8wOGYzWXJFbVJLNGprUXVwcUJmS1lzPQ==",
        "HostConfigCheckSum": "R2JwMEIwVUdHejhIck1GVHNaSTNpeHhCaCs0PQ==",
        "DescriptionTreeCheckSum": null,
        "GraphicsInfo": null,
        "SharedPassthruGpuTypes": null,
        "GraphicsConfig": {
          "HostDefaultGraphicsType": "shared",
          "SharedPassthruAssignmentPolicy": "performance",
          "DeviceType": null
        },
        "SharedGpuCapabilities": null,
        "IoFilterInfo": [
          {
            "Id": "VMW_spm_1.0.0",
            "Name": "spm",
            "Vendor": "VMW",
            "Version": "1.0.230",
            "Type": "datastoreIoControl",
            "Summary": "VMware Storage I/O Control",
            "ReleaseDate": "2016-07-21",
            "Available": true
          },
          {
            "Id": "VMW_vmwarevmcrypt_1.0.0",
            "Name": "vmwarevmcrypt",
            "Vendor": "VMW",
            "Version": "1.0.0",
            "Type": "encryption",
            "Summary": "VMcrypt IO Filter",
            "ReleaseDate": "2016-07-21",
            "Available": true
          }
        ],
        "SriovDevicePool": null,
        "AssignableHardwareBinding": null,
        "AssignableHardwareConfig": null
      },
      "Vm": [
        {
          "Type": "VirtualMachine",
          "Value": "1"
        },
        {
          "Type": "VirtualMachine",
          "Value": "2"
        }
      ],
      "Datastore": [
        {
          "Type": "Datastore",
          "Value": "60c4dd35-141ece76-d699-0800272601f9"
        }
      ],
      "Network": [
        {
          "Type": "Network",
          "Value": "HaNetwork-VM Network"
        },
        {
          "Type": "Network",
          "Value": "HaNetwork-VM Network2"
        }
      ],
      "DatastoreBrowser": {
        "Type": "HostDatastoreBrowser",
        "Value": "ha-host-datastorebrowser"
      },
      "SystemResources": {
        "Key": "host",
        "Config": {
          "Entity": null,
          "ChangeVersion": "",
          "LastModified": null,
          "CpuAllocation": {
            "Reservation": 4608,
            "ExpandableReservation": false,
            "Limit": 4608,
            "Shares": {
              "Shares": 4096000,
              "Level": "custom"
            },
            "OverheadLimit": 0
          },
          "MemoryAllocation": {
            "Reservation": 4072,
            "ExpandableReservation": false,
            "Limit": 4072,
            "Shares": {
              "Shares": 2147483647,
              "Level": "custom"
            },
            "OverheadLimit": 0
          },
          "ScaleDescendantsShares": ""
        },
        "Child": [
          {
            "Key": "host/system",
            "Config": {
              "Entity": null,
              "ChangeVersion": "",
              "LastModified": null,
              "CpuAllocation": {
                "Reservation": 230,
                "ExpandableReservation": true,
                "Limit": -1,
                "Shares": {
                  "Shares": 500,
                  "Level": "custom"
                },
                "OverheadLimit": -1
              },
              "MemoryAllocation": {
                "Reservation": 1339,
                "ExpandableReservation": true,
                "Limit": -1,
                "Shares": {
                  "Shares": 500,
                  "Level": "custom"
                },
                "OverheadLimit": -1
              },
              "ScaleDescendantsShares": ""
            },
            "Child": [
              {
                "Key": "host/system/kernel",
                "Config": {
                  "Entity": null,
                  "ChangeVersion": "",
                  "LastModified": null,
                  "CpuAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": false,
                    "Limit": 0,
                    "Shares": {
                      "Shares": 0,
                      "Level": "custom"
                    },
                    "OverheadLimit": 0
                  },
                  "MemoryAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 1000,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "ScaleDescendantsShares": ""
                },
                "Child": [
                  {
                    "Key": "host/system/kernel/visorfsPager",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 29,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/system/kernel/root",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 32,
                        "ExpandableReservation": true,
                        "Limit": 32,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/system/kernel/etc",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 28,
                        "ExpandableReservation": true,
                        "Limit": 28,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/system/kernel/opt",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 32,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/system/kernel/var",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 5,
                        "ExpandableReservation": true,
                        "Limit": 48,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/system/kernel/vmkplexer-0x43013b22c070",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 0,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/system/kernel/vmkplexer-0x43013b22c070/vmklinux-0x43013b22c0f0",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": -1
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "custom"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/system/kernel/tmp",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 2,
                        "ExpandableReservation": true,
                        "Limit": 256,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/system/kernel/iofilters",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 32,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/system/kernel/shm",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 1024,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/system/kernel/hostdstats",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 69,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  }
                ]
              },
              {
                "Key": "host/system/helper",
                "Config": {
                  "Entity": null,
                  "ChangeVersion": "",
                  "LastModified": null,
                  "CpuAllocation": {
                    "Reservation": 69,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 1000,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "MemoryAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": false,
                    "Limit": 0,
                    "Shares": {
                      "Shares": 0,
                      "Level": "custom"
                    },
                    "OverheadLimit": 0
                  },
                  "ScaleDescendantsShares": ""
                },
                "Child": null
              },
              {
                "Key": "host/system/lfhelper",
                "Config": {
                  "Entity": null,
                  "ChangeVersion": "",
                  "LastModified": null,
                  "CpuAllocation": {
                    "Reservation": 69,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 1000,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "MemoryAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": false,
                    "Limit": 0,
                    "Shares": {
                      "Shares": 0,
                      "Level": "custom"
                    },
                    "OverheadLimit": 0
                  },
                  "ScaleDescendantsShares": ""
                },
                "Child": null
              },
              {
                "Key": "host/system/drivers",
                "Config": {
                  "Entity": null,
                  "ChangeVersion": "",
                  "LastModified": null,
                  "CpuAllocation": {
                    "Reservation": 69,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 1000,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "MemoryAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": false,
                    "Limit": 0,
                    "Shares": {
                      "Shares": 0,
                      "Level": "custom"
                    },
                    "OverheadLimit": 0
                  },
                  "ScaleDescendantsShares": ""
                },
                "Child": null
              },
              {
                "Key": "host/system/ft",
                "Config": {
                  "Entity": null,
                  "ChangeVersion": "",
                  "LastModified": null,
                  "CpuAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 1000,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "MemoryAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": false,
                    "Limit": 0,
                    "Shares": {
                      "Shares": 0,
                      "Level": "custom"
                    },
                    "OverheadLimit": 0
                  },
                  "ScaleDescendantsShares": ""
                },
                "Child": null
              },
              {
                "Key": "host/system/vmotion",
                "Config": {
                  "Entity": null,
                  "ChangeVersion": "",
                  "LastModified": null,
                  "CpuAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 1000,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "MemoryAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": false,
                    "Limit": 0,
                    "Shares": {
                      "Shares": 0,
                      "Level": "custom"
                    },
                    "OverheadLimit": 0
                  },
                  "ScaleDescendantsShares": ""
                },
                "Child": null
              },
              {
                "Key": "host/system/svmotion",
                "Config": {
                  "Entity": null,
                  "ChangeVersion": "",
                  "LastModified": null,
                  "CpuAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 1000,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "MemoryAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": false,
                    "Limit": 0,
                    "Shares": {
                      "Shares": 0,
                      "Level": "custom"
                    },
                    "OverheadLimit": 0
                  },
                  "ScaleDescendantsShares": ""
                },
                "Child": null
              },
              {
                "Key": "host/system/vmkapimod",
                "Config": {
                  "Entity": null,
                  "ChangeVersion": "",
                  "LastModified": null,
                  "CpuAllocation": {
                    "Reservation": 69,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 1000,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "MemoryAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": false,
                    "Limit": 0,
                    "Shares": {
                      "Shares": 1000,
                      "Level": "custom"
                    },
                    "OverheadLimit": 0
                  },
                  "ScaleDescendantsShares": ""
                },
                "Child": null
              }
            ]
          },
          {
            "Key": "host/vim",
            "Config": {
              "Entity": null,
              "ChangeVersion": "",
              "LastModified": null,
              "CpuAllocation": {
                "Reservation": 1820,
                "ExpandableReservation": true,
                "Limit": -1,
                "Shares": {
                  "Shares": 500,
                  "Level": "custom"
                },
                "OverheadLimit": -1
              },
              "MemoryAllocation": {
                "Reservation": 0,
                "ExpandableReservation": true,
                "Limit": -1,
                "Shares": {
                  "Shares": 500,
                  "Level": "custom"
                },
                "OverheadLimit": -1
              },
              "ScaleDescendantsShares": ""
            },
            "Child": [
              {
                "Key": "host/vim/vmci",
                "Config": {
                  "Entity": null,
                  "ChangeVersion": "",
                  "LastModified": null,
                  "CpuAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": false,
                    "Limit": 0,
                    "Shares": {
                      "Shares": 0,
                      "Level": "custom"
                    },
                    "OverheadLimit": 0
                  },
                  "MemoryAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 1000,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "ScaleDescendantsShares": ""
                },
                "Child": null
              },
              {
                "Key": "host/vim/vmvisor",
                "Config": {
                  "Entity": null,
                  "ChangeVersion": "",
                  "LastModified": null,
                  "CpuAllocation": {
                    "Reservation": 806,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 1000,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "MemoryAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 1000,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "ScaleDescendantsShares": ""
                },
                "Child": [
                  {
                    "Key": "host/vim/vmvisor/aam",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/boot",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 200,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/dcui",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 500,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 45,
                        "ExpandableReservation": true,
                        "Limit": 101,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/dcui/dcui.2099194",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/dhclient",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 20,
                        "ExpandableReservation": true,
                        "Limit": 20,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/dhclient/dhclient-uw.2099588",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/dhclientrelease",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/hbrca",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/hostd",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 622,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 266,
                        "ExpandableReservation": true,
                        "Limit": 266,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/hostd/sh.2098483",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/hostd/hostd.2098493",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/hostd/sh.2098503",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/hostd/rhttpproxy.2098513",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/hostd-probe",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 28,
                        "ExpandableReservation": true,
                        "Limit": 28,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/hostd-probe/stats",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": -1
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": [
                          {
                            "Key": "host/vim/vmvisor/hostd-probe/stats/awk",
                            "Config": {
                              "Entity": null,
                              "ChangeVersion": "",
                              "LastModified": null,
                              "CpuAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 1000,
                                  "Level": "custom"
                                },
                                "OverheadLimit": -1
                              },
                              "MemoryAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 0,
                                  "Level": "normal"
                                },
                                "OverheadLimit": -1
                              },
                              "ScaleDescendantsShares": ""
                            },
                            "Child": null
                          },
                          {
                            "Key": "host/vim/vmvisor/hostd-probe/stats/head",
                            "Config": {
                              "Entity": null,
                              "ChangeVersion": "",
                              "LastModified": null,
                              "CpuAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 1000,
                                  "Level": "custom"
                                },
                                "OverheadLimit": -1
                              },
                              "MemoryAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 0,
                                  "Level": "normal"
                                },
                                "OverheadLimit": -1
                              },
                              "ScaleDescendantsShares": ""
                            },
                            "Child": null
                          },
                          {
                            "Key": "host/vim/vmvisor/hostd-probe/stats/logger",
                            "Config": {
                              "Entity": null,
                              "ChangeVersion": "",
                              "LastModified": null,
                              "CpuAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 1000,
                                  "Level": "custom"
                                },
                                "OverheadLimit": -1
                              },
                              "MemoryAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 0,
                                  "Level": "normal"
                                },
                                "OverheadLimit": -1
                              },
                              "ScaleDescendantsShares": ""
                            },
                            "Child": null
                          },
                          {
                            "Key": "host/vim/vmvisor/hostd-probe/stats/ls",
                            "Config": {
                              "Entity": null,
                              "ChangeVersion": "",
                              "LastModified": null,
                              "CpuAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 1000,
                                  "Level": "custom"
                                },
                                "OverheadLimit": -1
                              },
                              "MemoryAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 0,
                                  "Level": "normal"
                                },
                                "OverheadLimit": -1
                              },
                              "ScaleDescendantsShares": ""
                            },
                            "Child": null
                          },
                          {
                            "Key": "host/vim/vmvisor/hostd-probe/stats/pgrep",
                            "Config": {
                              "Entity": null,
                              "ChangeVersion": "",
                              "LastModified": null,
                              "CpuAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 1000,
                                  "Level": "custom"
                                },
                                "OverheadLimit": -1
                              },
                              "MemoryAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 0,
                                  "Level": "normal"
                                },
                                "OverheadLimit": -1
                              },
                              "ScaleDescendantsShares": ""
                            },
                            "Child": null
                          },
                          {
                            "Key": "host/vim/vmvisor/hostd-probe/stats/probe",
                            "Config": {
                              "Entity": null,
                              "ChangeVersion": "",
                              "LastModified": null,
                              "CpuAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 1000,
                                  "Level": "custom"
                                },
                                "OverheadLimit": -1
                              },
                              "MemoryAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 0,
                                  "Level": "normal"
                                },
                                "OverheadLimit": -1
                              },
                              "ScaleDescendantsShares": ""
                            },
                            "Child": null
                          },
                          {
                            "Key": "host/vim/vmvisor/hostd-probe/stats/sh",
                            "Config": {
                              "Entity": null,
                              "ChangeVersion": "",
                              "LastModified": null,
                              "CpuAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 1000,
                                  "Level": "custom"
                                },
                                "OverheadLimit": -1
                              },
                              "MemoryAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 0,
                                  "Level": "normal"
                                },
                                "OverheadLimit": -1
                              },
                              "ScaleDescendantsShares": ""
                            },
                            "Child": null
                          },
                          {
                            "Key": "host/vim/vmvisor/hostd-probe/stats/vmkbacktrace",
                            "Config": {
                              "Entity": null,
                              "ChangeVersion": "",
                              "LastModified": null,
                              "CpuAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 1000,
                                  "Level": "custom"
                                },
                                "OverheadLimit": -1
                              },
                              "MemoryAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 0,
                                  "Level": "normal"
                                },
                                "OverheadLimit": -1
                              },
                              "ScaleDescendantsShares": ""
                            },
                            "Child": null
                          },
                          {
                            "Key": "host/vim/vmvisor/hostd-probe/stats/vmware",
                            "Config": {
                              "Entity": null,
                              "ChangeVersion": "",
                              "LastModified": null,
                              "CpuAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 1000,
                                  "Level": "custom"
                                },
                                "OverheadLimit": -1
                              },
                              "MemoryAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 0,
                                  "Level": "normal"
                                },
                                "OverheadLimit": -1
                              },
                              "ScaleDescendantsShares": ""
                            },
                            "Child": null
                          }
                        ]
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/hostdCgiServer",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 30,
                        "ExpandableReservation": true,
                        "Limit": 50,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/hostdCgiServer/sh.2098434",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/hostdCgiServer/hostdCgiServer.2098444",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/imaShim32d",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/init",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 500,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 109,
                        "ExpandableReservation": true,
                        "Limit": 220,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/init/init.2097390",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/init/busybox.2098015",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/init/busybox.2098058",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/lacpd",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 230,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 13,
                        "ExpandableReservation": true,
                        "Limit": 13,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/lacpd/sh.2097670",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/lacpd/net-lacp.2097680",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/lbt",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 691,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 13,
                        "ExpandableReservation": true,
                        "Limit": 13,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/lbt/sh.2098670",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/lbt/net-lbt.2098688",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/likewise",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 691,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/loadESX",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/logging",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 161,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 106,
                        "ExpandableReservation": true,
                        "Limit": 106,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/logging/vmsyslogd.2097442",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/logging/vmsyslogd.2097443",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/memScrubber",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/net-daemons",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 230,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 15,
                        "ExpandableReservation": true,
                        "Limit": 50,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/net-daemons/sh.2098895",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/net-daemons/dcbd.2098904",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/net-daemons/sh.2098913",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/net-daemons/net-cdp.2098923",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/netcpa",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/petronas",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/nfcd",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 8,
                        "ExpandableReservation": true,
                        "Limit": 8,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/nfsgssd",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/nicmgmtd",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 15,
                        "ExpandableReservation": true,
                        "Limit": 15,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/nicmgmtd/sh.2098958",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/nicmgmtd/nicmgmtd.2098968",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/nscd",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 230,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 20,
                        "ExpandableReservation": true,
                        "Limit": 20,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/nscd/sh.2098935",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/nscd/nscd.2098944",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/ntpd",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 15,
                        "ExpandableReservation": true,
                        "Limit": 15,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/osfsd",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/pcscd",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/plugins",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 691,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/rabbitmqproxy",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 691,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/sensord",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 10,
                        "ExpandableReservation": true,
                        "Limit": 10,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/sfcb",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 691,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/sfcb_aux",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 691,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/sioc",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 691,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 56,
                        "ExpandableReservation": true,
                        "Limit": 56,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/sioc/sh.2098550",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/sioc/sdrsInjector.2098560",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/sioc/sh.2098609",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/sioc/storageRM.2098628",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/slp",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 691,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 11,
                        "ExpandableReservation": true,
                        "Limit": 11,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/slp/slpd.2098880",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/smartd",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 230,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 15,
                        "ExpandableReservation": true,
                        "Limit": 50,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/smartd/sh.2098990",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/smartd/smartd.2099000",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/snmpd",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 230,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/swapobjd",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 16,
                        "ExpandableReservation": true,
                        "Limit": 16,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/swapobjd/sh.2098310",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/swapobjd/swapobjd.2098320",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/upitd",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/upittraced",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/usbArbitrator",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 18,
                        "ExpandableReservation": true,
                        "Limit": 18,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/usbArbitrator/sh.2098220",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/usbArbitrator/vmware-usbarbitrator.2098228",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/uwdaemons",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/vmfstraced",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/vmkdevmgr",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 8,
                        "ExpandableReservation": true,
                        "Limit": 42,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/vmkdevmgr/vmkdevmgr.2097558",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/vmkeventd",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 25,
                        "ExpandableReservation": true,
                        "Limit": 92,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/vmkeventd/sh.2097490",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/vmkeventd/vmkeventd.2097497",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/vmkiscsid",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/vmsupport",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 1500,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/vobd",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 691,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 28,
                        "ExpandableReservation": true,
                        "Limit": 28,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/vobd/sh.2097454",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/vobd/vobd.2097465",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/vpxa",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 461,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 180,
                        "ExpandableReservation": true,
                        "Limit": 180,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vmvisor/vpxa/sh.2099009",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      },
                      {
                        "Key": "host/vim/vmvisor/vpxa/vpxa.2099019",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": false,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 1000,
                              "Level": "custom"
                            },
                            "OverheadLimit": 0
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  },
                  {
                    "Key": "host/vim/vmvisor/vvold",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/vvoltraced",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/vim/vmvisor/wsman",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 691,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": 0,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  }
                ]
              },
              {
                "Key": "host/vim/vimuser",
                "Config": {
                  "Entity": null,
                  "ChangeVersion": "",
                  "LastModified": null,
                  "CpuAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 250,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "MemoryAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 1000,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "ScaleDescendantsShares": ""
                },
                "Child": [
                  {
                    "Key": "host/vim/vimuser/terminal",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 500,
                          "Level": "custom"
                        },
                        "OverheadLimit": -1
                      },
                      "MemoryAllocation": {
                        "Reservation": 45,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": [
                      {
                        "Key": "host/vim/vimuser/terminal/shell",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 500,
                              "Level": "custom"
                            },
                            "OverheadLimit": -1
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": 512,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": [
                          {
                            "Key": "host/vim/vimuser/terminal/shell/sh.2099193",
                            "Config": {
                              "Entity": null,
                              "ChangeVersion": "",
                              "LastModified": null,
                              "CpuAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": false,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 1000,
                                  "Level": "custom"
                                },
                                "OverheadLimit": 0
                              },
                              "MemoryAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 0,
                                  "Level": "normal"
                                },
                                "OverheadLimit": -1
                              },
                              "ScaleDescendantsShares": ""
                            },
                            "Child": null
                          },
                          {
                            "Key": "host/vim/vimuser/terminal/shell/getty.2099213",
                            "Config": {
                              "Entity": null,
                              "ChangeVersion": "",
                              "LastModified": null,
                              "CpuAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": false,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 1000,
                                  "Level": "custom"
                                },
                                "OverheadLimit": 0
                              },
                              "MemoryAllocation": {
                                "Reservation": 0,
                                "ExpandableReservation": true,
                                "Limit": -1,
                                "Shares": {
                                  "Shares": 0,
                                  "Level": "normal"
                                },
                                "OverheadLimit": -1
                              },
                              "ScaleDescendantsShares": ""
                            },
                            "Child": null
                          }
                        ]
                      },
                      {
                        "Key": "host/vim/vimuser/terminal/ssh",
                        "Config": {
                          "Entity": null,
                          "ChangeVersion": "",
                          "LastModified": null,
                          "CpuAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": -1,
                            "Shares": {
                              "Shares": 500,
                              "Level": "custom"
                            },
                            "OverheadLimit": -1
                          },
                          "MemoryAllocation": {
                            "Reservation": 0,
                            "ExpandableReservation": true,
                            "Limit": 800,
                            "Shares": {
                              "Shares": 0,
                              "Level": "normal"
                            },
                            "OverheadLimit": -1
                          },
                          "ScaleDescendantsShares": ""
                        },
                        "Child": null
                      }
                    ]
                  }
                ]
              },
              {
                "Key": "host/vim/tmp",
                "Config": {
                  "Entity": null,
                  "ChangeVersion": "",
                  "LastModified": null,
                  "CpuAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 250,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "MemoryAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 1000,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "ScaleDescendantsShares": ""
                },
                "Child": null
              }
            ]
          },
          {
            "Key": "host/iofilters",
            "Config": {
              "Entity": null,
              "ChangeVersion": "",
              "LastModified": null,
              "CpuAllocation": {
                "Reservation": 0,
                "ExpandableReservation": true,
                "Limit": -1,
                "Shares": {
                  "Shares": 1000,
                  "Level": "custom"
                },
                "OverheadLimit": -1
              },
              "MemoryAllocation": {
                "Reservation": 0,
                "ExpandableReservation": true,
                "Limit": -1,
                "Shares": {
                  "Shares": 500,
                  "Level": "custom"
                },
                "OverheadLimit": -1
              },
              "ScaleDescendantsShares": ""
            },
            "Child": [
              {
                "Key": "host/iofilters/iofiltervpd",
                "Config": {
                  "Entity": null,
                  "ChangeVersion": "",
                  "LastModified": null,
                  "CpuAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 1000,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "MemoryAllocation": {
                    "Reservation": 25,
                    "ExpandableReservation": true,
                    "Limit": 25,
                    "Shares": {
                      "Shares": 0,
                      "Level": "normal"
                    },
                    "OverheadLimit": -1
                  },
                  "ScaleDescendantsShares": ""
                },
                "Child": [
                  {
                    "Key": "host/iofilters/iofiltervpd/sh.2098268",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": false,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": 0
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  },
                  {
                    "Key": "host/iofilters/iofiltervpd/ioFilterVPServer.2098282",
                    "Config": {
                      "Entity": null,
                      "ChangeVersion": "",
                      "LastModified": null,
                      "CpuAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": false,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 1000,
                          "Level": "custom"
                        },
                        "OverheadLimit": 0
                      },
                      "MemoryAllocation": {
                        "Reservation": 0,
                        "ExpandableReservation": true,
                        "Limit": -1,
                        "Shares": {
                          "Shares": 0,
                          "Level": "normal"
                        },
                        "OverheadLimit": -1
                      },
                      "ScaleDescendantsShares": ""
                    },
                    "Child": null
                  }
                ]
              },
              {
                "Key": "host/iofilters/spm",
                "Config": {
                  "Entity": null,
                  "ChangeVersion": "",
                  "LastModified": null,
                  "CpuAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 1000,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "MemoryAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 0,
                      "Level": "normal"
                    },
                    "OverheadLimit": -1
                  },
                  "ScaleDescendantsShares": ""
                },
                "Child": null
              },
              {
                "Key": "host/iofilters/vmwarevmcrypt",
                "Config": {
                  "Entity": null,
                  "ChangeVersion": "",
                  "LastModified": null,
                  "CpuAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 1000,
                      "Level": "custom"
                    },
                    "OverheadLimit": -1
                  },
                  "MemoryAllocation": {
                    "Reservation": 0,
                    "ExpandableReservation": true,
                    "Limit": -1,
                    "Shares": {
                      "Shares": 0,
                      "Level": "normal"
                    },
                    "OverheadLimit": -1
                  },
                  "ScaleDescendantsShares": ""
                },
                "Child": null
              }
            ]
          },
          {
            "Key": "host/user",
            "Config": {
              "Entity": null,
              "ChangeVersion": "",
              "LastModified": null,
              "CpuAllocation": {
                "Reservation": 0,
                "ExpandableReservation": true,
                "Limit": -1,
                "Shares": {
                  "Shares": 9000,
                  "Level": "custom"
                },
                "OverheadLimit": -1
              },
              "MemoryAllocation": {
                "Reservation": 0,
                "ExpandableReservation": true,
                "Limit": -1,
                "Shares": {
                  "Shares": 9000,
                  "Level": "custom"
                },
                "OverheadLimit": -1
              },
              "ScaleDescendantsShares": ""
            },
            "Child": null
          }
        ]
      },
      "AnswerFileValidationState": null,
      "AnswerFileValidationResult": null
    }
  ]
}
```

```
$ govc host.autostart.info
VM  StartAction  StartDelay  StartOrder  StopAction  StopDelay  WaitForHeartbeat
```

```json
$ govc host.autostart.info  --json
{
  "Self": {
    "Type": "HostAutoStartManager",
    "Value": "ha-autostart-mgr"
  },
  "Config": {
    "Defaults": {
      "Enabled": null,
      "StartDelay": 120,
      "StopDelay": 120,
      "WaitForHeartbeat": false,
      "StopAction": "PowerOff"
    },
    "PowerInfo": null
  }
}
```

```
$ govc host.cert.info
Certificate Status:          unknown
Issued To:                   
  Common Name (CN):          localhost.localdomain
  Organization (O):          VMware\
  Organizational Unit (OU):  VMware ESX Server Default Certificate
Issued By:                   
  Common Name (CN):          <Not Part Of Certificate>
  Organization (O):          VMware Installer
  Organizational Unit (OU):  <Not Part Of Certificate>
Validity Period:             
  Issued On:                 2021-06-12 16:15:05 +0000 UTC
  Expires On:                2032-12-11 16:15:05 +0000 UTC
```

```
$ govc host.date.info
Current date and time:  Sun Jun 13 12:02:58 UTC 2021
NTP client status:      Disabled
NTP service status:     Stopped
NTP servers:            None
```

```json
$ govc host.date.info --json
{
  "TimeZone": {
    "Key": "UTC",
    "Name": "UTC",
    "Description": "UTC",
    "GmtOffset": 0
  },
  "SystemClockProtocol": "",
  "NtpConfig": {
    "Server": null,
    "ConfigFile": [
      "restrict default nomodify notrap nopeer noquery",
      "restrict 127.0.0.1",
      "driftfile /etc/ntp.drift"
    ]
  },
  "Service": {
    "Key": "ntpd",
    "Label": "NTP Daemon",
    "Required": false,
    "Uninstallable": false,
    "Running": false,
    "Ruleset": [
      "ntpClient"
    ],
    "Policy": "off",
    "SourcePackage": {
      "SourcePackageName": "esx-base",
      "Description": "This VIB contains all of the base functionality of vSphere ESXi."
    }
  },
  "Current": "2021-06-13T12:02:58.276514Z"
}
```

```
$ govc host.portgroup.info
Name:                    VM Network
Virtual switch:          vSwitch0
VLAN ID:                 0
Active ports:            0
Allow promiscuous mode:  No
Allow forged transmits:  Yes
Allow MAC changes:       Yes

Name:                    Management Network
Virtual switch:          vSwitch0
VLAN ID:                 0
Active ports:            1
Allow promiscuous mode:  No
Allow forged transmits:  Yes
Allow MAC changes:       Yes

Name:                    VM Network2
Virtual switch:          vSwitch1
VLAN ID:                 0
Active ports:            0
Allow promiscuous mode:  No
Allow forged transmits:  No
Allow MAC changes:       No
```

```json
$ govc host.portgroup.info --json
{
  "Portgroup": [
    {
      "Key": "key-vim.host.PortGroup-VM Network",
      "Port": null,
      "Vswitch": "key-vim.host.VirtualSwitch-vSwitch0",
      "ComputedPolicy": {
        "Security": {
          "AllowPromiscuous": false,
          "MacChanges": true,
          "ForgedTransmits": true
        },
        "NicTeaming": {
          "Policy": "loadbalance_srcid",
          "ReversePolicy": true,
          "NotifySwitches": true,
          "RollingOrder": false,
          "FailureCriteria": {
            "CheckSpeed": "minimum",
            "Speed": 10,
            "CheckDuplex": false,
            "FullDuplex": false,
            "CheckErrorPercent": false,
            "Percentage": 0,
            "CheckBeacon": false
          },
          "NicOrder": {
            "ActiveNic": [
              "vmnic0"
            ],
            "StandbyNic": null
          }
        },
        "OffloadPolicy": {
          "CsumOffload": true,
          "TcpSegmentation": true,
          "ZeroCopyXmit": true
        },
        "ShapingPolicy": {
          "Enabled": false,
          "AverageBandwidth": 0,
          "PeakBandwidth": 0,
          "BurstSize": 0
        }
      },
      "Spec": {
        "Name": "VM Network",
        "VlanId": 0,
        "VswitchName": "vSwitch0",
        "Policy": {
          "Security": {
            "AllowPromiscuous": null,
            "MacChanges": null,
            "ForgedTransmits": null
          },
          "NicTeaming": {
            "Policy": "",
            "ReversePolicy": null,
            "NotifySwitches": null,
            "RollingOrder": null,
            "FailureCriteria": {
              "CheckSpeed": "",
              "Speed": 0,
              "CheckDuplex": null,
              "FullDuplex": null,
              "CheckErrorPercent": null,
              "Percentage": 0,
              "CheckBeacon": null
            },
            "NicOrder": null
          },
          "OffloadPolicy": {
            "CsumOffload": null,
            "TcpSegmentation": null,
            "ZeroCopyXmit": null
          },
          "ShapingPolicy": {
            "Enabled": null,
            "AverageBandwidth": 0,
            "PeakBandwidth": 0,
            "BurstSize": 0
          }
        }
      }
    },
    {
      "Key": "key-vim.host.PortGroup-Management Network",
      "Port": [
        {
          "Key": "key-vim.host.PortGroup.Port-33554436",
          "Mac": [
            "08:00:27:26:01:f9"
          ],
          "Type": "host"
        }
      ],
      "Vswitch": "key-vim.host.VirtualSwitch-vSwitch0",
      "ComputedPolicy": {
        "Security": {
          "AllowPromiscuous": false,
          "MacChanges": true,
          "ForgedTransmits": true
        },
        "NicTeaming": {
          "Policy": "loadbalance_srcid",
          "ReversePolicy": true,
          "NotifySwitches": true,
          "RollingOrder": false,
          "FailureCriteria": {
            "CheckSpeed": "minimum",
            "Speed": 10,
            "CheckDuplex": false,
            "FullDuplex": false,
            "CheckErrorPercent": false,
            "Percentage": 0,
            "CheckBeacon": false
          },
          "NicOrder": {
            "ActiveNic": [
              "vmnic0"
            ],
            "StandbyNic": null
          }
        },
        "OffloadPolicy": {
          "CsumOffload": true,
          "TcpSegmentation": true,
          "ZeroCopyXmit": true
        },
        "ShapingPolicy": {
          "Enabled": false,
          "AverageBandwidth": 0,
          "PeakBandwidth": 0,
          "BurstSize": 0
        }
      },
      "Spec": {
        "Name": "Management Network",
        "VlanId": 0,
        "VswitchName": "vSwitch0",
        "Policy": {
          "Security": {
            "AllowPromiscuous": null,
            "MacChanges": null,
            "ForgedTransmits": null
          },
          "NicTeaming": {
            "Policy": "loadbalance_srcid",
            "ReversePolicy": null,
            "NotifySwitches": true,
            "RollingOrder": false,
            "FailureCriteria": {
              "CheckSpeed": "",
              "Speed": 0,
              "CheckDuplex": null,
              "FullDuplex": null,
              "CheckErrorPercent": null,
              "Percentage": 0,
              "CheckBeacon": false
            },
            "NicOrder": {
              "ActiveNic": [
                "vmnic0"
              ],
              "StandbyNic": null
            }
          },
          "OffloadPolicy": {
            "CsumOffload": null,
            "TcpSegmentation": null,
            "ZeroCopyXmit": null
          },
          "ShapingPolicy": {
            "Enabled": null,
            "AverageBandwidth": 0,
            "PeakBandwidth": 0,
            "BurstSize": 0
          }
        }
      }
    },
    {
      "Key": "key-vim.host.PortGroup-VM Network2",
      "Port": null,
      "Vswitch": "key-vim.host.VirtualSwitch-vSwitch1",
      "ComputedPolicy": {
        "Security": {
          "AllowPromiscuous": false,
          "MacChanges": false,
          "ForgedTransmits": false
        },
        "NicTeaming": {
          "Policy": "loadbalance_srcid",
          "ReversePolicy": true,
          "NotifySwitches": true,
          "RollingOrder": false,
          "FailureCriteria": {
            "CheckSpeed": "minimum",
            "Speed": 10,
            "CheckDuplex": false,
            "FullDuplex": false,
            "CheckErrorPercent": false,
            "Percentage": 0,
            "CheckBeacon": false
          },
          "NicOrder": {
            "ActiveNic": [
              "vmnic1"
            ],
            "StandbyNic": null
          }
        },
        "OffloadPolicy": {
          "CsumOffload": true,
          "TcpSegmentation": true,
          "ZeroCopyXmit": true
        },
        "ShapingPolicy": {
          "Enabled": false,
          "AverageBandwidth": 0,
          "PeakBandwidth": 0,
          "BurstSize": 0
        }
      },
      "Spec": {
        "Name": "VM Network2",
        "VlanId": 0,
        "VswitchName": "vSwitch1",
        "Policy": {
          "Security": {
            "AllowPromiscuous": null,
            "MacChanges": null,
            "ForgedTransmits": null
          },
          "NicTeaming": {
            "Policy": "",
            "ReversePolicy": null,
            "NotifySwitches": null,
            "RollingOrder": null,
            "FailureCriteria": {
              "CheckSpeed": "",
              "Speed": 0,
              "CheckDuplex": null,
              "FullDuplex": null,
              "CheckErrorPercent": null,
              "Percentage": 0,
              "CheckBeacon": null
            },
            "NicOrder": null
          },
          "OffloadPolicy": {
            "CsumOffload": null,
            "TcpSegmentation": null,
            "ZeroCopyXmit": null
          },
          "ShapingPolicy": {
            "Enabled": null,
            "AverageBandwidth": 0,
            "PeakBandwidth": 0,
            "BurstSize": 0
          }
        }
      }
    }
  ]
}
```

```
$ govc host.storage.info
Name                                                                                           Type   Capacity  Model  
/vmfs/devices/disks/t10.ATA_____VBOX_HARDDISK___________________________VB22cd9e3f2Df90775ba_  disk   10.0GB    VBOX HARDDISK    (local)
/vmfs/devices/cdrom/mpx.vmhba64:C0:T0:L0                                                       cdrom  -         CD-ROM          
```

```json
$ govc host.storage.info --json
{
  "Self": {
    "Type": "HostStorageSystem",
    "Value": "storageSystem"
  },
  "Value": null,
  "AvailableField": null,
  "StorageDeviceInfo": {
    "HostBusAdapter": [
      {
        "Key": "key-vim.host.BlockHba-vmhba0",
        "Device": "vmhba0",
        "Bus": 0,
        "Status": "unknown",
        "Model": "PIIX4 for 430TX/440BX/MX IDE Controller",
        "Driver": "vmkata",
        "Pci": "0000:00:01.1",
        "StorageProtocol": ""
      },
      {
        "Key": "key-vim.host.BlockHba-vmhba64",
        "Device": "vmhba64",
        "Bus": 0,
        "Status": "unknown",
        "Model": "PIIX4 for 430TX/440BX/MX IDE Controller",
        "Driver": "vmkata",
        "Pci": "0000:00:01.1",
        "StorageProtocol": ""
      }
    ],
    "ScsiLun": [
      {
        "DeviceName": "/vmfs/devices/disks/t10.ATA_____VBOX_HARDDISK___________________________VB22cd9e3f2Df90775ba_",
        "DeviceType": "disk",
        "Key": "key-vim.host.ScsiDisk-0100000000564232326364396533662d66393037373562612056424f582048",
        "Uuid": "0100000000564232326364396533662d66393037373562612056424f582048",
        "Descriptor": [
          {
            "Quality": "highQuality",
            "Id": "t10.ATA_____VBOX_HARDDISK___________________________VB22cd9e3f2Df90775ba_"
          },
          {
            "Quality": "mediumQuality",
            "Id": "vml.0100000000564232326364396533662d66393037373562612056424f582048"
          },
          {
            "Quality": "mediumQuality",
            "Id": "0100000000564232326364396533662d66393037373562612056424f582048"
          }
        ],
        "CanonicalName": "t10.ATA_____VBOX_HARDDISK___________________________VB22cd9e3f2Df90775ba_",
        "DisplayName": "Local ATA Disk (t10.ATA_____VBOX_HARDDISK___________________________VB22cd9e3f2Df90775ba_)",
        "LunType": "disk",
        "Vendor": "ATA     ",
        "Model": "VBOX HARDDISK   ",
        "Revision": "1.0 ",
        "ScsiLevel": 5,
        "SerialNumber": "unavailable",
        "DurableName": {
          "Namespace": "SERIALNUM",
          "NamespaceId": 4,
          "Data": "MzI="
        },
        "AlternateName": [
          {
            "Namespace": "GENERIC_VPD",
            "NamespaceId": 5,
            "Data": "LTc5"
          },
          {
            "Namespace": "SERIALNUM",
            "NamespaceId": 4,
            "Data": "MzI="
          },
          {
            "Namespace": "UNKNOWN",
            "NamespaceId": 5,
            "Data": "MzI="
          },
          {
            "Namespace": "GENERIC_VPD",
            "NamespaceId": 5,
            "Data": "MA=="
          },
          {
            "Namespace": "GENERIC_VPD",
            "NamespaceId": 5,
            "Data": "MA=="
          },
          {
            "Namespace": "GENERIC_VPD",
            "NamespaceId": 5,
            "Data": "MA=="
          }
        ],
        "StandardInquiry": "MA==",
        "QueueDepth": 1,
        "OperationalState": [
          "ok"
        ],
        "Capabilities": {
          "UpdateDisplayNameSupported": true
        },
        "VStorageSupport": "vStorageUnsupported",
        "ProtocolEndpoint": false,
        "PerenniallyReserved": false,
        "ClusteredVmdkSupported": null,
        "Capacity": {
          "BlockSize": 512,
          "Block": 20971520
        },
        "DevicePath": "/vmfs/devices/disks/t10.ATA_____VBOX_HARDDISK___________________________VB22cd9e3f2Df90775ba_",
        "Ssd": false,
        "LocalDisk": true,
        "PhysicalLocation": null,
        "EmulatedDIXDIFEnabled": false,
        "VsanDiskInfo": null,
        "ScsiDiskType": "native512"
      },
      {
        "DeviceName": "/vmfs/devices/cdrom/mpx.vmhba64:C0:T0:L0",
        "DeviceType": "cdrom",
        "Key": "key-vim.host.ScsiLun-0005000000766d68626136343a303a30",
        "Uuid": "0005000000766d68626136343a303a30",
        "Descriptor": [
          {
            "Quality": "lowQuality",
            "Id": "mpx.vmhba64:C0:T0:L0"
          },
          {
            "Quality": "lowQuality",
            "Id": "vml.0005000000766d68626136343a303a30"
          },
          {
            "Quality": "lowQuality",
            "Id": "0005000000766d68626136343a303a30"
          }
        ],
        "CanonicalName": "mpx.vmhba64:C0:T0:L0",
        "DisplayName": "Local VBOX CD-ROM (mpx.vmhba64:C0:T0:L0)",
        "LunType": "cdrom",
        "Vendor": "VBOX    ",
        "Model": "CD-ROM          ",
        "Revision": "1.0 ",
        "ScsiLevel": 5,
        "SerialNumber": "unavailable",
        "DurableName": null,
        "AlternateName": null,
        "StandardInquiry": "MA==",
        "QueueDepth": 0,
        "OperationalState": [
          "ok"
        ],
        "Capabilities": {
          "UpdateDisplayNameSupported": false
        },
        "VStorageSupport": "vStorageUnsupported",
        "ProtocolEndpoint": false,
        "PerenniallyReserved": false,
        "ClusteredVmdkSupported": null
      }
    ],
    "ScsiTopology": {
      "Adapter": [
        {
          "Key": "key-vim.host.ScsiTopology.Interface-vmhba0",
          "Adapter": "key-vim.host.BlockHba-vmhba0",
          "Target": [
            {
              "Key": "key-vim.host.ScsiTopology.Target-vmhba0:0:0",
              "Target": 0,
              "Lun": [
                {
                  "Key": "key-vim.host.ScsiTopology.Lun-0100000000564232326364396533662d66393037373562612056424f582048",
                  "Lun": 0,
                  "ScsiLun": "key-vim.host.ScsiDisk-0100000000564232326364396533662d66393037373562612056424f582048"
                }
              ],
              "Transport": {}
            }
          ]
        },
        {
          "Key": "key-vim.host.ScsiTopology.Interface-vmhba64",
          "Adapter": "key-vim.host.BlockHba-vmhba64",
          "Target": [
            {
              "Key": "key-vim.host.ScsiTopology.Target-vmhba64:0:0",
              "Target": 0,
              "Lun": [
                {
                  "Key": "key-vim.host.ScsiTopology.Lun-0005000000766d68626136343a303a30",
                  "Lun": 0,
                  "ScsiLun": "key-vim.host.ScsiLun-0005000000766d68626136343a303a30"
                }
              ],
              "Transport": {}
            }
          ]
        }
      ]
    },
    "NvmeTopology": null,
    "MultipathInfo": {
      "Lun": [
        {
          "Key": "key-vim.host.MultipathInfo.LogicalUnit-0100000000564232326364396533662d66393037373562612056424f582048",
          "Id": "0100000000564232326364396533662d66393037373562612056424f582048",
          "Lun": "key-vim.host.ScsiDisk-0100000000564232326364396533662d66393037373562612056424f582048",
          "Path": [
            {
              "Key": "key-vim.host.MultipathInfo.Path-vmhba0:C0:T0:L0",
              "Name": "vmhba0:C0:T0:L0",
              "PathState": "active",
              "State": "active",
              "IsWorkingPath": true,
              "Adapter": "key-vim.host.BlockHba-vmhba0",
              "Lun": "key-vim.host.MultipathInfo.LogicalUnit-0100000000564232326364396533662d66393037373562612056424f582048",
              "Transport": {}
            }
          ],
          "Policy": {
            "Policy": "VMW_PSP_FIXED",
            "Prefer": "vmhba0:C0:T0:L0"
          },
          "StorageArrayTypePolicy": {
            "Policy": "VMW_SATP_LOCAL"
          }
        },
        {
          "Key": "key-vim.host.MultipathInfo.LogicalUnit-0005000000766d68626136343a303a30",
          "Id": "0005000000766d68626136343a303a30",
          "Lun": "key-vim.host.ScsiLun-0005000000766d68626136343a303a30",
          "Path": [
            {
              "Key": "key-vim.host.MultipathInfo.Path-vmhba64:C0:T0:L0",
              "Name": "vmhba64:C0:T0:L0",
              "PathState": "active",
              "State": "active",
              "IsWorkingPath": true,
              "Adapter": "key-vim.host.BlockHba-vmhba64",
              "Lun": "key-vim.host.MultipathInfo.LogicalUnit-0005000000766d68626136343a303a30",
              "Transport": {}
            }
          ],
          "Policy": {
            "Policy": "VMW_PSP_FIXED",
            "Prefer": "vmhba64:C0:T0:L0"
          },
          "StorageArrayTypePolicy": {
            "Policy": "VMW_SATP_LOCAL"
          }
        }
      ]
    },
    "PlugStoreTopology": {
      "Adapter": [
        {
          "Key": "key-vim.host.PlugStoreTopology.Adapter-vmhba0",
          "Adapter": "key-vim.host.BlockHba-vmhba0",
          "Path": [
            "key-vim.host.PlugStoreTopology.Path-vmhba0:C0:T0:L0"
          ]
        },
        {
          "Key": "key-vim.host.PlugStoreTopology.Adapter-vmhba64",
          "Adapter": "key-vim.host.BlockHba-vmhba64",
          "Path": [
            "key-vim.host.PlugStoreTopology.Path-vmhba64:C0:T0:L0"
          ]
        }
      ],
      "Path": [
        {
          "Key": "key-vim.host.PlugStoreTopology.Path-vmhba0:C0:T0:L0",
          "Name": "vmhba0:C0:T0:L0",
          "ChannelNumber": 0,
          "TargetNumber": 0,
          "LunNumber": 0,
          "Adapter": "key-vim.host.PlugStoreTopology.Adapter-vmhba0",
          "Target": "key-vim.host.PlugStoreTopology.Target-ide.0:0",
          "Device": "key-vim.host.PlugStoreTopology.Device-0100000000564232326364396533662d66393037373562612056424f582048"
        },
        {
          "Key": "key-vim.host.PlugStoreTopology.Path-vmhba64:C0:T0:L0",
          "Name": "vmhba64:C0:T0:L0",
          "ChannelNumber": 0,
          "TargetNumber": 0,
          "LunNumber": 0,
          "Adapter": "key-vim.host.PlugStoreTopology.Adapter-vmhba64",
          "Target": "key-vim.host.PlugStoreTopology.Target-ide.0:0",
          "Device": "key-vim.host.PlugStoreTopology.Device-0005000000766d68626136343a303a30"
        }
      ],
      "Target": [
        {
          "Key": "key-vim.host.PlugStoreTopology.Target-ide.0:0",
          "Transport": {}
        }
      ],
      "Device": [
        {
          "Key": "key-vim.host.PlugStoreTopology.Device-0100000000564232326364396533662d66393037373562612056424f582048",
          "Lun": "key-vim.host.ScsiDisk-0100000000564232326364396533662d66393037373562612056424f582048",
          "Path": [
            "key-vim.host.PlugStoreTopology.Path-vmhba0:C0:T0:L0"
          ]
        },
        {
          "Key": "key-vim.host.PlugStoreTopology.Device-0005000000766d68626136343a303a30",
          "Lun": "key-vim.host.ScsiLun-0005000000766d68626136343a303a30",
          "Path": [
            "key-vim.host.PlugStoreTopology.Path-vmhba64:C0:T0:L0"
          ]
        }
      ],
      "Plugin": [
        {
          "Key": "key-vim.host.PlugStoreTopology.Plugin-HPP",
          "Name": "HPP",
          "Device": null,
          "ClaimedPath": null
        },
        {
          "Key": "key-vim.host.PlugStoreTopology.Plugin-NMP",
          "Name": "NMP",
          "Device": [
            "key-vim.host.PlugStoreTopology.Device-0100000000564232326364396533662d66393037373562612056424f582048",
            "key-vim.host.PlugStoreTopology.Device-0005000000766d68626136343a303a30"
          ],
          "ClaimedPath": [
            "key-vim.host.PlugStoreTopology.Path-vmhba64:C0:T0:L0",
            "key-vim.host.PlugStoreTopology.Path-vmhba0:C0:T0:L0"
          ]
        }
      ]
    },
    "SoftwareInternetScsiEnabled": false
  },
  "FileSystemVolumeInfo": {
    "VolumeTypeList": [
      "VMFS",
      "NFS",
      "NFS41",
      "vsan",
      "VVOL",
      "VFFS",
      "OTHER",
      "PMEM"
    ],
    "MountInfo": [
      {
        "MountInfo": {
          "Path": "/vmfs/volumes/60c4dd35-141ece76-d699-0800272601f9",
          "AccessMode": "readWrite",
          "Mounted": true,
          "Accessible": true,
          "InaccessibleReason": ""
        },
        "Volume": {
          "Type": "VMFS",
          "Name": "datastore1",
          "Capacity": 2684354560,
          "BlockSizeMb": 1,
          "BlockSize": 1024,
          "UnmapGranularity": 1024,
          "UnmapPriority": "low",
          "UnmapBandwidthSpec": null,
          "MaxBlocks": 63963136,
          "MajorVersion": 6,
          "Version": "6.82",
          "Uuid": "60c4dd35-141ece76-d699-0800272601f9",
          "Extent": [
            {
              "DiskName": "t10.ATA_____VBOX_HARDDISK___________________________VB22cd9e3f2Df90775ba_",
              "Partition": 3
            }
          ],
          "VmfsUpgradable": false,
          "ForceMountedInfo": null,
          "Ssd": false,
          "Local": true,
          "ScsiDiskType": ""
        },
        "VStorageSupport": "vStorageUnsupported"
      },
      {
        "MountInfo": {
          "Path": "/vmfs/volumes/bdde4a74-a82ac71f-7a13-2f1804833403",
          "AccessMode": "readOnly",
          "Mounted": true,
          "Accessible": true,
          "InaccessibleReason": ""
        },
        "Volume": {
          "Type": "OTHER",
          "Name": "",
          "Capacity": 261853184
        },
        "VStorageSupport": ""
      },
      {
        "MountInfo": {
          "Path": "/vmfs/volumes/60c4dd36-32ef1a2f-8d22-0800272601f9",
          "AccessMode": "readOnly",
          "Mounted": true,
          "Accessible": true,
          "InaccessibleReason": ""
        },
        "Volume": {
          "Type": "OTHER",
          "Name": "",
          "Capacity": 4293591040
        },
        "VStorageSupport": ""
      },
      {
        "MountInfo": {
          "Path": "/vmfs/volumes/de1b2d7a-94ee883a-0a43-ada37e3393b2",
          "AccessMode": "readOnly",
          "Mounted": true,
          "Accessible": true,
          "InaccessibleReason": ""
        },
        "Volume": {
          "Type": "OTHER",
          "Name": "",
          "Capacity": 261853184
        },
        "VStorageSupport": ""
      },
      {
        "MountInfo": {
          "Path": "/vmfs/volumes/60c4dd2f-b439629d-bec9-0800272601f9",
          "AccessMode": "readOnly",
          "Mounted": true,
          "Accessible": true,
          "InaccessibleReason": ""
        },
        "Volume": {
          "Type": "OTHER",
          "Name": "",
          "Capacity": 299712512
        },
        "VStorageSupport": ""
      }
    ]
  },
  "SystemFile": null,
  "MultipathStateInfo": {
    "Path": [
      {
        "Name": "vmhba0:C0:T0:L0",
        "PathState": "active"
      },
      {
        "Name": "vmhba64:C0:T0:L0",
        "PathState": "active"
      }
    ]
  }
}
```

```
$ govc host.vnic.info
Device:            vmk0
Network label:     Management Network
Switch:            vSwitch0
IP address:        192.168.100.120
TCP/IP stack:      defaultTcpipStack
Enabled services:  management
```

```json
$ govc host.vnic.info --json
{
  "Info": [
    {
      "Device": "vmk0",
      "Network": "Management Network",
      "Switch": "vSwitch0",
      "Address": "192.168.100.120",
      "Stack": "defaultTcpipStack",
      "Services": [
        "management"
      ]
    }
  ]
}
```

```
$ govc host.vswitch.info
Name:                    vSwitch0
Portgroup:               VM Network, Management Network
Pnic:                    vmnic0
MTU:                     1500
Ports:                   2560
Ports Available:         2552
Allow promiscuous mode:  No
Allow forged transmits:  Yes
Allow MAC changes:       Yes

Name:                    vSwitch1
Portgroup:               VM Network2
Pnic:                    vmnic1
MTU:                     1500
Ports:                   2560
Ports Available:         2552
Allow promiscuous mode:  No
Allow forged transmits:  No
Allow MAC changes:       No
```

```json
$ govc host.vswitch.info --json
{
  "Vswitch": [
    {
      "Name": "vSwitch0",
      "Key": "key-vim.host.VirtualSwitch-vSwitch0",
      "NumPorts": 2560,
      "NumPortsAvailable": 2552,
      "Mtu": 1500,
      "Portgroup": [
        "key-vim.host.PortGroup-VM Network",
        "key-vim.host.PortGroup-Management Network"
      ],
      "Pnic": [
        "key-vim.host.PhysicalNic-vmnic0"
      ],
      "Spec": {
        "NumPorts": 128,
        "Bridge": {
          "NicDevice": [
            "vmnic0"
          ],
          "Beacon": {
            "Interval": 1
          },
          "LinkDiscoveryProtocolConfig": {
            "Protocol": "cdp",
            "Operation": "listen"
          }
        },
        "Policy": {
          "Security": {
            "AllowPromiscuous": false,
            "MacChanges": true,
            "ForgedTransmits": true
          },
          "NicTeaming": {
            "Policy": "loadbalance_srcid",
            "ReversePolicy": true,
            "NotifySwitches": true,
            "RollingOrder": false,
            "FailureCriteria": {
              "CheckSpeed": "minimum",
              "Speed": 10,
              "CheckDuplex": false,
              "FullDuplex": false,
              "CheckErrorPercent": false,
              "Percentage": 0,
              "CheckBeacon": false
            },
            "NicOrder": {
              "ActiveNic": [
                "vmnic0"
              ],
              "StandbyNic": null
            }
          },
          "OffloadPolicy": {
            "CsumOffload": true,
            "TcpSegmentation": true,
            "ZeroCopyXmit": true
          },
          "ShapingPolicy": {
            "Enabled": false,
            "AverageBandwidth": 0,
            "PeakBandwidth": 0,
            "BurstSize": 0
          }
        },
        "Mtu": 0
      }
    },
    {
      "Name": "vSwitch1",
      "Key": "key-vim.host.VirtualSwitch-vSwitch1",
      "NumPorts": 2560,
      "NumPortsAvailable": 2552,
      "Mtu": 1500,
      "Portgroup": [
        "key-vim.host.PortGroup-VM Network2"
      ],
      "Pnic": [
        "key-vim.host.PhysicalNic-vmnic1"
      ],
      "Spec": {
        "NumPorts": 1024,
        "Bridge": {
          "NicDevice": [
            "vmnic1"
          ],
          "Beacon": {
            "Interval": 1
          },
          "LinkDiscoveryProtocolConfig": {
            "Protocol": "cdp",
            "Operation": "listen"
          }
        },
        "Policy": {
          "Security": {
            "AllowPromiscuous": false,
            "MacChanges": false,
            "ForgedTransmits": false
          },
          "NicTeaming": {
            "Policy": "loadbalance_srcid",
            "ReversePolicy": true,
            "NotifySwitches": true,
            "RollingOrder": false,
            "FailureCriteria": {
              "CheckSpeed": "minimum",
              "Speed": 10,
              "CheckDuplex": false,
              "FullDuplex": false,
              "CheckErrorPercent": false,
              "Percentage": 0,
              "CheckBeacon": false
            },
            "NicOrder": {
              "ActiveNic": [
                "vmnic1"
              ],
              "StandbyNic": null
            }
          },
          "OffloadPolicy": {
            "CsumOffload": true,
            "TcpSegmentation": true,
            "ZeroCopyXmit": true
          },
          "ShapingPolicy": {
            "Enabled": false,
            "AverageBandwidth": 0,
            "PeakBandwidth": 0,
            "BurstSize": 0
          }
        },
        "Mtu": 0
      }
    }
  ]
}
```

## Virtual Machine

```
$ govc vm.info WindowsServer2019
Name:           WindowsServer2019
  Path:         /ha-datacenter/vm/WindowsServer2019
  UUID:         564d7dbc-291d-a35f-86bb-882aa4f5f763
  Guest name:   Microsoft Windows Server 2016 or later (64-bit)
  Memory:       2048MB
  CPU:          2 vCPU(s)
  Power state:  poweredOff
  Boot time:    <nil>
  IP address:   
  Host:         localhost.localdomain
```

```json
$ govc vm.info --json WindowsServer2019
{
  "VirtualMachines": [
    {
      "Self": {
        "Type": "VirtualMachine",
        "Value": "1"
      },
      "Value": null,
      "AvailableField": null,
      "Parent": {
        "Type": "Folder",
        "Value": "ha-folder-vm"
      },
      "CustomValue": null,
      "OverallStatus": "green",
      "ConfigStatus": "green",
      "ConfigIssue": null,
      "EffectiveRole": [
        -1
      ],
      "Permission": null,
      "Name": "WindowsServer2019",
      "DisabledMethod": [
        "setCustomValue",
        "AddTag",
        "RemoveTag",
        "RetrieveCustomValues",
        "RefreshStorageInfo",
        "RetrieveBackupAgent",
        "CreateSnapshotEx_Task",
        "RevertToCurrentSnapshot_Task",
        "ExtractOvfEnvironment",
        "PowerOffVM_Task",
        "SuspendVM_Task",
        "ResetVM_Task",
        "ShutdownGuest",
        "RebootGuest",
        "StandbyGuest",
        "AnswerVM",
        "CustomizeVM_Task",
        "CheckCustomizationSpec",
        "MigrateVM_Task",
        "RelocateVM_Task",
        "CloneVM_Task",
        "InstantClone_Task",
        "CreateForkChild_Task",
        "EnableForkParent_Task",
        "DisableForkParent_Task",
        "RetrieveForkChildren_Task",
        "RetrieveForkParent_Task",
        "CreateImportLease",
        "MarkAsTemplate",
        "MarkAsVirtualMachine",
        "ResetGuestInformation",
        "MountToolsInstaller",
        "MountToolsInstallerImage",
        "UnmountToolsInstaller",
        "UpgradeTools_Task",
        "UpgradeToolsFromImage_Task",
        "AcquireMksTicket",
        "GetConnections",
        "DropConnections",
        "AcquireTicket",
        "SetScreenResolution",
        "DefragmentAllDisks",
        "CreateSecondaryVM_Task",
        "CreateSecondaryVMEx_Task",
        "TurnOffFaultToleranceForVM_Task",
        "MakePrimaryVM_Task",
        "TerminateFaultTolerantVM_Task",
        "DisableSecondaryVM_Task",
        "EnableSecondaryVM_Task",
        "SetDisplayTopology",
        "StartRecording_Task",
        "StopRecording_Task",
        "StartReplaying_Task",
        "StopReplaying_Task",
        "PromoteDisks_Task",
        "CreateScreenshot_Task",
        "PutUsbScanCodes",
        "QueryChangedDiskAreas",
        "QueryUnownedFiles",
        "reloadVirtualMachineFromPath_Task",
        "QueryFaultToleranceCompatibility",
        "QueryFaultToleranceCompatibilityEx",
        "InvokeFSR_Task",
        "TerminateVM",
        "IsNativeSnapshotCapable",
        "ConfigureQuorumFilePathPrefix_Task",
        "InjectOvfEnvironment",
        "WipeDisk_Task",
        "ShrinkDisk_Task",
        "SendNMI",
        "ReloadEx_Task",
        "AttachDisk_Task",
        "DetachDisk_Task",
        "QueryPolicyStatus",
        "CryptoUnlock_Task"
      ],
      "RecentTask": null,
      "DeclaredAlarmState": null,
      "TriggeredAlarmState": null,
      "AlarmActionsEnabled": null,
      "Tag": null,
      "Capability": {
        "SnapshotOperationsSupported": true,
        "MultipleSnapshotsSupported": true,
        "SnapshotConfigSupported": true,
        "PoweredOffSnapshotsSupported": true,
        "MemorySnapshotsSupported": true,
        "RevertToSnapshotSupported": true,
        "QuiescedSnapshotsSupported": true,
        "DisableSnapshotsSupported": false,
        "LockSnapshotsSupported": true,
        "ConsolePreferencesSupported": false,
        "CpuFeatureMaskSupported": true,
        "S1AcpiManagementSupported": true,
        "SettingScreenResolutionSupported": false,
        "ToolsAutoUpdateSupported": false,
        "VmNpivWwnSupported": true,
        "NpivWwnOnNonRdmVmSupported": true,
        "VmNpivWwnDisableSupported": true,
        "VmNpivWwnUpdateSupported": true,
        "SwapPlacementSupported": true,
        "ToolsSyncTimeSupported": true,
        "VirtualMmuUsageSupported": false,
        "DiskSharesSupported": true,
        "BootOptionsSupported": true,
        "BootRetryOptionsSupported": true,
        "SettingVideoRamSizeSupported": true,
        "SettingDisplayTopologySupported": false,
        "RecordReplaySupported": false,
        "ChangeTrackingSupported": true,
        "MultipleCoresPerSocketSupported": true,
        "HostBasedReplicationSupported": true,
        "GuestAutoLockSupported": true,
        "MemoryReservationLockSupported": true,
        "FeatureRequirementSupported": true,
        "PoweredOnMonitorTypeChangeSupported": true,
        "SeSparseDiskSupported": true,
        "NestedHVSupported": true,
        "VPMCSupported": true,
        "SecureBootSupported": true,
        "PerVmEvcSupported": true,
        "VirtualMmuUsageIgnored": true,
        "VirtualExecUsageIgnored": true,
        "DiskOnlySnapshotOnSuspendedVMSupported": true,
        "ToolsSyncTimeAllowSupported": null,
        "SevSupported": null
      },
      "Config": {
        "ChangeVersion": "2021-06-13T00:59:48.575539Z",
        "Modified": "1970-01-01T00:00:00Z",
        "Name": "WindowsServer2019",
        "GuestFullName": "Microsoft Windows Server 2016 or later (64-bit)",
        "Version": "vmx-14",
        "Uuid": "564d7dbc-291d-a35f-86bb-882aa4f5f763",
        "CreateDate": "2021-06-13T00:59:48.542756Z",
        "InstanceUuid": "524aa1ee-d441-4f01-6cb3-c45e4167cb59",
        "NpivNodeWorldWideName": null,
        "NpivPortWorldWideName": null,
        "NpivWorldWideNameType": "",
        "NpivDesiredNodeWwns": 0,
        "NpivDesiredPortWwns": 0,
        "NpivTemporaryDisabled": true,
        "NpivOnNonRdmDisks": null,
        "LocationId": "564d7dbc-291d-a35f-86bb-882aa4f5f763",
        "Template": false,
        "GuestId": "windows9Server64Guest",
        "AlternateGuestName": "",
        "Annotation": "",
        "Files": {
          "VmPathName": "[datastore1] WindowsServer2019/WindowsServer2019.vmx",
          "SnapshotDirectory": "[datastore1] WindowsServer2019",
          "SuspendDirectory": "[datastore1] WindowsServer2019",
          "LogDirectory": "[datastore1] WindowsServer2019",
          "FtMetadataDirectory": ""
        },
        "Tools": {
          "ToolsVersion": 0,
          "ToolsInstallType": "",
          "AfterPowerOn": true,
          "AfterResume": true,
          "BeforeGuestStandby": true,
          "BeforeGuestShutdown": true,
          "BeforeGuestReboot": null,
          "ToolsUpgradePolicy": "manual",
          "PendingCustomization": "",
          "CustomizationKeyId": null,
          "SyncTimeWithHostAllowed": null,
          "SyncTimeWithHost": false,
          "LastInstallInfo": {
            "Counter": 0,
            "Fault": null
          }
        },
        "Flags": {
          "DisableAcceleration": false,
          "EnableLogging": true,
          "UseToe": false,
          "RunWithDebugInfo": false,
          "MonitorType": "release",
          "HtSharing": "any",
          "SnapshotDisabled": false,
          "SnapshotLocked": false,
          "DiskUuidEnabled": true,
          "VirtualMmuUsage": "automatic",
          "VirtualExecUsage": "hvAuto",
          "SnapshotPowerOffBehavior": "powerOff",
          "RecordReplayEnabled": false,
          "FaultToleranceType": "unset",
          "CbrcCacheEnabled": false,
          "VvtdEnabled": false,
          "VbsEnabled": false
        },
        "ConsolePreferences": null,
        "DefaultPowerOps": {
          "PowerOffType": "soft",
          "SuspendType": "soft",
          "ResetType": "soft",
          "DefaultPowerOffType": "soft",
          "DefaultSuspendType": "hard",
          "DefaultResetType": "soft",
          "StandbyAction": "checkpoint"
        },
        "Hardware": {
          "NumCPU": 2,
          "NumCoresPerSocket": 1,
          "MemoryMB": 2048,
          "VirtualICH7MPresent": false,
          "VirtualSMCPresent": false,
          "Device": [
            {
              "Key": 200,
              "DeviceInfo": {
                "Label": "IDE 0",
                "Summary": "IDE 0"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 0,
              "UnitNumber": null,
              "BusNumber": 0,
              "Device": null
            },
            {
              "Key": 201,
              "DeviceInfo": {
                "Label": "IDE 1",
                "Summary": "IDE 1"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 0,
              "UnitNumber": null,
              "BusNumber": 1,
              "Device": null
            },
            {
              "Key": 300,
              "DeviceInfo": {
                "Label": "PS2 controller 0",
                "Summary": "PS2 controller 0"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 0,
              "UnitNumber": null,
              "BusNumber": 0,
              "Device": [
                600,
                700
              ]
            },
            {
              "Key": 100,
              "DeviceInfo": {
                "Label": "PCI controller 0",
                "Summary": "PCI controller 0"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 0,
              "UnitNumber": null,
              "BusNumber": 0,
              "Device": [
                500,
                12000,
                14000,
                1000,
                15000,
                4000
              ]
            },
            {
              "Key": 400,
              "DeviceInfo": {
                "Label": "SIO controller 0",
                "Summary": "SIO controller 0"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 0,
              "UnitNumber": null,
              "BusNumber": 0,
              "Device": null
            },
            {
              "Key": 600,
              "DeviceInfo": {
                "Label": "Keyboard ",
                "Summary": "Keyboard"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 300,
              "UnitNumber": 0
            },
            {
              "Key": 700,
              "DeviceInfo": {
                "Label": "Pointing device",
                "Summary": "Pointing device; Device"
              },
              "Backing": {
                "DeviceName": "",
                "UseAutoDetect": false,
                "HostPointingDevice": "autodetect"
              },
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 300,
              "UnitNumber": 1
            },
            {
              "Key": 500,
              "DeviceInfo": {
                "Label": "Video card ",
                "Summary": "Video card"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 100,
              "UnitNumber": 0,
              "VideoRamSizeInKB": 4096,
              "NumDisplays": 1,
              "UseAutoDetect": true,
              "Enable3DSupport": false,
              "Use3dRenderer": "automatic",
              "GraphicsMemorySizeInKB": 262144
            },
            {
              "Key": 12000,
              "DeviceInfo": {
                "Label": "VMCI device",
                "Summary": "Device on the virtual machine PCI bus that provides support for the virtual machine communication interface"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 100,
              "UnitNumber": 17,
              "Id": -1,
              "AllowUnrestrictedCommunication": false,
              "FilterEnable": true,
              "FilterInfo": null
            },
            {
              "Key": 14000,
              "DeviceInfo": {
                "Label": "USB xHCI controller ",
                "Summary": "USB xHCI controller"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": {
                "PciSlotNumber": -1
              },
              "ControllerKey": 100,
              "UnitNumber": 23,
              "BusNumber": 0,
              "Device": null,
              "AutoConnectDevices": false
            },
            {
              "Key": 1000,
              "DeviceInfo": {
                "Label": "SCSI controller 0",
                "Summary": "LSI Logic SAS"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 100,
              "UnitNumber": 3,
              "BusNumber": 0,
              "Device": [
                2000
              ],
              "HotAddRemove": true,
              "SharedBus": "noSharing",
              "ScsiCtlrUnitNumber": 7
            },
            {
              "Key": 15000,
              "DeviceInfo": {
                "Label": "SATA controller 0",
                "Summary": "AHCI"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 100,
              "UnitNumber": 24,
              "BusNumber": 0,
              "Device": [
                16000
              ]
            },
            {
              "Key": 16000,
              "DeviceInfo": {
                "Label": "CD/DVD drive 1",
                "Summary": "ATAPI /vmfs/devices/cdrom/mpx.vmhba64:C0:T0:L0"
              },
              "Backing": {
                "DeviceName": "/vmfs/devices/cdrom/mpx.vmhba64:C0:T0:L0",
                "UseAutoDetect": false
              },
              "Connectable": {
                "MigrateConnect": "",
                "StartConnected": true,
                "AllowGuestControl": true,
                "Connected": false,
                "Status": "untried"
              },
              "SlotInfo": null,
              "ControllerKey": 15000,
              "UnitNumber": 0
            },
            {
              "Key": 2000,
              "DeviceInfo": {
                "Label": "Hard disk 1",
                "Summary": "3,145,728 KB"
              },
              "Backing": {
                "FileName": "[datastore1] WindowsServer2019/WindowsServer2019.vmdk",
                "Datastore": {
                  "Type": "Datastore",
                  "Value": "60c4dd35-141ece76-d699-0800272601f9"
                },
                "BackingObjectId": "",
                "DiskMode": "persistent",
                "Split": false,
                "WriteThrough": false,
                "ThinProvisioned": true,
                "EagerlyScrub": null,
                "Uuid": "6000C296-52f5-e853-3c25-d8e0ed16ace2",
                "ContentId": "7f0ca3471be8091fab9a257bfffffffe",
                "ChangeId": "",
                "Parent": null,
                "DeltaDiskFormat": "",
                "DigestEnabled": false,
                "DeltaGrainSize": 0,
                "DeltaDiskFormatVariant": "",
                "Sharing": "sharingNone",
                "KeyId": null
              },
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 1000,
              "UnitNumber": 0,
              "CapacityInKB": 3145728,
              "CapacityInBytes": 3221225472,
              "Shares": {
                "Shares": 1000,
                "Level": "normal"
              },
              "StorageIOAllocation": {
                "Limit": -1,
                "Shares": {
                  "Shares": 1000,
                  "Level": "normal"
                },
                "Reservation": 0
              },
              "DiskObjectId": "1-2000",
              "VFlashCacheConfigInfo": null,
              "Iofilter": null,
              "VDiskId": null,
              "NativeUnmanagedLinkedClone": false
            },
            {
              "Key": 4000,
              "DeviceInfo": {
                "Label": "Network adapter 1",
                "Summary": "VM Network"
              },
              "Backing": {
                "DeviceName": "VM Network",
                "UseAutoDetect": false,
                "Network": {
                  "Type": "Network",
                  "Value": "HaNetwork-VM Network"
                },
                "InPassthroughMode": null
              },
              "Connectable": {
                "MigrateConnect": "unset",
                "StartConnected": true,
                "AllowGuestControl": true,
                "Connected": false,
                "Status": "untried"
              },
              "SlotInfo": null,
              "ControllerKey": 100,
              "UnitNumber": 7,
              "AddressType": "generated",
              "MacAddress": "",
              "WakeOnLanEnabled": false,
              "ResourceAllocation": {
                "Reservation": 0,
                "Share": {
                  "Shares": 50,
                  "Level": "normal"
                },
                "Limit": -1
              },
              "ExternalId": "",
              "UptCompatibilityEnabled": false
            }
          ]
        },
        "VcpuConfig": null,
        "CpuAllocation": {
          "Reservation": 0,
          "ExpandableReservation": false,
          "Limit": -1,
          "Shares": {
            "Shares": 2000,
            "Level": "normal"
          },
          "OverheadLimit": null
        },
        "MemoryAllocation": {
          "Reservation": 0,
          "ExpandableReservation": false,
          "Limit": -1,
          "Shares": {
            "Shares": 20480,
            "Level": "normal"
          },
          "OverheadLimit": null
        },
        "LatencySensitivity": {
          "Level": "normal",
          "Sensitivity": 0
        },
        "MemoryHotAddEnabled": false,
        "CpuHotAddEnabled": false,
        "CpuHotRemoveEnabled": false,
        "HotPlugMemoryLimit": 0,
        "HotPlugMemoryIncrementSize": 0,
        "CpuAffinity": null,
        "MemoryAffinity": null,
        "NetworkShaper": null,
        "ExtraConfig": [
          {
            "Key": "nvram",
            "Value": "WindowsServer2019.nvram"
          },
          {
            "Key": "pciBridge0.present",
            "Value": "TRUE"
          },
          {
            "Key": "svga.present",
            "Value": "TRUE"
          },
          {
            "Key": "pciBridge4.present",
            "Value": "TRUE"
          },
          {
            "Key": "pciBridge4.virtualDev",
            "Value": "pcieRootPort"
          },
          {
            "Key": "pciBridge4.functions",
            "Value": "8"
          },
          {
            "Key": "pciBridge5.present",
            "Value": "TRUE"
          },
          {
            "Key": "pciBridge5.virtualDev",
            "Value": "pcieRootPort"
          },
          {
            "Key": "pciBridge5.functions",
            "Value": "8"
          },
          {
            "Key": "pciBridge6.present",
            "Value": "TRUE"
          },
          {
            "Key": "pciBridge6.virtualDev",
            "Value": "pcieRootPort"
          },
          {
            "Key": "pciBridge6.functions",
            "Value": "8"
          },
          {
            "Key": "pciBridge7.present",
            "Value": "TRUE"
          },
          {
            "Key": "pciBridge7.virtualDev",
            "Value": "pcieRootPort"
          },
          {
            "Key": "pciBridge7.functions",
            "Value": "8"
          },
          {
            "Key": "hpet0.present",
            "Value": "TRUE"
          },
          {
            "Key": "RemoteDisplay.maxConnections",
            "Value": "-1"
          },
          {
            "Key": "sched.cpu.latencySensitivity",
            "Value": "normal"
          },
          {
            "Key": "svga.autodetect",
            "Value": "TRUE"
          },
          {
            "Key": "disk.EnableUUID",
            "Value": "TRUE"
          },
          {
            "Key": "vmware.tools.internalversion",
            "Value": "0"
          },
          {
            "Key": "vmware.tools.requiredversion",
            "Value": "10346"
          },
          {
            "Key": "migrate.hostLogState",
            "Value": "none"
          },
          {
            "Key": "migrate.migrationId",
            "Value": "0"
          },
          {
            "Key": "migrate.hostLog",
            "Value": ""
          }
        ],
        "CpuFeatureMask": [
          {
            "Level": 0,
            "Vendor": "",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": 0,
            "Vendor": "amd",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": 1,
            "Vendor": "",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": 1,
            "Vendor": "amd",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": -2147483648,
            "Vendor": "",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": -2147483648,
            "Vendor": "amd",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": -2147483647,
            "Vendor": "",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": -2147483647,
            "Vendor": "amd",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": -2147483640,
            "Vendor": "",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": -2147483640,
            "Vendor": "amd",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          }
        ],
        "DatastoreUrl": [
          {
            "Name": "datastore1",
            "Url": "/vmfs/volumes/60c4dd35-141ece76-d699-0800272601f9/"
          }
        ],
        "SwapPlacement": "inherit",
        "BootOptions": {
          "BootDelay": 0,
          "EnterBIOSSetup": false,
          "EfiSecureBootEnabled": true,
          "BootRetryEnabled": false,
          "BootRetryDelay": 10,
          "BootOrder": null,
          "NetworkBootProtocol": "ipv4"
        },
        "FtInfo": null,
        "RepConfig": null,
        "VAppConfig": null,
        "VAssertsEnabled": false,
        "ChangeTrackingEnabled": false,
        "Firmware": "efi",
        "MaxMksConnections": -1,
        "GuestAutoLockEnabled": false,
        "ManagedBy": null,
        "MemoryReservationLockedToMax": false,
        "InitialOverhead": {
          "InitialMemoryReservation": 181518336,
          "InitialSwapReservation": 192794624
        },
        "NestedHVEnabled": false,
        "VPMCEnabled": false,
        "ScheduledHardwareUpgradeInfo": {
          "UpgradePolicy": "never",
          "VersionKey": "",
          "ScheduledHardwareUpgradeStatus": "none",
          "Fault": null
        },
        "ForkConfigInfo": null,
        "VFlashCacheReservation": 0,
        "VmxConfigChecksum": "aWtLaHRpQUVhckVZZ3FjbWtvQ0pWRUhQd2NzPQ==",
        "MessageBusTunnelEnabled": false,
        "VmStorageObjectId": "",
        "SwapStorageObjectId": "",
        "KeyId": null,
        "GuestIntegrityInfo": {
          "Enabled": false
        },
        "MigrateEncryption": "opportunistic",
        "SgxInfo": null,
        "ContentLibItemInfo": null,
        "GuestMonitoringModeInfo": null,
        "SevEnabled": null
      },
      "Layout": {
        "ConfigFile": [
          "WindowsServer2019.vmsd"
        ],
        "LogFile": null,
        "Disk": [
          {
            "Key": 2000,
            "DiskFile": [
              "[datastore1] WindowsServer2019/WindowsServer2019.vmdk"
            ]
          }
        ],
        "Snapshot": null,
        "SwapFile": ""
      },
      "LayoutEx": {
        "File": [
          {
            "Key": 0,
            "Name": "[datastore1] WindowsServer2019/WindowsServer2019.vmx",
            "Type": "config",
            "Size": 0,
            "UniqueSize": 0,
            "BackingObjectId": "",
            "Accessible": true
          },
          {
            "Key": 1,
            "Name": "[datastore1] WindowsServer2019/WindowsServer2019.vmsd",
            "Type": "snapshotList",
            "Size": 0,
            "UniqueSize": 0,
            "BackingObjectId": "",
            "Accessible": true
          },
          {
            "Key": 2,
            "Name": "[datastore1] WindowsServer2019/WindowsServer2019.vmdk",
            "Type": "diskDescriptor",
            "Size": 0,
            "UniqueSize": 0,
            "BackingObjectId": "",
            "Accessible": true
          },
          {
            "Key": 3,
            "Name": "[datastore1] WindowsServer2019/WindowsServer2019-flat.vmdk",
            "Type": "diskExtent",
            "Size": 0,
            "UniqueSize": 0,
            "BackingObjectId": "",
            "Accessible": true
          }
        ],
        "Disk": [
          {
            "Key": 2000,
            "Chain": [
              {
                "FileKey": [
                  2,
                  3
                ]
              }
            ]
          }
        ],
        "Snapshot": null,
        "Timestamp": "2021-06-13T00:59:48.652486Z"
      },
      "Storage": {
        "PerDatastoreUsage": [
          {
            "Datastore": {
              "Type": "Datastore",
              "Value": "60c4dd35-141ece76-d699-0800272601f9"
            },
            "Committed": 0,
            "Uncommitted": 5561504225,
            "Unshared": 0
          }
        ],
        "Timestamp": "2021-06-13T00:59:48.652747Z"
      },
      "EnvironmentBrowser": {
        "Type": "EnvironmentBrowser",
        "Value": "1-envmgr"
      },
      "ResourcePool": {
        "Type": "ResourcePool",
        "Value": "ha-root-pool"
      },
      "ParentVApp": null,
      "ResourceConfig": {
        "Entity": {
          "Type": "VirtualMachine",
          "Value": "1"
        },
        "ChangeVersion": "",
        "LastModified": null,
        "CpuAllocation": {
          "Reservation": 0,
          "ExpandableReservation": false,
          "Limit": -1,
          "Shares": {
            "Shares": 2000,
            "Level": "normal"
          },
          "OverheadLimit": null
        },
        "MemoryAllocation": {
          "Reservation": 0,
          "ExpandableReservation": false,
          "Limit": -1,
          "Shares": {
            "Shares": 20480,
            "Level": "normal"
          },
          "OverheadLimit": null
        },
        "ScaleDescendantsShares": ""
      },
      "Runtime": {
        "Device": [
          {
            "RuntimeState": {
              "VmDirectPathGen2Active": false,
              "VmDirectPathGen2InactiveReasonVm": [
                "vmNptIncompatibleAdapterType",
                "vmNptDisabledOrDisconnectedAdapter"
              ],
              "VmDirectPathGen2InactiveReasonOther": null,
              "VmDirectPathGen2InactiveReasonExtended": "",
              "ReservationStatus": "",
              "AttachmentStatus": "red",
              "FeatureRequirement": null
            },
            "Key": 4000
          }
        ],
        "Host": {
          "Type": "HostSystem",
          "Value": "ha-host"
        },
        "ConnectionState": "connected",
        "PowerState": "poweredOff",
        "FaultToleranceState": "notConfigured",
        "DasVmProtection": null,
        "ToolsInstallerMounted": false,
        "SuspendTime": null,
        "BootTime": null,
        "SuspendInterval": 0,
        "Question": null,
        "MemoryOverhead": 0,
        "MaxCpuUsage": 4608,
        "MaxMemoryUsage": 2048,
        "NumMksConnections": 0,
        "RecordReplayState": "inactive",
        "CleanPowerOff": true,
        "NeedSecondaryReason": "",
        "OnlineStandby": false,
        "MinRequiredEVCModeKey": "",
        "ConsolidationNeeded": false,
        "OfflineFeatureRequirement": [
          {
            "Key": "cpuid.lm",
            "FeatureName": "cpuid.lm",
            "Value": "Bool:Min:1"
          }
        ],
        "FeatureRequirement": null,
        "FeatureMask": null,
        "VFlashCacheAllocation": 0,
        "Paused": false,
        "SnapshotInBackground": false,
        "QuiescedForkParent": null,
        "InstantCloneFrozen": false,
        "CryptoState": ""
      },
      "Guest": {
        "ToolsStatus": "toolsNotInstalled",
        "ToolsVersionStatus": "guestToolsNotInstalled",
        "ToolsVersionStatus2": "guestToolsNotInstalled",
        "ToolsRunningStatus": "guestToolsNotRunning",
        "ToolsVersion": "0",
        "ToolsInstallType": "",
        "GuestId": "",
        "GuestFamily": "",
        "GuestFullName": "",
        "HostName": "",
        "IpAddress": "",
        "Net": null,
        "IpStack": null,
        "Disk": null,
        "Screen": {
          "Width": 0,
          "Height": 0
        },
        "GuestState": "notRunning",
        "AppHeartbeatStatus": "appStatusGray",
        "GuestKernelCrashed": false,
        "AppState": "none",
        "GuestOperationsReady": false,
        "InteractiveGuestOperationsReady": false,
        "GuestStateChangeSupported": false,
        "GenerationInfo": null,
        "HwVersion": ""
      },
      "Summary": {
        "Vm": {
          "Type": "VirtualMachine",
          "Value": "1"
        },
        "Runtime": {
          "Device": [
            {
              "RuntimeState": {
                "VmDirectPathGen2Active": false,
                "VmDirectPathGen2InactiveReasonVm": [
                  "vmNptIncompatibleAdapterType",
                  "vmNptDisabledOrDisconnectedAdapter"
                ],
                "VmDirectPathGen2InactiveReasonOther": null,
                "VmDirectPathGen2InactiveReasonExtended": "",
                "ReservationStatus": "",
                "AttachmentStatus": "red",
                "FeatureRequirement": null
              },
              "Key": 4000
            }
          ],
          "Host": {
            "Type": "HostSystem",
            "Value": "ha-host"
          },
          "ConnectionState": "connected",
          "PowerState": "poweredOff",
          "FaultToleranceState": "notConfigured",
          "DasVmProtection": null,
          "ToolsInstallerMounted": false,
          "SuspendTime": null,
          "BootTime": null,
          "SuspendInterval": 0,
          "Question": null,
          "MemoryOverhead": 0,
          "MaxCpuUsage": 4608,
          "MaxMemoryUsage": 2048,
          "NumMksConnections": 0,
          "RecordReplayState": "inactive",
          "CleanPowerOff": true,
          "NeedSecondaryReason": "",
          "OnlineStandby": false,
          "MinRequiredEVCModeKey": "",
          "ConsolidationNeeded": false,
          "OfflineFeatureRequirement": [
            {
              "Key": "cpuid.lm",
              "FeatureName": "cpuid.lm",
              "Value": "Bool:Min:1"
            }
          ],
          "FeatureRequirement": null,
          "FeatureMask": null,
          "VFlashCacheAllocation": 0,
          "Paused": false,
          "SnapshotInBackground": false,
          "QuiescedForkParent": null,
          "InstantCloneFrozen": false,
          "CryptoState": ""
        },
        "Guest": {
          "GuestId": "",
          "GuestFullName": "",
          "ToolsStatus": "toolsNotInstalled",
          "ToolsVersionStatus": "guestToolsNotInstalled",
          "ToolsVersionStatus2": "guestToolsNotInstalled",
          "ToolsRunningStatus": "guestToolsNotRunning",
          "HostName": "",
          "IpAddress": "",
          "HwVersion": ""
        },
        "Config": {
          "Name": "WindowsServer2019",
          "Template": false,
          "VmPathName": "[datastore1] WindowsServer2019/WindowsServer2019.vmx",
          "MemorySizeMB": 2048,
          "CpuReservation": 0,
          "MemoryReservation": 0,
          "NumCpu": 2,
          "NumEthernetCards": 1,
          "NumVirtualDisks": 1,
          "Uuid": "564d7dbc-291d-a35f-86bb-882aa4f5f763",
          "InstanceUuid": "524aa1ee-d441-4f01-6cb3-c45e4167cb59",
          "GuestId": "windows9Server64Guest",
          "GuestFullName": "Microsoft Windows Server 2016 or later (64-bit)",
          "Annotation": "",
          "Product": null,
          "InstallBootRequired": null,
          "FtInfo": null,
          "ManagedBy": null,
          "TpmPresent": false,
          "NumVmiopBackings": 0,
          "HwVersion": ""
        },
        "Storage": {
          "Committed": 0,
          "Uncommitted": 5561504225,
          "Unshared": 0,
          "Timestamp": "2021-06-13T00:59:48.652747Z"
        },
        "QuickStats": {
          "OverallCpuUsage": 0,
          "OverallCpuDemand": 0,
          "OverallCpuReadiness": 0,
          "GuestMemoryUsage": 0,
          "HostMemoryUsage": 0,
          "GuestHeartbeatStatus": "gray",
          "DistributedCpuEntitlement": 0,
          "DistributedMemoryEntitlement": 0,
          "StaticCpuEntitlement": 0,
          "StaticMemoryEntitlement": 0,
          "GrantedMemory": 0,
          "PrivateMemory": 0,
          "SharedMemory": 0,
          "SwappedMemory": 0,
          "BalloonedMemory": 0,
          "ConsumedOverheadMemory": 0,
          "FtLogBandwidth": 0,
          "FtSecondaryLatency": 0,
          "FtLatencyStatus": "",
          "CompressedMemory": 0,
          "UptimeSeconds": 0,
          "SsdSwappedMemory": 0
        },
        "OverallStatus": "green",
        "CustomValue": null
      },
      "Datastore": [
        {
          "Type": "Datastore",
          "Value": "60c4dd35-141ece76-d699-0800272601f9"
        }
      ],
      "Network": [
        {
          "Type": "Network",
          "Value": "HaNetwork-VM Network"
        }
      ],
      "Snapshot": null,
      "RootSnapshot": null,
      "GuestHeartbeatStatus": "gray"
    }
  ]
}
```

```
$ govc vm.info CentOS8
Name:           CentOS8
  Path:         /ha-datacenter/vm/CentOS8
  UUID:         564db0df-e258-fba3-27bd-075e6f67ddf9
  Guest name:   CentOS 8 (64-bit)
  Memory:       2048MB
  CPU:          1 vCPU(s)
  Power state:  poweredOff
  Boot time:    <nil>
  IP address:   
  Host:         localhost.localdomain
```

```json
$ govc vm.info --json CentOS8
{
  "VirtualMachines": [
    {
      "Self": {
        "Type": "VirtualMachine",
        "Value": "2"
      },
      "Value": null,
      "AvailableField": null,
      "Parent": {
        "Type": "Folder",
        "Value": "ha-folder-vm"
      },
      "CustomValue": null,
      "OverallStatus": "green",
      "ConfigStatus": "green",
      "ConfigIssue": null,
      "EffectiveRole": [
        -1
      ],
      "Permission": null,
      "Name": "CentOS8",
      "DisabledMethod": [
        "setCustomValue",
        "AddTag",
        "RemoveTag",
        "RetrieveCustomValues",
        "RefreshStorageInfo",
        "RetrieveBackupAgent",
        "CreateSnapshotEx_Task",
        "RevertToCurrentSnapshot_Task",
        "ExtractOvfEnvironment",
        "PowerOffVM_Task",
        "SuspendVM_Task",
        "ResetVM_Task",
        "ShutdownGuest",
        "RebootGuest",
        "StandbyGuest",
        "AnswerVM",
        "CustomizeVM_Task",
        "CheckCustomizationSpec",
        "MigrateVM_Task",
        "RelocateVM_Task",
        "CloneVM_Task",
        "InstantClone_Task",
        "CreateForkChild_Task",
        "EnableForkParent_Task",
        "DisableForkParent_Task",
        "RetrieveForkChildren_Task",
        "RetrieveForkParent_Task",
        "CreateImportLease",
        "MarkAsTemplate",
        "MarkAsVirtualMachine",
        "ResetGuestInformation",
        "MountToolsInstaller",
        "MountToolsInstallerImage",
        "UnmountToolsInstaller",
        "UpgradeTools_Task",
        "UpgradeToolsFromImage_Task",
        "AcquireMksTicket",
        "GetConnections",
        "DropConnections",
        "AcquireTicket",
        "SetScreenResolution",
        "DefragmentAllDisks",
        "CreateSecondaryVM_Task",
        "CreateSecondaryVMEx_Task",
        "TurnOffFaultToleranceForVM_Task",
        "MakePrimaryVM_Task",
        "TerminateFaultTolerantVM_Task",
        "DisableSecondaryVM_Task",
        "EnableSecondaryVM_Task",
        "SetDisplayTopology",
        "StartRecording_Task",
        "StopRecording_Task",
        "StartReplaying_Task",
        "StopReplaying_Task",
        "PromoteDisks_Task",
        "CreateScreenshot_Task",
        "PutUsbScanCodes",
        "QueryChangedDiskAreas",
        "QueryUnownedFiles",
        "reloadVirtualMachineFromPath_Task",
        "QueryFaultToleranceCompatibility",
        "QueryFaultToleranceCompatibilityEx",
        "InvokeFSR_Task",
        "TerminateVM",
        "IsNativeSnapshotCapable",
        "ConfigureQuorumFilePathPrefix_Task",
        "InjectOvfEnvironment",
        "WipeDisk_Task",
        "ShrinkDisk_Task",
        "SendNMI",
        "ReloadEx_Task",
        "AttachDisk_Task",
        "DetachDisk_Task",
        "QueryPolicyStatus",
        "CryptoUnlock_Task"
      ],
      "RecentTask": null,
      "DeclaredAlarmState": null,
      "TriggeredAlarmState": null,
      "AlarmActionsEnabled": null,
      "Tag": null,
      "Capability": {
        "SnapshotOperationsSupported": true,
        "MultipleSnapshotsSupported": true,
        "SnapshotConfigSupported": true,
        "PoweredOffSnapshotsSupported": true,
        "MemorySnapshotsSupported": true,
        "RevertToSnapshotSupported": true,
        "QuiescedSnapshotsSupported": true,
        "DisableSnapshotsSupported": false,
        "LockSnapshotsSupported": true,
        "ConsolePreferencesSupported": false,
        "CpuFeatureMaskSupported": true,
        "S1AcpiManagementSupported": true,
        "SettingScreenResolutionSupported": false,
        "ToolsAutoUpdateSupported": false,
        "VmNpivWwnSupported": true,
        "NpivWwnOnNonRdmVmSupported": true,
        "VmNpivWwnDisableSupported": true,
        "VmNpivWwnUpdateSupported": true,
        "SwapPlacementSupported": true,
        "ToolsSyncTimeSupported": true,
        "VirtualMmuUsageSupported": false,
        "DiskSharesSupported": true,
        "BootOptionsSupported": true,
        "BootRetryOptionsSupported": true,
        "SettingVideoRamSizeSupported": true,
        "SettingDisplayTopologySupported": false,
        "RecordReplaySupported": false,
        "ChangeTrackingSupported": true,
        "MultipleCoresPerSocketSupported": true,
        "HostBasedReplicationSupported": true,
        "GuestAutoLockSupported": true,
        "MemoryReservationLockSupported": true,
        "FeatureRequirementSupported": true,
        "PoweredOnMonitorTypeChangeSupported": true,
        "SeSparseDiskSupported": true,
        "NestedHVSupported": true,
        "VPMCSupported": true,
        "SecureBootSupported": true,
        "PerVmEvcSupported": true,
        "VirtualMmuUsageIgnored": true,
        "VirtualExecUsageIgnored": true,
        "DiskOnlySnapshotOnSuspendedVMSupported": true,
        "ToolsSyncTimeAllowSupported": null,
        "SevSupported": null
      },
      "Config": {
        "ChangeVersion": "2021-06-13T01:00:23.096613Z",
        "Modified": "1970-01-01T00:00:00Z",
        "Name": "CentOS8",
        "GuestFullName": "CentOS 8 (64-bit)",
        "Version": "vmx-14",
        "Uuid": "564db0df-e258-fba3-27bd-075e6f67ddf9",
        "CreateDate": "2021-06-13T01:00:22.981081Z",
        "InstanceUuid": "5232a648-07ee-3d68-e9cc-238fee3b0cc8",
        "NpivNodeWorldWideName": null,
        "NpivPortWorldWideName": null,
        "NpivWorldWideNameType": "",
        "NpivDesiredNodeWwns": 0,
        "NpivDesiredPortWwns": 0,
        "NpivTemporaryDisabled": true,
        "NpivOnNonRdmDisks": null,
        "LocationId": "564db0df-e258-fba3-27bd-075e6f67ddf9",
        "Template": false,
        "GuestId": "centos8_64Guest",
        "AlternateGuestName": "",
        "Annotation": "",
        "Files": {
          "VmPathName": "[datastore1] CentOS8/CentOS8.vmx",
          "SnapshotDirectory": "[datastore1] CentOS8",
          "SuspendDirectory": "[datastore1] CentOS8",
          "LogDirectory": "[datastore1] CentOS8",
          "FtMetadataDirectory": ""
        },
        "Tools": {
          "ToolsVersion": 0,
          "ToolsInstallType": "",
          "AfterPowerOn": true,
          "AfterResume": true,
          "BeforeGuestStandby": true,
          "BeforeGuestShutdown": true,
          "BeforeGuestReboot": null,
          "ToolsUpgradePolicy": "manual",
          "PendingCustomization": "",
          "CustomizationKeyId": null,
          "SyncTimeWithHostAllowed": null,
          "SyncTimeWithHost": false,
          "LastInstallInfo": {
            "Counter": 0,
            "Fault": null
          }
        },
        "Flags": {
          "DisableAcceleration": false,
          "EnableLogging": true,
          "UseToe": false,
          "RunWithDebugInfo": false,
          "MonitorType": "release",
          "HtSharing": "any",
          "SnapshotDisabled": false,
          "SnapshotLocked": false,
          "DiskUuidEnabled": false,
          "VirtualMmuUsage": "automatic",
          "VirtualExecUsage": "hvAuto",
          "SnapshotPowerOffBehavior": "powerOff",
          "RecordReplayEnabled": false,
          "FaultToleranceType": "unset",
          "CbrcCacheEnabled": false,
          "VvtdEnabled": false,
          "VbsEnabled": false
        },
        "ConsolePreferences": null,
        "DefaultPowerOps": {
          "PowerOffType": "soft",
          "SuspendType": "soft",
          "ResetType": "soft",
          "DefaultPowerOffType": "soft",
          "DefaultSuspendType": "hard",
          "DefaultResetType": "soft",
          "StandbyAction": "checkpoint"
        },
        "Hardware": {
          "NumCPU": 1,
          "NumCoresPerSocket": 1,
          "MemoryMB": 2048,
          "VirtualICH7MPresent": false,
          "VirtualSMCPresent": false,
          "Device": [
            {
              "Key": 200,
              "DeviceInfo": {
                "Label": "IDE 0",
                "Summary": "IDE 0"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 0,
              "UnitNumber": null,
              "BusNumber": 0,
              "Device": null
            },
            {
              "Key": 201,
              "DeviceInfo": {
                "Label": "IDE 1",
                "Summary": "IDE 1"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 0,
              "UnitNumber": null,
              "BusNumber": 1,
              "Device": null
            },
            {
              "Key": 300,
              "DeviceInfo": {
                "Label": "PS2 controller 0",
                "Summary": "PS2 controller 0"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 0,
              "UnitNumber": null,
              "BusNumber": 0,
              "Device": [
                600,
                700
              ]
            },
            {
              "Key": 100,
              "DeviceInfo": {
                "Label": "PCI controller 0",
                "Summary": "PCI controller 0"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 0,
              "UnitNumber": null,
              "BusNumber": 0,
              "Device": [
                500,
                12000,
                7000,
                1000,
                15000,
                4000
              ]
            },
            {
              "Key": 400,
              "DeviceInfo": {
                "Label": "SIO controller 0",
                "Summary": "SIO controller 0"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 0,
              "UnitNumber": null,
              "BusNumber": 0,
              "Device": null
            },
            {
              "Key": 600,
              "DeviceInfo": {
                "Label": "Keyboard ",
                "Summary": "Keyboard"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 300,
              "UnitNumber": 0
            },
            {
              "Key": 700,
              "DeviceInfo": {
                "Label": "Pointing device",
                "Summary": "Pointing device; Device"
              },
              "Backing": {
                "DeviceName": "",
                "UseAutoDetect": false,
                "HostPointingDevice": "autodetect"
              },
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 300,
              "UnitNumber": 1
            },
            {
              "Key": 500,
              "DeviceInfo": {
                "Label": "Video card ",
                "Summary": "Video card"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 100,
              "UnitNumber": 0,
              "VideoRamSizeInKB": 4096,
              "NumDisplays": 1,
              "UseAutoDetect": true,
              "Enable3DSupport": false,
              "Use3dRenderer": "automatic",
              "GraphicsMemorySizeInKB": 262144
            },
            {
              "Key": 12000,
              "DeviceInfo": {
                "Label": "VMCI device",
                "Summary": "Device on the virtual machine PCI bus that provides support for the virtual machine communication interface"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 100,
              "UnitNumber": 17,
              "Id": -1,
              "AllowUnrestrictedCommunication": false,
              "FilterEnable": true,
              "FilterInfo": null
            },
            {
              "Key": 7000,
              "DeviceInfo": {
                "Label": "USB controller ",
                "Summary": "Auto connect Disabled"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": {
                "PciSlotNumber": -1,
                "EhciPciSlotNumber": -1
              },
              "ControllerKey": 100,
              "UnitNumber": 22,
              "BusNumber": 0,
              "Device": null,
              "AutoConnectDevices": false,
              "EhciEnabled": true
            },
            {
              "Key": 1000,
              "DeviceInfo": {
                "Label": "SCSI controller 0",
                "Summary": "VMware paravirtual SCSI"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 100,
              "UnitNumber": 3,
              "BusNumber": 0,
              "Device": [
                2000
              ],
              "HotAddRemove": true,
              "SharedBus": "noSharing",
              "ScsiCtlrUnitNumber": 7
            },
            {
              "Key": 15000,
              "DeviceInfo": {
                "Label": "SATA controller 0",
                "Summary": "AHCI"
              },
              "Backing": null,
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 100,
              "UnitNumber": 24,
              "BusNumber": 0,
              "Device": [
                16000
              ]
            },
            {
              "Key": 16000,
              "DeviceInfo": {
                "Label": "CD/DVD drive 1",
                "Summary": "ATAPI /vmfs/devices/cdrom/mpx.vmhba64:C0:T0:L0"
              },
              "Backing": {
                "DeviceName": "/vmfs/devices/cdrom/mpx.vmhba64:C0:T0:L0",
                "UseAutoDetect": false
              },
              "Connectable": {
                "MigrateConnect": "",
                "StartConnected": true,
                "AllowGuestControl": true,
                "Connected": false,
                "Status": "untried"
              },
              "SlotInfo": null,
              "ControllerKey": 15000,
              "UnitNumber": 0
            },
            {
              "Key": 2000,
              "DeviceInfo": {
                "Label": "Hard disk 1",
                "Summary": "3,145,728 KB"
              },
              "Backing": {
                "FileName": "[datastore1] CentOS8/CentOS8.vmdk",
                "Datastore": {
                  "Type": "Datastore",
                  "Value": "60c4dd35-141ece76-d699-0800272601f9"
                },
                "BackingObjectId": "",
                "DiskMode": "persistent",
                "Split": false,
                "WriteThrough": false,
                "ThinProvisioned": true,
                "EagerlyScrub": null,
                "Uuid": "6000C298-deeb-2a67-df53-d90090160fa6",
                "ContentId": "3879d015208cf38203e2a694fffffffe",
                "ChangeId": "",
                "Parent": null,
                "DeltaDiskFormat": "",
                "DigestEnabled": false,
                "DeltaGrainSize": 0,
                "DeltaDiskFormatVariant": "",
                "Sharing": "sharingNone",
                "KeyId": null
              },
              "Connectable": null,
              "SlotInfo": null,
              "ControllerKey": 1000,
              "UnitNumber": 0,
              "CapacityInKB": 3145728,
              "CapacityInBytes": 3221225472,
              "Shares": {
                "Shares": 1000,
                "Level": "normal"
              },
              "StorageIOAllocation": {
                "Limit": -1,
                "Shares": {
                  "Shares": 1000,
                  "Level": "normal"
                },
                "Reservation": 0
              },
              "DiskObjectId": "2-2000",
              "VFlashCacheConfigInfo": null,
              "Iofilter": null,
              "VDiskId": null,
              "NativeUnmanagedLinkedClone": false
            },
            {
              "Key": 4000,
              "DeviceInfo": {
                "Label": "Network adapter 1",
                "Summary": "VM Network2"
              },
              "Backing": {
                "DeviceName": "VM Network2",
                "UseAutoDetect": false,
                "Network": {
                  "Type": "Network",
                  "Value": "HaNetwork-VM Network2"
                },
                "InPassthroughMode": null
              },
              "Connectable": {
                "MigrateConnect": "unset",
                "StartConnected": true,
                "AllowGuestControl": true,
                "Connected": false,
                "Status": "untried"
              },
              "SlotInfo": null,
              "ControllerKey": 100,
              "UnitNumber": 7,
              "AddressType": "generated",
              "MacAddress": "",
              "WakeOnLanEnabled": false,
              "ResourceAllocation": {
                "Reservation": 0,
                "Share": {
                  "Shares": 50,
                  "Level": "normal"
                },
                "Limit": -1
              },
              "ExternalId": "",
              "UptCompatibilityEnabled": true
            }
          ]
        },
        "VcpuConfig": null,
        "CpuAllocation": {
          "Reservation": 0,
          "ExpandableReservation": false,
          "Limit": -1,
          "Shares": {
            "Shares": 1000,
            "Level": "normal"
          },
          "OverheadLimit": null
        },
        "MemoryAllocation": {
          "Reservation": 0,
          "ExpandableReservation": false,
          "Limit": -1,
          "Shares": {
            "Shares": 20480,
            "Level": "normal"
          },
          "OverheadLimit": null
        },
        "LatencySensitivity": {
          "Level": "normal",
          "Sensitivity": 0
        },
        "MemoryHotAddEnabled": false,
        "CpuHotAddEnabled": false,
        "CpuHotRemoveEnabled": false,
        "HotPlugMemoryLimit": 0,
        "HotPlugMemoryIncrementSize": 0,
        "CpuAffinity": null,
        "MemoryAffinity": null,
        "NetworkShaper": null,
        "ExtraConfig": [
          {
            "Key": "nvram",
            "Value": "CentOS8.nvram"
          },
          {
            "Key": "pciBridge0.present",
            "Value": "TRUE"
          },
          {
            "Key": "svga.present",
            "Value": "TRUE"
          },
          {
            "Key": "pciBridge4.present",
            "Value": "TRUE"
          },
          {
            "Key": "pciBridge4.virtualDev",
            "Value": "pcieRootPort"
          },
          {
            "Key": "pciBridge4.functions",
            "Value": "8"
          },
          {
            "Key": "pciBridge5.present",
            "Value": "TRUE"
          },
          {
            "Key": "pciBridge5.virtualDev",
            "Value": "pcieRootPort"
          },
          {
            "Key": "pciBridge5.functions",
            "Value": "8"
          },
          {
            "Key": "pciBridge6.present",
            "Value": "TRUE"
          },
          {
            "Key": "pciBridge6.virtualDev",
            "Value": "pcieRootPort"
          },
          {
            "Key": "pciBridge6.functions",
            "Value": "8"
          },
          {
            "Key": "pciBridge7.present",
            "Value": "TRUE"
          },
          {
            "Key": "pciBridge7.virtualDev",
            "Value": "pcieRootPort"
          },
          {
            "Key": "pciBridge7.functions",
            "Value": "8"
          },
          {
            "Key": "hpet0.present",
            "Value": "TRUE"
          },
          {
            "Key": "RemoteDisplay.maxConnections",
            "Value": "-1"
          },
          {
            "Key": "sched.cpu.latencySensitivity",
            "Value": "normal"
          },
          {
            "Key": "svga.autodetect",
            "Value": "TRUE"
          },
          {
            "Key": "vmware.tools.internalversion",
            "Value": "0"
          },
          {
            "Key": "vmware.tools.requiredversion",
            "Value": "10346"
          },
          {
            "Key": "migrate.hostLogState",
            "Value": "none"
          },
          {
            "Key": "migrate.migrationId",
            "Value": "0"
          },
          {
            "Key": "migrate.hostLog",
            "Value": ""
          }
        ],
        "CpuFeatureMask": [
          {
            "Level": 0,
            "Vendor": "",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": 0,
            "Vendor": "amd",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": 1,
            "Vendor": "",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": 1,
            "Vendor": "amd",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": -2147483648,
            "Vendor": "",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": -2147483648,
            "Vendor": "amd",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": -2147483647,
            "Vendor": "",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": -2147483647,
            "Vendor": "amd",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": -2147483640,
            "Vendor": "",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          },
          {
            "Level": -2147483640,
            "Vendor": "amd",
            "Eax": "",
            "Ebx": "",
            "Ecx": "",
            "Edx": ""
          }
        ],
        "DatastoreUrl": [
          {
            "Name": "datastore1",
            "Url": "/vmfs/volumes/60c4dd35-141ece76-d699-0800272601f9/"
          }
        ],
        "SwapPlacement": "inherit",
        "BootOptions": {
          "BootDelay": 0,
          "EnterBIOSSetup": false,
          "EfiSecureBootEnabled": true,
          "BootRetryEnabled": false,
          "BootRetryDelay": 10,
          "BootOrder": null,
          "NetworkBootProtocol": "ipv4"
        },
        "FtInfo": null,
        "RepConfig": null,
        "VAppConfig": null,
        "VAssertsEnabled": false,
        "ChangeTrackingEnabled": false,
        "Firmware": "efi",
        "MaxMksConnections": -1,
        "GuestAutoLockEnabled": false,
        "ManagedBy": null,
        "MemoryReservationLockedToMax": false,
        "InitialOverhead": {
          "InitialMemoryReservation": 178376704,
          "InitialSwapReservation": 190685184
        },
        "NestedHVEnabled": false,
        "VPMCEnabled": false,
        "ScheduledHardwareUpgradeInfo": {
          "UpgradePolicy": "never",
          "VersionKey": "",
          "ScheduledHardwareUpgradeStatus": "none",
          "Fault": null
        },
        "ForkConfigInfo": null,
        "VFlashCacheReservation": 0,
        "VmxConfigChecksum": "QURRRTVUK1doWmJtWmhOeE9QSVFDWDN2dXhzPQ==",
        "MessageBusTunnelEnabled": false,
        "VmStorageObjectId": "",
        "SwapStorageObjectId": "",
        "KeyId": null,
        "GuestIntegrityInfo": {
          "Enabled": false
        },
        "MigrateEncryption": "opportunistic",
        "SgxInfo": null,
        "ContentLibItemInfo": null,
        "GuestMonitoringModeInfo": null,
        "SevEnabled": null
      },
      "Layout": {
        "ConfigFile": [
          "CentOS8.vmsd"
        ],
        "LogFile": null,
        "Disk": [
          {
            "Key": 2000,
            "DiskFile": [
              "[datastore1] CentOS8/CentOS8.vmdk"
            ]
          }
        ],
        "Snapshot": null,
        "SwapFile": ""
      },
      "LayoutEx": {
        "File": [
          {
            "Key": 0,
            "Name": "[datastore1] CentOS8/CentOS8.vmx",
            "Type": "config",
            "Size": 0,
            "UniqueSize": 0,
            "BackingObjectId": "",
            "Accessible": true
          },
          {
            "Key": 1,
            "Name": "[datastore1] CentOS8/CentOS8.vmsd",
            "Type": "snapshotList",
            "Size": 0,
            "UniqueSize": 0,
            "BackingObjectId": "",
            "Accessible": true
          },
          {
            "Key": 2,
            "Name": "[datastore1] CentOS8/CentOS8.vmdk",
            "Type": "diskDescriptor",
            "Size": 0,
            "UniqueSize": 0,
            "BackingObjectId": "",
            "Accessible": true
          },
          {
            "Key": 3,
            "Name": "[datastore1] CentOS8/CentOS8-flat.vmdk",
            "Type": "diskExtent",
            "Size": 0,
            "UniqueSize": 0,
            "BackingObjectId": "",
            "Accessible": true
          }
        ],
        "Disk": [
          {
            "Key": 2000,
            "Chain": [
              {
                "FileKey": [
                  2,
                  3
                ]
              }
            ]
          }
        ],
        "Snapshot": null,
        "Timestamp": "2021-06-13T01:00:23.169126Z"
      },
      "Storage": {
        "PerDatastoreUsage": [
          {
            "Datastore": {
              "Type": "Datastore",
              "Value": "60c4dd35-141ece76-d699-0800272601f9"
            },
            "Committed": 0,
            "Uncommitted": 5559394775,
            "Unshared": 0
          }
        ],
        "Timestamp": "2021-06-13T01:00:23.169288Z"
      },
      "EnvironmentBrowser": {
        "Type": "EnvironmentBrowser",
        "Value": "2-envmgr"
      },
      "ResourcePool": {
        "Type": "ResourcePool",
        "Value": "ha-root-pool"
      },
      "ParentVApp": null,
      "ResourceConfig": {
        "Entity": {
          "Type": "VirtualMachine",
          "Value": "2"
        },
        "ChangeVersion": "",
        "LastModified": null,
        "CpuAllocation": {
          "Reservation": 0,
          "ExpandableReservation": false,
          "Limit": -1,
          "Shares": {
            "Shares": 1000,
            "Level": "normal"
          },
          "OverheadLimit": null
        },
        "MemoryAllocation": {
          "Reservation": 0,
          "ExpandableReservation": false,
          "Limit": -1,
          "Shares": {
            "Shares": 20480,
            "Level": "normal"
          },
          "OverheadLimit": null
        },
        "ScaleDescendantsShares": ""
      },
      "Runtime": {
        "Device": [
          {
            "RuntimeState": {
              "VmDirectPathGen2Active": false,
              "VmDirectPathGen2InactiveReasonVm": [
                "vmNptDisabledOrDisconnectedAdapter"
              ],
              "VmDirectPathGen2InactiveReasonOther": null,
              "VmDirectPathGen2InactiveReasonExtended": "",
              "ReservationStatus": "",
              "AttachmentStatus": "red",
              "FeatureRequirement": null
            },
            "Key": 4000
          }
        ],
        "Host": {
          "Type": "HostSystem",
          "Value": "ha-host"
        },
        "ConnectionState": "connected",
        "PowerState": "poweredOff",
        "FaultToleranceState": "notConfigured",
        "DasVmProtection": null,
        "ToolsInstallerMounted": false,
        "SuspendTime": null,
        "BootTime": null,
        "SuspendInterval": 0,
        "Question": null,
        "MemoryOverhead": 0,
        "MaxCpuUsage": 2304,
        "MaxMemoryUsage": 2048,
        "NumMksConnections": 0,
        "RecordReplayState": "inactive",
        "CleanPowerOff": true,
        "NeedSecondaryReason": "",
        "OnlineStandby": false,
        "MinRequiredEVCModeKey": "",
        "ConsolidationNeeded": false,
        "OfflineFeatureRequirement": [
          {
            "Key": "cpuid.lm",
            "FeatureName": "cpuid.lm",
            "Value": "Bool:Min:1"
          }
        ],
        "FeatureRequirement": null,
        "FeatureMask": null,
        "VFlashCacheAllocation": 0,
        "Paused": false,
        "SnapshotInBackground": false,
        "QuiescedForkParent": null,
        "InstantCloneFrozen": false,
        "CryptoState": ""
      },
      "Guest": {
        "ToolsStatus": "toolsNotInstalled",
        "ToolsVersionStatus": "guestToolsNotInstalled",
        "ToolsVersionStatus2": "guestToolsNotInstalled",
        "ToolsRunningStatus": "guestToolsNotRunning",
        "ToolsVersion": "0",
        "ToolsInstallType": "",
        "GuestId": "",
        "GuestFamily": "",
        "GuestFullName": "",
        "HostName": "",
        "IpAddress": "",
        "Net": null,
        "IpStack": null,
        "Disk": null,
        "Screen": {
          "Width": 0,
          "Height": 0
        },
        "GuestState": "notRunning",
        "AppHeartbeatStatus": "appStatusGray",
        "GuestKernelCrashed": false,
        "AppState": "none",
        "GuestOperationsReady": false,
        "InteractiveGuestOperationsReady": false,
        "GuestStateChangeSupported": false,
        "GenerationInfo": null,
        "HwVersion": ""
      },
      "Summary": {
        "Vm": {
          "Type": "VirtualMachine",
          "Value": "2"
        },
        "Runtime": {
          "Device": [
            {
              "RuntimeState": {
                "VmDirectPathGen2Active": false,
                "VmDirectPathGen2InactiveReasonVm": [
                  "vmNptDisabledOrDisconnectedAdapter"
                ],
                "VmDirectPathGen2InactiveReasonOther": null,
                "VmDirectPathGen2InactiveReasonExtended": "",
                "ReservationStatus": "",
                "AttachmentStatus": "red",
                "FeatureRequirement": null
              },
              "Key": 4000
            }
          ],
          "Host": {
            "Type": "HostSystem",
            "Value": "ha-host"
          },
          "ConnectionState": "connected",
          "PowerState": "poweredOff",
          "FaultToleranceState": "notConfigured",
          "DasVmProtection": null,
          "ToolsInstallerMounted": false,
          "SuspendTime": null,
          "BootTime": null,
          "SuspendInterval": 0,
          "Question": null,
          "MemoryOverhead": 0,
          "MaxCpuUsage": 2304,
          "MaxMemoryUsage": 2048,
          "NumMksConnections": 0,
          "RecordReplayState": "inactive",
          "CleanPowerOff": true,
          "NeedSecondaryReason": "",
          "OnlineStandby": false,
          "MinRequiredEVCModeKey": "",
          "ConsolidationNeeded": false,
          "OfflineFeatureRequirement": [
            {
              "Key": "cpuid.lm",
              "FeatureName": "cpuid.lm",
              "Value": "Bool:Min:1"
            }
          ],
          "FeatureRequirement": null,
          "FeatureMask": null,
          "VFlashCacheAllocation": 0,
          "Paused": false,
          "SnapshotInBackground": false,
          "QuiescedForkParent": null,
          "InstantCloneFrozen": false,
          "CryptoState": ""
        },
        "Guest": {
          "GuestId": "",
          "GuestFullName": "",
          "ToolsStatus": "toolsNotInstalled",
          "ToolsVersionStatus": "guestToolsNotInstalled",
          "ToolsVersionStatus2": "guestToolsNotInstalled",
          "ToolsRunningStatus": "guestToolsNotRunning",
          "HostName": "",
          "IpAddress": "",
          "HwVersion": ""
        },
        "Config": {
          "Name": "CentOS8",
          "Template": false,
          "VmPathName": "[datastore1] CentOS8/CentOS8.vmx",
          "MemorySizeMB": 2048,
          "CpuReservation": 0,
          "MemoryReservation": 0,
          "NumCpu": 1,
          "NumEthernetCards": 1,
          "NumVirtualDisks": 1,
          "Uuid": "564db0df-e258-fba3-27bd-075e6f67ddf9",
          "InstanceUuid": "5232a648-07ee-3d68-e9cc-238fee3b0cc8",
          "GuestId": "centos8_64Guest",
          "GuestFullName": "CentOS 8 (64-bit)",
          "Annotation": "",
          "Product": null,
          "InstallBootRequired": null,
          "FtInfo": null,
          "ManagedBy": null,
          "TpmPresent": false,
          "NumVmiopBackings": 0,
          "HwVersion": ""
        },
        "Storage": {
          "Committed": 0,
          "Uncommitted": 5559394775,
          "Unshared": 0,
          "Timestamp": "2021-06-13T01:00:23.169288Z"
        },
        "QuickStats": {
          "OverallCpuUsage": 0,
          "OverallCpuDemand": 0,
          "OverallCpuReadiness": 0,
          "GuestMemoryUsage": 0,
          "HostMemoryUsage": 0,
          "GuestHeartbeatStatus": "gray",
          "DistributedCpuEntitlement": 0,
          "DistributedMemoryEntitlement": 0,
          "StaticCpuEntitlement": 0,
          "StaticMemoryEntitlement": 0,
          "GrantedMemory": 0,
          "PrivateMemory": 0,
          "SharedMemory": 0,
          "SwappedMemory": 0,
          "BalloonedMemory": 0,
          "ConsumedOverheadMemory": 0,
          "FtLogBandwidth": 0,
          "FtSecondaryLatency": 0,
          "FtLatencyStatus": "",
          "CompressedMemory": 0,
          "UptimeSeconds": 0,
          "SsdSwappedMemory": 0
        },
        "OverallStatus": "green",
        "CustomValue": null
      },
      "Datastore": [
        {
          "Type": "Datastore",
          "Value": "60c4dd35-141ece76-d699-0800272601f9"
        }
      ],
      "Network": [
        {
          "Type": "Network",
          "Value": "HaNetwork-VM Network2"
        }
      ],
      "Snapshot": null,
      "RootSnapshot": null,
      "GuestHeartbeatStatus": "gray"
    }
  ]
}
```

```
$ govc vm.option.info
amazonlinux2_64Guest     Amazon Linux 2 (64-bit)
windows9Server64Guest    Microsoft Windows Server 2016 or later (64-bit)
windows8Server64Guest    Microsoft Windows Server 2012 (64-bit)
windows7Server64Guest    Microsoft Windows Server 2008 R2 (64-bit)
winLonghorn64Guest       Microsoft Windows Server 2008 (64-bit)
winLonghornGuest         Microsoft Windows Server 2008 (32-bit)
winNetEnterprise64Guest  Microsoft Windows Server 2003 (64-bit)
winNetEnterpriseGuest    Microsoft Windows Server 2003 (32-bit)
winNetDatacenter64Guest  Microsoft Windows Server 2003 Datacenter (64-bit)
winNetDatacenterGuest    Microsoft Windows Server 2003 Datacenter (32-bit)
winNetStandard64Guest    Microsoft Windows Server 2003 Standard (64-bit)
winNetStandardGuest      Microsoft Windows Server 2003 Standard (32-bit)
winNetWebGuest           Microsoft Windows Server 2003 Web Edition (32-bit)
winNetBusinessGuest      Microsoft Small Business Server 2003
windows9_64Guest         Microsoft Windows 10 (64-bit)
windows9Guest            Microsoft Windows 10 (32-bit)
windows8_64Guest         Microsoft Windows 8.x (64-bit)
windows8Guest            Microsoft Windows 8.x (32-bit)
windows7_64Guest         Microsoft Windows 7 (64-bit)
windows7Guest            Microsoft Windows 7 (32-bit)
winVista64Guest          Microsoft Windows Vista (64-bit)
winVistaGuest            Microsoft Windows Vista (32-bit)
winXPPro64Guest          Microsoft Windows XP Professional (64-bit)
winXPProGuest            Microsoft Windows XP Professional (32-bit)
win2000AdvServGuest      Microsoft Windows 2000
win2000ServGuest         Microsoft Windows 2000 Server
win2000ProGuest          Microsoft Windows 2000 Professional
winNTGuest               Microsoft Windows NT
win98Guest               Microsoft Windows 98
win95Guest               Microsoft Windows 95
win31Guest               Microsoft Windows 3.1
dosGuest                 Microsoft MS-DOS
vmwarePhoton64Guest      VMware Photon OS (64-bit)
rhel8_64Guest            Red Hat Enterprise Linux 8 (64-bit)
rhel7_64Guest            Red Hat Enterprise Linux 7 (64-bit)
rhel6_64Guest            Red Hat Enterprise Linux 6 (64-bit)
rhel6Guest               Red Hat Enterprise Linux 6 (32-bit)
rhel5_64Guest            Red Hat Enterprise Linux 5 (64-bit)
rhel5Guest               Red Hat Enterprise Linux 5 (32-bit)
rhel4_64Guest            Red Hat Enterprise Linux 4 (64-bit)
rhel4Guest               Red Hat Enterprise Linux 4 (32-bit)
rhel3_64Guest            Red Hat Enterprise Linux 3 (64-bit)
rhel3Guest               Red Hat Enterprise Linux 3 (32-bit)
rhel2Guest               Red Hat Enterprise Linux 2.1
sles15_64Guest           SUSE Linux Enterprise 15 (64-bit)
sles12_64Guest           SUSE Linux Enterprise 12 (64-bit)
sles11_64Guest           SUSE Linux Enterprise 11 (64-bit)
sles11Guest              SUSE Linux Enterprise 11 (32-bit)
sles10_64Guest           SUSE Linux Enterprise 10 (64-bit)
sles10Guest              SUSE Linux Enterprise 10 (32-bit)
sles64Guest              SUSE Linux Enterprise 8/9 (64-bit)
slesGuest                SUSE Linux Enterprise 8/9 (32-bit)
centos8_64Guest          CentOS 8 (64-bit)
centos7_64Guest          CentOS 7 (64-bit)
centos6_64Guest          CentOS 6 (64-bit)
centos6Guest             CentOS 6 (32-bit)
centos64Guest            CentOS 4/5 or later (64-bit)
centosGuest              CentOS 4/5 or later (32-bit)
debian10_64Guest         Debian GNU/Linux 10 (64-bit)
debian10Guest            Debian GNU/Linux 10 (32-bit)
debian9_64Guest          Debian GNU/Linux 9 (64-bit)
debian9Guest             Debian GNU/Linux 9 (32-bit)
debian8_64Guest          Debian GNU/Linux 8 (64-bit)
debian8Guest             Debian GNU/Linux 8 (32-bit)
debian7_64Guest          Debian GNU/Linux 7 (64-bit)
debian7Guest             Debian GNU/Linux 7 (32-bit)
debian6_64Guest          Debian GNU/Linux 6 (64-bit)
debian6Guest             Debian GNU/Linux 6 (32-bit)
debian5_64Guest          Debian GNU/Linux 5 (64-bit)
debian5Guest             Debian GNU/Linux 5 (32-bit)
debian4_64Guest          Debian GNU/Linux 4 (64-bit)
debian4Guest             Debian GNU/Linux 4 (32-bit)
opensuse64Guest          SUSE openSUSE (64-bit)
opensuseGuest            SUSE openSUSE (32-bit)
asianux8_64Guest         Asianux 8 (64-bit)
asianux7_64Guest         Asianux 7 (64-bit)
asianux4_64Guest         Asianux 4 (64-bit)
asianux4Guest            Asianux 4 (32-bit)
asianux3_64Guest         Asianux 3 (64-bit)
asianux3Guest            Asianux 3 (32-bit)
fedora64Guest            Red Hat Fedora (64-bit)
fedoraGuest              Red Hat Fedora (32-bit)
oesGuest                 Novell Open Enterprise Server
oracleLinux8_64Guest     Oracle Linux 8 (64-bit)
oracleLinux7_64Guest     Oracle Linux 7 (64-bit)
oracleLinux6_64Guest     Oracle Linux 6 (64-bit)
oracleLinux6Guest        Oracle Linux 6 (32-bit)
oracleLinux64Guest       Oracle Linux 4/5 or later (64-bit)
oracleLinuxGuest         Oracle Linux 4/5 or later (32-bit)
ubuntu64Guest            Ubuntu Linux (64-bit)
ubuntuGuest              Ubuntu Linux (32-bit)
coreos64Guest            CoreOS Linux (64-bit)
other4xLinux64Guest      Other 4.x or later Linux (64-bit)
other4xLinuxGuest        Other 4.x or later Linux (32-bit)
other3xLinux64Guest      Other 3.x Linux (64-bit)
other3xLinuxGuest        Other 3.x Linux (32-bit)
other26xLinux64Guest     Other 2.6.x Linux (64-bit)
other26xLinuxGuest       Other 2.6.x Linux (32-bit)
other24xLinux64Guest     Other 2.4.x Linux (64-bit)
other24xLinuxGuest       Other 2.4.x Linux (32-bit)
otherLinux64Guest        Other Linux (64-bit)
otherLinuxGuest          Other Linux (32-bit)
darwin18_64Guest         Apple macOS 10.14 (64-bit)
darwin17_64Guest         Apple macOS 10.13 (64-bit)
darwin16_64Guest         Apple macOS 10.12 (64-bit)
darwin15_64Guest         Apple Mac OS X 10.11 (64-bit)
darwin14_64Guest         Apple Mac OS X 10.10 (64-bit)
darwin13_64Guest         Apple Mac OS X 10.9 (64-bit)
darwin12_64Guest         Apple Mac OS X 10.8 (64-bit)
darwin11_64Guest         Apple Mac OS X 10.7 (64-bit)
darwin11Guest            Apple Mac OS X 10.7 (32-bit)
darwin10_64Guest         Apple Mac OS X 10.6 (64-bit)
darwin10Guest            Apple Mac OS X 10.6 (32-bit)
darwin64Guest            Apple Mac OS X 10.5 (64-bit)
darwinGuest              Apple Mac OS X 10.5 (32-bit)
freebsd12_64Guest        FreeBSD 12 or later versions (64-bit)
freebsd12Guest           FreeBSD 12 or later versions (32-bit)
freebsd11_64Guest        FreeBSD 11 (64-bit)
freebsd11Guest           FreeBSD 11 (32-bit)
freebsd64Guest           FreeBSD Pre-11 versions (64-bit)
freebsdGuest             FreeBSD Pre-11 versions (32-bit)
os2Guest                 IBM OS/2
netware6Guest            Novell NetWare 6.x
netware5Guest            Novell NetWare 5.1
solaris11_64Guest        Oracle Solaris 11 (64-bit)
solaris10_64Guest        Oracle Solaris 10 (64-bit)
solaris10Guest           Oracle Solaris 10 (32-bit)
solaris9Guest            Sun Microsystems Solaris 9
solaris8Guest            Sun Microsystems Solaris 8
openServer6Guest         SCO OpenServer 6
openServer5Guest         SCO OpenServer 5
unixWare7Guest           SCO UnixWare 7
eComStation2Guest        Serenity Systems eComStation 2
eComStationGuest         Serenity Systems eComStation 1
otherGuest64             Other (64-bit)
otherGuest               Other (32-bit)
vmkernel65Guest          VMware ESXi 6.5 or later
vmkernel6Guest           VMware ESXi 6.0
vmkernel5Guest           VMware ESXi 5.x
vmkernelGuest            VMware ESX 4.x
```

## Others

```
$ govc device.info
```

```
$ govc dvs.portgroup.info
```

```
$ govc extension.info
```

```
$ govc fields.info
```

```
$ govc library.info
```

```
$ govc library.subscriber.info
```

```
$ govc metric.info CentOS8
```

```
$ govc metric.info WindowsServer2019
```

```
$ govc metric.interval.info
```

```
$ govc pool.info
```

```
$ govc storage.policy.info
```

```
$ govc tags.category.info
```

```
$ govc tags.info
```

```
$ govc vcsa.log.forwarding.info
```

