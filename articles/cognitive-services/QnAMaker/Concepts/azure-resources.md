---
title: Azure 資源 - QnA 製造商
description: QnA Maker 使用多個 Azure 源，每個源具有不同的用途。 瞭解它們是如何單獨使用的，您可以規劃和選擇正確的定價層，或知道何時更改定價層。 瞭解它們的組合使用方式，可以在問題發生時查找和修復問題。
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 8a5cc0f4889e31470514015035a92d230c40ed43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284240"
---
# <a name="azure-resources-for-qna-maker"></a>用於 QnA 製造商的 Azure 資源

QnA Maker 使用多個 Azure 源，每個源具有不同的用途。 瞭解它們是如何單獨使用的，您可以規劃和選擇正確的定價層，或知道何時更改定價層。 瞭解它們_的組合使用方式_，可以在問題發生時查找和修復問題。

## <a name="resource-planning"></a>資源規劃

當您首次開發 QnA Maker 知識庫時，在原型階段，通常擁有用於測試和生產的單個 QnA Maker 資源。

進入專案的開發階段時，應考慮：

* 您的知識庫系統將持有多少種語言
* 您需要在/從中提供多少區域
* 系統將保存每個域中的文檔數

計畫讓單個 QnA Maker 資源保存具有相同語言、相同區域和相同主題域組合的所有知識庫。

## <a name="pricing-tier-considerations"></a>定價層注意事項

一般而言，您有三個參數必須考量：

* **您所需的服務輸送量**：
    * 根據您的需求為您的應用程式服務選取適當的[應用程式方案](https://azure.microsoft.com/pricing/details/app-service/plans/)。 您可以[相應增加](https://docs.microsoft.com/azure/app-service/manage-scale-up)或相應減少應用程式。
    * 這還應影響 Azure**認知搜索**SKU 選擇，[請參閱此處](https://docs.microsoft.com/azure/search/search-sku-tier)的更多詳細資訊。 此外，您可能需要調整具有副本的認知搜索[容量](../../../search/search-capacity-planning.md)。

* **知識庫的大小和數量**：針對您的案例選擇適當的 [Azure 搜尋服務 SKU](https://azure.microsoft.com/pricing/details/search/)。 通常，您可以根據不同主題域的數量決定所需的知識庫數量。 一旦主題域（對於單一語言）應位於一個知識庫中。

    您可以在特定層中發佈 N-1 個知識庫，其中，N 是該層中允許的索引數上限。 同時也請檢查每個層允許的大小上限和文件數目。

    比方說，如果您的層次有 15 個允許的索引，您可以發佈 14 個知識庫 (每個已發佈的知識庫 1 個索引)。 第十五個索引用於撰寫和測試所有知識庫。

* **作為來源的文件數目**：QnA Maker 管理服務的免費 SKU 會將您可以透過入口網站和 API 管理的文件數目限制為 3 (每份文件的大小為 1 MB)。 標準 SKU 則不會限制您可管理的文件數目。 如需詳細資訊，請參閱[這裡](https://aka.ms/qnamaker-pricing)。

下表為您提供某些高階指導方針。

|                        | QnA Maker 管理 | App Service 方案 | Azue 認知搜尋 | 限制                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| 測試        | 免費 SKU             | 免費層   | 免費層    | 最多發佈 2 個 KB，大小為 50 MB  |
| 開發/測試環境   | 標準 SKU         | 共用      | 基本        | 最多發佈 14 KB，大小為 2 GB    |
| 生產環境 | 標準 SKU         | 基本       | 標準     | 最多發佈 49 個 KB，大小為 25 GB |

## <a name="when-to-change-a-pricing-tier"></a>何時更改定價層

|升級|原因|
|--|--|
|[升級](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)QnA 製造商管理 SKU|您希望在知識庫中擁有更多 QnA 集或文檔源。|
|[升級](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service)應用服務 SKU 並檢查認知搜索層並[創建認知搜索副本](../../../search/search-capacity-planning.md)|您的知識庫需要從用戶端應用（如聊天機器人）提供更多請求。|
|[升級](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)Azure 認知搜索服務|你計畫擁有許多知識庫。|

通過在[Azure 門戶中更新應用服務來](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)獲取最新的運行時更新。

## <a name="resource-naming-considerations"></a>資源命名注意事項

QnA Maker 資源（如`qna-westus-f0-b`）的資源名稱也用於命名其他資源。

Azure 門戶創建視窗允許您創建 QnA Maker 資源，並為其他資源選擇定價層。

> [!div class="mx-imgBorder"]
> ![用於創建 QnA Maker 資源的 Azure 門戶螢幕截圖](../media/concept-azure-resource/create-blade.png)

創建資源後，它們具有相同的名稱，但可選的應用程式見解資源除外，這些資源將字元後發佈到名稱。

> [!div class="mx-imgBorder"]
> ![Azure 門戶資源清單的螢幕截圖](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> 創建新資源組時創建 QnA Maker 資源。 這允許您在按資源組搜索時查看與 QnA Maker 資源關聯的所有資源。

> [!TIP]
> 使用命名約定指示資源或資源組名稱中的定價層。 當您收到創建新知識庫或添加新文檔時的錯誤時，認知搜索定價層限制是一個常見問題。

## <a name="resource-purposes"></a>資源用途

使用 QnA Maker 創建的每個 Azure 資源都有特定用途：

* QnA Maker 資源
* 認知搜索資源
* App Service 方案
* 應用計畫服務
* 應用程式洞察服務


### <a name="cognitive-search-resource"></a>認知搜索資源

[認知搜索](../../../search/index.yml)資源用於：

* 存儲 QnA 集
* 在運行時提供 QnA 集的初始排名（排名#1）

#### <a name="index-usage"></a>索引使用方式

資源保留一個索引作為測試索引，其餘索引與每個已發佈的知識庫相關聯。

一個資源定價，以容納15個索引，將持有14個已發佈的知識庫，一個索引用於測試所有的知識庫。 此測試索引按知識庫進行分區，以便使用互動式測試窗格的查詢將使用測試索引，但僅返回與特定知識庫關聯的特定分區的結果。

#### <a name="language-usage"></a>語言用法

在 QnA Maker 資源中創建的第一個知識庫用於確定認知搜索資源及其所有索引_的單一_語言集。 對於 QnA Maker 服務，您只能設置_一種語言_設置。

### <a name="qna-maker-resource"></a>QnA Maker 資源

QnA Maker 資源提供對創作和發佈 API 以及基於 QnA 集的自然語言處理 （NLP）（排名#2）的訪問。

第二個排名應用智慧篩選器，其中可以包含中繼資料和後續提示。

#### <a name="qna-maker-resource-configuration-settings"></a>QnA 製造商資源配置設置

在[QnA Maker 門戶](https://qnamaker.ai)中創建新的知識庫時，**語言**設置是在資源級別應用的唯一設置。 在為資源創建第一個知識庫時，請選擇該語言。

### <a name="app-service-and-app-service-plan"></a>應用服務和應用服務方案

用戶端應用程式使用[應用服務](../../../app-service/index.yml)通過運行時終結點訪問已發佈的知識庫。

要查詢已發佈的知識庫，所有已發佈的知識庫都使用相同的 URL 終結點，但在路由中指定**知識庫 ID。**

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[應用程式見解](../../../azure-monitor/app/app-insights-overview.md)用於收集聊天日誌和遙測資料。 查看常見的[Kusto 查詢](../how-to/get-analytics-knowledge-base.md)，瞭解有關服務的資訊。

## <a name="share-services-with-qna-maker"></a>與 QnA 製造商共用服務

QnA Maker 創建多個 Azure 資源。 要減少管理並從成本分攤中獲益，請使用下表瞭解您可以共用和不能共用的內容：

|服務|共用|原因|
|--|--|--|
|認知服務|X|設計上不可能|
|App Service 方案|✔|為應用服務方案分配的固定磁碟空間。 如果共用同一應用服務方案的其他應用使用大量磁碟空間，則 QnAMaker 應用服務實例將遇到問題。|
|App Service 方案|X|設計上不可能|
|Application Insights|✔|可以共用|
|搜尋服務|✔|1.`testkb`是 QnAMaker 服務的保留名稱;不能被別人使用。<br>2. 名稱`synonym-map`的 Synonym 地圖保留為 QnAMaker 服務。<br>3. 已發佈的知識庫的數量受搜索服務層的限制。 如果有可用的免費索引，其他服務可以使用它們。|

### <a name="using-a-single-cognitive-search-service"></a>使用單個認知搜索服務

如果通過門戶創建 QnA 服務及其依賴項（如搜索），則為您創建搜索服務並連結到 QnA Maker 服務。 創建這些資源後，可以更新應用服務設置以使用以前存在的搜索服務，並刪除剛剛創建的搜索服務。

[瞭解如何配置](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource)QnA Maker 以使用不同于作為 QnA Maker 資源創建過程一部分創建的認知服務資源。

## <a name="management-service-region"></a>管理服務區域

QnA Maker 的管理服務僅用於 QnA 製造商門戶和初始資料處理。 此服務僅在**美國西部**區域提供。 此美國西部服務中不存儲任何客戶資料。

## <a name="keys-in-qna-maker"></a>QnA 製造商中的金鑰

QnA Maker 服務處理兩種類型的金鑰：創作與 App 服務中託管的運行時一起使用的**金鑰**和**查詢終結點金鑰**。

如果您正在尋找**訂閱金鑰**[，術語已更改](#subscription-keys)。

通過 API 向服務發出請求時使用這些金鑰。

![金鑰管理](../media/qnamaker-how-to-key-management/key-management.png)

|名稱|Location|目的|
|--|--|--|
|撰寫金鑰|[Azure 門戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|這些金鑰可用來存取 [QnA Maker 管理服務 API](https://go.microsoft.com/fwlink/?linkid=2092179)。 這些 API 允許您編輯知識庫中的問題和答案，併發布您的知識庫。 當您創建新的 QnA Maker 服務時，將創建這些金鑰。<br><br>在 **"金鑰**"頁上的 **"認知服務**"資源上找到這些鍵。|
|查詢終結點鍵|[QnA Maker 入口網站](https://www.qnamaker.ai)|這些鍵用於查詢已發佈的知識庫終結點，以獲得使用者問題的回應。 您通常在聊天機器人或連接到 QnA Maker 服務的用戶端應用程式代碼中使用此查詢終結點。 這些金鑰是在發佈 QnA Maker 知識庫時創建的。<br><br>在 **"服務設置"** 頁中找到這些鍵。 從下拉式功能表右上角的使用者功能表中查找此頁面。|

### <a name="subscription-keys"></a>訂用帳戶金鑰

創作和查詢終結點鍵的術語是更正術語。 上一個術語是**訂閱金鑰**。 如果看到引用訂閱金鑰的其他文檔，這些文檔等效于創作和查詢終結點金鑰（在運行時使用）。

您必須知道金鑰是訪問、知識庫管理或知識庫查詢，才能知道需要查找哪個金鑰。

## <a name="next-steps"></a>後續步驟

* 瞭解 QnA 製造商[知識庫](knowledge-base.md)
* 瞭解[知識庫生命週期](development-lifecycle-knowledge-base.md)
* 審查服務和知識庫[限制](../limits.md)

