---
title: Azure 資源-QnA Maker
description: QnA Maker 會使用數個 Azure 來源，各有不同的用途。 瞭解如何個別使用它們可讓您規劃和選取正確的定價層，或知道何時變更您的定價層。 瞭解它們組合的使用方式，可讓您在發生問題時尋找並修正問題。
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 06731c97217e3b6a5c498743b83f1d16bab791eb
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548424"
---
# <a name="azure-resources-for-qna-maker"></a>適用于 QnA Maker 的 Azure 資源

QnA Maker 會使用數個 Azure 來源，各有不同的用途。 瞭解如何個別使用它們可讓您規劃和選取正確的定價層，或知道何時變更您的定價層。 瞭解它們 _組合_ 的使用方式，可讓您在發生問題時尋找並修正問題。

## <a name="resource-planning"></a>資源規劃

當您第一次開發 QnA Maker 知識庫時，在原型階段中，測試和生產環境中通常會有一個 QnA Maker 的資源。

當您移至專案的開發階段時，您應該考慮：

* 您的知識庫系統將保留的語言數量
* 您需要的知識庫有多少區域可供使用
* 您系統將保留的每個網域中有多少份檔

規劃讓單一 QnA Maker 資源保存所有具有相同語言、相同區域和相同主體網域組合的知識庫。

## <a name="pricing-tier-considerations"></a>定價層考慮

一般而言，您有三個參數必須考量：

* **您所需的服務輸送量**：
    * 根據您的需求為您的應用程式服務選取適當的[應用程式方案](https://azure.microsoft.com/pricing/details/app-service/plans/)。 您可以[相應增加](https://docs.microsoft.com/azure/app-service/manage-scale-up)或相應減少應用程式。
    * 這也會影響您的 Azure **認知搜尋** SKU 選取專案，請參閱 [這裡](https://docs.microsoft.com/azure/search/search-sku-tier)的詳細資料。 此外，您可能需要使用複本來調整認知搜尋的 [容量](../../../search/search-capacity-planning.md) 。

* **知識庫的大小和數量**：針對您的案例選擇適當的 [Azure 搜尋服務 SKU](https://azure.microsoft.com/pricing/details/search/)。 一般而言，您會根據不同的主旨網域數目，決定您需要的知識庫數目。 一旦適用于單一語言的主體網域 (，) 就應該在一個知識庫中。

    您可以在特定層中發佈 N-1 個知識庫，其中，N 是該層中允許的索引數上限。 同時也請檢查每個層允許的大小上限和文件數目。

    比方說，如果您的層次有 15 個允許的索引，您可以發佈 14 個知識庫 (每個已發佈的知識庫 1 個索引)。 第十五個索引用於撰寫和測試所有知識庫。

* **作為來源的文件數目**：QnA Maker 管理服務的免費 SKU 會將您可以透過入口網站和 API 管理的文件數目限制為 3 (每份文件的大小為 1 MB)。 標準 SKU 則不會限制您可管理的文件數目。 如需詳細資訊，請參閱[這裡](https://aka.ms/qnamaker-pricing)。

下表為您提供某些高階指導方針。

|                            | QnA Maker 管理 | App Service | Azue 認知搜尋 | 限制                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **測試**        | 免費 SKU             | 免費層   | 免費層    | 最多發佈 2 個 KB，大小為 50 MB  |
| **開發/測試環境**   | 標準 SKU         | 共用      | 基本        | 最多發佈 14 KB，大小為 2 GB    |
| **生產環境** | 標準 SKU         | 基本       | 標準     | 最多發佈 49 個 KB，大小為 25 GB |

## <a name="recommended-settings"></a>建議的設定

|目標 QPS | App Service | Azue 認知搜尋 |
| -------------------- | ----------- | ------------ |
| 3             | S1、1個實例   | S1、1個實例    |
| 50         | S3、10個實例       | S1、12個實例         |
| 80         | S3、10個實例      |  S3、12個實例  |
| 100         | P3V2，10個實例  | S3、12個實例、3個磁碟分割   |
| 200至250         | P3V2，20個實例 | S3、12個實例、3個磁碟分割    |

## <a name="when-to-change-a-pricing-tier"></a>變更定價層的時機

|升級|原因|
|--|--|
|[升級](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) QnA Maker 管理 SKU|您想要在知識庫中有更多 QnA 組或檔來源。|
|[升級](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) App Service SKU 並檢查認知搜尋層，並 [建立認知搜尋複本](../../../search/search-capacity-planning.md)|您的知識庫需要從用戶端應用程式（例如聊天機器人）提供更多要求。|
|[升級](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure 認知搜尋服務|您計畫有許多知識庫。|

[更新 Azure 入口網站中的 App Service，以](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)取得最新的執行時間更新。

## <a name="resource-naming-considerations"></a>資源命名考慮

QnA Maker 資源的資源名稱（例如 `qna-westus-f0-b` ）也會用來命名其他資源。

Azure 入口網站的 [建立] 視窗可讓您建立 QnA Maker 資源，然後選取其他資源的定價層。

> [!div class="mx-imgBorder"]
> ![QnA Maker 資源建立 Azure 入口網站的螢幕擷取畫面](../media/concept-azure-resource/create-blade.png)

資源建立後，會有相同的名稱，但選用的 Application Insights 資源除外，其會 postpends 名稱的字元。

> [!div class="mx-imgBorder"]
> ![Azure 入口網站資源清單的螢幕擷取畫面](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> 當您建立 QnA Maker 資源時，請建立新的資源群組。 這可讓您在依資源群組搜尋時，查看與 QnA Maker 資源相關聯的所有資源。

> [!TIP]
> 使用命名慣例來指出資源名稱或資源群組內的定價層。 當您從建立新的知識庫或新增檔時收到錯誤時，認知搜尋定價層限制是常見的問題。

## <a name="resource-purposes"></a>資源用途

使用 QnA Maker 建立的每個 Azure 資源都有特定用途：

* QnA Maker 資源
* 認知搜尋資源
* App Service
* App 方案服務
* Application Insights 服務


### <a name="cognitive-search-resource"></a>認知搜尋資源

[認知搜尋](../../../search/index.yml)資源可用來：

* 儲存 QnA 組
* 在執行時間提供 QnA 配對的初始排名 (ranker #1) 

#### <a name="index-usage"></a>索引使用方式

資源會保留一個索引作為測試索引，其餘的索引則會與每一個已發佈的知識庫相互關聯。

定價為保存15個索引的資源會保留14個已發佈的知識庫，而且會使用一個索引來測試所有知識庫。 此測試索引是依知識庫進行分割，因此使用 [互動式測試] 窗格的查詢將會使用測試索引，但只會從與特定知識庫相關聯的特定資料分割傳回結果。

#### <a name="language-usage"></a>語言使用方式

在 QnA Maker 資源中建立的第一個知識庫會用來判斷認知搜尋資源和其所有索引的 _單一_ 語言集。 QnA Maker 服務只能 _設定一個語言_ 。

### <a name="qna-maker-resource"></a>QnA Maker 資源

QnA Maker 資源可讓您存取撰寫和發佈 Api，以及自然語言處理 (NLP) 基礎的第二個排名層 (ranker #2 在執行時間的 QnA 組) 。

第二個排名套用可包含中繼資料和後續提示的智慧型篩選。

#### <a name="qna-maker-resource-configuration-settings"></a>QnA Maker 資源設定

當您在 [QnA Maker 入口網站](https://qnamaker.ai)中建立新的知識庫時， **語言** 設定是在資源層級套用的唯一設定。 當您建立資源的第一個知識庫時，請選取語言。

### <a name="app-service-and-app-service-plan"></a>App service 和 App service 方案

您的用戶端應用程式會使用 [App service](../../../app-service/index.yml) ，透過執行時間端點來存取已發佈的知識庫。

若要查詢已發佈的知識庫，所有已發佈的知識庫都會使用相同的 URL 端點，但是會在路由中指定 **知識庫識別碼** 。

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) 用來收集聊天記錄和遙測。 請參閱常見的 [Kusto 查詢](../how-to/get-analytics-knowledge-base.md) ，以取得服務的相關資訊。

## <a name="share-services-with-qna-maker"></a>使用 QnA Maker 共用服務

QnA Maker 會建立數個 Azure 資源。 若要降低管理成本並受益于成本共用，請使用下表來瞭解您可以和不能共用的內容：

|服務|共用|原因|
|--|--|--|
|認知服務|X|設計不可能|
|App Service 方案|✔|已修正配置給 App Service 方案的磁碟空間。 如果共用相同 App Service 方案的其他應用程式使用大量磁碟空間，QnAMaker App Service 實例將會發生問題。|
|App Service|X|設計不可能|
|Application Insights|✔|可以共用|
|搜尋服務|✔|1. `testkb` 是 QnAMaker 服務的保留名稱，不能由其他人使用。<br>2. 名稱的同義字對應 `synonym-map` 已保留給 QnAMaker 服務。<br>3. 已發佈的知識庫數目受限於搜尋服務層。 如果有可用的索引，則其他服務可以使用它們。|

### <a name="using-a-single-cognitive-search-service"></a>使用單一認知搜尋服務

如果您透過入口網站建立 QnA 服務及其相依性 (例如搜尋) ，系統就會為您建立一個搜尋服務，並連結到 QnA Maker 服務。 建立這些資源之後，您可以將 App Service 設定更新為使用先前現有的搜尋服務，並移除您剛才建立的。

瞭解 [如何設定](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) QnA Maker 使用不同的認知服務資源，而不是 QnA Maker 資源建立程式中所建立的資源。

## <a name="management-service-region"></a>管理服務區域

QnA Maker 的管理服務僅用於 QnA Maker 入口網站和初始資料處理。 這項服務僅適用于 **美國西部** 區域。 此美國西部服務中不會儲存任何客戶資料。

## <a name="keys-in-qna-maker"></a>QnA Maker 中的金鑰

您的 QnA Maker 服務會處理兩種類型的金鑰： **撰寫金鑰** 和 **查詢端點金鑰** 搭配 App service 中裝載的執行時間使用。

如果您要尋找訂用帳戶 **金鑰**， [術語已變更](#subscription-keys)。

透過 Api 對服務提出要求時，請使用這些金鑰。

![金鑰管理](../media/qnamaker-how-to-key-management/key-management.png)

|名稱|位置|目的|
|--|--|--|
|撰寫金鑰|[Azure 入口網站](https://azure.microsoft.com/free/cognitive-services/)|這些金鑰可用來存取 [QnA Maker 管理服務 API](https://go.microsoft.com/fwlink/?linkid=2092179)。 這些 Api 可讓您編輯知識庫中的問題和答案，併發布您的知識庫。 當您建立新的 QnA Maker 服務時，會建立這些金鑰。<br><br>在 [**金鑰**] 頁面上的**認知服務**資源上尋找這些金鑰。|
|查詢端點金鑰|[QnA Maker 入口網站](https://www.qnamaker.ai)|這些金鑰可用來查詢已發佈的知識庫端點，以取得使用者問題的回應。 您通常會在聊天機器人或連接到 QnA Maker 服務的用戶端應用程式程式碼中使用此查詢端點。 當您發佈 QnA Maker 知識庫時，會建立這些金鑰。<br><br>在 [ **服務設定** ] 頁面中找到這些金鑰。 從下拉式功能表右上方的使用者功能表中，尋找此頁面。|

### <a name="subscription-keys"></a>訂用帳戶金鑰

撰寫和查詢端點金鑰的條款是更正詞彙。 先前的詞彙是 **訂**用帳戶金鑰。 如果您看到參考訂用帳戶金鑰的其他檔，這些就相當於撰寫和查詢端點金鑰 (用於執行時間) 。

您必須知道金鑰的存取權、知識庫管理或知識庫查詢，以瞭解您需要尋找的金鑰。

## <a name="recommended-settings-for-network-isolation"></a>網路隔離的建議設定

* 藉由設定 [虛擬網路](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal)，保護認知服務資源免于公開存取。
* 保護 App Service (QnA 執行時間) 公用存取：
    * 只允許來自認知服務 Ip 的流量。 這些已包含在服務標記 "CognitiveServicesManagement" 中。 這是撰寫 Api 的必要項， (建立/更新 KB) 來叫用 app service，並據以更新 Azure 搜尋服務。
    * 確定您也允許其他進入點（例如 Bot 服務）、QnA Maker 入口網站 (可能是您的公司網路) 等，以便進行預測 "GenerateAnswer" API 存取。
    * 查看 [更多有關服務標記的資訊。](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

## <a name="next-steps"></a>接下來的步驟

* 瞭解 QnA Maker [知識庫](knowledge-base.md)
* 瞭解 [知識庫生命週期](development-lifecycle-knowledge-base.md)
* 審核服務和知識庫 [限制](../limits.md)

