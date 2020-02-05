---
title: 擴充 Azure VMware 解決方案（AVS）私人雲端
description: 說明如何擴充現有的 AVS 私人雲端，以在現有或新的叢集中新增容量
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3286b7537056a6c2f282533aa629ebbe47612690
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025294"
---
# <a name="expand-an-avs-private-cloud"></a>擴充 AVS 私人雲端

AVS 提供動態擴充 AVS 私用雲端的彈性。 您可以從較小的設定開始，然後在您需要更高的容量時進行擴充。 或者，您可以根據目前的需求建立 AVS 私人雲端，然後在使用量增加時加以展開。

AVS 私用雲端是由一或多個 vSphere 叢集所組成。 每個叢集可以有3到16個節點。 擴充 AVS 私用雲端時，您可以將節點新增至現有的叢集，或建立新的叢集。 若要擴充現有的叢集，其他節點的類型（SKU）必須與現有的節點相同。 若要建立新的叢集，節點可以是不同的類型。 如需有關 AVS 私用雲端限制的詳細資訊，請參閱[avs 私用雲端](cloudsimple-private-cloud.md)的限制一文。

使用 vCenter 的預設**資料中心**建立一個 AVS 私用雲端。 每個資料中心都作為最上層管理實體。 針對新的叢集，AVS 可以選擇新增至現有的資料中心，或建立新的資料中心。

在新叢集設定的過程中，AVS 會設定 VMware 基礎結構。 這些設定包括 vSAN 磁片群組的儲存設定、VMware 高可用性，以及分散式資源排程器（DRS）。

AVS 私用雲端可以擴充多次。 只有當您停留在整體節點限制範圍內時，才可以進行擴充。 每次您擴充您新增至現有叢集的 AVS 私人雲端，或建立新的叢集。

## <a name="before-you-begin"></a>開始之前

您必須先布建節點，才能擴充您的 AVS 私用雲端。 如需布建節點的詳細資訊，請參閱透過[AVS 布建 VMware 解決方案的節點-Azure](create-nodes.md)文章。 若要建立新的叢集，您至少必須有三個相同 SKU 的可用節點。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="expand-an-avs-private-cloud"></a>擴充 AVS 私人雲端

1. [存取 AVS 入口網站](access-cloudsimple-portal.md)。

2. 開啟 [**資源**] 頁面，然後選取您要擴充的 AVS 私人雲端。

3. 在 [摘要] 區段中，按一下 [**展開**]。

    ![擴充 AVS 私人雲端](media/resources-expand-private-cloud.png)

4. 選擇要擴充現有的叢集，或建立新的 vSphere 叢集。 當您進行變更時，會更新頁面上的摘要資訊。

    * 若要展開現有的叢集，請按一下 [**展開現有**的叢集]。 選取您想要展開的叢集，並輸入要新增的節點數目。 每個叢集最多可以有16個節點。
    * 若要新增叢集，請按一下 [**建立新**的叢集]。 輸入叢集的名稱。 選取現有的資料中心，或輸入名稱以建立新的資料中心。 選擇 [節點類型]。 在建立新的 vSphere 叢集時，您可以選擇不同的節點類型，但在擴充現有的 vSphere 叢集時則不能。 選取節點的數目。 每個新叢集必須至少有三個節點。

    ![擴充 AVS 私人雲端-新增節點](media/resources-expand-private-cloud-add-nodes.png)

5. 按一下 [**提交**] 以展開 [AVS 私用雲端]。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 上的 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 深入瞭解[AVS 私人](cloudsimple-private-cloud.md)雲端