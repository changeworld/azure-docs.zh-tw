---
title: Azure VMware 解決方案（按雲簡單 ） - 將工作負載 VM 遷移到私有雲
description: 描述如何將虛擬機器從本地 vCenter 遷移到雲簡單私有雲 vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87b8a112a319519dbde977ee30136a884137212d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019990"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>將工作負載 VM 從本地 vCenter 遷移到私有雲 vCenter 環境

要將 VM 從本地資料中心遷移到雲簡單私有雲，可以使用多個選項。  私有雲提供對 VMware vCenter 的本機訪問，VMware 支援的工具可用於工作負載遷移。 本文介紹了一些 vCenter 遷移選項。

## <a name="prerequisites"></a>Prerequisites

從本地資料中心遷移 VM 和資料需要從資料中心到私有雲環境的網路連接。  使用以下任一方法建立網路連接：

* 本地環境和私有雲之間的[網站到網站 VPN 連接](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。
* 特快線路全球覆蓋連接您的本地快速路由電路和雲簡單快速路由電路。

從本地 vCenter 環境到私有雲的網路路徑必須可用於使用 vMotion 遷移 VM。  本地 vCenter 上的 vMotion 網路必須具有路由能力。  驗證防火牆是否允許本地 vCenter 和私有雲 vCenter 之間的所有 vMotion 流量。 （在私有雲上，預設情況下配置 vMotion 網路上的路由。

## <a name="migrate-isos-and-templates"></a>遷移 ISO 和範本

要在私有雲上創建新虛擬機器，請使用 ISO 和 VM 範本。  要將 ISO 和範本上載到私有雲 vCenter 並使其可用，請使用以下方法。

1. 從 vCenter UI 將 ISO 上載到私有雲 vCenter。
2. 在私有雲 vCenter 上發佈[內容庫](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html)：

    1. 發佈本地內容庫。
    2. 在私有雲 vCenter 上創建新的內容庫。
    3. 訂閱已發佈的本地內容庫。
    4. 同步內容庫以訪問訂閱的內容。

## <a name="migrate-vms-using-powercli"></a>使用 PowerCLI 遷移 VM

要將 VM 從本地 vCenter 遷移到私有雲 vCenter，請使用 VMware PowerCLI 或 VMware 實驗室提供的跨 vCenter 工作負載遷移實用程式。  下面的示例腳本顯示了 PowerCLI 遷移命令。

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> 要使用目標 vCenter 伺服器和 ESXi 主機的名稱，請配置從本地到私有雲的 DNS 轉發。

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>使用 NSX 第 2 層 VPN 遷移 VM

此選項允許將工作負載從本地 VMware 環境即時移轉到 Azure 中的私有雲。  使用此拉伸的第 2 層網路，本地的子網將在私有雲上可用。  遷移後，VM 不需要新的 IP 位址分配。

[使用第 2 層拉伸網路遷移工作負載](migration-layer-2-vpn.md)介紹如何使用第 2 層 VPN 將第 2 層網路從本地環境擴展到私有雲。

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>使用備份和災害復原工具遷移 VM

可以使用備份/還原工具和災害復原工具將 VM 遷移到私有雲。  使用私有雲作為使用協力廠商工具創建的備份進行還原的目標。  使用 VMware SRM 或協力廠商工具，私有雲也可以用作災害復原的目標。

有關使用這些工具的詳細資訊，請參閱以下主題：

* [使用 Veeam B&R 備份雲簡單私有雲上的工作負載虛擬機器](backup-workloads-veeam.md)
* [將雲簡單私有雲設置為本地 VMware 工作負載的災害復原網站](disaster-recovery-zerto.md)
