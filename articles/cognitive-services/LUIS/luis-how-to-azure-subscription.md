---
title: 如何使用撰寫和執行時間金鑰-LUIS
titleSuffix: Azure Cognitive Services
description: 當您第一次使用 Language Understanding （LUIS）時，您不需要建立撰寫金鑰。 當您想要發佈應用程式時，請使用您的執行時間端點，您必須建立執行時間金鑰，並將其指派給應用程式。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: a5140da32eb6fce03131a42bfa90e71e64552431
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221325"
---
# <a name="using-authoring-and-runtime-resource-keys"></a>使用撰寫和執行時間資源金鑰

撰寫和執行時間資源可為您的 LUIS 應用程式和預測端點提供驗證。

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

當您登入 LUIS 入口網站時，您可以選擇繼續進行：

* 免費[試用金鑰](#trial-key)-提供撰寫和一些預測端點查詢。
* Azure [LUIS 撰寫](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)資源。 


<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>登入 LUIS 入口網站並開始撰寫

1. 登入[LUIS 入口網站](https://www.luis.ai)並同意使用條款。
1. 選擇您想要使用的 LUIS 撰寫金鑰類型，以開始您的 LUIS 應用程式：免費試用金鑰或新的 Azure LUIS 撰寫金鑰。 

    ![選擇 Language Understanding 撰寫資源的類型](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. 當您完成資源選取程式時，請[建立新的應用程式](luis-how-to-start-new-app.md#create-new-app-in-luis)。 

## <a name="trial-key"></a>試用金鑰

系統會為您提供試用版（starter）金鑰。 它會用來做為您的驗證金鑰，以查詢預測端點執行時間，每個月最多1000個查詢。 

它會顯示在 LUIS 入口網站中的 [**使用者設定**] 頁面和 [**管理 > 的 Azure 資源**] 頁面上。 

當您準備好要發行預測端點時，請建立並指派撰寫和預測執行時間金鑰，以取代入門金鑰功能。 

## <a name="create-resources-in-the-azure-portal"></a>在 Azure 入口網站中建立資源

1. 使用[此連結](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)在建立資源時，開啟的 Azure 入口網站。
1. 選取 [**兩者**] 以建立撰寫和預測端點執行時間索引鍵。 
1. 輸入建立資源所需的資訊，然後選取 [**建立**] 以完成程式。

    ![建立語言理解資源](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

    |名字|目的|
    |--|--|
    |資源名稱| 您選擇的自訂名稱，用來作為撰寫和預測端點查詢 URL 的一部分。|
    |訂閱名稱| 將收取資源費用的訂用帳戶。|
    |資源群組| 您選擇或建立的自訂資源組名。 資源群組可讓您將 Azure 資源分組，以在相同區域中進行存取和管理。|
    |製作位置|與您的模型相關聯的區域。|
    |製作定價層|定價層會決定每秒和每月的交易上限。|
    |執行階段位置|與已發行之預測端點執行時間相關聯的區域。|
    |執行階段定價層|定價層會決定每秒和每月的交易上限。|

    建立這兩個資源後，請在 LUIS 入口網站中指派資源。

## <a name="create-resources-in-azure-cli"></a>在 Azure CLI 中建立資源

使用[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)來個別建立每個資源。 

資源 `kind`：

* 撰寫： `LUIS.Authoring`
* 預測： `LUIS` 

1. 登入 Azure CLI：

    ```azurecli
    az login
    ```

    這會開啟瀏覽器，讓您選取正確的帳戶並提供驗證。

1. 在 `westus` 區功能變數名稱為 `my-resource-group` 的_現有_資源群組中，建立名為 `my-luis-authoring-resource` 的**LUIS 撰寫資源**（類型 `LUIS.Authoring`）。 

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. 在 `westus` 區功能變數名稱為 `my-resource-group` 的_現有_資源群組中，建立名為 `my-luis-prediction-resource` 的**LUIS 預測端點資源**（類型 `LUIS`）。 如果您想要比免費層更高的輸送量，請將 `F0` 變更為 `S0`。 深入瞭解[定價層和輸送量](luis-boundaries.md#key-limits)。

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note] 
    > LUIS 入口網站**不**會使用此金鑰，除非是在 LUIS 入口網站中的**管理 > Azure 資源**上指派。

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>在 LUIS 入口網站中為所有應用程式指派撰寫資源

您可以針對單一應用程式或 LUIS 中的所有應用程式，指派撰寫資源。 下列程式會將所有應用程式指派給單一撰寫資源。

1. 登入 [LUIS 入口網站](https://www.luis.ai)。
1. 在上方導覽列中，選取最右側的 [使用者帳戶]，然後選取 [**設定**]。
1. 在 [**使用者設定**] 頁面上，選取 [**新增撰寫資源**]，然後選取現有的撰寫資源。 選取 [儲存]。 

## <a name="assign-a-resource-to-an-app"></a>將資源指派給應用程式

您可以使用下列程式，將單一資源、撰寫或預測端點執行時間指派給應用程式。

1. 登入[LUIS 入口網站](https://www.luis.ai)，然後從 [**我的應用程式**] 清單中選取應用程式。
1. 流覽至 [**管理->] [Azure 資源**] 頁面。

    ![在 LUIS 入口網站中選取 [管理-> Azure 資源]，以將資源指派給應用程式。](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. 選取 [預測] 或 [撰寫資源] 索引標籤，然後選取 [**新增預測資源**] 或 [**新增撰寫資源**] 按鈕。 
1. 選取表單中的欄位以尋找正確的資源，然後選取 [**儲存**]。  

### <a name="assign-runtime-resource-without-using-luis-portal"></a>在不使用 LUIS 入口網站的情況下指派執行時間資源

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

    |Type|設定|值|
    |--|--|--|
    |頁首|`Authorization`|`Authorization` 的值為 `Bearer {token}`。 請注意，權杖值的開頭必須加上 `Bearer` 一字和空格。|
    |頁首|`Ocp-Apim-Subscription-Key`|您的撰寫金鑰。|
    |頁首|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS 應用程式識別碼。 
    |body||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    此 API 成功執行時，會傳回「201 - 已建立」狀態。 

## <a name="unassign-resource"></a>取消指派資源

1. 登入[LUIS 入口網站](https://www.luis.ai)，然後從 [**我的應用程式**] 清單中選取應用程式。
1. 流覽至 [**管理->] [Azure 資源**] 頁面。
1. 選取 [預測] 或 [撰寫資源] 索引標籤，然後選取資源的 [**取消指派資源**] 按鈕。 

當您取消指派資源時，不會將它從 Azure 中刪除。 只會從 LUIS 取消連結。 

## <a name="reset-authoring-key"></a>重設撰寫金鑰

**針對[撰寫資源遷移](luis-migration-authoring.md)的應用程式**：如果您的撰寫金鑰遭到入侵，請在該撰寫資源的 [**金鑰**] 頁面上，重設 Azure 入口網站中的金鑰。 

**針對尚未遷移的應用程式**：金鑰會在 LUIS 入口網站中的所有應用程式上重設。 如果您透過撰寫 Api 來撰寫應用程式，則需要將 Apim-訂用帳戶金鑰的值變更為新的金鑰。

## <a name="regenerate-azure-key"></a>重新產生 Azure 金鑰

從 [**金鑰**] 頁面上的 Azure 入口網站重新產生 Azure 金鑰。

## <a name="delete-account"></a>刪除帳戶

如需刪除帳戶時會刪除哪些資料的相關資訊，請參閱[資料儲存和移除](luis-concept-data-storage.md#accounts)。

## <a name="change-pricing-tier"></a>變更定價層

1.  在 [Azure](https://portal.azure.com) 中，尋找您的 LUIS 訂用帳戶。 選取 LUIS 訂用帳戶。
    ![尋找 LUIS 訂用帳戶](./media/luis-usage-tiers/find.png)
1.  選取 [定價層] 以查看可用的定價層。 
    ![檢視定價層](./media/luis-usage-tiers/subscription.png)
1.  選取定價層，然後選取 [選取] 以儲存變更。 
    ![變更 LUIS 付款層](./media/luis-usage-tiers/plans.png)
1.  定價變更完成時，快顯視窗會確認新的定價層。 
    ![驗證 LUIS 付款層](./media/luis-usage-tiers/updated.png)
1. 請記得在 [發佈] 頁面上[指派此端點金鑰](#assign-a-resource-to-an-app)，然後將它使用於所有端點查詢。 

## <a name="viewing-azure-resource-metrics"></a>查看 Azure 資源計量

### <a name="viewing-azure-resource-summary-usage"></a>查看 Azure 資源摘要使用量
您可以在 Azure 中檢視 LUIS 使用量資訊。 [概觀] 頁面會顯示近期摘要資訊，包括呼叫和錯誤。 如果您提出 LUIS 端點要求，然後立即觀看 [概觀] 頁面，允許使用量最多在五分鐘內出現。

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
* 瞭解這些概念，包括該資源的[撰寫資源](luis-concept-keys.md#authoring-key)和[參與者](luis-concept-keys.md#contributions-from-other-authors)。
* 瞭解[如何建立](luis-how-to-azure-subscription.md)撰寫和執行時間資源
* 遷移至新的[撰寫資源](luis-migration-authoring.md) 
