---
title: Azure VMware 解決方案（AVS）-建立 AVS 私人雲端
description: 描述如何建立 AVS 私用雲端，將 VMware 工作負載擴充到具有營運彈性和持續性的雲端
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 128a0a1eec03878d0deba93048c54324aab7d888
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024784"
---
# <a name="create-an-avs-private-cloud"></a>建立 AVS 私用雲端

AVS 私用雲端是一種隔離的 VMware 堆疊，可支援 ESXi 主機、vCenter、vSAN 和 NSX。 AVS 私人雲端是透過 AVS 入口網站來管理。 他們在自己的管理網域中有自己的 vCenter server。 堆疊會在專用節點和隔離的裸機硬體節點上執行。

建立 AVS 私人雲端可協助您解決網路基礎結構的各種常見需求：

* **成長**。 如果您已達到現有基礎結構的硬體重新整理點，AVS 私用雲端可讓您進行擴充，而不需要新的硬體投資。

* **快速擴充**。 如果發生任何暫時性或非計畫的容量需求，AVS 私用雲端可讓您建立額外的容量，而不會有延遲。

* **加強保護**。 有三個或更多節點的 AVS 私用雲端，您可以獲得自動的冗余和高可用性保護。

* **長期基礎結構需求**。 如果您的資料中心已達到產能，或您想要重新組織以降低成本，AVS 私用雲端可讓您淘汰資料中心並遷移至雲端式解決方案，同時與您的企業營運保持相容。

當您建立 AVS 私人雲端時，您會取得單一 vSphere 叢集，以及在該叢集中建立的所有管理 Vm。

## <a name="before-you-begin"></a>開始之前

您必須先布建節點，才能建立您的 AVS 私用雲端。 如需布建節點的詳細資訊，請參閱布建[Azure VMware 解決方案的節點（AVS）](create-nodes.md)。

為 AVS 私人雲端的 vSphere/vSAN 子網配置 CIDR 範圍。 AVS 私用雲端會建立為 vCenter server 所管理的隔離 VMware 堆疊環境（具有 ESXi 主機、vCenter、vSAN 和 NSX）。 管理元件會部署在為 vSphere/vSAN 子網 CIDR 選取的網路中。 在部署期間，網路 CIDR 範圍會分成不同的子網。 VSphere/vSAN 子網位址空間必須是唯一的。 它不得與任何與 AVS 環境通訊的網路重迭。 與 AVS 通訊的網路包含內部部署網路和 Azure 虛擬網路。 如需有關 vSphere/vSAN 子網的詳細資訊，請參閱 Vlan 和子網總覽。

* 最小 vSphere/vSAN 子網 CIDR 範圍前置詞：/24
* 最大 vSphere/vSAN 子網 CIDR 範圍前置詞：/21


## <a name="access-the-avs-portal"></a>存取 AVS 入口網站

存取[AVS 入口網站](access-cloudsimple-portal.md)。

## <a name="create-a-new-avs-private-cloud"></a>建立新的 AVS 私用雲端

1. 選取 [所有服務]。
2. 搜尋 [ **AVS 服務**]。
3. 選取您要在其上建立您的 AVS 私人雲端的 AVS 服務。
4. 在 **[總覽**] 中，按一下 [**建立 avs 私人雲端**] 以開啟 AVS 入口網站的新瀏覽器索引標籤。 若出現提示，請使用您的 Azure 登入認證登入。

    ![從 Azure 建立 AVS 私用雲端](media/create-private-cloud-from-azure.png)

5. 在 AVS 入口網站中，提供您的 AVS 私人雲端名稱。
6. 選取您的 AVS 私人雲端的 [**位置**]。
7. 選取 [**節點類型**]，與您在 Azure 上購買的內容一致。
8. 指定**節點計數**。 至少需要三個節點，才能建立 AVS 私人雲端。
5. 在 CloudSimple 入口網站中，提供私人雲端的 [名稱]。
6. 選取私人雲端的 [**位置**]。
7. 選取 [**節點類型**]，與您在 Azure 上布建的內容一致。
8. 指定**節點計數**。  至少需要三個節點，才能建立私人雲端。

    ![建立 AVS 私用雲端-基本資訊](media/create-private-cloud-basic-info.png)

9. 按 **[下一步： Advanced Options]** 。
10. 輸入 vSphere/vSAN 子網的 CIDR 範圍。 請確定 CIDR 範圍不會與您的任何內部部署或其他 Azure 子網（虛擬網路）或閘道子網重迭。

    **CIDR 範圍選項：** /24、/23、/22 或/21。 A/24 CIDR 範圍最多支援9個節點，/23 CIDR 範圍最多支援41個節點，而/22 和/21 CIDR 範圍最多支援64個節點（AVS 私人雲端中的節點數目上限）。

    > [!IMPORTANT]
    > VSphere/vSAN CIDR 範圍中的 IP 位址會保留供 AVS 私用雲端基礎結構使用。 請勿在任何虛擬機器上使用此範圍內的 IP 位址。

11. 按 **[下一步]： [審查並建立]** 。
12. 檢查設定。 如果您需要變更任何設定，請按一下 [**上一步**]。
13. 按一下頁面底部的 [新增]。

AVS 私用雲端布建程式開始。 最多可能需要兩個小時的時間，才能布建 AVS 私用雲端。

如需擴充現有的 AVS 私人雲端的指示，請參閱[擴充 Avs 私用雲端](expand-private-cloud.md)。
