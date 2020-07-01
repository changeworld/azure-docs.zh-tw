---
title: 如何使用撰寫和執行時間金鑰-LUIS
description: 當您第一次使用 Language Understanding （LUIS）時，您不需要建立撰寫金鑰。 當您想要發佈應用程式時，請使用您的執行時間端點，您必須建立執行時間金鑰，並將其指派給應用程式。
services: cognitive-services
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 5f6d62a63ea5ae0d3e4ca5913d6e7834ba07692a
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560421"
---
# <a name="create-luis-resources"></a>建立 LUIS 資源

撰寫和查詢預測執行時間資源可為您的 LUIS 應用程式和預測端點提供驗證。

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>LUIS 資源

LUIS 允許三種類型的 Azure 資源和一個非 Azure 資源：

|機碼|目的|認知服務`kind`|認知服務`type`|
|--|--|--|--|
|撰寫金鑰|使用撰寫、訓練、發行和測試來存取和管理應用程式的資料。 如果您想要以程式設計方式撰寫 LUIS 應用程式，請建立 LUIS 撰寫金鑰。<br><br>金鑰的目的 `LUIS.Authoring` 是要讓您能夠：<br>* 以程式設計方式管理 Language Understanding 應用程式和模型，包括定型和發佈<br> * 將人員指派給「[參與者」角色](#contributions-from-other-authors)，以控制撰寫資源的許可權。|`LUIS.Authoring`|`Cognitive Services`|
|查詢預測金鑰| 查詢預測端點要求。 在您的用戶端應用程式要求預測超過起始資源所提供的1000要求之前，請先建立 LUIS 預測金鑰。 |`LUIS`|`Cognitive Services`|
|[認知服務多服務資源金鑰](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|與 LUIS 和其他支援的認知服務共用的查詢預測端點要求。|`CognitiveServices`|`Cognitive Services`|
|入門|透過 LUIS 入口網站或 Api （包括 Sdk）的免費撰寫（不含角色型存取控制），透過瀏覽器、API 或 Sdk 每月免費1000預測端點要求|-|不是 Azure 資源|

Azure 資源建立程式完成時，請在 LUIS 入口網站中[將金鑰指派](#assign-a-resource-to-an-app)給應用程式。

請務必在您想要發佈和查詢的[區域](luis-reference-regions.md#publishing-regions)中撰寫 LUIS 應用程式。

## <a name="resource-ownership"></a>資源擁有權

Azure 資源（例如 LUIS）是由包含資源的訂用帳戶所擁有。

若要轉移資源的擁有權，ou 可以是：
* 轉移訂用帳戶的[擁有權](../../cost-management-billing/manage/billing-subscription-transfer.md)
* 將 LUIS 應用程式匯出為檔案，然後將應用程式匯入至不同的訂用帳戶。 您可以從 LUIS 入口網站的 [**我的應用程式**] 頁面取得匯出。


## <a name="resource-limits"></a>資源限制

### <a name="authoring-key-creation-limits"></a>撰寫金鑰建立限制

針對每個訂用帳戶，您最多可以為每個區域建立10個撰寫金鑰。

請參閱[金鑰限制](luis-limits.md#key-limits)和[Azure 區域](luis-reference-regions.md)。

發佈區域與撰寫區域不同。 請確定您在撰寫區域中建立的應用程式，與您想要用戶端應用程式所在的發列區域相對應。

### <a name="key-usage-limit-errors"></a>金鑰使用限制錯誤

使用量限制是以定價層為基礎。

如果您超過每秒交易數（TP）配額，您會收到 HTTP 429 錯誤。 如果您超過每月筆交易（TPS）配額，就會收到 HTTP 403 錯誤。


### <a name="reset-authoring-key"></a>重設撰寫金鑰

針對[撰寫資源遷移](luis-migration-authoring.md)的應用程式：如果您的撰寫金鑰遭到入侵，請在該撰寫資源的 [**金鑰**] 頁面上，重設 Azure 入口網站中的金鑰。

針對尚未遷移的應用程式：金鑰會在 LUIS 入口網站中的所有應用程式上重設。 如果您透過撰寫 Api 來撰寫應用程式，則需要將 Apim-訂用帳戶金鑰的值變更為新的金鑰。

### <a name="regenerate-azure-key"></a>重新產生 Azure 金鑰

從 [**金鑰**] 頁面上的 Azure 入口網站重新產生 Azure 金鑰。


## <a name="app-ownership-access-and-security"></a>應用程式擁有權、存取和安全性

應用程式是由其 Azure 資源所定義，由擁有者的訂用帳戶決定。

您可以移動 LUIS 應用程式。 請使用 Azure 入口網站或 Azure CLI 中的下列檔資源：

* [在 LUIS 撰寫資源之間移動應用程式](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [將資源移到新的資源群組或訂用帳戶](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [在相同訂用帳戶內或跨訂用帳戶移動資源](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>其他作者的貢獻

針對[撰寫資源遷移](luis-migration-authoring.md)的應用程式：使用 [**存取控制（IAM）** ] 頁面，可在撰寫資源的 Azure 入口網站中管理_參與者_。 瞭解如何使用共同作業者的電子郵件地址和_參與者_角色[來新增使用者](luis-how-to-collaborate.md)。

針對尚未遷移的應用程式 _：所有共同作業者都是_在 LUIS 入口網站中，從 [**管理-> 的共同**作業者] 頁面進行管理。

### <a name="query-prediction-access-for-private-and-public-apps"></a>私用和公用應用程式的查詢預測存取

針對**私**用應用程式，可以使用「擁有者」和「參與者」的查詢預測執行時間存取。 針對**公用**應用程式，具有自己的 Azure[認知服務](../cognitive-services-apis-create-account.md)或[LUIS](#create-resources-in-the-azure-portal)執行時間資源且具有公用應用程式識別碼的所有人都可以使用執行時間存取。

目前沒有公用應用程式的目錄。

### <a name="authoring-permissions-and-access"></a>撰寫許可權和存取
從[LUIS](luis-reference-regions.md#luis-website)入口網站或[撰寫 api](https://go.microsoft.com/fwlink/?linkid=2092087)存取應用程式是由 Azure 撰寫資源所控制。

擁有者和所有參與者都有權撰寫應用程式。

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

### <a name="prediction-endpoint-runtime-access"></a>預測端點執行時間存取

查詢預測端點的存取權是由 [**管理**] 區段中 [**應用程式資訊**] 頁面上的設定所控制。

|[私人端點](#runtime-security-for-private-apps)|[公用端點](#runtime-security-for-public-apps)|
|:--|:--|
|可供擁有者和參與者使用|適用于擁有者、參與者，以及任何知道應用程式識別碼的其他人|

您可以在伺服器對伺服器的環境中呼叫 LUIS 執行時間金鑰，以控制他們看到的人。 如果您是從 Bot 使用 LUIS，則 Bot 與 LUIS 之間的連線已經是安全連線。 如果您要直接呼叫 LUIS 端點，則應該建立一個具有受控存取權 (例如 [AAD](https://azure.microsoft.com/services/active-directory/)) 的伺服器端 API (例如 Azure [函數](https://azure.microsoft.com/services/functions/))。 當呼叫伺服器端 API 並驗證並驗證授權時，請將上的呼叫傳遞給 LUIS。 雖然此策略不會防止攔截式攻擊，但它會從您的使用者混淆您的金鑰和端點 URL，讓您能夠追蹤存取權，並可讓您新增端點回應記錄（例如[Application Insights](https://azure.microsoft.com/services/application-insights/)）。

### <a name="runtime-security-for-private-apps"></a>私用應用程式的執行時間安全性

私用應用程式的執行時間僅適用于下列各項：

|金鑰和使用者|說明|
|--|--|
|擁有者的撰寫金鑰| 最多 1000 次端點叫用|
|共同作業者/參與者撰寫金鑰| 最多 1000 次端點叫用|
|作者或共同作業者/參與者指派給 LUIS 的任何金鑰|以金鑰使用量為基礎|

### <a name="runtime-security-for-public-apps"></a>公用應用程式的執行時間安全性

將應用程式設定為公用之後，「任何」__ 有效的 LUIS 撰寫金鑰或 LUIS 端點金鑰都能查詢您的應用程式，只要該金鑰尚未使用整個端點配額即可。

如果指定了應用程式識別碼，則不是擁有者或參與者的使用者，只能存取公用應用程式的執行時間。 LUIS 沒有公用「市集」__ 或其他方式可供搜尋公用應用程式。

公用應用程式會在所有區域中發佈，以便具有區域型 LUIS 資源金鑰的使用者在與資源金鑰相關聯的區域中存取應用程式。


### <a name="securing-the-query-prediction-endpoint"></a>保護查詢預測端點

您可以在伺服器對伺服器的環境中呼叫它，以控制可查看 LUIS 預測執行時間端點金鑰的人員。 如果您是從 Bot 使用 LUIS，則 Bot 與 LUIS 之間的連線已經是安全連線。 如果您要直接呼叫 LUIS 端點，則應該建立一個具有受控存取權 (例如 [AAD](https://azure.microsoft.com/services/active-directory/)) 的伺服器端 API (例如 Azure [函數](https://azure.microsoft.com/services/functions/))。 呼叫伺服器端 API 並確認驗證和授權之後，請將呼叫繼續傳遞給 LUIS。 雖然此策略並無法防止攔截式攻擊，但可向您的使用者混淆端點、讓您能夠追蹤存取，以及讓您新增端點回應記錄功能 (例如[Application Insights](https://azure.microsoft.com/services/application-insights/))。

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>登入 LUIS 入口網站並開始撰寫

1. 登入[LUIS 入口網站](https://www.luis.ai)並同意使用條款。
1. 選擇您想要使用的 LUIS 撰寫金鑰類型，以開始您的 LUIS 應用程式：免費試用金鑰或新的 Azure LUIS 撰寫金鑰。

    ![選擇 Language Understanding 撰寫資源的類型](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. 當您完成資源選取程式時，請[建立新的應用程式](luis-how-to-start-new-app.md#create-new-app-in-luis)。


## <a name="create-azure-resources"></a>建立 Azure 資源

<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure Portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-azure-cli"></a>在 Azure CLI 中建立資源

使用[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)來個別建立每個資源。

資源 `kind` ：

* 製作`LUIS.Authoring`
* 翻`LUIS`

1. 登入 Azure CLI：

    ```azurecli
    az login
    ```

    這會開啟瀏覽器，讓您選取正確的帳戶並提供驗證。

1. 在為**LUIS authoring resource** `LUIS.Authoring` `my-luis-authoring-resource` 區域命名的_現有_資源群組中，建立名為的 LUIS 撰寫資源（種類） `my-resource-group` `westus` 。

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. 在為**LUIS prediction endpoint resource** `LUIS` `my-luis-prediction-resource` 區域命名的_現有_資源群組中，建立名為的 LUIS 預測端點資源（種類） `my-resource-group` `westus` 。 如果您想要比免費層更高的輸送量，請將變更 `F0` 為 `S0` 。 深入瞭解[定價層和輸送量](luis-limits.md#key-limits)。

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > LUIS 入口網站**不**會使用此金鑰，除非是在 LUIS 入口網站中的**管理 > Azure 資源**上指派。

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resource-in-the-luis-portal"></a>在 LUIS 入口網站中指派資源

您可以針對單一應用程式或 LUIS 中的所有應用程式，指派撰寫資源。 下列程式會將所有應用程式指派給單一撰寫資源。

1. 登入 [LUIS 入口網站](https://www.luis.ai)。
1. 在上方導覽列中，選取最右側的 [使用者帳戶]，然後選取 [**設定**]。
1. 在 [**使用者設定**] 頁面上，選取 [**新增撰寫資源**]，然後選取現有的撰寫資源。 選取 [儲存]。

## <a name="assign-a-resource-to-an-app"></a>將資源指派給應用程式

您可以使用下列程式將指派給應用程式。

1. 登入[LUIS 入口網站](https://www.luis.ai)，然後從 [**我的應用程式**] 清單中選取應用程式。
1. 流覽至 [**管理->] [Azure 資源**] 頁面。

    ![在 LUIS 入口網站中選取 [管理-> Azure 資源]，以將資源指派給應用程式。](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. 選取 [預測] 或 [撰寫資源] 索引標籤，然後選取 [**新增預測資源**] 或 [**新增撰寫資源**] 按鈕。
1. 選取表單中的欄位以尋找正確的資源，然後選取 [**儲存**]。

### <a name="assign-query-prediction-runtime-resource-without-using-luis-portal"></a>在不使用 LUIS 入口網站的情況下指派查詢預測執行時間資源

基於自動化目的（例如 CI/CD 管線），您可能會想要自動將 LUIS 執行時間資源指派給 LUIS 應用程式。 為此，您必須執行下列步驟：

1. 從這個[網站](https://resources.azure.com/api/token?plaintext=true)取得 Azure Resource Manager 權杖。 此權杖有使用期限，因此請立即使用。 要求會傳回 Azure Resource Manager 權杖。

    ![要求 Azure Resource Manager 權杖和接收 Azure Resource Manager 權杖](./media/luis-manage-keys/get-arm-token.png)

1. 使用權杖，從您的使用者帳戶可存取的[GET LUIS azure 帳戶 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)，跨訂用帳戶要求 LUIS 執行時間資源。

    此 POST API 需要下列設定︰

    |頁首|值|
    |--|--|
    |`Authorization`|`Authorization` 的值為 `Bearer {token}`。 請注意，權杖值的開頭必須加上 `Bearer` 一字和空格。|
    |`Ocp-Apim-Subscription-Key`|您的撰寫金鑰。|

    此 API 會傳回您 LUIS 訂用帳戶的 JSON 物件陣列，其中包含訂用帳戶識別碼、資源群組和資源名稱 (傳回作為帳戶名稱)。 請在此陣列中找出要指派給 LUIS 應用程式的 LUIS 資源項目。

1. 使用[將 LUIS Azure 帳戶指派給應用程式](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API，將權杖指派給 LUIS 資源。

    此 POST API 需要下列設定︰

    |類型|設定|值|
    |--|--|--|
    |頁首|`Authorization`|`Authorization` 的值為 `Bearer {token}`。 請注意，權杖值的開頭必須加上 `Bearer` 一字和空格。|
    |頁首|`Ocp-Apim-Subscription-Key`|您的撰寫金鑰。|
    |頁首|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS 應用程式識別碼。
    |主體||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    此 API 成功執行時，會傳回「201 - 已建立」狀態。

## <a name="unassign-resource"></a>取消指派資源

1. 登入[LUIS 入口網站](https://www.luis.ai)，然後從 [**我的應用程式**] 清單中選取應用程式。
1. 流覽至 [**管理->] [Azure 資源**] 頁面。
1. 選取 [預測] 或 [撰寫資源] 索引標籤，然後選取資源的 [**取消指派資源**] 按鈕。

當您取消指派資源時，不會將它從 Azure 中刪除。 只會從 LUIS 取消連結。


## <a name="delete-account"></a>刪除帳戶

如需刪除帳戶時會刪除哪些資料的相關資訊，請參閱[資料儲存和移除](luis-concept-data-storage.md#accounts)。

## <a name="change-pricing-tier"></a>變更定價層

1.  在 [Azure](https://portal.azure.com) 中，尋找您的 LUIS 訂用帳戶。 選取 LUIS 訂用帳戶。
    ![尋找 LUIS 訂用帳戶](./media/luis-usage-tiers/find.png)
1.  選取 [定價層]**** 以查看可用的定價層。
    ![檢視定價層](./media/luis-usage-tiers/subscription.png)
1.  選取定價層，然後選取 [選取]**** 以儲存變更。
    ![變更 LUIS 付款層](./media/luis-usage-tiers/plans.png)
1.  定價變更完成時，快顯視窗會確認新的定價層。
    ![驗證 LUIS 付款層](./media/luis-usage-tiers/updated.png)
1. 請記得在 [發佈]**** 頁面上[指派此端點金鑰](#assign-a-resource-to-an-app)，然後將它使用於所有端點查詢。

## <a name="viewing-azure-resource-metrics"></a>查看 Azure 資源計量

### <a name="viewing-azure-resource-summary-usage"></a>查看 Azure 資源摘要使用量
您可以在 Azure 中檢視 LUIS 使用量資訊。 [概觀]**** 頁面會顯示近期摘要資訊，包括呼叫和錯誤。 如果您提出 LUIS 端點要求，然後立即觀看 [概觀] 頁面****，允許使用量最多在五分鐘內出現。

![檢視摘要使用量](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>自訂 Azure 資源使用量圖表
計量會提供資料的更詳細檢視。

![預設度量](./media/luis-usage-tiers/metrics-default.png)

您可以設定時間週期和計量類型的計量圖表。

![自訂度量](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>交易總數閾值警示
如果您想要知道何時達到特定交易閾值 (例如 10,000 筆交易) 時，可以建立警示。

![預設警示](./media/luis-usage-tiers/alert-default.png)

為一段時間的**呼叫總數**計量新增計量警示。 新增應收到警示的所有人員電子郵件地址。 為應收到警示的所有系統新增 Webhook。 您也可以在警示觸發時執行邏輯應用程式。

## <a name="next-steps"></a>後續步驟

* 瞭解[如何使用版本](luis-how-to-manage-versions.md)來控制您的應用程式生命週期。
* 遷移至新的[撰寫資源](luis-migration-authoring.md)
