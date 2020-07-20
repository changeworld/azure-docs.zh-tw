---
title: Azure VMware Solution by CloudSimple-將工作負載 Vm 遷移至私用雲端
description: 說明如何將虛擬機器從內部部署 vCenter 遷移至 CloudSimple 私用雲端 vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87b8a112a319519dbde977ee30136a884137212d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77019990"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>將工作負載 Vm 從內部部署 vCenter 遷移至私用雲端 vCenter 環境

若要將 Vm 從內部部署資料中心遷移至您的 CloudSimple 私人雲端，有數個選項可供使用。  私用雲端提供 VMware vCenter 的原生存取，而 VMware 支援的工具則可用於工作負載遷移。 本文說明一些 vCenter 遷移選項。

## <a name="prerequisites"></a>必要條件

從您的內部部署資料中心遷移 Vm 和資料需要從資料中心到私人雲端環境的網路連線。  使用下列其中一種方法來建立網路連線能力：

* 內部部署環境與私人雲端之間的[站對站 VPN](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)連線。
* ExpressRoute 全球連線到您的內部部署 ExpressRoute 線路與 CloudSimple ExpressRoute 線路之間的連線。

從內部部署 vCenter 環境到私人雲端的網路路徑，必須可供使用 vMotion 來遷移 Vm。  您內部部署 vCenter 上的 vMotion 網路必須有路由功能。  確認您的防火牆允許內部部署 vCenter 與私人雲端 vCenter 之間的所有 vMotion 流量。 （在私人雲端上，預設會設定 vMotion 網路上的路由）。

## <a name="migrate-isos-and-templates"></a>遷移 Iso 和範本

若要在您的私人雲端上建立新的虛擬機器，請使用 Iso 和 VM 範本。  若要將 Iso 和範本上傳至您的私用雲端 vCenter 並使其可供使用，請使用下列方法。

1. 從 vCenter UI 將 ISO 上傳至私人雲端 vCenter。
2. 在您的私用雲端 vCenter 上[發佈內容庫](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html)：

    1. 發佈您的內部部署內容庫。
    2. 在私人雲端 vCenter 上建立新的內容庫。
    3. 訂閱已發佈的內部部署內容庫。
    4. 同步處理內容庫以存取訂閱的內容。

## <a name="migrate-vms-using-powercli"></a>使用 PowerCLI 遷移 Vm

若要將 Vm 從內部部署 vCenter 遷移至私人雲端 vCenter，請使用 vmware PowerCLI 或 VMware Labs 提供的跨 vCenter 工作負載遷移公用程式。  下列範例腳本會顯示 PowerCLI 遷移命令。

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> 若要使用目的地 vCenter 伺服器和 ESXi 主機的名稱，請設定從內部部署到私人雲端的 DNS 轉送。

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>使用 NSX 第2層 VPN 遷移 Vm

此選項可讓您將工作負載從內部部署 VMware 環境即時移轉至 Azure 中的私人雲端。  透過此延伸的第2層網路，私人雲端上將會提供來自內部部署的子網。  在遷移之後，Vm 不需要新的 IP 位址指派。

[使用第2層延展的網路遷移工作負載](migration-layer-2-vpn.md)說明如何使用第2層 VPN 將第2層網路從內部部署環境延伸到私人雲端。

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>使用備份和嚴重損壞修復工具遷移 Vm

您可以使用備份/還原工具和嚴重損壞修復工具來完成 Vm 到私人雲端的遷移。  使用私人雲端作為目標，從使用協力廠商工具建立的備份進行還原。  私用雲端也可作為使用 VMware SRM 或協力廠商工具的嚴重損壞修復目標。

如需使用這些工具的詳細資訊，請參閱下列主題：

* [使用 Veeam B&R 來備份 CloudSimple 私人雲端上的工作負載虛擬機器](backup-workloads-veeam.md)
* [將 CloudSimple 私用雲端設定為內部部署 VMware 工作負載的損毀修復網站](disaster-recovery-zerto.md)
