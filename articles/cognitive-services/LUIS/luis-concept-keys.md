---
title: 如何使用 LUIS 的創作和運行時金鑰
titleSuffix: Azure Cognitive Services
description: LUIS 使用兩個鍵，即創作鍵來創建模型，使用運行時鍵來查詢具有使用者話語的預測終結點。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9d213c8fa03ad2ca5e5fd7e620e52aa502749be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221505"
---
# <a name="authoring-and-runtime-keys"></a>撰寫和執行階段金鑰

語言理解 （LUIS） 有兩個服務和 API 集： 

* 創作（以前稱為_程式設計_）
* 預測運行時

有幾個關鍵類型，具體取決於要使用的服務以及要使用的方式。

## <a name="non-azure-resources-for-luis"></a>LUIS 的非 Azure 資源

### <a name="starter-key"></a>啟動鍵

首次使用 LUIS 時，會為您創建**入門金鑰**。 此資源提供：

* 通過 LUIS 門戶或 API（包括 SDK）提供免費創作服務請求
* 每月通過瀏覽器、API 或 SDK 釋放 1，000 個預測終結點請求

## <a name="azure-resources-for-luis"></a>LUIS 的 Azure 資源

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

LUIS 允許三種類型的 Azure 資源： 
 
|Key|目的|認知服務`kind`|認知服務`type`|
|--|--|--|--|
|[授權金鑰](#programmatic-key)|通過創作、培訓、發佈和測試訪問和管理應用程式資料。 如果要以程式設計方式創作 LUIS 應用，請創建 LUIS 創作金鑰。<br><br>`LUIS.Authoring`金鑰的目的是允許您：<br>• 以程式設計方式管理語言理解應用和模型，包括培訓和發佈<br> • 通過將人員分配給[參與者角色](#contributions-from-other-authors)來控制創作資源的許可權。|`LUIS.Authoring`|`Cognitive Services`|
|[預測鍵](#prediction-endpoint-runtime-key)| 查詢預測終結點請求。 在用戶端應用請求超出啟動資源提供的 1，000 個請求的預測之前創建 LUIS 預測金鑰。 |`LUIS`|`Cognitive Services`|
|[認知服務多服務資源金鑰](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|與 LUIS 和其他受支援的認知服務共用的查詢預測終結點請求。|`CognitiveServices`|`Cognitive Services`|

資源創建過程完成後，[將金鑰分配給](luis-how-to-azure-subscription.md)LUIS 門戶中的應用。

在要發佈和查詢[的區域](luis-reference-regions.md#publishing-regions)創作 LUIS 應用非常重要。

> [!CAUTION]
> 為方便起見，許多示例使用 Starter[鍵](#starter-key)，因為它在其[配額](luis-boundaries.md#key-limits)中提供了一些免費的預測終結點調用。  


### <a name="query-prediction-resources"></a>查詢預測資源

* 運行時金鑰可用於所有 LUIS 應用或特定 LUIS 應用。 
* 請勿使用運行時金鑰創作 LUIS 應用。 

LUIS 運行時終結點接受兩種查詢樣式，兩者都使用預測終結點運行時金鑰，但在不同的位置。

用於訪問運行時的終結點使用資源區域唯一的子域，如下表`{region}`中表示。 

## <a name="assignment-of-the-key"></a>金鑰的分配

您可以在[LUIS 門戶](https://www.luis.ai)中或通過相應的 API[分配](luis-how-to-azure-subscription.md)運行時金鑰。 

## <a name="key-limits"></a>金鑰限制

每個訂閱最多可以創建 10 個創作金鑰。 

請參閱[關鍵限制](luis-boundaries.md#key-limits)和[Azure 區域](luis-reference-regions.md)。 

發佈區域與撰寫區域不同。 請確保在創作區域中創建與希望用戶端應用程式所在的發佈區域對應的應用。

## <a name="key-limit-errors"></a>金鑰限制錯誤
如果超過每秒事務 （TPS） 配額，您將收到 HTTP 429 錯誤。 如果超出每月交易 （TPS） 配額，您將收到 HTTP 403 錯誤。 

## <a name="contributions-from-other-authors"></a>其他作者的貢獻

**對於[創作資源遷移](luis-migration-authoring.md)的應用**：_參與者_在 Azure 門戶中管理創作資源，使用**Access 控制項 （IAM）** 頁。 瞭解如何使用協作者的電子郵件地址和_參與者_角色[添加使用者](luis-how-to-collaborate.md)。 

**對於尚未遷移的應用**：所有_協作者_都在 **"管理->協作者"** 頁面的 LUIS 門戶中進行管理。

## <a name="move-transfer-or-change-ownership"></a>移動、轉讓或更改擁有權

應用由其 Azure 資源定義，這些資源由擁有者的訂閱決定。 

您可以移動 LUIS 應用。 在 Azure 門戶或 Azure CLI 中使用以下文檔資源：

* [在 LUIS 創作資源之間移動應用](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [將資源移動到新資源組或訂閱](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [在同一訂閱中或跨訂閱中移動資源](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

要轉移訂閱[的擁有權](../../cost-management-billing/manage/billing-subscription-transfer.md)，請進行： 

**對於已遷移 -[創作資源遷移](luis-migration-authoring.md)應用的使用者**：作為資源的擁有者，可以添加 。 `contributor`

**對於尚未遷移的使用者**：將應用匯出為 JSON 檔。 另一個 LUIS 使用者可以導入應用，從而成為應用擁有者。 新應用將具有不同的應用 ID。  

## <a name="access-for-private-and-public-apps"></a>對專用和公共應用的訪問

對於**專用**應用，運行時訪問可供擁有者和參與者使用。 對於**公共**應用，具有自己的 Azure[認知服務](../cognitive-services-apis-create-account.md)或[LUIS](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)運行時資源的並且具有公共應用 ID 的每個人都可以使用運行時存取權限。 

目前，沒有公共應用的目錄。

### <a name="authoring-access"></a>創作存取權限
從[LUIS](luis-reference-regions.md#luis-website)門戶訪問應用或創作[API](https://go.microsoft.com/fwlink/?linkid=2092087)由 Azure 創作資源控制。 

擁有者和所有參與者都有權訪問創建應用。 

|撰寫存取權包括|注意|
|--|--|
|新增或移除端點金鑰||
|匯出版本||
|匯出端點記錄||
|匯入版本||
|將應用程式設定為公用|當應用程式為公用時，任何具有撰寫或端點金鑰的使用者都可查詢該應用程式。|
|修改模型|
|發佈|
|檢閱用於[主動式學習](luis-how-to-review-endpoint-utterances.md)的端點語句|
|定型|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>預測終結點運行時訪問

查詢預測終結點的訪問由 **"管理**"部分中 **"應用程式資訊**"頁上的設置控制。 

|[專用終結點](#runtime-security-for-private-apps)|[公用端點](#runtime-security-for-public-apps)|
|:--|:--|
|可供擁有者和貢獻者使用|可供擁有者、參與者和任何知道應用 ID 的其他人使用|

通過在伺服器到伺服器環境中調用 LUIS 運行時金鑰，可以控制誰看到它。 如果您是從 Bot 使用 LUIS，則 Bot 與 LUIS 之間的連線已經是安全連線。 如果您要直接呼叫 LUIS 端點，則應該建立一個具有受控存取權 (例如 [AAD](https://azure.microsoft.com/services/active-directory/)) 的伺服器端 API (例如 Azure [函數](https://azure.microsoft.com/services/functions/))。 當調用伺服器端 API 並驗證身份驗證時，將調用傳遞給 LUIS。 雖然此策略不會阻止中間人攻擊，但它會將金鑰和終結點 URL 與使用者混淆，允許您跟蹤訪問，並允許您添加終結點回應日誌記錄（如[應用程式見解](https://azure.microsoft.com/services/application-insights/)）。

#### <a name="runtime-security-for-private-apps"></a>專用應用的運行時安全性

專用應用的運行時僅對以下內容可用：

|金鑰和使用者|說明|
|--|--|
|擁有者的撰寫金鑰| 最多 1000 次端點叫用|
|協作者/參與者創作金鑰| 最多 1000 次端點叫用|
|作者或合作者/貢獻者分配給 LUIS 的任何金鑰|以金鑰使用量為基礎|

#### <a name="runtime-security-for-public-apps"></a>公共應用的運行時安全性

將應用程式設定為公用之後，「任何」__ 有效的 LUIS 撰寫金鑰或 LUIS 端點金鑰都能查詢您的應用程式，只要該金鑰尚未使用整個端點配額即可。

不是擁有者或參與者的使用者，如果給定應用 ID，則只能訪問公共應用的運行時。 LUIS 沒有公用「市集」__ 或其他方式可供搜尋公用應用程式。  

公用應用程式會在所有區域中發佈，以便具有區域型 LUIS 資源金鑰的使用者在與資源金鑰相關聯的區域中存取應用程式。

## <a name="transfer-of-ownership"></a>擁有權的轉讓

LUIS 沒有轉移資源擁有權的概念。 

## <a name="securing-the-endpoint"></a>保護端點 

通過在伺服器到伺服器環境中調用 LUIS 預測運行時終結點金鑰，可以控制誰可以看到它。 如果您是從 Bot 使用 LUIS，則 Bot 與 LUIS 之間的連線已經是安全連線。 如果您要直接呼叫 LUIS 端點，則應該建立一個具有受控存取權 (例如 [AAD](https://azure.microsoft.com/services/active-directory/)) 的伺服器端 API (例如 Azure [函數](https://azure.microsoft.com/services/functions/))。 呼叫伺服器端 API 並確認驗證和授權之後，請將呼叫繼續傳遞給 LUIS。 雖然此策略並無法防止攔截式攻擊，但可向您的使用者混淆端點、讓您能夠追蹤存取，以及讓您新增端點回應記錄功能 (例如[Application Insights](https://azure.microsoft.com/services/application-insights/))。  

## <a name="next-steps"></a>後續步驟

* 了解[版本設定](luis-concept-version.md)概念。 
* [瞭解如何創建鍵](luis-how-to-azure-subscription.md)。
