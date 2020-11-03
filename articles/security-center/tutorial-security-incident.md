---
title: 警示回應教學課程 - Azure 資訊安全中心
description: 在本教學課程中，您將了解如何將安全性警示分級，並且判斷警示的根本原因和範圍。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2020
ms.author: memildin
ms.openlocfilehash: b9f0b3219a75900a44a73ca0fc3e453f023bddb8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92787154"
---
# <a name="tutorial-triage-investigate-and-respond-to-security-alerts"></a>教學課程：分級、調查及回應安全性警示
資訊安全中心會使用進階分析和威脅情報，持續分析混合式雲端工作負載，提醒您有關您的雲端資源中的潛在惡意活動。 您也可以將來自其他安全性產品和服務的警示整合到資訊安全中心。 一旦提出警示，您便需要迅速採取行動來調查和補救潛在的安全性問題。 

在本教學課程中，您將學會如何：

> [!div class="checklist"]
> * 將安全性警示分級
> * 調查安全性警示以判斷根本原因
> * 回應安全性警示並減輕根本原因

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites
若要逐步執行本教學課程中涵蓋的功能，您必須啟用 Azure Defender。 您可以免費試用 Azure Defender。 若要深入了解，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/security-center/)。 [開始使用資訊安全中心](security-center-get-started.md)的快速入門會逐步引導您了解如何升級。


## <a name="triage-security-alerts"></a>將安全性警示分級
資訊安全中心可讓您統一檢視所有安全性警示。 安全性警示會根據所偵測到活動的嚴重性進行排名。 

從 [安全性警示] 頁面，將您的警示分級：

:::image type="content" source="./media/tutorial-security-incident/alerts-list.png" alt-text="安全性警示清單頁面" lightbox="./media/tutorial-security-incident/alerts-list.png":::

使用此頁面來檢閱您環境中的作用中安全性警示，以決定要先調查哪一個警示。

將安全性警示分級時，會根據警示嚴重性來排列警示優先順序，先解決嚴重性較高的警示。 若要深入了解警示嚴重性，請參閱[如何分類警示？](security-center-alerts-overview.md#how-are-alerts-classified)。

> [!TIP]
> 您可以將 Azure 資訊安全中心連線到最熱門的 SIEM 解決方案 (包括 Azure Sentinel)，並從您選擇的工具取用警示。 請於[將警示串流至 SIEM、SOAR 或 IT 服務管理解決方案](export-to-siem.md)中深入了解。


## <a name="investigate-a-security-alert"></a>調查安全性警示

當您決定要先調查哪一個警示時：

1. 選取所需的警示。
1. 從警示概觀頁面中，選取要先調查的資源。
1. 從左窗格開始調查，其中會顯示有關安全性警示的高階資訊。

    :::image type="content" source="./media/tutorial-security-incident/alert-details-left-pane.png" alt-text="警示詳細資料頁面的左窗格，其中醒目提示高階資訊":::

    此窗格會顯示：
    - 警示嚴重性、狀態和活動時間
    - 說明所偵測到精確活動的描述
    - 受影響的資源
    - MITRE ATT&CK 矩陣上活動的終止鏈結意圖

1. 如需可協助您調查可疑活動的詳細資訊，請檢查 [警示詳細資料] 索引標籤。

1. 當您檢閱過此頁面上的資訊時，可能會有足夠的資訊可以繼續進行回應。 如果您需要進一步的詳細資料：

    - 請洽詢資源擁有者，以確認偵測到的活動是否為誤判。
    - 調查受攻擊資源產生的原始記錄

## <a name="respond-to-a-security-alert"></a>回應安全性警示
調查警示並了解其範圍之後，您可以從 Azure 資訊安全中心回應安全性警示：

1.  開啟 [採取動作] 索引標籤，以查看建議的回應。

    :::image type="content" source="./media/tutorial-security-incident/alert-details-take-action.png" alt-text="安全性警示採取動作索引標籤" lightbox="./media/tutorial-security-incident/alert-details-take-action.png":::

1.  請參閱 **降低威脅** 一節，以取得減輕問題所需的手動調查步驟。
1.  若要強化您的資源並防止未來遭受這種攻擊，請在 [防止未來的攻擊] 區段中補救安全性建議。
1.  若要使用自動化的回應步驟來觸發邏輯應用程式，請使用 [觸發程式自動化回應] 區段。
1.  如果偵測到的活動「不是」惡意的，您可以使用 [隱藏類似的警示] 區段，來隱藏此類型的未來警示。

1.  當您完成警示的調查並以適當方式回應時，請將狀態變更為 [已關閉]。

    :::image type="content" source="./media/tutorial-security-incident/set-status-dismissed.png" alt-text="設定警示的狀態":::

    這會從主要警示清單中移除警示。 您可以使用警示清單頁面中的篩選條件，檢視 [已關閉] 狀態的所有警示。

1.  我們鼓勵您將有關警示的意見反應提供給 Microsoft：
    1. 將警示標示為 [實用] 或 [不實用]。
    1. 選取原因並新增註解。

        :::image type="content" source="./media/tutorial-security-incident/alert-feedback.png" alt-text="針對警示的實用性提供意見反應給 Microsoft":::

    > [!TIP]
    > 我們會檢閱您的意見反應以改善我們的演算法，並提供更好的安全性警示。

## <a name="end-the-tutorial"></a>結束教學課程

此集合中的其他快速入門和教學課程會以本快速入門為基礎。 如果您打算繼續處理後續的快速入門和教學課程，請將自動佈建和 Azure Defender 維持為啟用狀態。 

如果您不打算繼續，或您想要停用下列其中一項功能：

1. 返回資訊安全中心主功能表，並選取 [定價和設定]。
1. 選取相關的訂用帳戶。
1. 若要降級，請選取 [Azure Defender 關閉]。
1. 若要停用自動佈建，請開啟 [資料收集] 頁面，並且將 [自動佈建] 設定為 [關閉]。
4. 選取 [儲存]。

>[!NOTE]
> 停用自動佈建不會從已具有代理程式的 Azure VM 移除 Log Analytics 代理程式。 停用自動佈建會限制對資源的安全性監視。
>

## <a name="next-steps"></a>後續步驟
在此教學課程中，您已了解在回應安全性警示時要使用的資訊安全中心功能。 如需相關內容，請參閱：

- [回應適用於 Key Vault 的 Azure Defender 警示](defender-for-key-vault-usage.md)
- [安全性警示 - 參考指南](alerts-reference.md)
- [Azure Defender 簡介](azure-defender.md)
