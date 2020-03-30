---
title: 保障存取和資料的安全性
description: 安全訪問 Azure 邏輯應用中的輸入、輸出、基於請求的觸發器、執行歷程記錄、管理工作和訪問其他資源
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: cc349e5851627ee830196982509f91a83198dfe0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349584"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Azure 邏輯應用中的安全訪問和資料

要控制 Azure 邏輯應用中的資料訪問和保護，可以在以下地區設定安全性：

* [訪問基於請求的觸發器](#secure-triggers)
* [訪問邏輯應用操作](#secure-operations)
* [訪問運行歷史輸入和輸出](#secure-run-history)
* [訪問參數輸入](#secure-action-parameters)
* [訪問從邏輯應用調用的服務和系統](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>訪問基於請求的觸發器

如果邏輯應用使用基於請求的觸發器（該觸發器接收撥入電話或請求（如[請求](../connectors/connectors-native-reqres.md)或[Webhook](../connectors/connectors-native-webhook.md)觸發器），則可以限制訪問，以便只有經過授權的用戶端才能調用邏輯應用。 邏輯應用程式收到的所有要求，都會使用安全通訊端層 (SSL) 通訊協定加密並保護。

以下是可説明您安全訪問此觸發器類型的選項：

* [生成共用訪問簽名](#sas)
* [限制入站 IP 位址](#restrict-inbound-ip-addresses)
* [添加 Azure 活動目錄 OAuth 或其他安全性](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>生成共用訪問簽名 （SAS）

邏輯應用中的每個請求終結點在終結點的 URL 中都有[共用訪問簽名 （SAS），](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas)該簽名的格式如下：

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

每個 URL 都`sp`包含`sv`，`sig`和 查詢參數，如下表所述：

| 查詢參數 | 描述 |
|-----------------|-------------|
| `sp` | 指定允許的 HTTP 方法使用的許可權。 |
| `sv` | 指定用於生成簽名的 SAS 版本。 |
| `sig` | 指定用於驗證對觸發器的訪問的簽名。 此簽名是通過使用 SHA256 演算法生成的所有 URL 路徑和屬性上具有機密訪問金鑰生成的。 從不公開或發佈，此金鑰將保持加密並與邏輯應用程式一起存儲。 您的邏輯應用程式只會針對包含使用祕密金鑰建立之有效簽章的觸發程序進行授權。 |
|||

有關使用 SAS 保護存取權限的詳細資訊，請參閱本主題中的以下部分：

* [重新產生存取金鑰](#access-keys)
* [建立即將到期的回呼 URL](#expiring-urls)
* [使用主要或次要金鑰建立 URL](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>重新產生存取金鑰

要隨時生成新的安全訪問金鑰，請使用 Azure REST API 或 Azure 門戶。 使用舊金鑰的所有以前生成的 URL 都無效，不再具有觸發邏輯應用的授權。 再生後檢索的 URL 使用新的訪問金鑰進行簽名。

1. 在[Azure 門戶](https://portal.azure.com)中，打開具有要重新生成的金鑰的邏輯應用。

1. 在邏輯應用程式功能表的 [設定]**** 下，選取 [存取金鑰]****。

1. 選擇要重新生成並完成該過程的鍵。

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>建立即將到期的回呼 URL

如果與其他參與方共用基於請求的觸發器的終結點 URL，則可以生成使用特定金鑰且具有到期日期的回檔 URL。 這樣，您可以無縫滾動鍵或限制訪問以基於特定時間跨度觸發邏輯應用。 要指定 URL 的到期日期，請使用[邏輯應用 REST API，](https://docs.microsoft.com/rest/api/logic/workflowtriggers)例如：

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在正文中，使用`NotAfter`JSON 日期字串包括屬性。 此屬性會傳回只在 `NotAfter` 日期與時間之前有效的回呼 URL。

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>使用主要或次要祕密金鑰建立 URL

為基於請求的觸發器生成或列出回檔 URL 時，可以指定用於對 URL 進行簽名的金鑰。 要生成由特定金鑰簽名的 URL，請使用[邏輯應用 REST API，](https://docs.microsoft.com/rest/api/logic/workflowtriggers)例如：

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在本文中，將屬性 `KeyType` 包含為 `Primary` 或 `Secondary`。 此屬性返回由指定的安全金鑰簽名的 URL。

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>限制入站 IP 位址

除了共用訪問簽名 （SAS），您可能希望特別限制可以調用邏輯應用的用戶端。 例如，如果使用 Azure API 管理管理請求終結點，則可以限制邏輯應用僅接受 API 管理實例的 IP 位址的請求。

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>在 Azure 門戶中限制入站 IP 範圍

1. 在[Azure 門戶](https://portal.azure.com)中，在邏輯應用設計器中打開邏輯應用。

1. 在邏輯應用程式功能表的 [設定]**** 底下，選取 [工作流程設定]****。

1. 在**存取控制配置** > **下允許入站 IP 位址**，選擇**特定的 IP 範圍**。

1. 在 [觸發程序的 IP 範圍]**** 底下，指定觸發程序可接受的 IP 位址範圍。

   有效的 IP 範圍使用這些格式：*x.x.x.x/x* 或 *x.x.x.x-x.x.x.x*

如果希望邏輯應用僅作為嵌套邏輯應用觸發，請從 **"允許入站 IP 位址"** 清單中選擇 **"僅其他邏輯應用**"。 此選項將空陣列寫入邏輯應用資源。 這樣，只有來自邏輯應用服務（父邏輯應用）的調用才能觸發嵌套邏輯應用。

> [!NOTE]
> 不論 IP 位址為何，您仍然可以使用 `/triggers/<trigger-name>/run` 透過 Azure REST API 或透過 API 管理來執行具有要求型觸發程序的邏輯應用程式。 但是，此方案仍然需要針對 Azure REST API 進行身份驗證。 所有事件都顯示在 Azure 稽核記錄中。 請確保相應地設置存取控制策略。

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>在 Azure 資源管理器範本中限制入站 IP 範圍

如果使用[資源管理器範本自動部署邏輯應用](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，則可以通過使用邏輯應用資源定義中的`accessControl``triggers`節來指定 IP 範圍，例如：

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>添加 Azure 活動目錄 OAuth 或其他安全性

要向邏輯應用添加更多授權協定，請考慮使用[Azure API 管理](../api-management/api-management-key-concepts.md)服務。 此服務可説明您將邏輯應用公開為 API，並為任何終結點提供豐富的監視、安全性、策略和文檔。 API 管理可以公開邏輯應用的公共終結點或專用終結點。 要授權訪問此終結點，可以使用 Azure[活動目錄 OAuth、](#azure-active-directory-oauth-authentication)[用戶端憑證](#client-certificate-authentication)或其他安全標準來授權訪問該終結點。 當 API 管理收到要求時，服務會傳送要求至您的邏輯應用程式，也會在過程中進行任何必要的轉換或限制。 要僅允許 API 管理觸發邏輯應用，可以使用邏輯應用的入站 IP 範圍設置。

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>訪問邏輯應用操作

只能允許特定使用者或組運行特定任務，例如管理、編輯和查看邏輯應用。 要控制其許可權，請使用[Azure 基於角色的存取控制 （RBAC），](../role-based-access-control/role-assignments-portal.md)以便可以為 Azure 訂閱中的成員分配自訂或內置角色：

* [邏輯應用參與者](../role-based-access-control/built-in-roles.md#logic-app-contributor)：允許您管理邏輯應用，但不能更改對它們的訪問。

* [邏輯應用運算子](../role-based-access-control/built-in-roles.md#logic-app-operator)：允許您讀取、啟用和禁用邏輯應用，但不能編輯或更新它們。

若要防止變更或刪除邏輯應用程式，您可以使用 [Azure 資源鎖定](../azure-resource-manager/management/lock-resources.md)。 此功能可防止其他人更改或刪除生產資源。

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>訪問執行歷程記錄資料

在邏輯應用運行期間，所有資料在傳輸過程中使用傳輸層安全 （TLS） 和[靜態](../security/fundamentals/encryption-atrest.md)[進行加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)。 邏輯應用完成運行時，您可以查看該運行的歷史記錄，包括與每個操作的狀態、持續時間、輸入和輸出一起運行的步驟。 此豐富的詳細資訊提供了有關邏輯應用如何運行以及從何處開始排除出現任何問題的見解。

當您查看邏輯應用的執行歷程記錄時，邏輯應用會驗證您的存取權限，然後提供指向每次運行的請求和回應的輸入和輸出的連結。 但是，對於處理任何密碼、機密、金鑰或其他敏感資訊的操作，您希望阻止其他人查看和訪問該資料。 例如，如果邏輯應用從[Azure 金鑰保存庫](../key-vault/key-vault-overview.md)獲取了用於驗證 HTTP 操作時使用的秘密，則希望從視圖中隱藏該機密。

要控制對邏輯應用執行歷程記錄中的輸入和輸出的訪問，可以使用以下選項：

* [按 IP 位址範圍限制訪問](#restrict-ip)。

  此選項可説明您根據來自特定 IP 位址範圍的請求保護執行歷程記錄的存取權限。

* [使用模糊處理從執行歷程記錄隱藏資料](#obfuscate)。

  在許多觸發器和操作中，可以從邏輯應用的執行歷程記錄中隱藏它們的輸入、輸出或兩者。

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>按 IP 位址範圍限制訪問

您可以限制對邏輯應用執行歷程記錄中的輸入和輸出的訪問，以便只有來自特定 IP 位址範圍的請求才能查看該資料。 例如，要阻止任何人訪問輸入和輸出，請指定 IP 位址範圍，如`0.0.0.0-0.0.0.0`。 只有具有管理員許可權的人才能刪除此限制，這提供了對邏輯應用資料的"即時"訪問的可能性。 可以使用 Azure 門戶或用於邏輯應用部署的 Azure 資源管理器範本指定要限制的 IP 範圍。

#### <a name="restrict-ip-ranges-in-azure-portal"></a>限制 Azure 門戶中的 IP 範圍

1. 在 Azure 入口網站的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定]**** 底下，選取 [工作流程設定]****。

1. 在**存取控制配置** > **下允許入站 IP 位址**，選擇**特定的 IP 範圍**。

1. 在 [內容的 IP 範圍]**** 底下，指定可存取輸入和輸出內容的 IP 位址範圍。 

   有效的 IP 範圍使用這些格式：*x.x.x.x/x* 或 *x.x.x.x-x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>在 Azure 資源管理器範本中限制 IP 範圍

如果使用[資源管理器範本自動部署邏輯應用](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，則可以通過使用邏輯應用資源定義中的`accessControl``contents`節來指定 IP 範圍，例如：

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="obfuscate"></a>

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>使用模糊處理從執行歷程記錄隱藏資料

許多觸發器和操作都有從邏輯應用的執行歷程記錄中隱藏輸入、輸出或兩者的設置。 當您使用這些設置來説明保護此資料時，需要考慮以下[注意事項](#obfuscation-considerations)。

#### <a name="hide-inputs-and-outputs-in-the-designer"></a>隱藏設計器中的輸入和輸出

1. 在[Azure 門戶](https://portal.azure.com)中，在邏輯應用設計器中打開邏輯應用。

   ![邏輯應用設計器中的打開邏輯應用](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. 在要隱藏敏感性資料的觸發器或操作上，選擇橢圓 **（...）** 按鈕，然後選擇 **"設置**"。

   ![打開觸發器或操作設置](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. 打開**安全輸入**、**安全輸出**或兩者。 完成之後，選取 [完成]****。

   ![打開"安全輸入"或"安全輸出"](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   操作或觸發器現在顯示標題列中的鎖定圖示。

   ![操作或觸發器標題列顯示鎖定圖示](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   表示以前操作的安全輸出的權杖也會顯示鎖圖示。 例如，當您從動態內容清單中選擇要在操作中使用的此類輸出時，該權杖將顯示一個鎖圖示。

   ![為安全輸出選擇權杖](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. 邏輯應用運行後，您可以查看該運行的歷史記錄。

   1. 在邏輯應用的 **"概述"** 窗格中，選擇要查看的運行。

   1. 在 **"邏輯"應用運行**窗格中，展開要查看的操作。

      如果選擇遮蓋輸入和輸出，則這些值現在顯示為隱藏值。

      ![執行歷程記錄中的隱藏輸入和輸出](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="hide-inputs-and-outputs-in-code-view"></a>在代碼視圖中隱藏輸入和輸出

在基礎觸發器或操作定義中`runtimeConfiguration.secureData.properties`，使用以下任一或兩個值添加或更新陣列：

* `"inputs"`：保護執行歷程記錄中的輸入。
* `"outputs"`：保護執行歷程記錄中的輸出。

當您使用這些設置來説明保護此資料時，需要考慮以下[注意事項](#obfuscation-considerations)。

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>隱藏輸入和輸出時的注意事項

* 當您遮蓋觸發器或操作上的輸入或輸出時，邏輯應用不會將安全資料發送到 Azure 日誌分析。 此外，您無法將[跟蹤的屬性](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)添加到該觸發器或用於監視的操作。

* [用於處理工作流歷史記錄的邏輯應用 API](https://docs.microsoft.com/rest/api/logic/)不會返回安全輸出。

* 要隱藏來自模糊輸入或顯式遮蓋輸出的操作的輸出，請手動打開該操作中**的安全輸出**。

* 請確保在下游操作中打開**安全輸入**或**安全輸出**，而您希望執行歷程記錄會掩蓋該資料。

  **安全輸出設置**

  當您手動打開觸發器或操作中**的安全輸出**時，邏輯應用會在執行歷程記錄中保護這些輸出。 如果下游操作顯式使用這些安全輸出作為輸入，邏輯應用將此操作的輸入隱藏在執行歷程記錄中，但不會*啟用*該操作**的安全輸入**設置。

  ![將輸出作為輸入和下游對大多數操作的影響進行保護](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  "撰寫"、"分析 JSON"和"回應"操作僅具有 **"安全輸入"** 設置。 啟用時，該設置還會隱藏這些操作的輸出。 如果這些操作顯式使用上游安全輸出作為輸入，邏輯應用將隱藏這些操作的輸入和輸出，但不會*啟用*這些操作的安全**輸入**設置。 如果下游操作顯式使用來自"合成"、"分析 JSON"或"回應"操作的隱藏輸出作為輸入，則邏輯應用*不會隱藏此下游操作的輸入或輸出*。

  ![將輸出作為輸入進行保護，對具體操作具有下游影響](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **安全輸入設置**

  當您手動打開觸發器或操作中**的安全輸入**時，邏輯應用會在執行歷程記錄中保護這些輸入。 如果下游操作顯式使用該觸發器或操作中的可見輸出作為輸入，邏輯應用會在執行歷程記錄中隱藏此下游操作的輸入，但不會在此操作中*啟用***安全輸入**，也不會隱藏此操作的輸出。

  ![安全輸入和對大多數操作的下游影響](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  如果"組合"、"分析 JSON"和"回應"操作顯式使用具有安全輸入的觸發器或操作中的可見輸出，則邏輯應用將隱藏這些操作的輸入和輸出，但不會*啟用*這些操作的安全**輸入**設置。 如果下游操作顯式使用來自"合成"、"分析 JSON"或"回應"操作的隱藏輸出作為輸入，則邏輯應用*不會隱藏此下游操作的輸入或輸出*。

  ![安全投入和對具體行動的下游影響](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>訪問參數輸入

如果跨不同的環境進行部署，請考慮參數化工作流定義中因這些環境而異的值。 這樣，可以使用[Azure 資源管理器範本](../azure-resource-manager/templates/overview.md)部署邏輯應用，通過定義安全參數來保護敏感性資料，並使用[參數檔](../azure-resource-manager/templates/parameter-files.md)將這些資料作為單獨的輸入傳遞到[範本的參數](../azure-resource-manager/templates/template-parameters.md)中，從而避免硬編碼資料。

例如，如果使用[Azure 活動目錄 OAuth](#azure-active-directory-oauth-authentication)對 HTTP 操作進行身份驗證，則可以定義和遮蓋接受用於身份驗證的用戶端 ID 和用戶端金鑰的參數。 要在邏輯應用中定義這些參數，請使用邏輯應用的`parameters`工作流定義和資源管理器範本中的部分進行部署。 要隱藏編輯邏輯應用或查看執行歷程記錄時不希望顯示的參數值，請使用`securestring`或`secureobject`類型定義參數，並根據需要使用編碼。 具有此類型的參數不會隨資源定義一起返回，在部署後查看資源時無法訪問。 要在運行時訪問這些參數值，`@parameters('<parameter-name>')`請使用工作流定義中的運算式。 此運算式僅在運行時計算，並由[工作流定義語言](../logic-apps/logic-apps-workflow-definition-language.md)描述。

> [!NOTE]
> 如果在請求標頭或正文中使用參數，則當您查看邏輯應用的執行歷程記錄和傳出 HTTP 要求時，該參數可能可見。 請確保也相應地設置內容訪問策略。 您還可以使用[模糊隱藏](#obfuscate)來隱藏執行歷程記錄中的輸入和輸出。 授權標頭絕對不會透過輸入或輸出來顯示。 如此一來如果該處有使用密碼，就無法擷取密碼。

有關詳細資訊，請參閱本主題中的以下部分：

* [工作流定義中的安全參數](#secure-parameters-workflow)
* [使用模糊處理從執行歷程記錄隱藏資料](#obfuscate)

如果使用[資源管理器範本自動部署邏輯應用](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，則可以定義安全[範本參數](../azure-resource-manager/templates/template-parameters.md)，這些參數在部署時使用`securestring`和`secureobject`類型進行評估。 要定義範本參數，請使用範本的頂層`parameters`部分，該部分與工作流定義的部分是分開的`parameters`，並且不同。 要提供範本參數的值，請使用單獨的[參數檔](../azure-resource-manager/templates/parameter-files.md)。

例如，如果使用機密，則可以定義和使用安全範本參數，這些參數在部署時從[Azure 金鑰保存庫](../key-vault/key-vault-overview.md)中檢索這些機密。 然後，您可以在參數檔中引用金鑰保存庫和機密。 如需詳細資訊，請參閱下列主題：

* [使用 Azure 金鑰保存庫在部署時傳遞敏感值](../azure-resource-manager/templates/key-vault-parameter.md)
* 在本主題的後面部分[，Azure 資源管理器範本中的安全參數](#secure-parameters-deployment-template)

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>工作流定義中的安全參數

為了保護邏輯應用工作流定義中的敏感資訊，請使用安全參數，以便在保存邏輯應用後此資訊不可見。 例如，假設您有一個 HTTP 操作需要基本驗證，該身份驗證使用使用者名和密碼。 在工作流定義中`parameters`，該部分使用`basicAuthPasswordParam``basicAuthUsernameParam``securestring`類型定義 和 參數。 然後，操作定義在`authentication`節中引用這些參數。

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Azure 資源管理器範本中的安全參數

邏輯應用的[資源管理器範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)有多個`parameters`部分。 為了保護密碼、金鑰、機密和其他敏感資訊，請使用`securestring`或`secureobject`類型在範本級別和工作流定義級別定義安全參數。 然後，可以將這些值存儲在[Azure 金鑰保存庫中](../key-vault/key-vault-overview.md)，並使用[參數檔](../azure-resource-manager/templates/parameter-files.md)引用金鑰保存庫和機密。 然後，範本在部署時檢索該資訊。 有關詳細資訊，請參閱使用[Azure 金鑰保存庫在部署時傳遞敏感值](../azure-resource-manager/templates/key-vault-parameter.md)。

以下是有關以下`parameters`部分的詳細資訊：

* 在範本的頂層，部分`parameters`定義範本在*部署*時使用的值的參數。 例如，這些值可以包括特定部署環境的連接字串。 然後，您可以將這些值存儲在單獨的[參數檔中](../azure-resource-manager/templates/parameter-files.md)，從而更輕鬆地更改這些值。

* 在邏輯應用的資源定義中，但在工作流定義之外，`parameters`部分指定工作流定義參數的值。 在本節中，可以使用引用範本參數的範本運算式來分配這些值。 這些運算式在部署時計算。

* 在工作流定義中，一`parameters`節定義邏輯應用在運行時使用的參數。 然後，可以使用工作流定義運算式在邏輯應用的工作流中引用這些參數，這些運算式在運行時進行評估。

此示例範本具有多個使用`securestring`類型的安全參數定義：

| 參數名稱 | 描述 |
|----------------|-------------|
| `TemplatePasswordParam` | 接受密碼的範本參數，然後傳遞給工作流定義的`basicAuthPasswordParam`參數 |
| `TemplateUsernameParam` | 接受使用者名的範本參數，然後傳遞給工作流定義的`basicAuthUserNameParam`參數 |
| `basicAuthPasswordParam` | 接受 HTTP 操作中基本驗證密碼的工作流定義參數 |
| `basicAuthUserNameParam` | 接受 HTTP 操作中基本驗證的使用者名的工作流定義參數 |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>訪問從邏輯應用調用的服務和系統

以下是一些可以説明保護從邏輯應用接收呼叫或請求的終結點的方法：

* 向出站請求添加身份驗證。

  當您使用基於 HTTP 的觸發器或進行出站調用的操作（如 HTTP、HTTP + Swagger 或 Webhook）時，可以將身份驗證添加到邏輯應用發送的請求。 例如，可以使用以下身份驗證類型：

  * [基本驗證](#basic-authentication)

  * [用戶端憑證身份驗證](#client-certificate-authentication)

  * [活動目錄身份驗證](#azure-active-directory-oauth-authentication)

  * [受控識別驗證](#managed-identity-authentication)
  
  有關詳細資訊，請參閱在本主題的後面部分[將身份驗證添加到出站調用](#add-authentication-outbound)。

* 限制從邏輯應用 IP 位址進行訪問。

  邏輯應用對終結點的所有調用都源自基於邏輯應用區域的特定指定 IP 位址。 您可以新增篩選，以只接受來自那些 IP 位址的要求。 要獲取這些 IP 位址，請參閱[Azure 邏輯應用的限制和配置](logic-apps-limits-and-config.md#configuration)。

* 提高與本地系統連接的安全性。

  Azure 邏輯應用提供與這些服務的集成，以説明提供更安全、更可靠的本地通信。

  * 內部部署資料閘道

    Azure 邏輯應用中的許多託管連接器都有助於與本地系統（如檔案系統、SQL、SharePoint 和 DB2）的安全連線。 閘道會在加密通道上經過 Azure 服務匯流排傳送來自內部部署來源的資料。 所有流量都作為安全出站流量源自閘道代理。 了解[內部部署資料閘道的運作方式](logic-apps-gateway-install.md#gateway-cloud-service)。

  * 通過 Azure API 管理進行連接

    [Azure API 管理](../api-management/api-management-key-concepts.md)提供內部部署連線選項，例如站對站虛擬私人網路和 ExpressRoute 整合，以對內部部署系統提供安全的 Proxy 和通訊。 從邏輯應用在邏輯應用設計器中的工作流中，您可以選擇由 API 管理公開的 API，該 API 提供對本地系統的快速訪問。

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>向出站呼叫添加身份驗證

HTTP 和 HTTPS 終結點支援各種身份驗證。 根據用於進行出站調用或訪問這些終結點的請求的觸發器或操作，您可以從不同的身份驗證類型範圍中進行選擇。 為了確保保護邏輯應用處理的任何敏感資訊，請使用安全參數並根據需要對資料進行編碼。 有關使用和保護參數的詳細資訊，請參閱[訪問參數輸入](#secure-action-parameters)。

> [!NOTE]
> 在邏輯應用設計器中，**身份驗證**屬性可能隱藏在某些觸發器和操作上，您可以在其中指定身份驗證類型。 要使屬性在這些情況下顯示在觸發器或操作上，請打開 **"添加新參數**清單"，然後選擇 **"身份驗證**"。 有關詳細資訊，請參閱[使用託管標識進行身份驗證訪問](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)。

| 驗證類型 | 支援者 |
|---------------------|--------------|
| [基本](#basic-authentication) | Azure API 管理、Azure 應用服務、HTTP、HTTP + 搖擺、HTTP Webhook |
| [用戶端憑證](#client-certificate-authentication) | Azure API 管理、Azure 應用服務、HTTP、HTTP + 搖擺、HTTP Webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API 管理、Azure 應用服務、Azure 功能、HTTP、HTTP + 搖擺、HTTP Webhook |
| [原始](#raw-authentication) | Azure API 管理、Azure 應用服務、Azure 功能、HTTP、HTTP + 搖擺、HTTP Webhook |
| [受控身分識別](#managed-identity-authentication) | Azure API 管理、Azure 應用服務、Azure 功能、HTTP、HTTP + 搖擺、HTTP Webhook |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>基本驗證

如果["基本"](../active-directory-b2c/secure-rest-api.md)選項可用，請指定以下屬性值：

| 屬性（設計師） | Property (JSON) | 必要 | 值 | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| [驗證]**** | `type` | 是 | 基本 | 要使用的身份驗證類型 |
| **使用者** | `username` | 是 | <*使用者名*>| 用來驗證存取目標服務端點的使用者名稱 |
| **密碼** | `password` | 是 | <*密碼*> | 用來驗證存取目標服務端點的密碼 |
||||||

使用[安全參數](#secure-action-parameters)來處理和保護敏感資訊時（例如，在[Azure 資源管理器範本中用於自動部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)時），可以使用運算式在運行時訪問這些參數值。 此示例 HTTP 操作定義指定身份驗證`type`為`Basic`，並使用[參數（）函數](../logic-apps/workflow-definition-language-functions-reference.md#parameters)獲取參數值：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>用戶端憑證驗證

如果[用戶端憑證](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)選項可用，請指定以下屬性值：

| 屬性（設計師） | Property (JSON) | 必要 | 值 | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| [驗證]**** | `type` | 是 | **用戶端憑證** <br>或 <br>`ClientCertificate` | 用於安全通訊端層 (SSL) 用戶端憑證的驗證類型。 雖支援自我簽署憑證，但不支援 SSL 自我簽署憑證。 |
| **普夫克斯** | `pfx` | 是 | <*編碼-pfx-檔內容*> | Base64 編碼的個人資訊交換 (PFX) 檔案內容 <p><p>要將 PFX 檔轉換為基本編碼格式，可以使用 PowerShell 以下步驟： <p>1. 將證書內容保存到變數中： <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. 使用`ToBase64String()`函數轉換證書內容，並將該內容保存到文字檔： <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **密碼** | `password`| 否 | <*密碼換pfx檔*> | 用於存取 PFX 檔案的密碼 |
|||||

使用[安全參數](#secure-action-parameters)來處理和保護敏感資訊時（例如，在[Azure 資源管理器範本中用於自動部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)時），可以使用運算式在運行時訪問這些參數值。 此示例 HTTP 操作定義指定身份驗證`type`為`ClientCertificate`，並使用[參數（）函數](../logic-apps/workflow-definition-language-functions-reference.md#parameters)獲取參數值：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

有關使用用戶端憑證身份驗證保護服務的詳細資訊，請參閱以下主題：

* [通過在 Azure API 管理中使用用戶端憑證身份驗證來提高 API 的安全性](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [通過在 Azure API 管理中使用用戶端憑證身份驗證，提高後端服務的安全性](../api-management/api-management-howto-mutual-certificates.md)
* [使用用戶端憑證提高 RESTfuL 服務的安全性](../active-directory-b2c/secure-rest-api.md)
* [適用於應用程式驗證的憑證認證](../active-directory/develop/active-directory-certificate-credentials.md)
* [在 Azure App Service 中的應用程式程式碼中使用 SSL 憑證](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Azure 活動目錄身份驗證

如果[活動目錄 OAuth](../active-directory/develop/about-microsoft-identity-platform.md)選項可用，請指定以下屬性值：

| 屬性（設計師） | Property (JSON) | 必要 | 值 | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| [驗證]**** | `type` | 是 | **Active Directory OAuth** <br>或 <br>`ActiveDirectoryOAuth` | 要使用的驗證類型。 邏輯應用目前遵循[OAuth 2.0協定](../active-directory/develop/v2-overview.md)。 |
| **權威** | `authority` | 否 | <*URL for 授權權杖頒發者*> | 提供身份驗證權杖的頒發機構的 URL。 根據預設，此值為 `https://login.windows.net`。 |
| **租戶** | `tenant` | 是 | <*租戶 ID*> | Azure AD 租用戶的租用戶識別碼 |
| **觀眾** | `audience` | 是 | <*資源到授權*> | 您希望用於授權的資源，例如，`https://management.core.windows.net/` |
| **用戶端 ID** | `clientId` | 是 | <*用戶端 ID*> | 要求授權的應用程式用戶端識別碼 |
| **憑據類型** | `credentialType` | 是 | 憑證 <br>或 <br>祕密 | 用戶端用於請求授權的憑據類型。 此屬性和值不顯示在邏輯應用的基礎定義中，但確定為所選憑據類型顯示的屬性。 |
| **秘密** | `secret` | 是，但僅適用于"機密"憑據類型 | <*用戶端-機密*> | 要求授權用的用戶端密碼 |
| **普夫克斯** | `pfx` | 是，但僅適用于"證書"憑據類型 | <*編碼-pfx-檔內容*> | Base64 編碼的個人資訊交換 (PFX) 檔案內容 |
| **密碼** | `password` | 是，但僅適用于"證書"憑據類型 | <*密碼換pfx檔*> | 用於存取 PFX 檔案的密碼 |
|||||

使用[安全參數](#secure-action-parameters)來處理和保護敏感資訊時（例如，在[Azure 資源管理器範本中用於自動部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)時），可以使用運算式在運行時訪問這些參數值。 此示例 HTTP 操作定義將身份驗證`type`指定`ActiveDirectoryOAuth`為 ，憑據類型`Secret`為 ，並使用[參數（）函數](../logic-apps/workflow-definition-language-functions-reference.md#parameters)獲取參數值：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

### <a name="raw-authentication"></a>原始身份驗證

如果**Raw**選項可用，則當必須使用不遵循[OAuth 2.0 協定的](https://oauth.net/2/)[身份驗證方案](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml)時，可以使用此身份驗證類型。 使用此類型，您可以手動創建與傳出請求一起發送的授權標頭值，並在觸發器或操作中指定該標頭值。

例如，下面是遵循[OAuth 1.0 協定](https://tools.ietf.org/html/rfc5849)的 HTTPS 請求的示例標頭：

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

在支援原始身份驗證的觸發器或操作中，指定以下屬性值：

| 屬性（設計師） | Property (JSON) | 必要 | 值 | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| [驗證]**** | `type` | 是 | Raw | 要使用的身份驗證類型 |
| **價值** | `value` | 是 | <*授權-標頭值*> | 用於身份驗證的授權標頭值 |
||||||

使用[安全參數](#secure-action-parameters)來處理和保護敏感資訊時（例如，在[Azure 資源管理器範本中用於自動部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)時），可以使用運算式在運行時訪問這些參數值。 此示例 HTTP 操作定義將身份驗證`type`指定`Raw`為 ，並使用[參數（）函數](../logic-apps/workflow-definition-language-functions-reference.md#parameters)獲取參數值：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

### <a name="managed-identity-authentication"></a>受控識別驗證

如果[託管標識](../active-directory/managed-identities-azure-resources/overview.md)選項可用，則邏輯應用可以使用系統分配的標識或手動創建的使用者分配的*標識*來驗證對其他 Azure 活動目錄 （Azure AD） 租戶中的資源的存取權限，而無需登錄。 Azure 會為您管理此身分識別，並協助保護您的認證，因為您不需要提供或輪替使用祕密。 詳細瞭解支援[Azure AD 身份驗證的託管標識的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

1. 在邏輯應用可以使用託管標識之前，請按照[Azure 邏輯應用中使用託管標識對 Azure 資源進行身份驗證訪問](../logic-apps/create-managed-service-identity.md)的步驟。 這些步驟在邏輯應用中啟用託管標識，並設置該標識對目標 Azure 資源的訪問。

1. 在 Azure 函數可以使用託管標識之前，首先[為 Azure 函數啟用身份驗證](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)。

1. 在要使用託管標識的觸發器或操作中，指定以下屬性值：

   | 屬性（設計師） | Property (JSON) | 必要 | 值 | 描述 |
   |---------------------|-----------------|----------|-------|-------------|
   | [驗證]**** | `type` | 是 | **託管標識** <br>或 <br>`ManagedServiceIdentity` | 要使用的身份驗證類型 |
   | **託管標識** | `identity` | 是 | * **系統分配的託管標識** <br>或 <br>`SystemAssigned` <p><p>• <*使用者分配的標識名稱*> | 要使用的託管標識 |
   | **觀眾** | `audience` | 是 | <*目標資源識別碼*> | 要訪問的目標資源的資源識別碼。 <p>例如，`https://storage.azure.com/`使身份驗證的訪問權杖對所有存儲帳戶都有效。 但是，您還可以指定根服務 URL，例如`https://fabrikamstorageaccount.blob.core.windows.net`為特定存儲帳戶指定。 <p>**注意**： **"訪問群體"** 屬性可能隱藏在某些觸發器或操作中。 要使此屬性可見，請在觸發器或操作中打開 **"添加新參數**清單"，然後選擇 **"訪問群體**"。 <p><p>**重要提示**：確保此目標資源識別碼 與 Azure AD 期望的值*完全符合*，包括任何必需的尾隨斜杠。 因此，`https://storage.azure.com/`所有 Azure Blob 存儲帳戶的資源識別碼 都需要尾隨斜杠。 但是，特定存儲帳戶的資源識別碼 不需要尾隨斜杠。 要查找這些資源標識，請參閱[支援 Azure AD 的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。 |
   |||||

   使用[安全參數](#secure-action-parameters)來處理和保護敏感資訊時（例如，在[Azure 資源管理器範本中用於自動部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)時），可以使用運算式在運行時訪問這些參數值。 此示例 HTTP 操作定義指定身份驗證`type`為`ManagedServiceIdentity`，並使用[參數（）函數](../logic-apps/workflow-definition-language-functions-reference.md#parameters)獲取參數值：

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

## <a name="next-steps"></a>後續步驟

* [自動部署 Azure 邏輯應用](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)  
* [監視邏輯應用程式](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [診斷邏輯應用程式失敗和問題](../logic-apps/logic-apps-diagnosing-failures.md)  
* [將邏輯應用程式部署自動化](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
