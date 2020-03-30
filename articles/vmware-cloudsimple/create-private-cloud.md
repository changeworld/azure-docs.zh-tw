---
title: Azure VMware 解決方案（按雲簡單 - 創建雲簡單私有雲）
description: 描述如何創建雲簡單私有雲，以具有操作靈活性和連續性將 VMware 工作負載擴展到雲
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f700ac34b6c6e2a651366bee7dd1785c608064f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024784"
---
# <a name="create-a-cloudsimple-private-cloud"></a>創建雲簡單私有雲

私有雲是一個孤立的 VMware 堆疊，支援 ESXi 主機、vCenter、vSAN 和 NSX。 私有雲通過雲簡單門戶進行管理。 它們有自己的 vCenter 伺服器。 堆疊在專用節點和隔離裸機硬體節點上運行。

創建私有雲可説明您滿足網路基礎設施的各種常見需求：

* **增長**。 如果您已達到現有基礎架構的硬體刷新點，則私有雲允許您擴展，無需新的硬體投資。

* **快速擴展**。 如果出現任何臨時或計畫外容量需求，私有雲允許您立即創建額外的容量。

* **加強保護**。 使用三個或更多節點的私有雲，您可以獲得自動冗余和高可用性保護。

* **長期需要基礎設施**。 如果您的資料中心處於容量，或者您想要進行重組以降低成本，則私有雲允許您停用資料中心並遷移到基於雲的解決方案，同時保持與企業運營相容。

創建私有雲時，您將獲得單個 vSphere 群集以及該群集中創建的所有管理 VM。

## <a name="before-you-begin"></a>開始之前

必須先預配節點，然後才能創建私有雲。 有關預配節點的詳細資訊，請參閱[按雲簡單為 的 Azure VMware 解決方案預配節點](create-nodes.md)。

為私有雲為 vSphere/vSAN 子網分配 CIDR 範圍。 私有雲創建為獨立的 VMware 堆疊環境（具有 ESXi 主機、vCenter、vSAN 和 NSX），由 vCenter 伺服器管理。 管理元件部署在為 vSphere/vSAN 子網 CIDR 選擇的網路中。 網路 CIDR 範圍在部署期間分為不同的子網。 vSphere/vSAN 子網位址空間必須是唯一的。 它不得與任何與 CloudSimple 環境通信的網路重疊。 與 CloudSimple 通信的網路包括本地網路和 Azure 虛擬網路。 有關 vSphere/vSAN 子網的詳細資訊，請參閱 VLAN 和子網概述。

* 最小 vSphere/vSAN 子網 CIDR 範圍首碼： /24
* 最大 vSphere/vSAN 子網 CIDR 範圍首碼： /21


## <a name="access-the-cloudsimple-portal"></a>存取 CloudSimple 入口網站

訪問[雲簡單門戶](access-cloudsimple-portal.md)。

## <a name="create-a-new-private-cloud"></a>創建新私有雲

1. 選擇**所有服務**。
2. 搜索**雲簡單服務**。
3. 選擇要在其中創建私有雲的雲簡單服務。
4. 從**概述**中，按一下 **"創建私有雲**"可為雲簡單門戶打開新的瀏覽器選項卡。 如果出現提示，請使用 Azure 登錄憑據登錄。

    ![從 Azure 創建私有雲](media/create-private-cloud-from-azure.png)

5. 在雲簡單門戶中，為私有雲提供名稱。
6. 選擇私有雲**的位置**。
7. 選擇**節點類型**，與在 Azure 上預配的內容一致。
8. 指定**節點計數**。  創建私有雲至少需要三個節點。

    ![創建私有雲 - 基本資訊](media/create-private-cloud-basic-info.png)

9. 按一下 **"下一步：高級選項**"。
10. 輸入 vSphere/vSAN 子網的 CIDR 範圍。 確保 CIDR 範圍不會與任何本地或其他 Azure 子網（虛擬網路）或閘道子網重疊。

    **CIDR 範圍選項**：/24、/23、/22 或 /21。 /24 CIDR 範圍最多支援 9 個節點，/23 CIDR 範圍最多支援 41 個節點，/22 和 /21 CIDR 範圍支援多達 64 個節點（私有雲中最大節點數）。

    > [!IMPORTANT]
    > vSphere/vSAN CIDR 範圍內的 IP 位址保留供私有雲基礎設施使用。  不要在任何虛擬機器上使用此範圍內的 IP 位址。

11. 按一下 **"下一步："查看並創建**。
12. 查看設置。 如果需要更改任何設置，請按一下"**上一個**"。
13. 按一下 **[建立]**。

私有雲預配過程開始。 預配私有雲最多可能需要兩個小時。

有關擴展現有私有雲的說明，請參閱[擴展私有雲](expand-private-cloud.md)。
