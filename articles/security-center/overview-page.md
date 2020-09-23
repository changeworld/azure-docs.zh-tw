---
title: Azure 資訊安全中心的主要儀表板或 [總覽] 頁面
description: 深入瞭解資訊安全中心總覽頁面的功能
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 637f7dde63d562f71aa31561a960ed3502a80229
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933879"
---
# <a name="azure-security-centers-overview-page"></a>Azure 資訊安全中心的總覽頁面

當您開啟 Azure 資訊安全中心時，要顯示的第一個頁面是 [總覽] 頁面。 

:::image type="content" source="media/overview-page/overview.png" alt-text="資訊安全中心的概觀頁面":::

使用「安全性中心」總覽頁面探索和評估工作負載的安全性，並識別和降低風險。

此總覽針對混合式雲端工作負載的安全性狀態提供統一的觀點。 此外，它還會顯示安全性警示、涵蓋範圍資訊等。


## <a name="features-of-the-overview-page"></a>總覽頁面的功能

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="安全性中心的總覽頁面頂端列":::

**頂端功能表列**提供：
- 訂用帳戶-您可以藉由選取此按鈕來查看和**篩選訂閱清單**。 資訊安全中心會調整顯示內容，以反映所選訂用帳戶的安全性狀態。
- **新** 功能-開啟 [版本](release-notes.md) 資訊，讓您可以隨時掌握新功能、bug 修正和已淘汰的功能。
- 連接的雲端帳戶的**高階數位**，以顯示下列主要磚中資訊的內容。 以及有效的建議和警示數目。
    深入瞭解如何連接您的 [AWS 帳戶](quickstart-onboard-aws.md) 和 [GCP 專案](quickstart-onboard-gcp.md)。


頁面中央有 **四個中央磚**，每個都連結到專用儀表板，以取得更多詳細資料：
- **安全分數** -安全中心會持續評估您的資源、訂用帳戶和組織是否有安全性問題。 然後將所有的發現彙總成一個分數，讓您可以立即得知目前的安全性情況：分數越高，風險層級越低。 [深入了解](secure-score-security-controls.md)。
- **合規性** -資訊安全中心會根據您 Azure 環境的連續評量，提供您合規性狀態的見解。 資訊安全中心會根據安全性最佳做法，來分析混合式雲端環境中的風險因素。 這些評量會從一組支援的標準對應到合規性控制。[深入瞭解](security-center-compliance-dashboard.md)。
- **Azure Defender** -這是在安全中心內整合的雲端工作負載保護平臺 (CWPP) ，可針對您的 Azure 和混合式工作負載進行先進、智慧型的保護。 圖格會顯示目前所選訂用帳戶的已連線資源的涵蓋範圍 () 和最近的警示（以色彩標示的嚴重性）。 [深入了解](azure-defender.md)。
- **清查** ：磚會顯示未受監視的 vm 數目，以及由安全性 CenterBen 監視之資源的簡單 barometer。 [深入了解](asset-inventory.md)。


[ **見解** ] 窗格提供您環境的自訂專案，包括：
- 您最常遭受攻擊的資源
- 具有最高潛力的安全性控制項，可提高您的安全分數
- 影響最多資源的有效建議
- Azure 資訊安全中心專家的近期 blog 文章

## <a name="next-steps"></a>下一步

此頁面引進了「安全性中心」總覽頁面。 如需相關資訊，請參閱：

- [使用資產清查和管理工具探索及管理您的資源](asset-inventory.md)
- [Azure 資訊安全中心的安全分數](secure-score-security-controls.md)