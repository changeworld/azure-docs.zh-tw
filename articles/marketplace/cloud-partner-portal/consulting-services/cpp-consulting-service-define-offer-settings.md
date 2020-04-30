---
title: 定義諮詢服務供應專案的供應專案設定 |Azure Marketplace
description: 在 Azure Marketplace 的 Cloud Partner 入口網站中，定義 Azure 或 Dynamics 365 諮詢服務供應專案中的供應專案設定。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 406a37a1ef946b1c3ceb0d7b02ba318f423dcf53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146686"
---
# <a name="offer-settings-tab"></a>供應項目設定索引標籤

>[!Important]
>從2020年4月13日開始，我們會開始將您的諮詢服務供應專案的管理移至合作夥伴中心。 在遷移之後，您將在合作夥伴中心建立和管理您的供應專案。 遵循[諮詢服務建立總覽](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-consulting-service-offer)中的指示，管理您的已遷移供應專案。

在 [**新增供應**專案] 畫面上，第一個步驟是建立供應專案身分識別。 供應項目身分識別由三個部分組成：**供應項目識別碼**、**發行者識別碼**及**名稱**。 以下各節會分別說明上述各部分。

![建立新諮詢服務供應項目 - 供應項目設定索引標籤](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>供應專案識別碼 *

這個識別碼是您第一次提交供應項目時，所建立的唯一名稱。 它只能包含小寫英數字元、連字號或底線。 **供應項目識別碼**會顯示在 URL 中，並影響搜尋引擎結果。 例如，*yourcompanyname_exampleservice*。

如範例中所示，**供應項目識別碼**會附加至您的發行者識別碼，以建立唯一識別碼。 這組唯一識別碼會公開為永久連結，且可由搜尋引擎預定和編製索引。

>[!Note]
>供應項目上架之後，即無法更新其識別碼。


### <a name="publisher-id"></a>發行者識別碼 *

此識別碼與您的帳戶相關。 您登入您的組織帳戶後，您的**發行者識別碼**會顯示在下拉式功能表中。


### <a name="name"></a>檔案名

此字串將在 AppSource 上或在 Azure Marketplace 中顯示為供應項目名稱。 [名稱]**** 方塊上限為 50 個字元。 檢閱者可能需要編輯您的標題，才能將持續時間和供應項目類型附加到供應項目名稱。

下列範例顯示供應項目名稱的組合方式。 

![建立新的諮詢服務供應項目](media/cppsampleconsultingoffer.png)

供應項目名稱由四個部分組成：

-   **持續時間：** 在編輯器的 [**店面詳細資料**] 索引標籤上定義。 持續時間可以用小時、天或週來表示。
-   **服務類型：** 在編輯器的 [**店面詳細資料**] 索引標籤上定義。 服務類型為 `Assessment`、`Briefing`、`Implementation`、`Proof of concept` 及 `Workshop`。
-   **介係詞：** 由審查者插入。
-   **名稱：** 在 [**供應專案設定**] 頁面上定義。

>[!Note]
>[名稱]**** 方塊上限為 50 個字元。 檢閱者可能需要編輯您的標題，才能將持續時間和供應項目類型附加到供應項目名稱。

下列清單提供數個妥善命名的供應項目名稱：

-   專業服務的基本資訊：1 小時簡報
-   雲端移轉平台：1 小時簡報
-   PowerApps 和 Microsoft Flow：1 天工作坊
-   Azure Machine Learning： 3-Wk PoC
-   Brick and Click 零售解決方案：1 小時簡報
-   攜帶您自己的資料： 1-Wk 研討會
-   雲端分析：3 天工作坊
-   Power BI 訓練：3 天工作坊
-   銷售管理解決方案：1 週實作
-   CRM 快速入門：1天的研討會
-   Dynamics 365 for Sales：2 天評定

填妥 [供應項目設定]**** 索引標籤之後，儲存您提交的內容。 供應項目名稱現在會顯示在編輯器上方，您可以在 [所有供應項目]**** 中找到它。

## <a name="next-steps"></a>後續步驟

現在您可以輸入[電子店面詳細資料，並決定要在 Azure Marketplace 還是 AppSource](./cpp-consulting-service-storefront-details.md) 上發佈。
