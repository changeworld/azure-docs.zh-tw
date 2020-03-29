---
title: 高性能計算 - Azure 虛擬機器 |微軟文檔
description: 瞭解 Azure 上的高性能計算。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 10549abfbdacf1fc1ae6b99f4cab20a290c32a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707814"
---
# <a name="optimization-for-linux"></a>針對 Linux 進行最佳化

本文展示了優化作業系統映射的一些關鍵技術。 瞭解有關啟用[InfiniBand](enable-infiniband.md)和優化作業系統映射的更多詳細資訊。

## <a name="update-lis"></a>更新 LIS

如果使用自訂映射進行部署（例如，較舊的作業系統（如 CentOS/RHEL 7.4 或 7.5），則更新 VM 上的 LIS。

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>回收記憶體

通過自動回收記憶體來提高效率，以避免遠端記憶體訪問。

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

要使這種情況在 VM 重新開機後持續：

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>禁用防火牆和 SELinux

禁用防火牆和 SELinux。

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>禁用 cpu 電源

禁用 cpu 電源。

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>後續步驟

* 瞭解有關啟用[InfiniBand](enable-infiniband.md)和優化作業系統映射的更多詳細資訊。

* 在 Azure 上瞭解有關[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)的更多詳細資訊。
