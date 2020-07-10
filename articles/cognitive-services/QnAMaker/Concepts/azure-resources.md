---
title: Azure 資源-QnA Maker
description: QnA Maker 使用數個 Azure 來源，各有不同的用途。 瞭解個別使用它們的方式，可讓您規劃並選取正確的定價層，或知道何時要變更定價層。 瞭解它們的組合使用方式，可讓您在發生問題時找出並加以修正。
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 7d6598510ae75a76e0a4fcaff59297b4c724ba78
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171069"
---
# <a name="azure-resources-for-qna-maker"></a>適用于 QnA Maker 的 Azure 資源

QnA Maker 使用數個 Azure 來源，各有不同的用途。 瞭解個別使用它們的方式，可讓您規劃並選取正確的定價層，或知道何時要變更定價層。 瞭解它們的_組合_使用方式，可讓您在發生問題時找出並加以修正。

## <a name="resource-planning"></a>資源規劃

當您第一次在原型階段開發 QnA Maker 知識庫時，測試和生產環境通常會有單一 QnA Maker 資源。

當您移至專案的開發階段時，您應該考慮：

* 您的知識庫系統將保留多少語言
* 您需要的知識庫有多少區域可供使用/從
* 您的系統會在每個網域中保存多少份檔

打算讓單一 QnA Maker 資源保存所有具有相同語言、相同區域和相同主體網域組合的知識庫。

## <a name="pricing-tier-considerations"></a>定價層考慮

一般而言，您有三個參數必須考量：

* **您所需的服務輸送量**：
    * 根據您的需求為您的應用程式服務選取適當的[應用程式方案](https://azure.microsoft.com/pricing/details/app-service/plans/)。 您可以[相應增加](https://docs.microsoft.com/azure/app-service/manage-scale-up)或相應減少應用程式。
    * 這應該也會影響您的 Azure**認知搜尋**SKU 選擇，請參閱[這裡](https://docs.microsoft.com/azure/search/search-sku-tier)的詳細資料。 此外，您可能需要使用複本來調整認知搜尋[容量](../../../search/search-capacity-planning.md)。

* **知識庫的大小和數量**：針對您的案例選擇適當的 [Azure 搜尋服務 SKU](https://azure.microsoft.com/pricing/details/search/)。 一般來說，您會根據不同的主旨網域數目來決定所需的知識庫數目。 一旦將主體網域 (用於單一語言) 就應該在一個知識庫中。

    您可以在特定層中發佈 N-1 個知識庫，其中，N 是該層中允許的索引數上限。 同時也請檢查每個層允許的大小上限和文件數目。

    比方說，如果您的層次有 15 個允許的索引，您可以發佈 14 個知識庫 (每個已發佈的知識庫 1 個索引)。 第十五個索引用於撰寫和測試所有知識庫。

* **作為來源的文件數目**：QnA Maker 管理服務的免費 SKU 會將您可以透過入口網站和 API 管理的文件數目限制為 3 (每份文件的大小為 1 MB)。 標準 SKU 則不會限制您可管理的文件數目。 如需詳細資訊，請參閱[這裡](https://aka.ms/qnamaker-pricing)。

下表為您提供某些高階指導方針。

|                            | QnA Maker 管理 | App Service 方案 | Azure 認知搜尋 | 限制                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **測試**        | 免費 SKU             | 免費層   | 免費層    | 最多發佈 2 個 KB，大小為 50 MB  |
| **開發/測試環境**   | 標準 SKU         | 共用      | 基本        | 最多發佈 14 KB，大小為 2 GB    |
| **生產環境** | 標準 SKU         | 基本       | 標準     | 最多發佈 49 個 KB，大小為 25 GB |

## <a name="recommended-settings"></a>建議的設定

|目標 QPS | App Service 方案 | Azure 認知搜尋 |
| -------------------- | ----------- | ------------ |
| 3             | S1，1個實例   | S1，1個實例    |
| 50         | S3，10個實例       | S1、12個實例         |
| 80         | S3，10個實例      |  S3、12個實例  |
| 100         | P3V2，10個實例  | S3、12個實例、3個磁碟分割   |
| 200到250         | P3V2，20個實例 | S3、12個實例、3個磁碟分割    |

## <a name="when-to-change-a-pricing-tier"></a>變更定價層的時機

|升級|原因|
|--|--|
|[升級](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)QnA Maker 管理 SKU|您想要在知識庫中擁有更多 QnA 組或檔來源。|
|[升級](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service)App Service SKU 及檢查認知搜尋層，並[建立認知搜尋複本](../../../search/search-capacity-planning.md)|您的知識庫需要為來自用戶端應用程式的更多要求提供服務，例如聊天機器人。|
|[升級](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)Azure 認知搜尋服務|您打算有許多知識庫。|

[更新 Azure 入口網站中的 App Service，以](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)取得最新的執行時間更新。

## <a name="resource-naming-considerations"></a>資源命名考慮

QnA Maker 資源的資源名稱（例如 `qna-westus-f0-b` ）也會用來命名其他資源。

[Azure 入口網站建立] 視窗可讓您建立 QnA Maker 資源，並選取其他資源的定價層。

> [!div class="mx-imgBorder"]
> ![QnA Maker 資源建立 Azure 入口網站的螢幕擷取畫面](../media/concept-azure-resource/create-blade.png)

建立資源之後，它們會具有相同的名稱，但選擇性的 Application Insights 資源除外，其 postpends 的字元名稱。

> [!div class="mx-imgBorder"]
> ![Azure 入口網站資源清單的螢幕擷取畫面](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> 當您建立 QnA Maker 資源時，請建立新的資源群組。 這可讓您在依資源群組搜尋時，查看與 QnA Maker 資源相關聯的所有資源。

> [!TIP]
> 使用命名慣例來表示資源或資源群組名稱內的定價層。 當您從建立新的知識庫或加入新的檔收到錯誤時，認知搜尋定價層限制是常見的問題。

## <a name="resource-purposes"></a>資源用途

使用 QnA Maker 建立的每個 Azure 資源都有特定的用途：

* QnA Maker 資源
* 認知搜尋資源
* App Service 方案
* 應用程式方案服務
* Application Insights 服務


### <a name="cognitive-search-resource"></a>認知搜尋資源

[認知搜尋](../../../search/index.yml)資源是用來：

* 儲存 QnA 配對
* 在執行時間提供 QnA 配對的初始排名 (ranker #1) 

#### <a name="index-usage"></a>索引使用方式

資源會保留一個索引作為測試索引，而其餘的索引會與每個已發佈的知識庫相互關聯。

定價為保留15個索引的資源會保留14個已發佈的知識庫，並使用一個索引來測試所有知識庫。 此測試索引是由知識庫進行分割，因此使用 [互動式測試] 窗格的查詢將會使用測試索引，但只會傳回與特定知識庫相關聯之特定資料分割的結果。

#### <a name="language-usage"></a>語言使用方式

在 QnA Maker 資源中建立的第一個知識庫，是用來判斷針對認知搜尋資源及其所有索引所設定的_單一_語言。 QnA Maker 服務只能_設定一種語言_。

### <a name="qna-maker-resource"></a>QnA Maker 資源

QnA Maker 資源可讓您存取撰寫和發佈 Api，以及自然語言處理 (NLP) 為基礎的第二個排名層 (ranker #2) 在執行時間的 QnA 配對。

第二個排名會套用可包含中繼資料和後續提示的智慧型篩選準則。

#### <a name="qna-maker-resource-configuration-settings"></a>QnA Maker 資源設定

當您在[QnA Maker 入口網站](https://qnamaker.ai)中建立新的知識庫時，**語言**設定是在資源層級套用的唯一設定。 當您建立資源的第一個知識庫時，請選取語言。

### <a name="app-service-and-app-service-plan"></a>App service 和 App service 方案

您的用戶端應用程式會使用[App service](../../../app-service/index.yml) ，透過執行時間端點存取已發佈的知識庫。

為了查詢已發佈的知識庫，所有已發佈的知識庫都會使用相同的 URL 端點，但會指定路由內的**知識庫識別碼**。

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md)可用來收集聊天記錄和遙測。 如需服務的相關資訊，請參閱常見的[Kusto 查詢](../how-to/get-analytics-knowledge-base.md)。

## <a name="share-services-with-qna-maker"></a>與 QnA Maker 共用服務

QnA Maker 會建立數個 Azure 資源。 若要減少管理和受益于成本共用，請使用下表來瞭解您可以和無法共用的內容：

|服務|共用|原因|
|--|--|--|
|認知服務|X|設計不可行|
|App Service 方案|✔|已修正配置給 App Service 方案的磁碟空間。 如果其他共用相同 App Service 方案的應用程式使用大量磁碟空間，QnAMaker App Service 實例就會發生問題。|
|App Service|X|設計不可行|
|Application Insights|✔|可以共用|
|搜尋服務|✔|1. `testkb` 是 QnAMaker 服務的保留名稱，不能供其他人使用。<br>2. 為 QnAMaker 服務保留名稱為的同義字對應 `synonym-map` 。<br>3. 已發佈的知識庫數目會受到搜尋服務層級的限制。 如果有可用的索引，其他服務就可以使用它們。|

### <a name="using-a-single-cognitive-search-service"></a>使用單一認知搜尋服務

如果您透過入口網站建立 QnA 服務及其相依性 (例如 [搜尋) ]，系統就會為您建立搜尋服務，並連結至 QnA Maker 服務。 建立這些資源之後，您可以更新 App Service 設定，以使用先前現有的搜尋服務，並移除您剛建立的搜尋服務。

瞭解[如何設定](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource)QnA Maker 使用與在 QnA Maker 資源建立程式中建立的不同認知服務資源。

## <a name="management-service-region"></a>管理服務區域

QnA Maker 的管理服務僅適用于 QnA Maker 入口網站，以及用於初始資料處理。 此服務僅適用于**美國西部**區域。 此美國西部服務中不會儲存任何客戶資料。

## <a name="keys-in-qna-maker"></a>QnA Maker 中的機碼

您的 QnA Maker 服務會處理兩種金鑰：**撰寫金鑰**和**查詢端點金鑰**，搭配裝載在 App service 中的執行時間使用。

如果您要尋找訂用帳戶**金鑰**，[術語已改變](#subscription-keys)。

透過 Api 對服務提出要求時，請使用這些金鑰。

![金鑰管理](../media/qnamaker-how-to-key-management/key-management.png)

|名稱|位置|用途|
|--|--|--|
|撰寫金鑰|[Azure 入口網站](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|這些金鑰可用來存取 [QnA Maker 管理服務 API](https://go.microsoft.com/fwlink/?linkid=2092179)。 這些 Api 可讓您編輯知識庫中的問題和答案，併發布您的知識庫。 當您建立新的 QnA Maker 服務時，會建立這些金鑰。<br><br>在 [**金鑰**] 頁面上的**認知服務**資源上尋找這些金鑰。|
|查詢端點金鑰|[QnA Maker 入口網站](https://www.qnamaker.ai)|這些金鑰可用來查詢已發佈的知識庫端點，以取得使用者問題的回應。 您通常會在聊天機器人或連接到 QnA Maker 服務的用戶端應用程式程式碼中使用此查詢端點。 當您發佈 QnA Maker 知識庫時，會建立這些金鑰。<br><br>在 [**服務設定**] 頁面中尋找這些金鑰。 從下拉式選單上頁面右上方的使用者功能表中，尋找此頁面。|

### <a name="subscription-keys"></a>訂用帳戶金鑰

撰寫和查詢端點金鑰這兩者都是更正詞彙。 先前的詞彙是**訂**用帳戶金鑰。 如果您看到參考訂用帳戶金鑰的其他檔，這些就相當於撰寫和查詢端點金鑰 (在執行時間) 中使用。

您必須知道金鑰的存取權、知識庫管理或知識庫查詢，以知道您需要尋找的索引鍵。

## <a name="recommended-settings-for-network-isolation"></a>網路隔離的建議設定

* 藉由設定[虛擬網路](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal)，保護認知服務資源免于公用存取。
* 保護 App Service (QnA 執行時間) 從公用存取：
    * 只允許來自認知服務 Ip 的流量。 這些已包含在服務標記 "CognitiveServicesManagement" 中。 撰寫 Api 時， (建立/更新 KB) 來叫用 app service 並據以更新 Azure 搜尋服務服務，這是必要的。
    * 請確定您也允許其他進入點，例如 Bot 服務、QnA Maker 入口網站 (可能是您的公司網路) 等等，以進行預測 "GenerateAnswer" API 存取。
    * 查看[服務標記的詳細資訊。](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

## <a name="next-steps"></a>後續步驟

* 瞭解 QnA Maker[知識庫](knowledge-base.md)
* 瞭解[知識庫生命週期](development-lifecycle-knowledge-base.md)
* 審查服務和知識庫[限制](../limits.md)

