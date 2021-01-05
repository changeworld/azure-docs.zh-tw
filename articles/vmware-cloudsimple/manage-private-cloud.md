---
title: 管理由 CloudSimple 私人雲端提供的 Azure VMware 解決方案
description: 描述可用來管理 CloudSimple 私人雲端資源和活動的功能
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f2f66c2e1e2e8aa596393d4c69a757138ab5a91
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895201"
---
# <a name="manage-private-cloud-resources-and-activity"></a>管理私人雲端資源和活動

私人雲端是從 CloudSimple 入口網站進行管理。  您可從 CloudSimple 入口網站檢查狀態、可用的資源、私人雲端上的活動及其他設定。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="access-the-cloudsimple-portal"></a>存取 CloudSimple 入口網站

存取 [CloudSimple 入口網站](access-cloudsimple-portal.md)。

## <a name="view-the-list-of-private-clouds"></a>檢視私人雲端清單

[資源] 頁面上的 [私人雲端] 索引標籤會列出訂用帳戶中的所有私人雲端。 資訊包括名稱、vSphere 叢集數目、位置、私人雲端的目前狀態和資源資訊。

![[私人雲端] 頁面](media/manage-private-cloud.png)

選取私人雲端以取得其他資訊和動作。

## <a name="private-cloud-summary"></a>私人雲端摘要

檢視所選私人雲端的完整摘要。  [摘要] 頁面包含部署在私人雲端上的 DNS 伺服器。  您可設定從內部部署 DNS 伺服器到私人雲端 DNS 伺服器的 DNS 轉送。  如需 DNS 轉送的詳細資訊，請參閱[設定 DNS 名稱解析以從內部部署存取私人雲端 vCenter](./on-premises-dns-setup.md) (機器翻譯)。

![私人雲端摘要](media/private-cloud-summary.png)

### <a name="available-actions"></a>可用動作

* [啟動 vSphere 用戶端](./vcenter-access.md)。 存取此私人雲端的 vCenter。
* [購買節點](create-nodes.md)。 將節點新增至此私人雲端。
* [擴充](expand-private-cloud.md)。 將節點新增至此私人雲端。
* **重新整理**。 更新此頁面上的資訊。
* **刪除**。 您可隨時刪除私人雲端。 **刪除之前，請確定已備份所有系統和資料。** 刪除私人雲端會刪除所有 VM、vCenter 設定和資料。 在所選私人雲端的 [摘要] 區段中，按一下 [刪除]。 刪除之後，會以安全且高度相容的清除程序來清除所有私人雲端資料。
* [變更 vSphere 權限](escalate-private-cloud-privileges.md)。  提升在此私人雲端上的權限。

## <a name="private-cloud-vlanssubnets"></a>私人雲端 VLAN/子網路

檢視為所選私人雲端定義的 VLAN/子網路清單。  此清單包含建立私人雲端時所建立的管理 VLAN/子網路。

![私人雲端 - VLAN/子網路](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>可用動作

* [新增 VLAN/子網路](./create-vlan-subnet.md)。 將 VLAN/子網路新增至此私人雲端。

選取 VLAN/子網路以執行下列動作
* [連結防火牆資料表](./firewall.md)。 將防火牆資料表連結至此私人雲端。
* **編輯**
* **刪除** (僅限使用者定義的 VLAN/子網路)

## <a name="private-cloud-activity"></a>私人雲端活動

檢視所選私人雲端的下列資訊。  活動資訊是所選私人雲端上所有活動的篩選清單。  此頁面最多會顯示 25 個最近的活動。

* 最近的警示
* 最近的事件
* 最近的工作
* 最近的稽核

![私人雲端 - 活動](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>雲端機架

雲端機架是私人雲端的建置組塊。 每個機架會提供一個容量單位。 CloudSimple 會在建立或擴充私人雲端時，根據選擇自動設定雲端機架。  檢視雲端機架的完整清單，包括指派每個機架的私人雲端。

![私人雲端 - 雲端機架](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere 管理網路

目前在私人雲端上設定的 VMware 管理資源和虛擬機器清單。 資訊包括軟體版本、完整網域名稱 (FQDN) 和資源的 IP 位址。

![私人雲端 - vSphere 管理網路](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>後續步驟

* [在 Azure 上取用 VMware VM](quickstart-create-vmware-virtual-machine.md)
* 深入了解[私人雲端](cloudsimple-private-cloud.md)
