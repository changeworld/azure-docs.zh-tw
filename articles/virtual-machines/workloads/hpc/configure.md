---
title: 高效能計算-Azure 虛擬機器 |Microsoft Docs
description: 瞭解 Azure 上的高效能運算。
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
ms.openlocfilehash: 723419b97dc024a700d860dd3fe61ff48073a587
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87019993"
---
# <a name="optimization-for-linux"></a>針對 Linux 進行最佳化

本文說明將 OS 映射優化的幾個重要技巧。 深入瞭解如何[啟用](enable-infiniband.md)「未充分運用」和「優化 OS 映射」。

## <a name="update-lis"></a>更新 .LIS

如果使用自訂映射（例如，CentOS/RHEL 7.4 或7.5 等較舊的 OS）進行部署，請在 VM 上更新 .LIS。

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>回收記憶體

藉由自動回收記憶體來避免遠端記憶體存取，藉此提升效率。

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

若要在 VM 重新開機之後保存此內容：

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>停用防火牆和 SELinux

停用防火牆和 SELinux。

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>停用 cpupower

停用 cpupower。

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>接下來的步驟

* 深入瞭解如何[啟用](enable-infiniband.md)「未充分運用」和「優化 OS 映射」。

* 深入瞭解 Azure 上的[HPC](/azure/architecture/topics/high-performance-computing/) 。
