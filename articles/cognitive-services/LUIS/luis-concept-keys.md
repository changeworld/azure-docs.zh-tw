---
title: 如何搭配 LUIS 使用撰寫和執行時間金鑰
titleSuffix: Azure Cognitive Services
description: LUIS 使用兩個金鑰，也就是撰寫金鑰來建立您的模型和執行時間索引鍵，以使用使用者語句查詢預測端點。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 954e7a22ae6b242c6221119c688259e4ce629a2a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82101054"
---
# <a name="authoring-and-runtime-keys"></a>撰寫和執行階段金鑰

Language Understanding （LUIS）有兩個服務和 API 集：

* 撰寫（先前稱為程式_設計_）
* 預測執行時間

有數個金鑰類型，取決於您想要使用的服務，以及您想要如何使用它。

## <a name="non-azure-resources-for-luis"></a>適用于 LUIS 的非 Azure 資源

### <a name="starter-key"></a>入門金鑰

當您第一次開始使用 LUIS 時，系統會為您建立**入門金鑰**。 此資源提供：

* 透過 LUIS 入口網站或 Api （包括 Sdk）的免費撰寫服務要求
* 透過瀏覽器、API 或 Sdk 每月的免費1000預測端點要求

## <a name="azure-resources-for-luis"></a>適用于 LUIS 的 Azure 資源

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

LUIS 允許三種類型的 Azure 資源：

|Key|目的|認知服務`kind`|認知服務`type`|
|--|--|--|--|
|[授權金鑰](#programmatic-key)|使用撰寫、訓練、發行和測試來存取和管理應用程式的資料。 如果您想要以程式設計方式撰寫 LUIS 應用程式，請建立 LUIS 撰寫金鑰。<br><br>`LUIS.Authoring`金鑰的目的是要讓您能夠：<br>* 以程式設計方式管理 Language Understanding 應用程式和模型，包括定型和發佈<br> * 將人員指派給「[參與者」角色](#contributions-from-other-authors)，以控制撰寫資源的許可權。|`LUIS.Authoring`|`Cognitive Services`|
|[預測金鑰](#prediction-endpoint-runtime-key)| 查詢預測端點要求。 在您的用戶端應用程式要求預測超過起始資源所提供的1000要求之前，請先建立 LUIS 預測金鑰。 |`LUIS`|`Cognitive Services`|
|[認知服務多服務資源金鑰](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|與 LUIS 和其他支援的認知服務共用的查詢預測端點要求。|`CognitiveServices`|`Cognitive Services`|

當資源建立程式完成時，請在 LUIS 入口網站中[將金鑰指派](luis-how-to-azure-subscription.md)給應用程式。

請務必在您想要發佈和查詢的[區域](luis-reference-regions.md#publishing-regions)中撰寫 LUIS 應用程式。

> [!CAUTION]
> 為了方便起見，許多範例都會使用[入門金鑰](#starter-key)，因為它會在其[配額](luis-limits.md#key-limits)中提供幾個免費的預測端點呼叫。


### <a name="query-prediction-resources"></a>查詢預測資源

* 執行時間金鑰可用於您的所有 LUIS 應用程式或特定 LUIS 應用程式。
* 請勿使用執行時間金鑰來撰寫 LUIS 應用程式。

LUIS 執行時間端點接受兩種查詢樣式，兩者都使用預測端點執行時間索引鍵，但位於不同的位置。

用來存取執行時間的端點會使用資源區域特有的子域， `{region}`在下表中以表示。

## <a name="assignment-of-the-key"></a>金鑰的指派

您可以在[LUIS 入口網站](https://www.luis.ai)中或透過對應的 api[指派](luis-how-to-azure-subscription.md)執行時間金鑰。

## <a name="key-limits"></a>金鑰限制

針對每個訂用帳戶，您最多可以為每個區域建立10個撰寫金鑰。

請參閱[金鑰限制](luis-limits.md#key-limits)和[Azure 區域](luis-reference-regions.md)。

發佈區域與撰寫區域不同。 請確定您在撰寫區域中建立的應用程式，與您想要用戶端應用程式所在的發列區域相對應。

## <a name="key-limit-errors"></a>金鑰限制錯誤
如果您超過每秒交易數（TP）配額，您會收到 HTTP 429 錯誤。 如果您超過每月筆交易（TPS）配額，就會收到 HTTP 403 錯誤。

## <a name="contributions-from-other-authors"></a>其他作者的貢獻

**針對[撰寫資源遷移](luis-migration-authoring.md)的應用程式**：使用 [**存取控制（IAM）** ] 頁面，可在撰寫資源的 Azure 入口網站中管理_參與者_。 瞭解如何使用共同作業者的電子郵件地址和_參與者_角色[來新增使用者](luis-how-to-collaborate.md)。

**針對尚未遷移的應用程式** _：所有共同作業者都是_在 LUIS 入口網站中，從 [**管理-> 的共同**作業者] 頁面進行管理。

## <a name="move-transfer-or-change-ownership"></a>移動、轉移或變更擁有權

應用程式是由其 Azure 資源所定義，由擁有者的訂用帳戶決定。

您可以移動 LUIS 應用程式。 請使用 Azure 入口網站或 Azure CLI 中的下列檔資源：

* [在 LUIS 撰寫資源之間移動應用程式](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [將資源移到新的資源群組或訂用帳戶](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [在相同訂用帳戶內或跨訂用帳戶移動資源](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

若要轉移訂用帳戶的[擁有權](../../cost-management-billing/manage/billing-subscription-transfer.md)：

**針對已遷移並[撰寫資源](luis-migration-authoring.md)的已遷移應用程式的使用者**：身為資源的擁有者，您`contributor`可以新增。

**針對尚未遷移的使用者**：將您的應用程式匯出為 JSON 檔案。 另一個 LUIS 使用者可以匯入應用程式，因此成為應用程式擁有者。 新的應用程式將會有不同的應用程式識別碼。

## <a name="access-for-private-and-public-apps"></a>私用和公用應用程式的存取權

針對**私**用應用程式，擁有者和參與者可以使用執行時間存取。 針對**公用**應用程式，具有自己的 Azure[認知服務](../cognitive-services-apis-create-account.md)或[LUIS](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)執行時間資源且具有公用應用程式識別碼的所有人都可以使用執行時間存取。

目前沒有公用應用程式的目錄。

### <a name="authoring-access"></a>撰寫存取權
從[LUIS](luis-reference-regions.md#luis-website)入口網站或[撰寫 api](https://go.microsoft.com/fwlink/?linkid=2092087)存取應用程式是由 Azure 撰寫資源所控制。

擁有者和所有參與者都有權撰寫應用程式。

|撰寫存取權包括|備忘錄|
|--|--|
|新增或移除端點金鑰||
|匯出版本||
|匯出端點記錄||
|匯入版本||
|將應用程式設定為公用|當應用程式為公用時，任何具有撰寫或端點金鑰的使用者都可查詢該應用程式。|
|修改模型|
|發行|
|檢閱用於[主動式學習](luis-how-to-review-endpoint-utterances.md)的端點語句|
|定型|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>預測端點執行時間存取

查詢預測端點的存取權是由 [**管理**] 區段中 [**應用程式資訊**] 頁面上的設定所控制。

|[私用端點](#runtime-security-for-private-apps)|[公用端點](#runtime-security-for-public-apps)|
|:--|:--|
|可供擁有者和參與者使用|適用于擁有者、參與者，以及任何知道應用程式識別碼的其他人|

您可以在伺服器對伺服器的環境中呼叫 LUIS 執行時間金鑰，以控制他們看到的人。 如果您是從 Bot 使用 LUIS，則 Bot 與 LUIS 之間的連線已經是安全連線。 如果您要直接呼叫 LUIS 端點，則應該建立一個具有受控存取權 (例如 [AAD](https://azure.microsoft.com/services/active-directory/)) 的伺服器端 API (例如 Azure [函數](https://azure.microsoft.com/services/functions/))。 當呼叫伺服器端 API 並驗證並驗證授權時，請將上的呼叫傳遞給 LUIS。 雖然此策略不會防止攔截式攻擊，但它會從您的使用者混淆您的金鑰和端點 URL，讓您能夠追蹤存取權，並可讓您新增端點回應記錄（例如[Application Insights](https://azure.microsoft.com/services/application-insights/)）。

#### <a name="runtime-security-for-private-apps"></a>私用應用程式的執行時間安全性

私用應用程式的執行時間僅適用于下列各項：

|金鑰和使用者|說明|
|--|--|
|擁有者的撰寫金鑰| 最多 1000 次端點叫用|
|共同作業者/參與者撰寫金鑰| 最多 1000 次端點叫用|
|作者或共同作業者/參與者指派給 LUIS 的任何金鑰|以金鑰使用量為基礎|

#### <a name="runtime-security-for-public-apps"></a>公用應用程式的執行時間安全性

將應用程式設定為公用之後，「任何」__ 有效的 LUIS 撰寫金鑰或 LUIS 端點金鑰都能查詢您的應用程式，只要該金鑰尚未使用整個端點配額即可。

如果指定了應用程式識別碼，則不是擁有者或參與者的使用者，只能存取公用應用程式的執行時間。 LUIS 沒有公用「市集」__ 或其他方式可供搜尋公用應用程式。

公用應用程式會在所有區域中發佈，以便具有區域型 LUIS 資源金鑰的使用者在與資源金鑰相關聯的區域中存取應用程式。

## <a name="transfer-of-ownership"></a>擁有權的轉讓

LUIS 沒有轉移資源擁有權的概念。

## <a name="securing-the-endpoint"></a>保護端點

您可以在伺服器對伺服器的環境中呼叫它，以控制可查看 LUIS 預測執行時間端點金鑰的人員。 如果您是從 Bot 使用 LUIS，則 Bot 與 LUIS 之間的連線已經是安全連線。 如果您要直接呼叫 LUIS 端點，則應該建立一個具有受控存取權 (例如 [AAD](https://azure.microsoft.com/services/active-directory/)) 的伺服器端 API (例如 Azure [函數](https://azure.microsoft.com/services/functions/))。 呼叫伺服器端 API 並確認驗證和授權之後，請將呼叫繼續傳遞給 LUIS。 雖然此策略並無法防止攔截式攻擊，但可向您的使用者混淆端點、讓您能夠追蹤存取，以及讓您新增端點回應記錄功能 (例如[Application Insights](https://azure.microsoft.com/services/application-insights/))。

## <a name="next-steps"></a>後續步驟

* 了解[版本設定](luis-concept-version.md)概念。
* 瞭解[如何建立金鑰](luis-how-to-azure-subscription.md)。
