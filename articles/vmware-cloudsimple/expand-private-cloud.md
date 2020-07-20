---
title: 通過雲簡單私有雲擴展 Azure VMware 解決方案
description: 描述如何擴展現有雲簡單私有雲以在現有或新群集中添加容量
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a82ba1b433e62ed1c4b72b8e942d4ade29f26c4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025294"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>擴展雲簡單私有雲

CloudSimple 提供了動態擴展私有雲的靈活性。 您可以從較小的配置開始，然後根據需要擴展容量。 或者，您可以根據當前需求創建私有雲，然後隨著消費的增長而擴展。

私有雲由一個或多個 vSphere 群集組成。 每個群集可以有 3 到 16 個節點。  擴展私有雲時，會向現有群集添加節點或創建新群集。 要擴展現有群集，其他節點必須與現有節點的類型 （SKU） 相同。 對於創建新群集，節點可以是不同類型的。 有關私有雲限制的詳細資訊，請參閱[雲簡單私有雲概述](cloudsimple-private-cloud.md)文章中的限制部分。

使用**vCenter**上的預設資料中心創建私有雲。  每個資料中心都用作頂級管理實體。  對於新群集，CloudSimple 提供了添加到現有資料中心或創建新資料中心的選擇。

作為新群集配置的一部分，CloudSimple 配置 VMware 基礎結構。  這些設置包括 vSAN 磁片組的存儲設置、VMware 高可用性和分散式資源調度程式 （DRS）。

私有雲可以多次擴展。 僅當保持在總體節點限制內時，才能進行擴展。 每次擴展私有雲時，您都會添加到現有群集或創建新群集。

## <a name="before-you-begin"></a>開始之前

必須先預配節點，然後才能擴展私有雲。  有關預配節點的詳細資訊，請參閱[按雲簡單預配 VMware 解決方案的預配節點 - Azure](create-nodes.md)一文。  要創建新群集，必須至少具有同一 SKU 的三個可用節點。

## <a name="sign-in-to-azure"></a>登入 Azure

登錄到 中的[https://portal.azure.com](https://portal.azure.com)Azure 門戶。

## <a name="expand-a-private-cloud"></a>擴充私人雲端

1. [訪問雲簡單門戶](access-cloudsimple-portal.md)。

2. 打開 **"資源"** 頁面並選擇要為其擴展的私有雲。

3. 在摘要部分中，按一下 **"展開**"。

    ![擴展私有雲](media/resources-expand-private-cloud.png)

4. 選擇是擴展現有群集還是創建新的 vSphere 群集。 進行更改時，頁面上的摘要資訊將更新。

    * 要擴展現有群集，請按一下"**展開現有群集**"。 選擇要展開的群集，然後輸入要添加的節點數。 每個群集最多可以有 16 個節點。
    * 要添加新群集，請按一下"**創建新群集**"。 輸入叢集的名稱。 選擇現有資料中心，或輸入名稱以創建新資料中心。 選擇節點類型。 創建新 vSphere 群集時可以選擇不同的節點類型，但不能在擴展現有 vSphere 群集時選擇。 選擇節點數。 每個新群集必須至少有三個節點。

    ![擴展私有雲 - 添加節點](media/resources-expand-private-cloud-add-nodes.png)

5. 按一下"**提交**"以擴展私有雲。

## <a name="next-steps"></a>後續步驟

* [在 Azure 上取用 VMware VM](quickstart-create-vmware-virtual-machine.md)
* 瞭解有關[私有雲](cloudsimple-private-cloud.md)的更多