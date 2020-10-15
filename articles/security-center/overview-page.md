---
title: Azure 資訊安全中心的主要儀表板或 [概觀] 頁面
description: 了解資訊安全中心概觀頁面的功能
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 899fddf08a817126ce775733da38ffd10ab3ab23
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952109"
---
# <a name="azure-security-centers-overview-page"></a>Azure 資訊安全中心的概觀頁面

當您開啟 Azure 資訊安全中心時，第一個出現的頁面會是概觀頁面。 

:::image type="content" source="media/overview-page/overview.png" alt-text="資訊安全中心的概觀頁面":::

透過資訊安全中心的概觀頁面，探索及評估工作負載的安全性，以及識別和降低風險。

概觀可讓您統合檢視混合式雲端工作負載的安全性態勢。 此外也會顯示安全性警示、涵蓋範圍資訊等等。


## <a name="features-of-the-overview-page"></a>概觀頁面的功能

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="資訊安全中心的概觀頁面":::

**頂端功能表列**提供：
- **訂用帳戶** - 您可以選取此按鈕以檢視和篩選訂用帳戶清單。 資訊安全中心會調整顯示內容，以反映所選訂用帳戶的安全性狀態。
- **新功能** - 會開啟 [版本資訊](release-notes.md)，讓您能夠隨時掌握新功能、錯誤 (bug) 修正和已淘汰的功能。
- **連線雲端帳戶的高階數據**，顯示下列主要圖格中的資訊內容。 此外也顯示作用中建議和警示的數目。
    深入了解如何連接您的 [AWS 帳戶](quickstart-onboard-aws.md)和 [GCP 專案](quickstart-onboard-gcp.md)。


頁面中央有**四個中央圖格**，分別連結到專用儀表板中的詳細資料：
- **安全分數** - 資訊安全中心會持續評估資源、訂用帳戶、組織的安全性問題。 然後將所有的發現彙總成一個分數，讓您可以立即得知目前的安全性情況：分數越高，風險層級越低。 [深入了解](secure-score-security-controls.md)。
- **合規性** - 資訊安全中心會根據 Azure 環境的持續性評估，提供合規性狀況的深入解析。 資訊安全中心會根據安全性最佳做法，來分析混合式雲端環境中的風險因素。 這些評量會對應到一組所支援標準中的合規性控制項。 [深入了解](security-center-compliance-dashboard.md)。
- **Azure Defender** - 這是整合於資訊安全中心的雲端工作負載保護平台 (CWPP)，可為您的 Azure 和混合式工作負載提供進階的智慧型保護功能。 此圖格會顯示連線資源的涵蓋範圍 (針對目前選取的訂用帳戶)，以及最近的警示 (依嚴重性以不同色彩標示)。 [深入了解](azure-defender.md)。
- **清查** - 此圖格會顯示未受監視的 VM 數目，以及由 Security Center 監視之資源的簡單指標。 [深入了解](asset-inventory.md)。


[深入解析] 窗格會為您的環境提供自訂項目，包括：
- 您最常遭受攻擊的資源
- 最有可能提高安全分數的安全性控制項
- 最受影響資源的作用中建議
- Azure 資訊安全中心專家最近的部落格文章

## <a name="next-steps"></a>後續步驟

此頁面導入了資訊安全中心概觀頁面。 如需相關資訊，請參閱：

- [使用資產清查和管理工具探索及管理資源](asset-inventory.md)
- [Azure 資訊安全中心的安全分數](secure-score-security-controls.md)