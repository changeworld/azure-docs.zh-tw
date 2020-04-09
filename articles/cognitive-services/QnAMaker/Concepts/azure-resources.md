---
title: Azure 資源 - QnA 製造商
description: QnA Maker 使用多個 Azure 源,每個源具有不同的用途。 瞭解它們是如何單獨使用的,您可以規劃和選擇正確的定價層,或知道何時更改定價層。 瞭解它們的組合使用方式,可以在問題發生時查找和修復問題。
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 581029d2372f7a2ef704dcf02f266b66440aa246
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80873900"
---
# <a name="azure-resources-for-qna-maker"></a>QnA 製造商的 Azure 資源

QnA Maker 使用多個 Azure 源,每個源具有不同的用途。 瞭解它們是如何單獨使用的,您可以規劃和選擇正確的定價層,或知道何時更改定價層。 瞭解它們_的組合使用方式_,可以在問題發生時查找和修復問題。

## <a name="resource-planning"></a>資源規劃

當您首次開發QnAMaker知識庫時,在原型階段,通常擁有用於測試和生產的單個QnAMaker資源。

進入專案的開發階段時,應考慮:

* 您的知識庫系統將持有多少種語言
* 您需要在/從中提供多少區域
* 系統儲存每個網域的文件數

計劃讓單個 QnA Maker 資源保存具有相同語言、相同區域和相同主題域組合的所有知識庫。

## <a name="pricing-tier-considerations"></a>定價層注意事項

一般而言，您有三個參數必須考量：

* **您所需的服務輸送量**：
    * 根據您的需求為您的應用程式服務選取適當的[應用程式方案](https://azure.microsoft.com/pricing/details/app-service/plans/)。 您可以[相應增加](https://docs.microsoft.com/azure/app-service/manage-scale-up)或相應減少應用程式。
    * 這還應影響 Azure**認知搜尋**SKU 選擇,[請參閱此處](https://docs.microsoft.com/azure/search/search-sku-tier)的更多詳細資訊。 此外,您可能需要調整具有複本的認知搜尋[容量](../../../search/search-capacity-planning.md)。

* **知識庫的大小和數量**：針對您的案例選擇適當的 [Azure 搜尋服務 SKU](https://azure.microsoft.com/pricing/details/search/)。 通常,您可以根據不同主題域的數量決定所需的知識庫數量。 一旦主題域(對於單一語言)應位於一個知識庫中。

    您可以在特定層中發佈 N-1 個知識庫，其中，N 是該層中允許的索引數上限。 同時也請檢查每個層允許的大小上限和文件數目。

    比方說，如果您的層次有 15 個允許的索引，您可以發佈 14 個知識庫 (每個已發佈的知識庫 1 個索引)。 第十五個索引用於撰寫和測試所有知識庫。

* **作為來源的文件數目**：QnA Maker 管理服務的免費 SKU 會將您可以透過入口網站和 API 管理的文件數目限制為 3 (每份文件的大小為 1 MB)。 標準 SKU 則不會限制您可管理的文件數目。 如需詳細資訊，請參閱[這裡](https://aka.ms/qnamaker-pricing)。

下表為您提供某些高階指導方針。

|                        | QnA Maker 管理 | App Service 方案 | Azue 認知搜尋 | 限制                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| 測試        | 免費 SKU             | 免費層   | 免費層    | 最多發佈 2 個 KB，大小為 50 MB  |
| 開發/測試環境   | 標準 SKU         | 共用      | 基本        | 最多發佈 14 KB，大小為 2 GB    |
| 生產環境 | 標準 SKU         | 基本       | 標準     | 最多發佈 49 個 KB，大小為 25 GB |

## <a name="recommended-settings"></a>建議的設定

|目標 QPS | App Service 方案 | Azue 認知搜尋 |
| -------------------- | ----------- | ------------ |
| 3             | S1,1 個實體   | S1,1 個實體    |
| 50         | S3,10 個實體       | S1, 12 實體         |
| 80         | S3,10 個實體      |  S3, 12 實體  |
| 100         | P3V2,10 個實體  | S3,12 個實例,3 個分區   |
| 200 到 250         | P3V2,20 個實體 | S3,12 個實例,3 個分區    |

## <a name="when-to-change-a-pricing-tier"></a>何時變更定價層

|升級|原因|
|--|--|
|[升級](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)QnA 製造商管理 SKU|您希望在知識庫中有更多的 QnA 對或文檔源。|
|[升級](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service)套用服務 SKU 並檢查認知搜尋層並[建立認知搜尋副本](../../../search/search-capacity-planning.md)|您的知識庫需要從用戶端應用(如聊天機器人)提供更多請求。|
|[升級](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)Azure 認知搜尋服務|你計劃擁有許多知識庫。|

通過在[Azure 門戶中更新應用服務來](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)獲取最新的運行時更新。

## <a name="resource-naming-considerations"></a>資源命名注意事項

QnA Maker 資源`qna-westus-f0-b`(如 )的資源名稱也用於命名其他資源。

Azure 門戶建立視窗允許您創建 QnA Maker 資源,並為其他資源選擇定價層。

> [!div class="mx-imgBorder"]
> ![建立 QnA Maker 資源的 Azure 門戶螢幕截圖](../media/concept-azure-resource/create-blade.png)

創建資源後,它們具有相同的名稱,但可選的應用程式見解資源除外,這些資源將字元後發佈到名稱。

> [!div class="mx-imgBorder"]
> ![Azure 門戶資源清單的螢幕截圖](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> 創建新資源群組時創建 QnA Maker 資源。 這允許您在按資源組搜索時查看與 QnA Maker 資源關聯的所有資源。

> [!TIP]
> 使用命名約定指示資源或資源組名稱中的定價層。 當您收到創建新知識庫或添加新文檔時的錯誤時,認知搜索定價層限制是一個常見問題。

## <a name="resource-purposes"></a>資源用途

使用 QnA Maker 建立的每個 Azure 資源都有特定用途:

* QnA Maker 資源
* 認知搜尋資源
* App Service 方案
* 應用計劃服務
* 應用程式洞察服務


### <a name="cognitive-search-resource"></a>認知搜尋資源

[認知搜尋](../../../search/index.yml)資源用於:

* 儲存 QnA 對
* 在執行時提供 QnA 對的初始排名(排名#1)

#### <a name="index-usage"></a>索引使用方式

資源保留一個索引作為測試索引,其餘索引與每個已發佈的知識庫相關聯。

一個資源定價,以容納15個索引,將持有14個已發佈的知識庫,一個索引用於測試所有的知識庫。 此測試索引按知識庫進行分區,以便使用互動式測試窗格的查詢將使用測試索引,但僅返回與特定知識庫關聯的特定分區的結果。

#### <a name="language-usage"></a>語言用法

在 QnA Maker 資源中創建的第一個知識庫用於確定認知搜索資源及其所有索引_的單一_語言集。 對於 QnA Maker 服務,您只能設置_一種語言_設置。

### <a name="qna-maker-resource"></a>QnA Maker 資源

QnA Maker 資源提供對創作和發佈 API 以及運行時 QnA 對的第二排名層 (NLP)(排名#2)的訪問。

第二個排名應用智慧篩選器,其中可以包含元數據和後續提示。

#### <a name="qna-maker-resource-configuration-settings"></a>QnA 製造商資源設定設定

在[QnA Maker 門戶](https://qnamaker.ai)中建立新的知識庫時,**語言**設置是在資源級別應用的唯一設置。 在為資源創建第一個知識庫時,請選擇該語言。

### <a name="app-service-and-app-service-plan"></a>套用服務和應用程式服務計劃

用戶端應用程式使用[應用服務](../../../app-service/index.yml)通過運行時終結點訪問已發佈的知識庫。

要查詢已發佈的知識庫,所有已發佈的知識庫都使用相同的 URL 終結點,但在路由中指定**知識庫 ID。**

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[應用程式見解](../../../azure-monitor/app/app-insights-overview.md)用於收集聊天日誌和遙測數據。 查看常見的[Kusto 查詢](../how-to/get-analytics-knowledge-base.md),瞭解有關服務的資訊。

## <a name="share-services-with-qna-maker"></a>與 QnA 製造商分享服務

QnA Maker 創建多個 Azure 資源。 要減少管理並從成本分攤中獲益,請使用下表瞭解您可以共用和不能共用的內容:

|服務|共用|原因|
|--|--|--|
|認知服務|X|設計上不可能|
|App Service 方案|✔|為應用服務計劃分配的固定磁碟空間。 如果共用同一應用服務計劃的其他應用使用大量磁碟空間,則 QnAMaker 應用服務實例將遇到問題。|
|App Service 方案|X|設計上不可能|
|Application Insights|✔|可以共用|
|搜尋服務|✔|1.`testkb`是 QnAMaker 服務的保留名稱;不能被別人使用。<br>2.`synonym-map`名稱 的 Synonym 地圖保留為 QnAMaker 服務。<br>3. 已發佈的知識庫的數量受搜索服務層的限制。 如果有可用的免費索引,其他服務可以使用它們。|

### <a name="using-a-single-cognitive-search-service"></a>使用單一個認知搜尋服務

如果透過門戶建立 QnA 服務及其依賴項(如搜尋),則為您創建搜尋服務並連結到 QnA Maker 服務。 創建這些資源後,可以更新應用服務設置以使用以前存在的搜索服務,並刪除剛剛創建的搜索服務。

[瞭解如何設定](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource)QnA Maker 以使用不同於作為 QnA Maker 資源創建過程一部分創建的認知服務資源。

## <a name="management-service-region"></a>管理服務區域

QnA Maker 的管理服務僅用於 QnA 製造商門戶和初始數據處理。 此服務僅在**美國西部**區域提供。 此美國西部服務中不存儲任何客戶數據。

## <a name="keys-in-qna-maker"></a>QnA 製造商的金鑰

QnA Maker 服務處理兩種類型的金鑰:創作與 App 服務中託管的執行時一起使用的**金鑰**與**查詢終結點金鑰**。

如果您要尋找**訂閱金鑰**[,字語已變更](#subscription-keys)。

通過 API 向服務發出請求時使用這些密鑰。

![金鑰管理](../media/qnamaker-how-to-key-management/key-management.png)

|名稱|Location|目的|
|--|--|--|
|撰寫金鑰|[Azure 入口網站](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|這些金鑰可用來存取 [QnA Maker 管理服務 API](https://go.microsoft.com/fwlink/?linkid=2092179)。 這些 API 允許您編輯知識庫中的問題和答案,併發布您的知識庫。 當您創建新的 QnA Maker 服務時,將創建這些密鑰。<br><br>在 **「密鑰**」頁上的 **「認知服務**」資源上找到這些鍵。|
|查詢終結點鍵|[QnA Maker 入口網站](https://www.qnamaker.ai)|這些鍵用於查詢已發佈的知識庫終結點,以獲得用戶問題的回應。 您通常在聊天機器人或連接到 QnA Maker 服務的用戶端應用程式代碼中使用此查詢終結點。 這些密鑰是在發佈QnAMaker知識庫時創建的。<br><br>在 **「服務設置」** 頁中找到這些鍵。 從下拉菜單右上角的用戶功能表中尋找此頁面。|

### <a name="subscription-keys"></a>訂用帳戶金鑰

創作和查詢終結點鍵的術語是更正術語。 前一個字語是**訂閱金鑰**。 如果看到引用訂閱密鑰的其他文件,這些文檔等效於創作和查詢終結點密鑰(在運行時使用)。

您必須知道金鑰是訪問、知識庫管理或知識庫查詢,才能知道需要查找哪個密鑰。

## <a name="next-steps"></a>後續步驟

* 瞭解 QnA 製造商[知識庫](knowledge-base.md)
* 瞭解[知識庫生命週期](development-lifecycle-knowledge-base.md)
* 審查服務和知識庫[限制](../limits.md)

