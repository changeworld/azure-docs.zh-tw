---
title: 使用撰寫和執行時間金鑰-LUIS
description: 當您第一次使用 LUIS 時，您不需要建立撰寫金鑰。 當您想要發佈應用程式，然後使用您的執行時間端點時，您需要建立執行時間金鑰並將其指派給應用程式。
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 07a274bd4ac227b6260f7891b24dad0eacdfb4f7
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561503"
---
# <a name="create-luis-resources"></a>建立 LUIS 資源

撰寫和查詢預測執行時間資源可提供驗證給 Language Understanding (LUIS) 應用程式和預測端點。

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>LUIS 資源

LUIS 允許三種類型的 Azure 資源和一個非 Azure 資源：

|資源|目的|認知服務 `kind`|認知服務 `type`|
|--|--|--|--|
|撰寫資源|可讓您建立、管理、定型、測試及發佈您的應用程式。 如果您想要以程式設計方式或從 LUIS 入口網站撰寫 LUIS apps，請[建立 LUIS 撰寫資源](luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal)。 您必須先 [遷移您的 LUIS 帳戶](luis-migration-authoring.md#what-is-migration) ，才能將 Azure 撰寫資源連結至應用程式。 您可以藉由指派 [參與者角色](#contributions-from-other-authors)的人員來控制撰寫資源的許可權。 <br><br> LUIS 撰寫資源可以使用一層：<br> <ul> <li>**免費的 F0 撰寫資源** ，可提供1000000個免費的撰寫交易和1000個月免費的測試預測端點要求。 |`LUIS.Authoring`|`Cognitive Services`|
|預測資源| 發佈 LUIS 應用程式之後，請使用預測資源/金鑰來查詢預測端點要求。 在用戶端應用程式要求預測超過撰寫或入門資源所提供的1000要求之前，請先建立 LUIS 預測資源。 <br><br> 預測資源可以使用兩個層級：<br><ul> <li> **免費的 F0 預測資源** ，每月提供10000個免費的預測端點要求。<br> <li> **標準 S0 預測資源** ，也就是付費層。 [深入瞭解定價。](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|入門/試用資源|可讓您建立、管理、定型、測試及發佈您的應用程式。 如果您在第一次登入 LUIS 時選擇入門資源選項，預設會建立此資源。 入門金鑰最後將被取代。 所有 LUIS 使用者都必須 [遷移其帳戶](luis-migration-authoring.md#what-is-migration) ，並將其 LUIS 應用程式連結至撰寫資源。 與撰寫資源不同的是，此資源並不會授與您 Azure 角色型存取控制的許可權。 <br><br> 就像撰寫資源一樣，入門資源提供1000000個免費的撰寫交易和1000個免費的測試預測端點要求。|-|不是 Azure 資源。|
|[認知服務 multiservice 資源金鑰](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|查詢與 LUIS 和其他支援的認知服務共用的預測端點要求。|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> LUIS 提供兩種類型的 F0 () 資源的免費層：一個用於撰寫交易，另一個用於預測交易。 如果您的預測交易可用配額不足，請確定您使用的是 F0 預測資源，它會每月提供免費的10000筆交易，而不是撰寫資源，每個月都會提供1000個預測交易。

當 Azure 資源建立程式完成時，請在 LUIS 入口網站中 [將資源指派](#assign-a-resource-to-an-app) 給應用程式。

> [!important]
> 您應該在要發佈和查詢的 [區域](luis-reference-regions.md#publishing-regions) 中撰寫 LUIS 應用程式。

## <a name="resource-ownership"></a>資源擁有權

Azure 資源（例如 LUIS 資源）是由包含資源的訂用帳戶所擁有。

若要變更資源的擁有權，您可以採取下列其中一項動作：
* 轉移訂用帳戶的 [擁有權](../../cost-management-billing/manage/billing-subscription-transfer.md) 。
* 將 LUIS 應用程式匯出為檔案，然後將應用程式匯入至不同的訂用帳戶。 您可以在 LUIS 入口網站的 [ **我的應用程式** ] 頁面上取得匯出。


## <a name="resource-limits"></a>資源限制

### <a name="authoring-key-creation-limits"></a>撰寫金鑰建立限制

每個訂用帳戶最多可以為每個區域建立10個撰寫金鑰。

如需詳細資訊，請參閱 [主要限制](luis-limits.md#key-limits) 和 [Azure 區域](luis-reference-regions.md)。

發佈區域與撰寫區域不同。 請確定您在撰寫區域中建立的應用程式，與您想要放置用戶端應用程式的發列區域相對應。

### <a name="errors-for-key-usage-limits"></a>金鑰使用限制的錯誤

使用量限制是以定價層為基礎。

如果您超出每秒交易 (TPS) 配額，就會收到 HTTP 429 錯誤。 如果您每個月超過交易 (TPM) 配額，您會收到 HTTP 403 錯誤。


### <a name="reset-an-authoring-key"></a>重設撰寫金鑰

針對已 [遷移的撰寫資源](luis-migration-authoring.md) 應用程式：如果您的撰寫金鑰遭到入侵，請在撰寫資源的 [ **金鑰** ] 頁面上重設 Azure 入口網站中的金鑰。

針對尚未遷移的應用程式：您可以在 LUIS 入口網站中的所有應用程式上重設金鑰。 如果您透過撰寫 API 來撰寫您的應用程式，則需要將 `Ocp-Apim-Subscription-Key` 的值變更成新的金鑰。

### <a name="regenerate-an-azure-key"></a>重新產生 Azure 金鑰

您可以從 Azure 入口網站的 [ **金鑰** ] 頁面重新產生 Azure 金鑰。


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>應用程式擁有權、存取權和安全性

應用程式是由其 Azure 資源所定義，由擁有者的訂用帳戶所決定。

您可以移動您的 LUIS 應用程式。 使用下列資源可協助您使用 Azure 入口網站或 Azure CLI：

* [在 LUIS 編寫資源之間移動應用程式](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [將資源移至新的資源群組或訂用帳戶](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [移動相同訂用帳戶內或跨訂用帳戶的資源](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>其他作者的投稿

針對已 [遷移的撰寫資源](luis-migration-authoring.md)應用程式：您可以使用 **存取控制 (IAM)** 頁面，在 Azure 入口網站中管理撰寫資源的 _參與者_ 。 瞭解如何使用共同作業者的電子郵件地址和參與者角色 [來新增使用者](luis-how-to-collaborate.md) 。

針對尚未遷移的應用程式：您可以在 LUIS 入口網站的 [ **管理-> 共同** 作業者] _頁面上管理_ 所有共同作業者。

### <a name="query-prediction-access-for-private-and-public-apps"></a>私用和公用應用程式的查詢預測存取

若為私用應用程式，擁有者和參與者都可以使用查詢預測執行時間存取。 針對公用應用程式，具有自己的 Azure [認知服務](../cognitive-services-apis-create-account.md) 或 [LUIS](#create-resources-in-the-azure-portal) 執行時間資源和公用應用程式識別碼的使用者可以使用執行時間存取。

目前沒有公用應用程式的目錄。

### <a name="authoring-permissions-and-access"></a>撰寫許可權和存取權
從 [LUIS](luis-reference-regions.md#luis-website) 入口網站或 [撰寫 api](https://go.microsoft.com/fwlink/?linkid=2092087) 存取應用程式是由 Azure 撰寫資源所控制。

擁有者和所有參與者都具有編寫應用程式的存取權。

|撰寫存取權包括：|備註|
|--|--|
|新增或移除端點金鑰||
|匯出版本||
|匯出端點記錄||
|匯入版本||
|將應用程式設定為公用|當應用程式是公用時，具有撰寫或端點金鑰的任何人都可以查詢應用程式。|
|修改模型|
|發行|
|檢閱用於[主動式學習](luis-how-to-review-endpoint-utterances.md)的端點語句|
|定型|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>預測端點執行時間存取

查詢預測端點的存取權是由 [ **管理** ] 區段中 [ **應用程式資訊** ] 頁面上的設定所控制。

|[私人端點](#runtime-security-for-private-apps)|[公用端點](#runtime-security-for-public-apps)|
|:--|:--|
|適用于擁有者和參與者|適用于擁有者、參與者，以及任何知道應用程式識別碼的其他人|

您可以在伺服器對伺服器環境中呼叫，以控制誰看到您的 LUIS 執行時間金鑰。 如果您是從 bot 使用 LUIS，則 bot 與 LUIS 之間的連線已更安全。 如果您要直接呼叫 LUIS 端點，您應該建立伺服器端 API ([例如 Azure 函](https://azure.microsoft.com/services/functions/) 式) ，以及透過 [Azure AD](https://azure.microsoft.com/services/active-directory/)) 之類的控制存取 (。 當呼叫伺服器端 API 並驗證並驗證授權時，請將呼叫傳遞給 LUIS。 這項策略不會防止攔截式攻擊。 但是，它會從您的使用者混淆您的金鑰和端點 URL，讓您能夠追蹤存取權，並可讓您新增端點回應記錄 (例如 [Application Insights](https://azure.microsoft.com/services/application-insights/)) 。

### <a name="runtime-security-for-private-apps"></a>私用應用程式的執行時間安全性

私用應用程式的執行時間僅適用于下列金鑰：

|金鑰和使用者|說明|
|--|--|
|擁有者的撰寫金鑰| 最多1000端點叫用|
|共同作業者/參與者撰寫金鑰| 最多1000端點叫用|
|作者或共同作業者/參與者指派給 LUIS 的任何金鑰|以金鑰使用量為基礎|

### <a name="runtime-security-for-public-apps"></a>公用應用程式的執行時間安全性

當您的應用程式設定為公用時，只要金鑰未使用整個端點配額， _任何_ 有效的 LUIS 撰寫金鑰或 LUIS 端點金鑰都可以查詢它。

如果使用者不是擁有者或參與者，則只有在指定應用程式識別碼時，才能存取公用應用程式的執行時間。 LUIS 沒有公開市場或任何其他使用者搜尋公用應用程式的方式。

公開應用程式會在所有區域中發行。 因此，具有以區域為基礎之 LUIS 資源金鑰的使用者，可以在任何與資源金鑰相關聯的區域中存取應用程式。


### <a name="control-access-to-your-query-prediction-endpoint"></a>控制查詢預測端點的存取權

您可以在伺服器對伺服器環境中呼叫，以控制誰可以查看您的 LUIS 預測執行時間端點金鑰。 如果您是從 bot 使用 LUIS，則 bot 與 LUIS 之間的連線已更安全。 如果您要直接呼叫 LUIS 端點，您應該建立伺服器端 API ([例如 Azure 函](https://azure.microsoft.com/services/functions/) 式) ，以及透過 [Azure AD](https://azure.microsoft.com/services/active-directory/)) 之類的控制存取 (。 呼叫伺服器端 API 並確認驗證和授權之後，請將呼叫繼續傳遞給 LUIS。 這項策略不會防止攔截式攻擊。 但是，它會從您的使用者混淆您的端點，讓您能夠追蹤存取權，並可讓您新增端點回應記錄 (例如 [Application Insights](https://azure.microsoft.com/services/application-insights/)) 。

<a name="starter-key"></a>

## <a name="sign-in-to-the-luis-portal-and-begin-authoring"></a>登入 LUIS 入口網站並開始撰寫

1. 登入 [LUIS 入口網站](https://www.luis.ai) ，並同意使用條款。
1. 選擇您的 Azure LUIS 撰寫金鑰以開始撰寫您的 LUIS 應用程式：

   ![顯示 [歡迎使用] 畫面的螢幕擷取畫面。](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. 當您完成資源選取流程時，請 [建立新的應用程式](luis-how-to-start-new-app.md#create-new-app-in-luis)。


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-the-azure-cli"></a>在 Azure CLI 中建立資源

使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 來個別建立每個資源。

資源 `kind` ：

* 創作： `LUIS.Authoring`
* 預測： `LUIS`

1. 登入 Azure CLI：

    ```azurecli
    az login
    ```

    此命令會開啟瀏覽器，讓您可以選取正確的帳戶並提供驗證。

1. 建立種類的 LUIS 撰寫資源 `LUIS.Authoring` ，名為 `my-luis-authoring-resource` 。 在為該區域命名的 _現有_ 資源群組中建立它 `my-resource-group` `westus` 。

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. 建立類型的 LUIS 預測端點資源 `LUIS` ，名為 `my-luis-prediction-resource` 。 在為該區域命名的 _現有_ 資源群組中建立它 `my-resource-group` `westus` 。 如果您想要比免費層所提供的更高輸送量，請將變更 `F0` 為 `S0` 。 [深入瞭解定價層和輸送量。](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > 在 LUIS 入口網站的 [ **管理**  >  **Azure 資源** ] 頁面上指派這些金鑰之前，LUIS 入口網站不會使用這些金鑰。

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resources-in-the-luis-portal"></a>在 LUIS 入口網站中指派資源

您可以針對單一應用程式或 LUIS 中的所有應用程式，指派撰寫資源。 下列程式會將所有應用程式指派給單一撰寫資源。

1. 登入 [LUIS 入口網站](https://www.luis.ai)。
1. 在右上角選取您的使用者帳戶，然後選取 [ **設定** ]。
1. 在 [ **使用者設定** ] 頁面上，選取 [ **新增撰寫資源** ]，然後選取現有的撰寫資源。 選取 [儲存]。

## <a name="assign-a-resource-to-an-app"></a>將資源指派給應用程式

>[!NOTE]
>如果您沒有 Azure 訂用帳戶，您將無法指派或建立新的資源。 您必須建立 [Azure 免費帳戶](https://azure.microsoft.com/en-us/free/) ，然後返回 LUIS 以從入口網站建立新的資源。

您可以使用這個程式來建立撰寫或預測資源，或將它指派給應用程式： 

1. 登入 [LUIS 入口網站](https://www.luis.ai)。 從 [我的應用程式] 清單中選取應用程式。
1. 移至 **管理**  >  **Azure 資源** ：

    ![顯示 Azure 資源頁面的螢幕擷取畫面。](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. 在 [ **預測資源** 或 **撰寫資源** ] 索引標籤上，選取 [ **新增預測資源** ] 或 [ **加入撰寫資源** ] 按鈕。
1. 使用表單中的欄位來尋找正確的資源，然後選取 [ **儲存** ]。
1. 如果您沒有現有的資源，您可以選取視窗底部的 [ **建立新的 LUIS 資源？** ] 建立一個資源。


### <a name="assign-a-query-prediction-runtime-resource-without-using-the-luis-portal"></a>在不使用 LUIS 入口網站的情況下指派查詢預測執行時間資源

針對 CI/CD 管線之類的自動化程式，您可能會想要自動將 LUIS 執行時間資源指派給 LUIS 應用程式。 若要這樣做，請完成下列步驟：

1. 從 [這個網站](https://resources.azure.com/api/token?plaintext=true)取得 Azure Resource Manager 的權杖。 此權杖已過期，因此立即使用。 要求會傳回 Azure Resource Manager 權杖。

    ![顯示要求 Azure Resource Manager 權杖之網站的螢幕擷取畫面。](./media/luis-manage-keys/get-arm-token.png)

1. 使用權杖來跨訂用帳戶要求 LUIS 執行時間資源。 使用您的使用者帳戶可存取的 [GET LUIS Azure 帳戶 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)。

    此 POST API 需要下列值：

    |頁首|值|
    |--|--|
    |`Authorization`|`Authorization` 的值是 `Bearer {token}`。 Token 值的前面必須加上單字 `Bearer` 和空格。|
    |`Ocp-Apim-Subscription-Key`|您的撰寫金鑰。|

    API 會傳回 JSON 物件的陣列，代表您的 LUIS 訂閱。 傳回的值包括訂用帳戶識別碼、資源群組和資源名稱，並以傳回 `AccountName` 。 在陣列中尋找您想要指派給 LUIS 應用程式的 LUIS 資源的專案。

1. 使用將 [LUIS Azure 帳戶指派給應用程式](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API，將權杖指派給 LUIS 資源。

    此 POST API 需要下列值：

    |類型|設定|值|
    |--|--|--|
    |標頭|`Authorization`|`Authorization` 的值是 `Bearer {token}`。 Token 值的前面必須加上單字 `Bearer` 和空格。|
    |標頭|`Ocp-Apim-Subscription-Key`|您的撰寫金鑰。|
    |標頭|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS 應用程式識別碼。
    |主體||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    當此 API 成功時，它會傳回 `201 - created status` 。

## <a name="unassign-a-resource"></a>取消指派資源

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後從 **我的應用程式** 清單中選取應用程式。
1. 移至 [ **管理**  >  **Azure 資源** ]。
1. 在 [ **預測資源** 或 **撰寫資源** ] 索引標籤上，選取資源的 [ **取消指派資源** ] 按鈕。

當您取消指派資源時，它不會從 Azure 中刪除。 它只會從 LUIS 取消連結。


## <a name="delete-an-account"></a>刪除帳戶

如需刪除帳戶時會刪除哪些資料的相關資訊，請參閱[資料儲存和移除](luis-concept-data-storage.md#accounts)。

## <a name="change-the-pricing-tier"></a>變更定價層

1.  在 [[Azure 入口網站](https://portal.azure.com)中，尋找並選取您的 LUIS 訂用帳戶：

    ![顯示 Azure 入口網站中 LUIS 訂用帳戶的螢幕擷取畫面。](./media/luis-usage-tiers/find.png)
1.  選取 **定價層** 以查看可用的定價層：

    ![顯示 [定價層] 功能表項目的螢幕擷取畫面。](./media/luis-usage-tiers/subscription.png)
1.  選取定價層，然後按一下 [ **選取** ] 以儲存您的變更：

    ![顯示如何選取和儲存定價層的螢幕擷取畫面。](./media/luis-usage-tiers/plans.png)

    當定價變更完成時，快顯視窗會驗證定價層更新：

    ![驗證定價更新之快顯視窗的螢幕擷取畫面。](./media/luis-usage-tiers/updated.png)
1. 請記得在 [發佈] 頁面上[指派此端點金鑰](#assign-a-resource-to-an-app)，然後將它使用於所有端點查詢。

## <a name="view-azure-resource-metrics"></a>查看 Azure 資源計量

### <a name="view-a-summary-of-azure-resource-usage"></a>查看 Azure 資源使用量的摘要
您可以在 Azure 入口網站中查看 LUIS 使用量資訊。 [ **總覽** ] 頁面會顯示摘要，包括最近的呼叫和錯誤。 如果您提出 LUIS 端點要求，最多允許五分鐘的時間，變更才會出現。

![顯示 [總覽] 頁面的螢幕擷取畫面。](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>自訂 Azure 資源使用量圖表
[ **計量** ] 頁面提供更詳細的資料查看：

![顯示 [計量] 頁面的螢幕擷取畫面。](./media/luis-usage-tiers/metrics-default.png)

您可以針對特定時間週期和計量類型設定計量圖表：

![顯示自訂圖表的螢幕擷取畫面。](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>交易總數閾值警示
如果您想要知道何時達到特定交易閾值（例如10000交易），您可以建立警示：

![顯示 [警示規則] 頁面的螢幕擷取畫面。](./media/luis-usage-tiers/alert-default.png)

為一段時間的 **呼叫總數** 計量新增計量警示。 新增所有應接收警示的人員電子郵件地址。 針對所有應接收警示的系統新增 webhook。 您也可以在警示觸發時執行邏輯應用程式。

## <a name="next-steps"></a>後續步驟

* 瞭解 [如何使用版本](luis-how-to-manage-versions.md) 來控制您的應用程式生命週期。
* 遷移至新的 [撰寫資源](luis-migration-authoring.md)。
