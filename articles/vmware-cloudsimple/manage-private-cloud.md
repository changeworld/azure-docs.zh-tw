---
title: 透過 CloudSimple 私用雲端管理 Azure VMware 解決方案
description: 描述可用來管理 CloudSimple 私用雲端資源和活動的功能
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 13496a18f4c99b69a5b8095caf5b74a04d1bba88
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869290"
---
# <a name="manage-private-cloud-resources-and-activity"></a>管理私人雲端資源和活動

私人雲端是從 CloudSimple 入口網站進行管理。  檢查 [狀態]、[可用的資源]、私人雲端上的活動，以及 [CloudSimple] 入口網站中的其他設定。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="access-the-cloudsimple-portal"></a>存取 CloudSimple 入口網站

存取[CloudSimple 入口網站](access-cloudsimple-portal.md)。

## <a name="view-the-list-of-private-clouds"></a>查看私人雲端清單

[**資源**] 頁面上的 [**私人**雲端] 索引標籤會列出您訂用帳戶中的所有私人雲端。 資訊包括 [名稱]、[vSphere 叢集數目]、[位置]、私人雲端的 [目前狀態] 和 [資源資訊]。

![[私人雲端] 頁面](media/manage-private-cloud.png)

選取私人雲端以取得其他資訊和動作。

## <a name="private-cloud-summary"></a>私人雲端摘要

查看所選私用雲端的完整摘要。  [摘要] 頁面包含部署在私人雲端上的 DNS 伺服器。  您可以設定從內部部署 DNS 伺服器到私人雲端 DNS 伺服器的 DNS 轉送。  如需 DNS 轉送的詳細資訊，請參閱針對[私人雲端 vCenter 從內部部署設定 DNS 以進行名稱解析](https://docs.microsoft.com/azure/vmware-cloudsimple/on-premises-dns-setup/)。

![私人雲端摘要](media/private-cloud-summary.png)

### <a name="available-actions"></a>可用動作

* [啟動 vSphere 用戶端](https://docs.microsoft.com/azure/vmware-cloudsimple/vcenter-access)。 存取此私人雲端的 vCenter。
* [購買節點](create-nodes.md)。 將節點新增至此私人雲端。
* [展開](expand-private-cloud.md)。 將節點新增至此私人雲端。
* 重新**整理。** 更新此頁面上的資訊。
* **刪除**。 您可以隨時刪除私人雲端。 **在刪除之前，請確定您已備份所有系統和資料。** 刪除私人雲端會刪除所有的 Vm、vCenter 設定和資料。 在所選私用雲端的 [摘要] 區段中，按一下 [**刪除**]。 刪除之後，所有私用雲端資料都會以安全且高度相容的抹除程式來清除。
* [變更 vSphere 許可權](escalate-private-cloud-privileges.md)。  在此私人雲端上提升您的許可權。

## <a name="private-cloud-vlanssubnets"></a>私人雲端 VLAN/子網

針對選取的私人雲端，查看已定義的 Vlan/子網清單。  此清單包含建立私人雲端時所建立的管理 Vlan/子網。

![私用雲端-Vlan/子網](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>可用動作

* [新增 vlan/子網](https://docs.microsoft.com/azure/vmware-cloudsimple/create-vlan-subnet/)。 將 VLAN/子集新增至此私人雲端。

選取 VLAN/子網以執行下列動作
* [附加防火牆資料表](https://docs.microsoft.com/azure/vmware-cloudsimple/firewall/)。 將防火牆資料表附加到此私人雲端。
* **編輯**
* **刪除**（僅限使用者定義的 Vlan/子網）

## <a name="private-cloud-activity"></a>私用雲端活動

查看所選取私人雲端的下列資訊。  活動資訊是所選私人雲端之所有活動的篩選清單。  此頁面會顯示最多25個最近的活動。

* 最近的警示
* 最近的事件
* 最近的工作
* 最近的審核

![私用雲端-活動](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>雲端機架

雲端機架是私用雲端的基礎構件。 每個機架都會提供一個單位的容量。 CloudSimple 會在您建立或擴充私人雲端時，根據您的選擇自動設定雲端機架。  查看雲端機架的完整清單，包括每個已指派給的私人雲端。

![私用雲端-雲端機架](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere 管理網路

目前在私人雲端上設定的 VMware 管理資源和虛擬機器清單。 資訊包括軟體版本、完整功能變數名稱（FQDN），以及資源的 IP 位址。

![私用雲端-vSphere 管理網路](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>後續步驟

* [在 Azure 上取用 VMware VM](quickstart-create-vmware-virtual-machine.md)
* 深入瞭解[私人](cloudsimple-private-cloud.md)雲端