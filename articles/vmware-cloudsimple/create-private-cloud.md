---
title: Azure VMware Solution by CloudSimple-建立 CloudSimple 私用雲端
description: 說明如何建立 CloudSimple 私用雲端，以利用操作彈性和持續性將 VMware 工作負載延伸至雲端
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2f4af4a36e719cbf15b3f0af77db81a32f2f2e42
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97896272"
---
# <a name="create-a-cloudsimple-private-cloud"></a>建立 CloudSimple 私人雲端

私用雲端是一種隔離的 VMware 堆疊，可支援 ESXi 主機、vCenter、vSAN 和 NSX。 私人雲端是透過 CloudSimple 入口網站進行管理。 他們在自己的管理網域中有自己的 vCenter server。 堆疊會在專用節點上執行，並隔離裸機硬體節點。

建立私用雲端可協助您解決各種常見的網路基礎結構需求：

* **成長**。 如果您已達到現有基礎結構的硬體更新點，私用雲端可讓您擴充，而不需要新的硬體投資。

* **快速擴充**。 如果發生任何暫時性或非計畫的容量需求，私用雲端可讓您建立額外的容量，而不會有延遲。

* **加強保護**。 有三個或多個節點的私用雲端，您可以取得自動冗余和高可用性保護。

* **長期基礎結構需求**。 如果您的資料中心容量，或您想要重建以降低成本，私用雲端可讓您淘汰資料中心並遷移至雲端式解決方案，同時與您的企業營運保持相容。

當您建立私人雲端時，您會取得單一 vSphere 叢集，以及在該叢集中建立的所有管理 Vm。

## <a name="before-you-begin"></a>開始之前

您必須先布建節點，才能建立私人雲端。 如需布建節點的詳細資訊，請參閱布建 [Azure VMware Solution By CloudSimple 的節點](create-nodes.md)。

配置私用雲端的 vSphere/vSAN 子網的 CIDR 範圍。 私人雲端會建立為隔離的 VMware 堆疊環境 (與 vCenter server 所管理的 ESXi 主機、vCenter、vSAN 和 NSX) 。 系統會將管理元件部署在針對 vSphere/vSAN 子網 CIDR 選取的網路中。 網路 CIDR 範圍會在部署期間分成不同的子網。 VSphere/vSAN 子網位址空間必須是唯一的。 它不能與任何與 CloudSimple 環境通訊的網路重迭。 與 CloudSimple 通訊的網路包括內部部署網路和 Azure 虛擬網路。 如需 vSphere/vSAN 子網的詳細資訊，請參閱 Vlan 和子網總覽。

* 最小 vSphere/vSAN 子網 CIDR 範圍前置詞：/24
* 最大 vSphere/vSAN 子網 CIDR 範圍前置詞：/21


## <a name="access-the-cloudsimple-portal"></a>存取 CloudSimple 入口網站

存取 [CloudSimple 入口網站](access-cloudsimple-portal.md)。

## <a name="create-a-new-private-cloud"></a>建立新的私人雲端

1. 選取 [所有服務]  。
2. 搜尋 **CloudSimple 服務**。
3. 選取您要在其上建立私人雲端的 CloudSimple 服務。
4. 從 **總覽** 中，按一下 [ **建立私人雲端** ] 以開啟 CloudSimple 入口網站的新瀏覽器索引標籤。 如果出現提示，請使用您的 Azure 登入認證登入。

    ![從 Azure 建立私人雲端](media/create-private-cloud-from-azure.png)

5. 在 CloudSimple 入口網站中，提供私人雲端的名稱。
6. 選取私人雲端的 **位置** 。
7. 選取與您在 Azure 上布建內容一致的 **節點類型**。
8. 指定 **節點計數**。  至少需要三個節點，才能建立私人雲端。

    ![建立私用雲端-基本資訊](media/create-private-cloud-basic-info.png)

9. 按一下 **[下一步： Advanced Options]**。
10. 輸入 vSphere/vSAN 子網的 CIDR 範圍。 請確定 CIDR 範圍未與任何內部部署或其他 Azure 子網重迭 (虛擬網路) 或使用閘道子網。

    **CIDR 範圍選項：** /24、/23、/22 或/21。 /24 CIDR 範圍最多可支援九個節點，/23 個 CIDR 範圍最多可支援41個節點，而 a/22 和/21 CIDR 範圍最多可支援64個節點， (私人雲端) 的節點數目上限。

    > [!IMPORTANT]
    > VSphere/vSAN CIDR 範圍中的 IP 位址會保留供私用雲端基礎結構使用。  請勿在任何虛擬機器上使用此範圍內的 IP 位址。

11. 按 **[下一步]：檢查並建立**。
12. 檢查設定。 如果您需要變更任何設定，請按一下 [ **上一步**]。
13. 按一下 [建立]。

私用雲端布建程式開始。 布建私人雲端最多可能需要兩個小時的時間。

如需擴充現有私人雲端的指示，請參閱 [擴充私人雲端](expand-private-cloud.md)。
