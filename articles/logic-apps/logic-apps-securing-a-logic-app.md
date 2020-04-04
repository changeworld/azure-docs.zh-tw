---
title: 保障存取和資料的安全性
description: 安全存取 Azure 邏輯應用中輸入、輸出、基於請求的觸發器、執行歷史記錄、管理任務和存取其他資源
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 4fc4960eb3af8a3d3c9902c9b24505bb5610b709
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657167"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Azure 邏輯應用中的安全存取和資料

要控制 Azure 邏輯應用中的資料存取和保護,可以在以下區域設定安全性:

* [存取基於要求的觸發器](#secure-triggers)
* [存取邏輯應用程式操作](#secure-operations)
* [存取執行歷史輸入及輸出](#secure-run-history)
* [存取參數輸入](#secure-action-parameters)
* [存取從邏輯應用呼叫的服務和系統](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>存取基於要求的觸發器

如果邏輯應用使用基於請求的觸發器(該觸發器接收傳入呼叫或請求(如[請求](../connectors/connectors-native-reqres.md)或[Webhook](../connectors/connectors-native-webhook.md)觸發器),則可以限制訪問,以便只有經過授權的用戶端才能調用邏輯應用。 邏輯應用收到的所有請求都使用傳輸層安全 (TLS)進行加密和保護,以前稱為安全套接字層 (SSL) 協定。

以下是可説明您安全存取此觸發器類型的選項:

* [產生分享存取簽名](#sas)
* [限制入站 IP 位址](#restrict-inbound-ip-addresses)
* [新增 Azure 活動目錄 OAuth 或其他安全性](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>產生分享存取簽名 (SAS)

邏輯應用程式中的每個要求終結點在終結點的網址 中都有[共享存取簽名 (SAS),](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas)簽章的格式如下:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

每個網址`sp``sv`都`sig`包含, 與查詢參數,如下表所述:

| 查詢參數 | 描述 |
|-----------------|-------------|
| `sp` | 指定允許的 HTTP 方法使用的許可權。 |
| `sv` | 指定用於生成簽名的 SAS 版本。 |
| `sig` | 指定用於驗證對觸發器的訪問的簽名。 此簽名是通過使用 SHA256 演演演算法生成的所有 URL 路徑和屬性上具有機密存取密鑰生成的。 從不公開或發佈,此密鑰將保持加密並與邏輯應用程式一起存儲。 您的邏輯應用程式只會針對包含使用祕密金鑰建立之有效簽章的觸發程序進行授權。 |
|||

有關使用 SAS 保護存取權限的詳細資訊,請參閱本主題中的以下部分:

* [重新產生存取金鑰](#access-keys)
* [建立即將到期的回呼 URL](#expiring-urls)
* [使用主要或次要金鑰建立 URL](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>重新產生存取金鑰

要隨時生成新的安全訪問密鑰,請使用 Azure REST API 或 Azure 門戶。 使用舊金鑰的所有以前生成的 URL 都無效,不再具有觸發邏輯應用的授權。 再生後檢索的 URL 使用新的訪問密鑰進行簽名。

1. 在[Azure 門戶](https://portal.azure.com)中,打開具有要重新生成的密鑰的邏輯應用。

1. 在邏輯應用程式功能表的 [設定]**** 下，選取 [存取金鑰]****。

1. 選擇要重新生成並完成該過程的鍵。

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>建立即將到期的回呼 URL

如果與其他參與方共享基於請求的觸發器的終結點 URL,則可以生成使用特定密鑰且具有到期日期的回調 URL。 這樣,您可以無縫滾動鍵或限制訪問以基於特定時間跨度觸發邏輯應用。 要指定網址的到期日期,請使用[邏輯應用程式 REST API,](https://docs.microsoft.com/rest/api/logic/workflowtriggers)例如:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在正文中,使用`NotAfter`JSON 日期字串包括屬性。 此屬性會傳回只在 `NotAfter` 日期與時間之前有效的回呼 URL。

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>使用主要或次要祕密金鑰建立 URL

為基於請求的觸發器生成或列出回調 URL 時,可以指定用於對 URL 進行簽名的密鑰。 要產生由特定金鑰簽署的網址,請使用[邏輯應用程式 REST API,](https://docs.microsoft.com/rest/api/logic/workflowtriggers)例如:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在本文中，將屬性 `KeyType` 包含為 `Primary` 或 `Secondary`。 此屬性返回由指定的安全金鑰簽名的 URL。

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>限制入站 IP 位址

除了共享訪問簽名 (SAS),您可能希望特別限制可以調用邏輯應用的用戶端。 例如,如果使用 Azure API 管理請求終結點,則可以限制邏輯應用僅接受 API 管理實例的 IP 位址的請求。

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>在 Azure 門戶中限制入站 IP 範圍

1. 在[Azure 門戶](https://portal.azure.com)中,在邏輯應用設計器中打開邏輯應用。

1. 在邏輯應用程式功能表的 [設定]**** 底下，選取 [工作流程設定]****。

1. 在**存取控制設定** > **下允許入站 IP 位址**,選擇**特定的 IP 範圍**。

1. 在 [觸發程序的 IP 範圍]**** 底下，指定觸發程序可接受的 IP 位址範圍。

   有效的 IP 範圍使用這些格式：*x.x.x.x/x* 或 *x.x.x.x-x.x.x.x*

如果希望邏輯應用僅作為嵌套邏輯應用觸發,請從 **「允許入站 IP 位址」** 清單中選擇 **「僅其他邏輯應用**」。 此選項將空數組寫入邏輯應用資源。 這樣,只有來自邏輯應用服務(父邏輯應用)的調用才能觸發嵌套邏輯應用。

> [!NOTE]
> 不論 IP 位址為何，您仍然可以使用 `/triggers/<trigger-name>/run` 透過 Azure REST API 或透過 API 管理來執行具有要求型觸發程序的邏輯應用程式。 但是,此方案仍然需要針對 Azure REST API 進行身份驗證。 所有事件都顯示在 Azure 審核日誌中。 請確保相應地設置訪問控制策略。

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>在 Azure 資源管理員樣本中限制入站 IP 範圍

如果使用[資源管理員樣本自動部署邏輯應用](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md),則可以透過使用邏輯應用資源`accessControl``triggers`定義中的 節來指定 IP 範圍,例如:

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

### <a name="add-azure-active-directory-oauth-or-other-security"></a>新增 Azure 活動目錄 OAuth 或其他安全性

要向邏輯應用添加更多授權協定,請考慮使用[Azure API 管理](../api-management/api-management-key-concepts.md)服務。 此服務可説明您將邏輯應用公開為 API,並為任何終結點提供豐富的監視、安全性、策略和文檔。 API 管理可以公開邏輯應用的公共終結點或專用終結點。 要授權存取此終結點,可以使用 Azure[活動目錄 OAuth、](#azure-active-directory-oauth-authentication)[用戶端證書](#client-certificate-authentication)或其他安全標準來授權訪問該終結點。 當 API 管理收到要求時，服務會傳送要求至您的邏輯應用程式，也會在過程中進行任何必要的轉換或限制。 要僅允許 API 管理觸發邏輯應用,可以使用邏輯應用的入站 IP 範圍設置。

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>存取邏輯應用程式操作

只能允許特定使用者或組運行特定任務,例如管理、編輯和查看邏輯應用。 要控制其權限,請使用[Azure 基於角色的存取控制 (RBAC),](../role-based-access-control/role-assignments-portal.md)以便可以為 Azure 訂閱中的成員分配自訂或內建角色:

* [邏輯應用參與者](../role-based-access-control/built-in-roles.md#logic-app-contributor):允許您管理邏輯應用,但不能更改對它們的訪問。

* [邏輯應用運算子](../role-based-access-control/built-in-roles.md#logic-app-operator):允許您讀取、啟用和禁用邏輯應用,但不能編輯或更新它們。

若要防止變更或刪除邏輯應用程式，您可以使用 [Azure 資源鎖定](../azure-resource-manager/management/lock-resources.md)。 此功能可防止其他人更改或刪除生產資源。

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>存取執行歷程記錄資料

在邏輯應用執行期間,所有資料在傳輸過程中使用傳輸層安全 (TLS) 與[靜態](../security/fundamentals/encryption-atrest.md)[進行加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)。 邏輯應用完成運行時,您可以查看該運行的歷史記錄,包括與每個操作的狀態、持續時間、輸入和輸出一起運行的步驟。 此豐富的詳細資訊提供了有關邏輯應用如何運行以及從何處開始排除出現任何問題的見解。

當您查看邏輯應用的運行歷史記錄時,邏輯應用會驗證您的訪問許可權,然後提供指向每次運行的請求和回應的輸入和輸出的連結。 但是,對於處理任何密碼、機密、密鑰或其他敏感資訊的操作,您希望阻止其他人查看和訪問該數據。 例如,如果邏輯應用從[Azure 密鑰保管庫](../key-vault/key-vault-overview.md)獲取了用於驗證 HTTP 操作時使用的秘密,則希望從視圖中隱藏該機密。

要控制對邏輯應用執行歷史記錄中的輸入和輸出的存取,可以使用以下選項:

* [依 IP 位址範圍限制存取](#restrict-ip)。

  這個選項可幫助您根據來自特定 IP 位址範圍的請求保護運行歷史記錄的訪問許可權。

* [使用模糊處理從執行歷史紀錄隱藏資料](#obfuscate)。

  在許多觸發器和操作中,可以從邏輯應用的運行歷史記錄中隱藏它們的輸入、輸出或兩者。

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>依 IP 位址範圍限制存取

您可以限制對邏輯應用執行歷史記錄中的輸入和輸出的訪問,以便只有來自特定 IP 位址範圍的請求才能查看該資料。 例如,要阻止任何人存取輸入和輸出,請指定 IP 位址`0.0.0.0-0.0.0.0`範圍,如 。 只有具有管理員許可權的人才能刪除此限制,這提供了對邏輯應用數據的「即時」訪問的可能性。 可以使用 Azure 門戶或用於邏輯應用部署的 Azure 資源管理器範本指定要限制的 IP 範圍。

#### <a name="restrict-ip-ranges-in-azure-portal"></a>限制 Azure 門戶中的 IP 範圍

1. 在 Azure 入口網站的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定]**** 底下，選取 [工作流程設定]****。

1. 在**存取控制設定** > **下允許入站 IP 位址**,選擇**特定的 IP 範圍**。

1. 在 [內容的 IP 範圍]**** 底下，指定可存取輸入和輸出內容的 IP 位址範圍。 

   有效的 IP 範圍使用這些格式：*x.x.x.x/x* 或 *x.x.x.x-x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Azure 資源管理員樣本限制 IP 範圍

如果使用[資源管理員樣本自動部署邏輯應用](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md),則可以透過使用邏輯應用資源`accessControl``contents`定義中的 節來指定 IP 範圍,例如:

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

許多觸發器和操作都有從邏輯應用的運行歷史記錄中隱藏輸入、輸出或兩者的設置。 當您使用這些設定來幫助保護此資料時,需要考慮以下[注意事項](#obfuscation-considerations)。

#### <a name="hide-inputs-and-outputs-in-the-designer"></a>隱藏設計器的輸入和輸出

1. 在[Azure 門戶](https://portal.azure.com)中,在邏輯應用設計器中打開邏輯應用。

   ![邏輯應用設計器中的開啟邏輯應用](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. 在要隱藏敏感數據的觸發器或操作上,選擇橢圓 **(...)** 按鈕,然後選擇 **"設置**"。

   ![開啟觸發器或操作設定](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. 打開**安全輸入**、**安全輸出**或兩者。 完成之後，選取 [完成]****。

   ![打開"安全輸入"或"安全輸出"](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   操作或觸發器現在顯示標題列中的鎖定圖示。

   ![操作或觸發器標題列顯示鎖定圖示](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   表示以前操作的安全輸出的權杖也會顯示鎖圖示。 例如,當您從動態內容清單中選擇要在操作中使用的此類輸出時,該令牌將顯示一個鎖圖示。

   ![為安全輸出選擇權杖](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. 邏輯應用運行後,您可以查看該運行的歷史記錄。

   1. 在邏輯應用的 **「概述」** 窗格中,選擇要查看的運行。

   1. 在 **「邏輯」應用執行**窗格中,展開要查看的操作。

      如果選擇遮蓋輸入和輸出,則這些值現在顯示為隱藏值。

      ![執行歷程記錄中的隱藏輸入與輸出](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="hide-inputs-and-outputs-in-code-view"></a>隱藏輸入與輸出

在基礎觸發器或操作定義中`runtimeConfiguration.secureData.properties`,使用以下任一或兩個值新增或更新陣列:

* `"inputs"`:保護運行歷史記錄中的輸入。
* `"outputs"`:保護運行歷史記錄中的輸出。

當您使用這些設定來幫助保護此資料時,需要考慮以下[注意事項](#obfuscation-considerations)。

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

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>隱藏輸入與輸出時的注意事項

* 當您遮蓋觸發器或操作上的輸入或輸出時,邏輯應用不會將安全數據發送到 Azure 日誌分析。 此外,您無法將[跟蹤的屬性](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)添加到該觸發器或用於監視的操作。

* [用於處理工作流歷史記錄的邏輯應用 API](https://docs.microsoft.com/rest/api/logic/)不會返回安全輸出。

* 要隱藏來自模糊輸入或顯式遮蓋輸出的操作的輸出,請手動開啟該操作中**的安全輸出**。

* 請確保在下游操作中打開**安全輸入**或**安全輸出**,而您希望運行歷史記錄會掩蓋該數據。

  **安全性輸出設定**

  當您手動打開觸發器或操作中**的安全輸出**時,邏輯應用會在運行歷史記錄中保護這些輸出。 如果下游操作顯式使用這些安全輸出作為輸入,邏輯應用將此操作的輸入隱藏在運行歷史記錄中,但不會*啟用*該操作**的安全輸入**設置。

  ![將輸出作為輸入和下游對大多數操作的影響進行保護](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  "撰寫"、"分析 JSON"和"回應"操作僅具有 **"安全輸入"** 設置。 啟用時,該設置還會隱藏這些操作的輸出。 如果這些操作顯式使用上游安全輸出作為輸入,邏輯應用將隱藏這些操作的輸入和輸出,但不會*啟用*這些操作的安全**輸入**設置。 如果下游操作顯示式使用來自"合成"、"分析 JSON"或"回應'操作的隱藏輸出作為輸入,則邏輯應用*不會隱藏此下游操作的輸入或輸出*。

  ![將輸出作為輸入進行保護,對具體操作具有下游影響](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **安全輸入設定**

  當您手動打開觸發器或操作中**的安全輸入**時,邏輯應用會在運行歷史記錄中保護這些輸入。 如果下游操作顯式使用該觸發器或操作中的可見輸出作為輸入,邏輯應用會在運行歷史記錄中隱藏此下游操作的輸入,但不會在此操作中*啟用***安全輸入**,也不會隱藏此操作的輸出。

  ![安全輸入和對大多數操作的下游影響](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  如果"組合"、"分析 JSON"和"回應"操作顯式使用具有安全輸入的觸發器或操作中的可見輸出,則邏輯應用將隱藏這些操作的輸入和輸出,但不會*啟用*這些操作的安全**輸入**設置。 如果下游操作顯示式使用來自"合成"、"分析 JSON"或"回應'操作的隱藏輸出作為輸入,則邏輯應用*不會隱藏此下游操作的輸入或輸出*。

  ![安全投入和對具體行動的下游影響](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>存取參數輸入

如果跨不同的環境進行部署,請考慮參數化工作流定義中因這些環境而異的值。 這樣,可以使用[Azure 資源管理器範本](../azure-resource-manager/templates/overview.md)部署邏輯應用,通過定義安全參數來保護敏感數據,並使用[參數檔](../azure-resource-manager/templates/parameter-files.md)將這些數據作為單獨的輸入傳遞到[範本的參數](../azure-resource-manager/templates/template-parameters.md)中,從而避免硬編碼數據。

例如,如果使用[Azure 活動目錄 OAuth](#azure-active-directory-oauth-authentication)對 HTTP 操作進行身份驗證,則可以定義和遮蓋接受用於身份驗證的用戶端 ID 和用戶端金鑰的參數。 要在邏輯應用中定義這些參數,請使用邏輯應用的`parameters`工作流定義和資源管理器範本中的部分進行部署。 要隱藏編輯邏輯應用或查看運行歷史記錄時不希望顯示的參數值,請使用`securestring``secureobject`或類型定義參數,並根據需要使用編碼。 具有此類型的參數不會隨資源定義一起返回,在部署後查看資源時無法訪問。 要在執行時存取這些參數值`@parameters('<parameter-name>')`, 請使用工作流定義中的運算式。 此運算式僅在運行時計算,並由[工作流定義語言](../logic-apps/logic-apps-workflow-definition-language.md)描述。

> [!NOTE]
> 如果在請求標頭或正文中使用參數,則當您查看邏輯應用的運行歷史記錄和傳出 HTTP 請求時,該參數可能可見。 請確保也相應地設置內容訪問策略。 您還可以使用[模糊隱藏](#obfuscate)來隱藏執行歷史記錄中的輸入和輸出。 授權標頭絕對不會透過輸入或輸出來顯示。 如此一來如果該處有使用密碼，就無法擷取密碼。

有關詳細資訊,請參閱本主題中的以下部分:

* [工作流定義中的安全性參數](#secure-parameters-workflow)
* [使用模糊處理從執行歷程記錄隱藏資料](#obfuscate)

如果使用[資源管理器範本自動部署邏輯應用](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md),則可以定義安全[範本參數](../azure-resource-manager/templates/template-parameters.md),這些參數`securestring`在`secureobject`部署時使用和類型進行評估。 要定義範本參數,請使用範本的頂層`parameters`部分,該部分與工作流定義的部分是分開`parameters`的 ,並且不同。 要提供樣本參數的值,請使用單獨的[參數檔](../azure-resource-manager/templates/parameter-files.md)。

例如,如果使用機密,則可以定義和使用安全範本參數,這些參數在部署時從[Azure 密鑰保管庫](../key-vault/key-vault-overview.md)中檢索這些機密。 然後,您可以在參數檔中引用密鑰保管庫和機密。 如需詳細資訊，請參閱下列主題：

* [使用 Azure 金鑰保管庫在部署時傳遞敏感值](../azure-resource-manager/templates/key-vault-parameter.md)
* 在本主題的後面部分[,Azure 資源管理員樣本中的安全參數](#secure-parameters-deployment-template)

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>工作流定義中的安全性參數

為了保護邏輯應用工作流定義中的敏感資訊,請使用安全參數,以便在保存邏輯應用后此資訊不可見。 例如,假設您有一個 HTTP 操作需要基本身份驗證,該身份驗證使用使用者名和密碼。 在工作流定義中`parameters`,該部分`basicAuthPasswordParam``basicAuthUsernameParam``securestring`使用 類型定義 和 參數。 然後,操作定義在`authentication`節中引用這些參數。

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

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Azure 資源管理員樣本中的安全性參數

邏輯應用的[資源管理器範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)`parameters`有多個 部分。 為了保護密碼、密鑰、機密和其他敏感資訊,請使用`securestring``secureobject`或類型在範本級別和工作流定義級別定義安全參數。 然後,可以將這些值存儲在[Azure 密鑰保管庫中](../key-vault/key-vault-overview.md),並使用[參數檔](../azure-resource-manager/templates/parameter-files.md)引用金鑰保管庫和機密。 然後,範本在部署時檢索該資訊。 關於詳細資訊,請參閱使用[Azure 金鑰保管庫在部署時傳遞敏感值](../azure-resource-manager/templates/key-vault-parameter.md)。

以下是有關以下`parameters`部分的詳細資訊:

* 在範本的頂層,部分`parameters`定義範本在*部署*時使用的值的參數。 例如,這些值可以包括特定部署環境的連接字串。 然後,您可以將這些值存儲在單獨的[參數檔中](../azure-resource-manager/templates/parameter-files.md),從而更輕鬆地更改這些值。

* 在邏輯應用的資源定義中,但在工作流定義之外,`parameters`部分指定工作流定義參數的值。 在本節中,可以使用引用範本參數的範本運算式來分配這些值。 這些運算式在部署時計算。

* 在工作流定義中,一`parameters`節定義邏輯應用在運行時使用的參數。 然後,可以使用工作流定義表達式在邏輯應用的工作流中引用這些參數,這些運算式在運行時進行評估。

此範例樣本具有多個使用`securestring`型態的安全參數定義:

| 參數名稱 | 描述 |
|----------------|-------------|
| `TemplatePasswordParam` | 接受密碼的樣本參數,然後傳遞給工作流定義的`basicAuthPasswordParam`參數 |
| `TemplateUsernameParam` | 接受使用者名的樣本參數,然後傳遞給工作流定義的`basicAuthUserNameParam`參數 |
| `basicAuthPasswordParam` | 接受 HTTP 動作中基本身份驗證密碼的參數 |
| `basicAuthUserNameParam` | 接受 HTTP 動作中基本身份驗證的使用者名稱的工作串流定義參數 |
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

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>存取從邏輯應用呼叫的服務和系統

以下是一些可以幫助保護從邏輯應用接收話務或請求的終結點的方法:

* 向出站請求添加身份驗證。

  當您使用基於 HTTP 的觸發器或進行出站呼叫的作業(如 HTTP、HTTP+ Swagger 或 Webhook)時,可以將身份驗證添加到邏輯應用程式發送的請求。 例如,可以使用以下身份驗證類型:

  * [基本身份驗證](#basic-authentication)

  * [用戶端憑證身份驗證](#client-certificate-authentication)

  * [活動目錄身分驗證](#azure-active-directory-oauth-authentication)

  * [受控識別驗證](#managed-identity-authentication)
  
  有關詳細資訊,請參閱在本主題的後面部分[將身份驗證添加到出站呼叫](#add-authentication-outbound)。

* 限制從邏輯應用 IP 位址進行訪問。

  邏輯應用對終結點的所有調用都源自基於邏輯應用區域的特定指定 IP 位址。 您可以新增篩選，以只接受來自那些 IP 位址的要求。 要取得這些 IP 位址,請參考[Azure 邏輯應用的限制和設定](logic-apps-limits-and-config.md#configuration)。

* 提高與本地系統連接的安全性。

  Azure 邏輯應用提供與這些服務的整合,以説明提供更安全、更可靠的本地通訊。

  * 內部部署資料閘道

    Azure 邏輯應用中的許多託管連接器都有助於與本地系統(如檔案系統、SQL、SharePoint 和 DB2)的安全連接。 閘道會在加密通道上經過 Azure 服務匯流排傳送來自內部部署來源的資料。 所有流量都作為安全出站流量源自網關代理。 了解[內部部署資料閘道的運作方式](logic-apps-gateway-install.md#gateway-cloud-service)。

  * 透過 Azure API 管理連線

    [Azure API 管理](../api-management/api-management-key-concepts.md)提供內部部署連線選項，例如站對站虛擬私人網路和 ExpressRoute 整合，以對內部部署系統提供安全的 Proxy 和通訊。 從邏輯應用在邏輯應用設計器中的工作流中,您可以選擇由 API 管理公開的 API,該 API 提供對本地系統的快速存取。

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>傳出站撥號加入認證

HTTP 和 HTTPS 終結點支援各種身份驗證。 根據用於進行出站調用或訪問這些終結點的請求的觸發器或操作,您可以從不同的身份驗證類型範圍中進行選擇。 為了確保保護邏輯應用處理的任何敏感資訊,請使用安全參數並根據需要對數據進行編碼。 有關使用與保護參數的詳細資訊,請參考[參數輸入](#secure-action-parameters)。

> [!NOTE]
> 在邏輯應用設計器中,**身份驗證**屬性可能隱藏在某些觸發器和操作上,您可以在其中指定身份驗證類型。 要使屬性在這些情況下顯示在觸發器或操作上,請打開 **「添加新參數**清單」,然後選擇 **「身份驗證**」。。 有關詳細資訊,請參閱[使用託管識別進行身份認證存取](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)。

| 驗證類型 | 支援者 |
|---------------------|--------------|
| [基本](#basic-authentication) | Azure API 管理、Azure 應用服務、HTTP、HTTP + 搖擺、HTTP Webhook |
| [用戶端憑證](#client-certificate-authentication) | Azure API 管理、Azure 應用服務、HTTP、HTTP + 搖擺、HTTP Webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API 管理、Azure 應用服務、Azure 功能、HTTP、HTTP + 搖擺、HTTP Webhook |
| [原始](#raw-authentication) | Azure API 管理、Azure 應用服務、Azure 功能、HTTP、HTTP + 搖擺、HTTP Webhook |
| [託管識別](#managed-identity-authentication) | Azure API 管理、Azure 應用服務、Azure 功能、HTTP、HTTP + 搖擺、HTTP Webhook |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>基本驗證

如果[「基本」](../active-directory-b2c/secure-rest-api.md)選項可用,請指定以下屬性值:

| 屬性(設計師) | Property (JSON) | 必要 | 值 | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| [驗證]**** | `type` | 是 | 基本 | 要使用的身份驗證類型 |
| **使用者名稱** | `username` | 是 | <*使用者名稱*>| 用來驗證存取目標服務端點的使用者名稱 |
| **密碼** | `password` | 是 | <*密碼*> | 用來驗證存取目標服務端點的密碼 |
||||||

使用[安全參數](#secure-action-parameters)來處理和保護敏感資訊時(例如,在[Azure 資源管理器範本中用於自動部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)時),可以使用運算式在執行時存取這些參數值。 此範例 HTTP 動作定義指定`type`認證`Basic`為 ,並使用[參數()函數](../logic-apps/workflow-definition-language-functions-reference.md#parameters)取得參數值:

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

如果[客戶端憑證](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)選項可用,請指定以下屬性值:

| 屬性(設計師) | Property (JSON) | 必要 | 值 | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| [驗證]**** | `type` | 是 | **用戶端憑證** <br>或 <br>`ClientCertificate` | 用於 TLS/SSL 客戶端憑證的身份驗證類型。 雖然支援自簽名證書,但不支援 TLS/SSL 的自簽名證書。 |
| **普夫克斯** | `pfx` | 是 | <*編碼-pfx-文件內容*> | Base64 編碼的個人資訊交換 (PFX) 檔案內容 <p><p>要將 PFX 檔轉換為基本編碼格式,可以使用 PowerShell 以下步驟: <p>1. 將憑證內容儲存到變數中: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2.`ToBase64String()`使用 函數轉換憑證內容,並將該內容儲存到文字檔: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **密碼** | `password`| 否 | <*密碼換pfx檔*> | 用於存取 PFX 檔案的密碼 |
|||||

使用[安全參數](#secure-action-parameters)來處理和保護敏感資訊時(例如,在[Azure 資源管理器範本中用於自動部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)時),可以使用運算式在執行時存取這些參數值。 此範例 HTTP 動作定義指定`type`認證`ClientCertificate`為 ,並使用[參數()函數](../logic-apps/workflow-definition-language-functions-reference.md#parameters)取得參數值:

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

有關使用客戶端憑證身份驗證保護服務的詳細資訊,請參閱以下主題:

* [透過 Azure API 管理中使用客戶端憑證認證來提高 API 的安全性](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [通過在 Azure API 管理中使用用戶端證書身份驗證,提高後端服務的安全性](../api-management/api-management-howto-mutual-certificates.md)
* [使用用戶端憑證提高 RESTfuL 服務的安全性](../active-directory-b2c/secure-rest-api.md)
* [適用於應用程式驗證的憑證認證](../active-directory/develop/active-directory-certificate-credentials.md)
* [在 Azure 應用服務中的代碼中使用 TLS/SSL 憑證](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Azure 活動目錄認證

如果[作用目錄 OAuth](../active-directory/develop/about-microsoft-identity-platform.md)選項可用,請指定以下屬性值:

| 屬性(設計師) | Property (JSON) | 必要 | 值 | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| [驗證]**** | `type` | 是 | **Active Directory OAuth** <br>或 <br>`ActiveDirectoryOAuth` | 要使用的驗證類型。 邏輯應用目前遵循[OAuth 2.0 協定](../active-directory/develop/v2-overview.md)。 |
| **權威** | `authority` | 否 | <*網址 for 授權權杖發行者*> | 提供身份驗證權杖的頒發機構的網址。 根據預設，此值為 `https://login.windows.net`。 |
| **租用戶** | `tenant` | 是 | <*租戶識別碼*> | Azure AD 租用戶的租用戶識別碼 |
| **觀眾** | `audience` | 是 | <*資源到授權*> | 您希望用於授權的資源，例如，`https://management.core.windows.net/` |
| **用戶端識別碼** | `clientId` | 是 | <*用戶端識別碼*> | 要求授權的應用程式用戶端識別碼 |
| **認證類型** | `credentialType` | 是 | 憑證 <br>或 <br>祕密 | 用戶端用於請求授權的認證類型。 此屬性和值不顯示在邏輯應用的基礎定義中,但確定為所選憑據類型顯示的屬性。 |
| **秘密** | `secret` | 是,但僅適用於「機密」憑據類型 | <*用戶端-機密*> | 要求授權用的用戶端密碼 |
| **普夫克斯** | `pfx` | 是,但僅適用於"證書"認證類型 | <*編碼-pfx-文件內容*> | Base64 編碼的個人資訊交換 (PFX) 檔案內容 |
| **密碼** | `password` | 是,但僅適用於"證書"認證類型 | <*密碼換pfx檔*> | 用於存取 PFX 檔案的密碼 |
|||||

使用[安全參數](#secure-action-parameters)來處理和保護敏感資訊時(例如,在[Azure 資源管理器範本中用於自動部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)時),可以使用運算式在執行時存取這些參數值。 此範例 HTTP 動作定義將`type`認證`ActiveDirectoryOAuth`指定 為 ,`Secret`認證類型為 ,並使用[參數()函數](../logic-apps/workflow-definition-language-functions-reference.md#parameters)取得參數值:

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

如果**Raw**選項可用,則當必須使用不遵循[OAuth 2.0 協定的](https://oauth.net/2/)[身份驗證方案](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml)時,可以使用此身份驗證類型。 使用此類型,您可以手動創建與傳出請求一起發送的授權標頭值,並在觸發器或操作中指定該標頭值。

例如,下面是遵循[OAuth 1.0 協定](https://tools.ietf.org/html/rfc5849)的 HTTPS 請求的範示例標頭:

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

在支援原始身份驗證的觸發器或操作中,指定以下屬性值:

| 屬性(設計師) | Property (JSON) | 必要 | 值 | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| [驗證]**** | `type` | 是 | Raw | 要使用的身份驗證類型 |
| **ReplTest1** | `value` | 是 | <*授權-標頭值*> | 認證授權標頭值 |
||||||

使用[安全參數](#secure-action-parameters)來處理和保護敏感資訊時(例如,在[Azure 資源管理器範本中用於自動部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)時),可以使用運算式在執行時存取這些參數值。 此範例 HTTP 動作定義指定`type`的`Raw`成員指定為 ,並使用[參數()函數](../logic-apps/workflow-definition-language-functions-reference.md#parameters)取得參數值:

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

如果[託管標識](../active-directory/managed-identities-azure-resources/overview.md)選項可用,則邏輯應用可以使用系統分配的標識或手動創建的使用者分配的*識別*來驗證對其他 Azure 活動目錄 (Azure AD) 租戶中的資源的存取許可權,而無需登錄。 Azure 會為您管理此身分識別，並協助保護您的認證，因為您不需要提供或輪替使用祕密。 詳細瞭解支援[Azure AD 認證的系統辨識的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

1. 在邏輯應用可以使用託管標識之前,請按照[Azure 邏輯應用中使用託管標識對 Azure 資源進行身份驗證訪問](../logic-apps/create-managed-service-identity.md)的步驟。 這些步驟在邏輯應用中啟用託管標識,並設置該標識對目標 Azure 資源的訪問。

1. 在 Azure 函數可以使用託管識別之前,首先[為 Azure 函數啟用身份驗證](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)。

1. 在要使用託管識別的觸發器或操作中,指定以下屬性值:

   | 屬性(設計師) | Property (JSON) | 必要 | 值 | 描述 |
   |---------------------|-----------------|----------|-------|-------------|
   | [驗證]**** | `type` | 是 | **受控識別** <br>或 <br>`ManagedServiceIdentity` | 要使用的身份驗證類型 |
   | **受控識別** | `identity` | 是 | * **系統配置的託管識別** <br>或 <br>`SystemAssigned` <p><p>• <*使用者配置的識別名稱*> | 要使用的託管識別 |
   | **觀眾** | `audience` | 是 | <*目標資源識別碼*> | 要訪問的目標資源的資源 ID。 <p>例如,`https://storage.azure.com/`使身份驗證的存取權杖對所有儲存帳戶都有效。 但是,您還可以指定根服務 URL,`https://fabrikamstorageaccount.blob.core.windows.net`例如為特定存儲帳戶指定。 <p>**注意**: **"存取群體'** 屬性可能隱藏在某些觸發器或操作中。 要使此屬性可見,請在觸發器或操作中打開 **「添加新參數**清單」,然後選擇 **「訪問群體**」。 <p><p>**重要提示**:確保此目標資源 ID 與 Azure AD 期望的值*完全符合*,包括任何必需的尾隨斜杠。 因此,`https://storage.azure.com/`所有 Azure Blob 儲存帳戶的資源 ID 都需要尾隨斜杠。 但是,特定存儲帳戶的資源 ID 不需要尾隨斜杠。 要尋找這些資源識別,請參閱[支援 Azure AD 的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。 |
   |||||

   使用[安全參數](#secure-action-parameters)來處理和保護敏感資訊時(例如,在[Azure 資源管理器範本中用於自動部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)時),可以使用運算式在執行時存取這些參數值。 此範例 HTTP 動作定義指定`type`認證`ManagedServiceIdentity`為 ,並使用[參數()函數](../logic-apps/workflow-definition-language-functions-reference.md#parameters)取得參數值:

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
