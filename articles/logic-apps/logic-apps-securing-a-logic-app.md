---
title: 保障存取和資料的安全性
description: 在 Azure Logic Apps 中保護對於輸入、輸出、以要求為基礎的觸發程序、執行歷程記錄、管理工作及其他資源的存取
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla, rarayudu
ms.topic: conceptual
ms.date: 01/20/2021
ms.openlocfilehash: 337e242e3c194c8ec9f66e1888926e6a8f6a8375
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633073"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>在 Azure Logic Apps 中保護存取和資料

Azure Logic Apps 依賴 [Azure 儲存體](../storage/index.yml) 來儲存和自動 [加密待用資料](../security/fundamentals/encryption-atrest.md)。 此加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。 根據預設，Azure 儲存體會使用 Microsoft 管理的金鑰來加密您的資料。 如需詳細資訊，請參閱[待用資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)。

若要進一步控制存取權並保護 Azure Logic Apps 中的敏感性資料，您可以在下欄區域中設定額外的安全性：

* [以要求為基礎的觸發程式之撥入電話的存取權](#secure-inbound-requests)
* [存取邏輯應用程式作業](#secure-operations)
* [存取執行歷程記錄輸入和輸出](#secure-run-history)
* [存取參數輸入](#secure-action-parameters)
* [存取其他服務和系統的輸出呼叫](#secure-outbound-requests)
* [封鎖建立特定連接器的連接](#block-connections)
* [邏輯應用程式的隔離指引](#isolation-logic-apps)
* [適用于 Azure Logic Apps 的 Azure 安全性基準](../logic-apps/security-baseline.md)

如需 Azure 安全性的詳細資訊，請參閱下列主題：

* [Azure 加密概觀](../security/fundamentals/encryption-overview.md)
* [Azure 資料靜態加密](../security/fundamentals/encryption-atrest.md)
* [Azure 安全性效能評定](../security/benchmarks/overview.md)

<a name="secure-inbound-requests"></a>

## <a name="access-for-inbound-calls-to-request-based-triggers"></a>以要求為基礎的觸發程式之撥入電話的存取權

邏輯應用程式透過以要求為基礎的觸發程式接收的輸入呼叫（例如 [要求](../connectors/connectors-native-reqres.md) 觸發程式或 [HTTP Webhook](../connectors/connectors-native-webhook.md) 觸發程式）支援加密，並且會以 [最少的傳輸層安全性來保護 (TLS) 1.2](https://en.wikipedia.org/wiki/Transport_Layer_Security)，之前稱為安全通訊端層 (SSL) 。 Logic Apps 在接收要求觸發程式的輸入呼叫或 HTTP Webhook 觸發程式或動作的回呼時，會強制執行此版本。 如果收到 TLS 交握錯誤，請確定您使用 TLS 1.2。 如需詳細資訊，請參閱[解決 TLS 1.0 問題](/security/solving-tls1-problem)。

輸入呼叫支援下列加密套件：

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

以下是您可以限制存取觸發程式的其他方法，這些觸發程式會接收邏輯應用程式的撥入電話，讓只有經過授權的用戶端可以呼叫您的邏輯應用程式：

* [產生共用存取簽章 (SAS)](#sas)
* [啟用 Azure Active Directory 開放式驗證 (Azure AD OAuth)](#enable-oauth)
* [使用 Azure API 管理公開您的邏輯應用程式](#azure-api-management)
* [限制輸入 IP 位址](#restrict-inbound-ip-addresses)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>產生共用存取簽章 (SAS)

邏輯應用程式上的每個要求端點在端點的 URL 中，都有[共用存取簽章 (SAS)](/rest/api/storageservices/constructing-a-service-sas)，格式如下：

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

每個 URL 都包含 `sp`、`sv` 和 `sig` 查詢參數，如下表所述：

| 查詢參數 | 描述 |
|-----------------|-------------|
| `sp` | 指定權限供允許的 HTTP 方法使用。 |
| `sv` | 指定用於產生簽章的 SAS 版本。 |
| `sig` | 指定簽章以用於驗證對觸發程序的存取。 在所有 URL 路徑和屬性上，系統會使用 SHA256 演算法，搭配祕密存取金鑰，以產生此簽章。 此金鑰永不公開或發佈，且保持加密，並與邏輯應用程式一起儲存。 您的邏輯應用程式只會針對包含使用祕密金鑰建立之有效簽章的觸發程序進行授權。 |
|||

要求端點的輸入呼叫只能使用一個授權配置，也就是 SAS 或 [Azure Active Directory 開啟驗證](#enable-oauth)。 雖然使用一個配置並不會停用其他配置，但同時使用兩種配置會導致錯誤，因為服務不知道要選擇哪一個配置。

如需使用 SAS 保護存取安全性的詳細資訊，請參閱本主題中下列各節：

* [重新產生存取金鑰](#access-keys)
* [建立即將到期的回呼 URL](#expiring-urls)
* [使用主要或次要金鑰建立 URL](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>重新產生存取金鑰

隨時想要產生新的安全性存取金鑰時，請使用 Azure REST API 或 Azure 入口網站。 之前使用舊金鑰產生的所有 URL 都會失效，不再有授權來觸發邏輯應用程式。 重新產生之後，將以新的存取金鑰來簽署您取出的 URL。

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟邏輯應用程式，內有您要重新產生的金鑰。

1. 在邏輯應用程式功能表的 [設定] 下，選取 [存取金鑰]。

1. 選取您要重新產生的金鑰，並完成流程。

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>建立有期限的回呼 URL

對於以要求為基礎的觸發程序，如果您要將端點 URL 分享給其他人，您可以產生使用特定金鑰且有到期日的回呼 URL。 如此一來，您可以根據特定的時間範圍，無縫輪替金鑰，或限制有無存取權來觸發邏輯應用程式。 若要指定 URL 的到期日，請使用 [Logic Apps REST API](/rest/api/logic/workflowtriggers)，例如：

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在本文中，使用 JSON 日期字串來加入 `NotAfter` 屬性。 此屬性會傳回只在 `NotAfter` 日期與時間之前有效的回呼 URL。

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>使用主要或次要祕密金鑰建立 URL

針對以要求為基礎的觸發程序，當您產生或列出回呼 URL 時，您可以指定用於簽署 URL 的金鑰。 若要產生以特定金鑰簽署的 URL，請使用 [Logic Apps REST API](/rest/api/logic/workflowtriggers)，例如：

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在本文中，將屬性 `KeyType` 包含為 `Primary` 或 `Secondary`。 此屬性會傳回以特定安全金鑰簽署的 URL。

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-open-authentication-azure-ad-oauth"></a>啟用 Azure Active Directory 開放式驗證 (Azure AD OAuth)

針對以要求為基礎的觸發程式所建立之端點的輸入呼叫，您可以藉由定義或新增邏輯應用程式的授權原則，啟用 [Azure Active Directory Open Authentication (Azure AD OAuth) ](../active-directory/develop/index.yml) 。 如此一來，輸入呼叫就會使用 OAuth [存取權杖](../active-directory/develop/access-tokens.md) 來進行授權。

當您的邏輯應用程式收到包含 OAuth 存取權杖的輸入要求時，Azure Logic Apps 服務會將權杖的宣告與每個授權原則所指定的宣告進行比較。 如果權杖的宣告與至少一個原則中的所有宣告相符，則會成功授權輸入要求。 權杖的宣告可以比授權原則指定的數目更多。

在您啟用 Azure AD OAuth 之前，請先參閱下列考慮：

* 要求端點的輸入呼叫只能使用一個授權配置，Azure AD OAuth 或共用存取簽章 [ (SAS) ](#sas)。 雖然使用一個配置並不會停用其他配置，但同時使用兩種配置會導致錯誤，因為 Logic Apps 服務並不知道要選擇哪一個配置。

* Azure AD OAuth 存取權杖僅支援 [持有人類型](../active-directory/develop/active-directory-v2-protocols.md#tokens) 授權配置，這表示 `Authorization` 存取權杖的標頭必須指定 `Bearer` 類型。

* 邏輯應用程式受限於授權原則數目上限。 每個授權原則也有[宣告](../active-directory/develop/developer-glossary.md#claim)數目上限。 如需詳細資訊，請參閱 [Azure Logic Apps 的限制和設定](../logic-apps/logic-apps-limits-and-config.md#authentication-limits)。

* 授權原則至少必須包含 **簽發者** 宣告，此宣告的值開頭為 `https://sts.windows.net/` 或 `https://login.microsoftonline.com/` (OAuth V2) 作為 Azure AD 簽發者識別碼。

  例如，假設您的邏輯應用程式有一個授權原則，需要兩個宣告類型： **物件** 和 **簽發者**。 針對已解碼存取權杖的這個範例承載 [區段](../active-directory/develop/access-tokens.md#payload-claims) 包含宣告類型，其中 `aud` 是 **物件** 的值，而 `iss` 是 **簽發者** 值：

  ```json
  {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/<Azure-AD-issuer-ID>/",
      "iat": 1582056988,
      "nbf": 1582056988,
      "exp": 1582060888,
      "_claim_names": {
         "groups": "src1"
      },
      "_claim_sources": {
         "src1": {
            "endpoint": "https://graph.windows.net/7200000-86f1-41af-91ab-2d7cd011db47/users/00000-f433-403e-b3aa-7d8406464625d7/getMemberObjects"
         }
      },
      "acr": "1",
      "aio": "AVQAq/8OAAAA7k1O1C2fRfeG604U9e6EzYcy52wb65Cx2OkaHIqDOkuyyr0IBa/YuaImaydaf/twVaeW/etbzzlKFNI4Q=",
      "amr": [
         "rsa",
         "mfa"
      ],
      "appid": "c44b4083-3bb0-00001-b47d-97400853cbdf3c",
      "appidacr": "2",
      "deviceid": "bfk817a1-3d981-4dddf82-8ade-2bddd2f5f8172ab",
      "family_name": "Sophia Owen",
      "given_name": "Sophia Owen (Fabrikam)",
      "ipaddr": "167.220.2.46",
      "name": "sophiaowen",
      "oid": "3d5053d9-f433-00000e-b3aa-7d84041625d7",
      "onprem_sid": "S-1-5-21-2497521184-1604012920-1887927527-21913475",
      "puid": "1003000000098FE48CE",
      "scp": "user_impersonation",
      "sub": "KGlhIodTx3XCVIWjJarRfJbsLX9JcdYYWDPkufGVij7_7k",
      "tid": "72f988bf-86f1-41af-91ab-2d7cd011db47",
      "unique_name": "SophiaOwen@fabrikam.com",
      "upn": "SophiaOwen@fabrikam.com",
      "uti": "TPJ7nNNMMZkOSx6_uVczUAA",
      "ver": "1.0"
   }
   ```

<a name="define-authorization-policy-portal"></a>

#### <a name="define-authorization-policy-in-azure-portal"></a>在 Azure 入口網站中定義授權原則

若要在 Azure 入口網站中為您的邏輯應用程式啟用 Azure AD OAuth，請遵循下列步驟，將一或多個授權原則新增至您的邏輯應用程式：

1. 在 [Azure 入口網站](https://portal.microsoft.com)的邏輯應用程式設計工具中，尋找並開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定] 下，選取 [授權]。 在 [授權] 窗格開啟之後，選取 [新增原則]。

   ![選取 [授權] > [新增原則]](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. 藉由在要求觸發程式的每個輸入呼叫所呈現的存取權杖中指定您的邏輯應用程式預期的宣告 [類型](../active-directory/develop/developer-glossary.md#claim) 和值，以提供授權原則的相關資訊：

   ![提供授權原則的資訊](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **原則名稱** | 是 | 您想要用於授權原則的名稱 |
   | **宣告** | 是 | 邏輯應用程式所接受來自輸入呼叫的宣告類型和值。 宣告值限制為 [最大字元數](logic-apps-limits-and-config.md#authentication-limits)。 以下是可用的宣告類型： <p><p>- **簽發者** <br>- **對象** <br>- **主旨** <br>- **JWT 識別碼** (JSON Web 權杖識別碼) <p><p>**宣告** 清單至少必須包含 **簽發者** 宣告，此宣告的值開頭為 `https://sts.windows.net/` 或 `https://login.microsoftonline.com/` Azure AD 簽發者識別碼。 如需這些宣告類型的詳細資訊，請參閱 [Azure AD 安全性權杖中的宣告](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens)。 您也可以指定自己的宣告類型和值。 |
   |||

1. 若要新增另一個宣告，請從下列選項中選取：

   * 若要新增另一個宣告類型，請選取 [新增標準宣告]，選取宣告類型，然後指定宣告值。

   * 若要新增您自己的宣告，請選取 [新增自訂宣告]，然後指定自訂宣告值。

1. 若要新增另一個授權原則，請選取 [新增原則]。 重複上述步驟來設定原則。

1. 完成時，選取 [儲存]。

1. 若要 `Authorization` 在以要求為基礎的觸發程式輸出中包含存取權杖的標頭，請參閱 [在要求觸發程式輸出中包含 ' Authorization ' 標頭](#include-auth-header)。

<a name="define-authorization-policy-template"></a>

#### <a name="define-authorization-policy-in-azure-resource-manager-template"></a>在 Azure Resource Manager 範本中定義授權原則

若要在 ARM 範本中啟用 Azure AD OAuth 來部署邏輯應用程式，請遵循下列步驟和下列語法：

1. 在 `properties` [邏輯應用程式資源定義](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)的區段中，新增 `accessControl` 包含物件的物件（如果沒有的話） `triggers` 。

   如需物件的詳細資訊 `accessControl` ，請參閱 [限制 Azure Resource Manager 範本中的輸入 IP 範圍](#restrict-inbound-ip-template) 和 [Microsoft. 邏輯工作流程範本參考](/azure/templates/microsoft.logic/2019-05-01/workflows)。

1. 在 `triggers` 物件中，加入 `openAuthenticationPolicies` 包含 `policies` 您定義一或多個授權原則之物件的物件。

1. 提供授權原則的名稱、將原則類型設定為 `AAD` ，並包含 `claims` 您指定一或多個宣告類型的陣列。

   `claims`陣列至少必須包含您將宣告的屬性設定為的簽發者宣告類型 `name` `iss` ，並將設定 `value` 為開頭為 `https://sts.windows.net/` 或 `https://login.microsoftonline.com/` Azure AD 簽發者識別碼。 如需這些宣告類型的詳細資訊，請參閱 [Azure AD 安全性權杖中的宣告](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens)。 您也可以指定自己的宣告類型和值。

1. 若要 `Authorization` 在以要求為基礎的觸發程式輸出中包含存取權杖的標頭，請參閱 [在要求觸發程式輸出中包含 ' Authorization ' 標頭](#include-auth-header)。

以下是要遵循的語法：

```json
"resources": [
   {
      // Start logic app resource definition
      "properties": {
         "state": "<Enabled-or-Disabled>",
         "definition": {<workflow-definition>},
         "parameters": {<workflow-definition-parameter-values>},
         "accessControl": {
            "triggers": {
               "openAuthenticationPolicies": {
                  "policies": {
                     "<policy-name>": {
                        "type": "AAD",
                        "claims": [
                           {
                              "name": "<claim-name>",
                              "value": "<claim-value>"
                           }
                        ]
                     }
                  }
               }
            },
         },
      },
      "name": "[parameters('LogicAppName')]",
      "type": "Microsoft.Logic/workflows",
      "location": "[parameters('LogicAppLocation')]",
      "apiVersion": "2016-06-01",
      "dependsOn": [
      ]
   }
   // End logic app resource definition
],
```

<a name="include-auth-header"></a>

#### <a name="include-authorization-header-in-request-trigger-outputs"></a>在要求觸發程式輸出中包含 ' Authorization ' 標頭

針對 [啟用 Azure Active Directory Open Authentication 的邏輯應用程式 (Azure AD oauth) ](#enable-oauth) 以存取以要求為基礎的觸發程式的輸入呼叫，您可以啟用要求觸發程式或 HTTP Webhook 觸發程式輸出，以包含 `Authorization` 來自 OAuth 存取權杖的標頭。 在觸發程式的基礎 JSON 定義中，將屬性加入並設定 `operationOptions` 為 `IncludeAuthorizationHeadersInOutputs` 。 以下是要求觸發程式的範例：

```json
"triggers": {
   "manual": {
      "inputs": {
         "schema": {}
      },
      "kind": "Http",
      "type": "Request",
      "operationOptions": "IncludeAuthorizationHeadersInOutputs"
   }
}
```

如需詳細資訊，請參閱下列主題：

* [觸發程式和動作類型的架構參考-要求觸發程式](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)
* [觸發程式和動作類型的架構參考-HTTP Webhook 觸發程式](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [觸發程式和動作類型的架構參考-作業選項](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)

<a name="azure-api-management"></a>

### <a name="expose-your-logic-app-with-azure-api-management"></a>使用 Azure API 管理公開您的邏輯應用程式

若要將更多的 [驗證通訊協定](../active-directory/develop/authentication-vs-authorization.md) 新增至邏輯應用程式，請考慮使用 [Azure API 管理](../api-management/api-management-key-concepts.md) 服務。 此服務協助您將邏輯應用程式公開為 API，並為任何端點提供豐富的監視、安全性、原則和文件。 API 管理可以公開邏輯應用程式的公用或私人端點。 若要授與此端點的存取權，您可以使用 Azure AD OAuth、 [用戶端憑證](#client-certificate-authentication)或其他安全性標準來授權存取該端點。 當 API 管理收到要求時，服務會傳送要求至您的邏輯應用程式，也會在過程中進行任何必要的轉換或限制。 若只要讓 API 管理呼叫您的邏輯應用程式，您可以 [限制邏輯應用程式的輸入 IP 位址](#restrict-inbound-ip)。

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>限制輸入 IP 位址

除了共用存取簽章 (SAS) ，您可能還想特別限制哪些用戶端可呼叫邏輯應用程式。 例如，如果您使用 [AZURE API 管理](../api-management/api-management-key-concepts.md)來管理您的要求端點，您可以限制邏輯應用程式只接受來自 [您所建立 API 管理服務實例](../api-management/get-started-create-service-instance.md)之 IP 位址的要求。

無論您指定的任何 IP 位址為何，您仍然可以使用 [Logic Apps REST API：工作流程觸發程式-執行](/rest/api/logic/workflowtriggers/run) 要求或使用 API 管理，來執行具有要求型觸發程式的邏輯應用程式。 不過，此情況仍然需要經過 Azure REST API 來[驗證](../active-directory/develop/authentication-vs-authorization.md)。 所有事件都出現在 Azure 稽核記錄中。 請確定您已適當地設定存取控制原則。

<a name="restrict-inbound-ip-portal"></a>

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>在 Azure 入口網站中限制輸入 IP 範圍

當您使用入口網站來限制邏輯應用程式的輸入 IP 位址時，這些限制會影響觸發程式 *和* 動作，儘管入口網站中的描述是在 [ **允許的輸入 ip 位址**] 下。 若要設定與動作分開的觸發程式限制，請[ `accessControl` 在邏輯應用程式的 Azure Resource Manager 範本](#restrict-inbound-ip-template)或[Logic Apps REST API：工作流程建立或更新](/rest/api/logic/workflows/createorupdate)作業中使用物件。

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定] 底下，選取 [工作流程設定]。

1. 在 [ **存取控制** 設定] 區段的 [ **允許的輸入 IP 位址**] 下，選擇您案例的路徑：

   * 若要使用內建的 [Azure Logic Apps 動作](../logic-apps/logic-apps-http-endpoint.md)，讓邏輯應用程式只能以嵌套的邏輯應用程式的方式來呼叫，請 **只選取其他 Logic Apps**，此作業 *只* 會在您使用 **Azure Logic Apps** 動作來呼叫嵌套邏輯應用程式時使用。
   
     此選項會將空的陣列寫入您的邏輯應用程式資源，而且只需要使用內建 **Azure Logic Apps** 動作的父邏輯應用程式呼叫，就可以觸發嵌套邏輯應用程式。

   * 若要使用 HTTP 動作，讓邏輯應用程式只能以嵌套的應用程式來呼叫，請選取 **特定的 IP 範圍**，*而不***只是其他 Logic Apps**。 出現 [ **觸發程式的 IP 範圍** ] 方塊時，輸入父邏輯應用程式的 [輸出 IP 位址](../logic-apps/logic-apps-limits-and-config.md#outbound)。 有效的 IP 範圍會使用下列格式： x.x.x.x. x */x* 或 *x.x.x.x*. x. x. x. x. x. x. x. x. x. x. x. x. x。
   
     > [!NOTE]
     > 如果您使用 **唯一的其他 Logic Apps** 選項和 HTTP 動作來呼叫您的嵌套邏輯應用程式，則會封鎖呼叫，而您會收到「401未經授權」的錯誤。
        
   * 如果您想要限制來自其他 Ip 的輸入呼叫，請在 [ **觸發程式的 IP 範圍** ] 方塊出現時，指定觸發程式可接受的 ip 位址範圍。 有效的 IP 範圍會使用下列格式： x.x.x.x. x */x* 或 *x.x.x.x*. x. x. x. x. x. x. x. x. x. x. x. x. x。

1. （選擇性）在 **[限制呼叫] 下，從執行歷程記錄取得輸入和輸出訊息至提供的 IP 位址**，您可以在執行歷程記錄中指定可存取輸入和輸出訊息之輸入呼叫的 IP 位址範圍。

<a name="restrict-inbound-ip-template"></a>

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>在 Azure Resource Manager 範本中限制輸入 IP 範圍

如果您 [使用 Resource Manager 範本將邏輯應用程式的部署自動化](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，您可以使用區段，在邏輯應用程式的資源定義中指定允許的輸入 IP 位址範圍 `accessControl` 。 在本節中，您可以將區段包含在屬性中， `triggers` `actions` `contents` `allowedCallerIpAddresses` `addressRange` 並將屬性值設定為 *x.x.x.x/x*  . x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x

* 如果您的嵌套邏輯應用程式使用 **唯一的 Logic Apps** 選項，只允許來自使用 Azure Logic Apps 動作之其他邏輯應用程式的輸入呼叫，請將屬性設定 `addressRange` 為空陣列 (**[]**) 。

* 如果您的嵌套邏輯應用程式針對其他輸入呼叫（例如使用 HTTP 動作的其他邏輯應用程式）使用 **特定的 ip 範圍** 選項，請將 `addressRange` 屬性設定為允許的 IP 範圍。

此範例顯示的是嵌套邏輯應用程式的資源定義，只允許來自使用內建 Azure Logic Apps 動作之邏輯應用程式的輸入呼叫：

```json
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
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": []
                     }
                  ]
               },
               "actions": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": []
                     }
                  ]
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

此範例顯示可允許來自使用 HTTP 動作之邏輯應用程式的輸入呼叫之嵌套邏輯應用程式的資源定義：

```json
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
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               },
               "actions": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>存取邏輯應用程式作業

您可以只允許特定使用者或群組執行特定工作，例如管理、編輯和檢視邏輯應用程式。 若要控制其許可權，請使用 [azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/role-assignments-portal.md) ，讓您可以將自訂或內建角色指派給 azure 訂用帳戶中的成員：

* [邏輯應用程式參與者](../role-based-access-control/built-in-roles.md#logic-app-contributor)：可讓您管理邏輯應用程式，但無法變更對邏輯應用程式的存取。

* [邏輯應用程式操作員](../role-based-access-control/built-in-roles.md#logic-app-operator)：可讓您讀取、啟用及停用邏輯應用程式，但無法編輯或更新邏輯應用程式。

若要防止變更或刪除邏輯應用程式，您可以使用 [Azure 資源鎖定](../azure-resource-manager/management/lock-resources.md)。 這項功能可防止其他人變更或刪除生產資源。

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>存取執行歷程記錄資料

在邏輯應用程式執行期間，所有資料都[在傳輸期間加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)，使用的是傳輸層安全性 (TLS) 和[待用](../security/fundamentals/encryption-atrest.md)。 當邏輯應用程式執行完成時，您可以檢視本次執行的歷程記錄，包括已執行的步驟，以及每個動作的狀態、持續時間、輸入和輸出。 這項豐富的詳細資料可讓您深入解析邏輯應用程式的執行情形，而且對於任何發生的問題，可讓您知道從何處開始進行疑難排解。

當您檢視邏輯應用程式的執行歷程記錄時，Logic Apps 會驗證您的存取，然後針對每次執行的要求和回應，提供連結指向輸入和輸出。 不過，如果動作處理任何密碼、秘密、金鑰或其他敏感性資訊，建議您防止其他人檢視和存取該資料。 例如，如果邏輯應用程式從 [Azure Key Vault](../key-vault/general/overview.md) 取得祕密來用於驗證 HTTP 動作，建議您隱藏該秘密。

若要控制存取邏輯應用程式執行歷程記錄中的輸入和輸出，您有下列選項：

* [依 IP 位址範圍限制存取](#restrict-ip)。

  此選項協助您根據來自特定 IP 位址範圍的要求，以保護對執行歷程記錄的存取。

* [使用混淆來保護執行歷程記錄中的資料](#obfuscate)。

  在許多觸發程序和動作中，您可以保護邏輯應用程式執行歷程記錄中的輸入、輸出或兩者。

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>依 IP 位址範圍限制存取

您可以限制存取邏輯應用程式執行歷程記錄中的輸入和輸出，只允許來自特定 IP 位址範圍的要求才能檢視該資料。 例如，若要阻止任何人存取輸入和輸出，請指定像 `0.0.0.0-0.0.0.0` 這樣的 IP 位址範圍。 只有具備管理員權限的人才能移除此限制，這樣才有機會「及時」存取邏輯應用程式的資料。 您可以使用 Azure 入口網站，或在用於邏輯應用程式部署的 Azure Resource Manager 範本中，指定要限制的 IP 範圍。

#### <a name="restrict-ip-ranges-in-azure-portal"></a>在 Azure 入口網站中限制 IP 範圍

1. 在 Azure 入口網站的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定] 底下，選取 [工作流程設定]。

1. 在 [存取控制設定] > [允許的輸入 IP 位址] 底下，選取 [特定 IP 範圍]。

1. 在 [內容的 IP 範圍] 底下，指定可存取輸入和輸出內容的 IP 位址範圍。

   有效的 IP 範圍使用這些格式：*x.x.x.x/x* 或 *x.x.x.x-x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>在 Azure Resource Manager 範本中限制 IP 範圍

如果您[使用 Resource Manager 範本來自動部署邏輯應用程式](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，您可以在邏輯應用程式的資源定義中，使用 `accessControl` 區段與 `contents` 區段來指定 IP 範圍，例如：

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

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>使用混淆來保護執行歷程記錄中的資料

許多觸發程序和動作都有設定，可保護來自邏輯應用程式執行歷程記錄的輸入、輸出或兩者。 使用這些設定來協助您保護此資料之前，請[檢閱這些考量](#obfuscation-considerations)。

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>在設計工具中保護輸入和輸出

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

   ![在 Logic Apps 設計工具中開啟邏輯應用程式](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. 在您要保護敏感性資料的觸發程序或動作上，選取省略符號 ( **...** ) 按鈕，然後選取 [設定]。

   ![開啟觸發程序或動作設定](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. 開啟 [保護輸入]、[保護輸出] 或兩者。 完成之後，選取 [完成]。

   ![開啟「保護輸入」或「保護輸出」](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   動作或觸發程序的標題列中現在顯示鎖定圖示。

   ![動作或觸發程序標題列顯示鎖定圖示](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   如果權杖代表先前動作的受保護輸出，則也會顯示鎖定圖示。 例如，當您從動態內容清單中選取這種輸出來用於動作時，該權杖會顯示鎖定圖示。

   ![針對受保護的輸出選取權杖](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. 當邏輯應用程式執行之後，您可以檢視本次執行的歷程記錄。

   1. 在邏輯應用程式的 [概觀] 窗格中，選取您要檢視的執行。

   1. 在 [邏輯應用程式執行] 窗格中，展開您要檢閱的動作。

      如果您已選擇遮蔽輸入和輸出，則那些值現在已隱藏。

      ![執行歷程記錄中隱藏的輸入和輸出](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>在程式碼檢視中保護輸入和輸出

在基礎觸發程序或動作定義中，使用下列其中一個或兩個值來新增或更新 `runtimeConfiguration.secureData.properties` 陣列：

* `"inputs"`:保護執行歷程記錄中的輸入。
* `"outputs"`:保護執行歷程記錄中的輸出。

當您使用這些設定來協助保護此資料時，以下提供一些[應檢閱的考量](#obfuscation-considerations)。

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

#### <a name="considerations-when-securing-inputs-and-outputs"></a>保護輸入和輸出安全性時的考量

* 當您遮蔽觸發程序或動作的輸入或輸出時，Logic Apps 不會將受保護的資料傳送至 Azure Log Analytics。 此外，您無法將[追蹤的屬性](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)新增至該觸發程序或動作來監視。

* [用於處理工作流程歷程記錄的 Logic Apps API](/rest/api/logic/) 不會傳回受保護的輸出。

* 若要保護動作的輸出，但該動作已遮蔽輸入或明確遮蔽輸出，請在該動作中手動開啟 [保護輸出]。

* 在您預期執行歷程記錄會遮蔽該資料的下游動作中，請務必開啟 [保護輸入] 或 [保護輸出]。

  **保護輸出設定**

  當您在觸發程序或動作中手動開啟 [保護輸出] 時，Logic Apps 會在執行歷程記錄中隱藏這些輸出。 如果下游動作明確使用這些受保護的輸出作為輸入，Logic Apps 會在執行歷程記錄中隱藏此動作的輸入，但「不會啟用」動作的 [保護輸入] 設定。

  ![受保護的輸出作為輸入和下游對大部分動作的影響](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  「撰寫」、「剖析 JSON」和「回應」動作只有 [保護輸入] 設定。 此設定開啟時也會隱藏這些動作的輸出。 如果這些動作明確使用上游保護的輸出作為輸入，Logic Apps 會隱藏這些動作的輸入和輸出，但「不會啟用」這些動作的 [保護輸入] 設定。 如果下游動作明確使用「撰寫」、「剖析 JSON」或「回應」動作的隱藏輸出作為輸入，Logic Apps「不會隱藏此下游動作的輸入或輸出」。

  ![受保護的輸出作為輸入和下游對特定動作的影響](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **保護輸入設定**

  當您在觸發程序或動作中手動開啟 [保護輸入] 時，Logic Apps 會在執行歷程記錄中隱藏這些輸入。 如果下游動作明確使用該觸發程序或動作的可見輸出作為輸入，Logic Apps 會在執行歷程記錄中隱藏此下游動作的輸入，但在此動作中「不會啟用」[保護輸入]，也不會隱藏此動作的輸出。

  ![受保護的輸入和下游對大部分動作的影響](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  如果「撰寫」、「剖析 JSON」和「回應」動作明確使用的可見輸出，來自於具有受保護輸入的觸發程序或動作，Logic Apps 會隱藏這些動作的輸入和輸出，但「不會啟用」這些動作的 [保護輸入] 設定。 如果下游動作明確使用「撰寫」、「剖析 JSON」或「回應」動作的隱藏輸出作為輸入，Logic Apps「不會隱藏此下游動作的輸入或輸出」。

  ![受保護的輸入和下游對特定動作的影響](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>存取參數輸入

如果您在不同的環境之間部署，請考慮將工作流程定義中的值參數化，讓值隨著這些環境而改變。 如此一來，您可以使用 [Azure Resource Manager 範本](../azure-resource-manager/templates/overview.md)部署邏輯應用程式以避免硬式編碼的資料、定義受保護的參數來保護敏感性資料，以及使用[參數檔案](../azure-resource-manager/templates/parameter-files.md)來透過[範本的參數](../azure-resource-manager/templates/template-parameters.md)，將該資料當作個別輸入來傳遞。

例如，如果您使用 [Azure Active Directory 開放式驗證](#azure-active-directory-oauth-authentication) (Azure AD OAuth) 來驗證 HTTP 動作，您可以定義並遮蔽參數，以接受用於驗證的用戶端識別碼和用戶端祕密。 若要在邏輯應用程式中定義這些參數，請在邏輯應用程式的工作流程定義和 Resource Manager 部署範本中，使用 `parameters` 區段。 若要保護參數值而不要在編輯邏輯應用程式或檢視執行歷程記錄時出現，請使用 `securestring` 或 `secureobject` 型別來定義參數，並視需要使用編碼。 此型別的參數不會隨著資源定義一起傳回，而在部署之後檢視資源時也無法存取。 若要在執行階段存取這些參數值，請在工作流程定義內使用 `@parameters('<parameter-name>')` 運算式。 此運算式只在執行階段評估，而且以[工作流程定義語言](../logic-apps/logic-apps-workflow-definition-language.md)來描述。

> [!NOTE]
> 如果您在要求標頭或本文中使用參數，則在檢視邏輯應用程式的執行歷程記錄及傳出 HTTP 要求時，可能會看見此參數。 請務必也適當地設定內容存取原則。
> 您也可以使用[混淆](#obfuscate)，以隱藏執行歷程記錄中的輸入和輸出。 授權標頭絕對不會透過輸入或輸出來顯示。 如此一來如果該處有使用密碼，就無法擷取密碼。

如需詳細資訊，請參閱本主題中的下列各節：

* [保護工作流程定義中的參數](#secure-parameters-workflow)
* [使用混淆來保護執行歷程記錄中的資料](#obfuscate)

如果您[使用 Resource Manager 範本來自動部署邏輯應用程式](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，您可以使用 `securestring` 和 `secureobject` 型別來定義受保護的[範本參數](../azure-resource-manager/templates/template-parameters.md) (在部署時評估)。 若要定義範本參數，請使用範本的最上層 `parameters` 區段，此區段獨立且不同於工作流程定義的 `parameters` 區段。 若要提供範本參數的值，請使用個別的[參數檔案](../azure-resource-manager/templates/parameter-files.md)。

例如，如果您使用秘密，您可以定義和使用受保護的範本參數，在部署時從 [Azure Key Vault](../key-vault/general/overview.md) 取得這些秘密。 然後，您可以在參數檔案中參考金鑰保存庫和祕密。 如需詳細資訊，請參閱下列主題：

* [在部署時使用 Azure Key Vault 傳遞敏感值](../azure-resource-manager/templates/key-vault-parameter.md)
* 本主題稍後的[保護 Azure Resource Manager 範本中的參數](#secure-parameters-deployment-template)

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>保護工作流程定義中的參數

若要保護邏輯應用程式工作流程定義中的敏感性資訊，請使用受保護的參數，在您儲存邏輯應用程式之後就不會看到此資訊。 例如，假設您的 HTTP 動作需要基本驗證，而這會用到使用者名稱和密碼。 在工作流程定義中，`parameters` 區段使用 `securestring` 型別來定義 `basicAuthPasswordParam` 和 `basicAuthUsernameParam` 參數。 然後，動作定義會在 `authentication` 區段中參考這些參數。

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

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>保護 Azure Resource Manager 範本中的參數

邏輯應用程式的 [Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)有多個 `parameters` 區段。 若要保護密碼、金鑰、秘密及其他敏感性資訊，請在範本層級和工作流程定義層級，使用 `securestring` 或 `secureobject` 型別來定義受保護的參數。 然後，您可以將這些值儲存在 [Azure Key Vault](../key-vault/general/overview.md) 中，並使用[參數檔案](../azure-resource-manager/templates/parameter-files.md)來參考金鑰保存庫和密碼。 然後，您的範本會在部署時取出該資訊。 如需詳細資訊，請參閱[在部署時使用 Azure Key Vault 傳遞敏感值](../azure-resource-manager/templates/key-vault-parameter.md)。

以下是有關這些 `parameters` 區段的詳細資訊：

* 在範本的最上層，`parameters` 區段定義的參數指定範本在「部署」時使用的值。 例如，這些值可以包含特定部署環境的連接字串。 然後，您可以將這些值儲存在個別的[參數檔案](../azure-resource-manager/templates/parameter-files.md)中，以更輕鬆變更這些值。

* 在邏輯應用程式的資源定義內 (但在工作流程定義外)，`parameters` 區段指定工作流程定義參數的值。 在此區段中，您可以使用範本運算式來參考範本的參數，以指派這些值。 部署時會評估這些運算式。

* 在工作流程定義內，`parameters` 區段定義邏輯應用程式在執行階段使用的參數。 然後，您可以在邏輯應用程式的工作流程內，使用工作流程定義運算式 (在執行階段評估) 來參考這些參數。

這個範例範本有多個使用 `securestring` 型別的受保護參數定義：

| 參數名稱 | 描述 |
|----------------|-------------|
| `TemplatePasswordParam` | 此範本參數接受密碼，再傳遞給工作流程定義的 `basicAuthPasswordParam` 參數 |
| `TemplateUsernameParam` | 此範本參數接受使用者名稱，再傳遞給工作流程定義的 `basicAuthUserNameParam` 參數 |
| `basicAuthPasswordParam` | 此工作流程定義參數接受密碼，以用於 HTTP 動作中的基本驗證 |
| `basicAuthUserNameParam` | 此工作流程定義參數接受使用者名稱，以用於 HTTP 動作中的基本驗證 |
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

<a name="secure-outbound-requests"></a>

## <a name="access-for-outbound-calls-to-other-services-and-systems"></a>存取其他服務和系統的輸出呼叫

根據目標端點的功能， [HTTP 觸發程式或 HTTP 動作](../connectors/connectors-native-http.md)所傳送的輸出呼叫支援加密，並且會使用 [傳輸層安全性來保護 (TLS) 1.0、1.1 或 1.2](https://en.wikipedia.org/wiki/Transport_Layer_Security)，之前稱為安全通訊端層 (SSL) 。 Logic Apps 使用支援的最高可能版本來協商目標端點。 例如，如果目標端點支援1.2，HTTP 觸發程式或動作會先使用1.2。 否則，連接器會使用下一個支援的最高版本。

以下是 TLS/SSL 自我簽署憑證的相關資訊：

* 針對全域、多租使用者 Azure 環境中的邏輯應用程式，HTTP 連接器不允許自我簽署的 TLS/SSL 憑證。 如果您的邏輯應用程式對伺服器進行 HTTP 呼叫，並顯示 TLS/SSL 自我簽署憑證，則 HTTP 呼叫會失敗並出現 `TrustFailure` 錯誤。

* 若為 [整合服務環境 (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的邏輯應用程式，HTTP 連接器允許使用自我簽署憑證進行 TLS/SSL 交握。 不過，您必須先使用 Logic Apps REST API 啟用現有 ISE 或新 ISE 的 [自我簽署憑證支援](../logic-apps/create-integration-service-environment-rest-api.md#request-body) ，並在該位置安裝公開憑證 `TrustedRoot` 。

以下是您可以用來協助保護處理從邏輯應用程式傳送之呼叫的端點安全的方法：

* [將驗證新增至輸出要求](#add-authentication-outbound)。

  當您使用 HTTP 觸發程式或動作來傳送輸出呼叫時，可以將驗證新增至您的邏輯應用程式所傳送的要求。 例如，您可以選取下列驗證類型：

  * [基本驗證](#basic-authentication)

  * [用戶端憑證驗證](#client-certificate-authentication)

  * [Active Directory OAuth 驗證](#azure-active-directory-oauth-authentication)

  * [受控識別驗證](#managed-identity-authentication)

* 限制來自邏輯應用程式 IP 位址的存取。

  從邏輯應用程式對端點的所有呼叫，都源自以邏輯應用程式的區域為基礎的特定 IP 位址。 您可以新增篩選，以只接受來自那些 IP 位址的要求。 如需取得這些 IP 位址，請參閱 [Azure Logic Apps 的限制和設定](logic-apps-limits-and-config.md#configuration)。

* 改善內部部署系統的連線安全性。

  Azure Logic Apps 與下列服務整合，提供更安全可靠的內部部署通訊。

  * 內部部署資料閘道

    Azure Logic Apps 的許多受控連接器都支援安全連線至內部部署系統，例如檔案系統、SQL、SharePoint 和 DB2。 閘道會在加密通道上經過 Azure 服務匯流排傳送來自內部部署來源的資料。 所有流量都是源自閘道代理程式的受保護輸出流量。 了解[內部部署資料閘道的運作方式](logic-apps-gateway-install.md#gateway-cloud-service)。

  * 透過 Azure API 管理進行連線

    [AZURE API 管理](../api-management/api-management-key-concepts.md) 會提供內部部署連線選項，例如站對站虛擬私人網路和 [ExpressRoute](../expressroute/expressroute-introduction.md) 整合，以進行安全的 proxy 和內部部署系統的通訊。 如果您有可存取內部部署系統的 API，而且您藉由建立 [Api 管理服務實例](../api-management/get-started-create-service-instance.md)來公開該 api，您可以在邏輯應用程式設計工具中選取內建 api 管理觸發程式或動作，以在邏輯應用程式的工作流程中呼叫該 api。

    > [!NOTE]
    > 連接器只會顯示您有權查看和連線的 API 管理服務，但不會顯示以耗用量為基礎的 API 管理服務。

    1. 在邏輯應用程式設計工具中，于 `api management` [搜尋] 方塊中輸入。 根據您要新增觸發程式或動作來選擇步驟：<p>

       * 如果您要新增觸發程式，這一律是工作流程中的第一個步驟，請選取 **[選擇 AZURE API 管理觸發程式**]。

       * 如果您要新增動作，請選取 **[選擇 AZURE API 管理動作**]。

       此範例會新增觸發程式：

       ![新增 Azure API 管理觸發程式](./media/logic-apps-securing-a-logic-app/select-api-management.png)

    1. 選取先前建立的 API 管理服務實例。

       ![選取 API 管理服務實例](./media/logic-apps-securing-a-logic-app/select-api-management-service-instance.png)

    1. 選取要使用的 API 呼叫。

       ![選取現有的 API](./media/logic-apps-securing-a-logic-app/select-api.png)

<a name="add-authentication-outbound"></a>

### <a name="add-authentication-to-outbound-calls"></a>將驗證新增至輸出呼叫

HTTP 和 HTTPS 端點支援各種類型的驗證。 在您用來將輸出呼叫或要求傳送至這些端點的某些觸發程式和動作上，您可以指定驗證類型。 在邏輯應用程式設計工具中，支援選擇驗證類型的觸發程式和動作都具有 **驗證** 屬性。 不過，預設不一定會顯示此屬性。 在這些情況下，請在 [觸發程式] 或 [動作] 上，開啟 [ **加入新參數** ] 清單，然後選取 [ **驗證**]。

> [!IMPORTANT]
> 若要保護邏輯應用程式處理的機密資訊，請視需要使用安全的參數並編碼資料。
> 如需使用及保護參數的詳細資訊，請參閱[存取參數輸入](#secure-action-parameters)。

<a name="authentication-types-supported-triggers-actions"></a>

#### <a name="authentication-types-for-triggers-and-actions-that-support-authentication"></a>支援驗證之觸發程式和動作的驗證類型

下表識別可在觸發程式和動作上使用的驗證類型，您可以在其中選取驗證類型：

| 驗證類型 | 支援的觸發程式和動作 |
|---------------------|--------------------------------|
| [基本](#basic-authentication) | Azure API 管理、Azure App Service、HTTP、HTTP + Swagger、HTTP Webhook |
| [用戶端憑證](#client-certificate-authentication) | Azure API 管理、Azure App Service、HTTP、HTTP + Swagger、HTTP Webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API 管理、Azure App Service、Azure Functions、HTTP、HTTP + Swagger、HTTP Webhook |
| [原始](#raw-authentication) | Azure API 管理、Azure App Service、Azure Functions、HTTP、HTTP + Swagger、HTTP Webhook |
| [受控身分識別](#managed-identity-authentication) | **內建觸發程式和動作** <p><p>Azure API 管理，Azure App 服務，Azure Functions，HTTP，HTTP Webhook <p><p>**受控連接器** <p><p>Azure AD Identity Protection，Azure 自動化，Azure 容器實例，Azure 資料總管，Azure Data Factory，Azure Data Lake，Azure 事件方格，Azure IoT Central V3，Azure Key Vault，Azure Log Analytics，Azure 監視器記錄，Azure Resource Manager，Azure Sentinel，HTTP 與 Azure AD <p><p>**注意**：受控連接器的支援目前為預覽狀態。 |
|||

<a name="basic-authentication"></a>

#### <a name="basic-authentication"></a>基本驗證

如果有[基本](../active-directory-b2c/secure-rest-api.md)選項可用，請指定下列屬性值：

| 屬性 (設計工具) | 屬性 (JSON) | 必要 | 值 | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| **驗證** | `type` | 是 | 基本 | 要使用的驗證類型 |
| **使用者名稱** | `username` | 是 | <*user-name*>| 用來驗證存取目標服務端點的使用者名稱 |
| **密碼** | `password` | 是 | <*password*> | 用來驗證存取目標服務端點的密碼 |
||||||

當您使用[受保護的參數](#secure-action-parameters)來處理和保護敏感性資訊時，例如，在[用於自動化部署的 Azure Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中，您可以使用運算式在執行階段存取這些參數值。 這個 HTTP 動作定義範例將驗證 `type` 指定為 `Basic`，並使用 [parameters() 函式](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 來取得參數值：

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

#### <a name="client-certificate-authentication"></a>用戶端憑證驗證

如果有[用戶端憑證](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)選項可用，請指定下列屬性值：

| 屬性 (設計工具) | 屬性 (JSON) | 必要 | 值 | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| **驗證** | `type` | 是 | **用戶端憑證** <br>或 <br>`ClientCertificate` | 要使用的驗證類型。 您可以使用 [AZURE API 管理](../api-management/api-management-howto-mutual-certificates.md)來管理憑證。 <p></p>**注意**：自訂連接器不支援傳入和撥出電話的憑證型驗證。 |
| **Pfx** | `pfx` | 是 | <*encoded-pfx-file-content*> | Base64 編碼的個人資訊交換 (PFX) 檔案內容 <p><p>若要將 PFX 檔案轉換成 base64 編碼格式，您可以依照下列步驟使用 PowerShell： <p>1.將憑證內容儲存至變數： <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2.使用 `ToBase64String()` 函式來轉換憑證內容，並將該內容儲存至文字檔： <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` <p><p>**疑難排解**：如果您使用 `cert mmc/PowerShell` 命令，可能會收到此錯誤： <p><p>`Could not load the certificate private key. Please check the authentication certificate password is correct and try again.` <p><p>若要解決此錯誤，請嘗試使用下列命令將 PFX 檔案轉換為 PEM 檔案，然後再次返回 `openssl` ： <p><p>`openssl pkcs12 -in certificate.pfx -out certificate.pem` <br>`openssl pkcs12 -in certificate.pem -export -out certificate2.pfx` <p><p>之後，當您取得憑證新轉換之 PFX 檔案的 base64 編碼字串時，字串現在可以在 Azure Logic Apps 中使用。 |
| **密碼** | `password`| 否 | <*password-for-pfx-file*> | 用於存取 PFX 檔案的密碼 |
|||||

當您使用[受保護的參數](#secure-action-parameters)來處理和保護敏感性資訊時，例如，在[用於自動化部署的 Azure Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中，您可以使用運算式在執行階段存取這些參數值。 這個 HTTP 動作定義範例將驗證 `type` 指定為 `ClientCertificate`，並使用 [parameters() 函式](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 來取得參數值：

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

如需以用戶端憑證驗證來保護服務的詳細資訊，請參閱下列主題：

* [在 Azure API 管理中以用戶端憑證驗證改善 API 的安全性](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [在 Azure API 管理中以用戶端憑證驗證改善後端服務的安全性](../api-management/api-management-howto-mutual-certificates.md)
* [以用戶端憑證改善 RESTful 服務的安全性](../active-directory-b2c/secure-rest-api.md)
* [應用程式驗證的憑證認證](../active-directory/develop/active-directory-certificate-credentials.md)
* [在 Azure App Service 的程式碼中使用 TLS/SSL 憑證](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

#### <a name="azure-active-directory-open-authentication"></a>Azure Active Directory 開放式驗證

在要求觸發程式上，您可以在設定邏輯應用程式的[Azure AD 授權原則](#enable-oauth)之後，使用[Azure Active Directory 的 Open Authentication (Azure AD OAuth) ](../active-directory/develop/index.yml)）來驗證傳入的呼叫。 對於有 **Active Directory OAuth** 驗證類型可供您選擇的其他所有觸發程序和動作，請指定下列屬性值：

| 屬性 (設計工具) | 屬性 (JSON) | 必要 | 值 | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| **驗證** | `type` | 是 | **Active Directory OAuth** <br>或 <br>`ActiveDirectoryOAuth` | 要使用的驗證類型。 Logic Apps 目前遵循 [OAuth 2.0 通訊協定](../active-directory/develop/v2-overview.md)。 |
| **授權單位** | `authority` | 否 | <*URL-for-authority-token-issuer*> | 提供存取權杖的授權單位 URL。 根據預設，此值為 `https://login.windows.net`。 |
| **租用戶** | `tenant` | 是 | <*tenant-ID*> | Azure AD 租用戶的租用戶識別碼 |
| **目標對象** | `audience` | 是 | <*resource-to-authorize*> | 您希望用於授權的資源，例如，`https://management.core.windows.net/` |
| **用戶端識別碼** | `clientId` | 是 | <*client-ID*> | 要求授權的應用程式用戶端識別碼 |
| **認證類型** | `credentialType` | 是 | 憑證 <br>或 <br>祕密 | 用戶端要求授權時使用的認證類型。 此屬性和值不會出現在邏輯應用程式的基礎定義中，但可決定在選取的認證類型上出現的屬性。 |
| **祕密** | `secret` | 是，但僅適用於「祕密」認證類型 | <*client-secret*> | 要求授權用的用戶端密碼 |
| **Pfx** | `pfx` | 是，但僅適用於「憑證」認證類型 | <*encoded-pfx-file-content*> | Base64 編碼的個人資訊交換 (PFX) 檔案內容 |
| **密碼** | `password` | 是，但僅適用於「憑證」認證類型 | <*password-for-pfx-file*> | 用於存取 PFX 檔案的密碼 |
|||||

當您使用[受保護的參數](#secure-action-parameters)來處理和保護敏感性資訊時，例如，在[用於自動化部署的 Azure Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中，您可以使用運算式在執行階段存取這些參數值。 這個 HTTP 動作定義範例將驗證 `type` 指定為 `ActiveDirectoryOAuth`，將認證類型指定為 `Secret`，並使用 [parameters() 函式](../logic-apps/workflow-definition-language-functions-reference.md#parameters)來取得參數值：

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

#### <a name="raw-authentication"></a>原始驗證

如果有 [原始] 選項可用，當您必須使用未遵循 [OAuth 2.0 通訊協定](https://oauth.net/2/)的[驗證配置](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml)時，您可以使用此驗證類型。 使用此類型時，您可以手動建立授權標頭值以隨著傳出要求一起傳送，並在觸發程序或動作中指定該標頭值。

例如，在遵循 [OAuth 1.0 通訊協定](https://tools.ietf.org/html/rfc5849)的 HTTPS 要求中，標頭範例如下：

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

在支援原始驗證的觸發程序或動作中，請指定下列屬性值：

| 屬性 (設計工具) | 屬性 (JSON) | 必要 | 值 | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| **驗證** | `type` | 是 | Raw | 要使用的驗證類型 |
| **ReplTest1** | `value` | 是 | <*authorization-header-value*> | 要用於驗證的授權標頭值 |
||||||

當您使用[受保護的參數](#secure-action-parameters)來處理和保護敏感性資訊時，例如，在[用於自動化部署的 Azure Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中，您可以使用運算式在執行階段存取這些參數值。 這個 HTTP 動作定義範例將驗證 `type` 指定為 `Raw`，並使用 [parameters() 函式](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 來取得參數值：

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

#### <a name="managed-identity-authentication"></a>受控識別驗證

當 [受控識別](../active-directory/managed-identities-azure-resources/overview.md) 選項可在 [支援受控識別驗證的觸發程式或動作](#add-authentication-outbound)上使用時，您的邏輯應用程式可以使用系統指派的身分識別或 *單一* 手動建立的使用者指派身分識別，來驗證受 Azure Active Directory (Azure AD) 保護的 Azure 資源，而不是認證、秘密或 Azure AD 權杖的存取權。 Azure 會為您管理此身分識別，並協助您保護您的認證，因為您沒有管理秘密或直接使用 Azure AD 權杖。 深入了解[支援使用受控識別進行 Azure AD 驗證的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

1. 若要讓邏輯應用程式使用受控識別，請遵循[在 Azure Logic Apps 中使用受控識別來驗證對 Azure 資源的存取](../logic-apps/create-managed-service-identity.md)中的步驟。 這些步驟會在邏輯應用程式上啟用受控識別，並設定該身分識別對目標 Azure 資源的存取權。

1. 請先[啟用 Azure 函式的驗證](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions)，Azure 函式才能使用受控識別。

1. 在支援使用受控識別的觸發程式或動作中，提供下列資訊：

   **內建觸發程式和動作**

   | 屬性 (設計工具) | 屬性 (JSON) | 必要 | 值 | 描述 |
   |---------------------|-----------------|----------|-------|-------------|
   | **驗證** | `type` | 是 | **受控身分識別** <br>或 <br>`ManagedServiceIdentity` | 要使用的驗證類型 |
   | **受控身分識別** | `identity` | 是 | * **系統指派的受控識別** <br>或 <br>`SystemAssigned` <p><p>* <*user-assigned-identity-name*> | 要使用的受控識別 |
   | **目標對象** | `audience` | 是 | <*target-resource-ID*> | 資源識別碼，代表您想要存取的目標資源。 <p>例如，`https://storage.azure.com/` 為所有儲存體帳戶提供有效的驗證[存取權杖](../active-directory/develop/access-tokens.md)。 不過，您也可以為特定儲存體帳戶指定根服務 URL，例如 `https://fabrikamstorageaccount.blob.core.windows.net`。 <p>**注意**：某些觸發程序或動作中，可能會隱藏 [對象] 屬性。 若要顯示此屬性，請在觸發程序或動作中開啟 [新增參數] 清單，然後選取 [對象]。 <p><p>**重要**：請確定目標資源識別碼 *完全符合* Azure AD 所預期的值，包括任何必要的尾端斜線。 因此，所有 Azure Blob 儲存體帳戶的 `https://storage.azure.com/` 資源識別碼需要有尾端斜線。 不過，特定儲存體帳戶的資源識別碼不需要尾端斜線。 若要尋找這些資源識別碼，請參閱[支援 Azure AD 的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。 |
   |||||

   當您使用[受保護的參數](#secure-action-parameters)來處理和保護敏感性資訊時，例如，在[用於自動化部署的 Azure Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中，您可以使用運算式在執行階段存取這些參數值。 例如，這個 HTTP 動作定義會將驗證指定 `type` 為 `ManagedServiceIdentity` ，並使用 [參數 ( # A1 函數](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 來取得參數值：

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

   **受控連接器觸發程式和動作**

   | 屬性 (設計工具) | 必要 | 值 | 描述 |
   |---------------------|----------|-------|-------------|
   | **連線名稱** | Yes | <*連接-名稱*> ||
   | **受控識別** | Yes | **系統指派的受控識別** <br>或 <br> <*使用者指派的受控-身分識別名稱*> | 要使用的驗證類型 |
   |||||


<a name="block-connections"></a>

## <a name="block-creating-connections"></a>封鎖建立連接

如果您的組織不允許使用 Azure Logic Apps 中的連接器來連線到特定資源，您可以在邏輯應用程式工作流程中，使用[Azure 原則](../governance/policy/overview.md)來[封鎖為特定連接器建立這些連接的功能](../logic-apps/block-connections-connectors.md)。 如需詳細資訊，請參閱 [在 Azure Logic Apps 中封鎖特定連接器所建立的連接](../logic-apps/block-connections-connectors.md)。

<a name="isolation-logic-apps"></a>

## <a name="isolation-guidance-for-logic-apps"></a>邏輯應用程式的隔離指引

您可以使用 [Azure Government](../azure-government/documentation-government-welcome.md) 中的 Azure Logic Apps，以支援 [Azure Government 影響等級5隔離指引](../azure-government/documentation-government-impact-level-5.md#azure-logic-apps) 和 [美國防禦雲端計算安全性需求指南 (SRG) ](https://dl.dod.cyber.mil/wp-content/uploads/cloud/SRG/index.html)所述區域中的所有影響層級。 為了符合這些需求，Logic Apps 支援可讓您在具有專用資源的環境中建立和執行工作流程的功能，讓您可以減少邏輯應用程式上其他 Azure 租使用者的效能影響，並避免與其他租使用者共用運算資源。

* 若要執行您自己的程式碼或執行 XML 轉換，請 [建立並呼叫 Azure 函式](../logic-apps/logic-apps-azure-functions.md)，而不是使用 [內嵌程式碼功能](../logic-apps/logic-apps-add-run-inline-code.md) ，或分別提供 [元件來使用做為對應](../logic-apps/logic-apps-enterprise-integration-maps.md)。 此外，也請為您的函數應用程式設定裝載環境，以符合您的隔離需求。

  例如，若要符合影響等級5的需求，請使用 [**獨立** 定價層](../app-service/overview-hosting-plans.md)來建立您的函數應用 [App Service](../azure-functions/dedicated-plan.md)程式，並搭配也使用 **隔離** 定價層的 [App Service 環境 (ASE)](../app-service/environment/intro.md) 。 在此環境中，函式應用程式會在專用的 Azure 虛擬機器和專用的 Azure 虛擬網路上執行，以針對您的應用程式和最大的向外延展功能，在計算隔離之上提供網路隔離。 如需詳細資訊，請參閱 [Azure Government 影響等級5隔離指引-Azure Functions](../azure-government/documentation-government-impact-level-5.md#azure-functions)。

  如需詳細資訊，請參閱下列主題：<p>

  * [Azure App Service 方案](../app-service/overview-hosting-plans.md)
  * [Azure Functions 網路選項](../azure-functions/functions-networking-options.md)
  * [適用于虛擬機器的 Azure 專用主機](../virtual-machines/dedicated-hosts.md)
  * [Azure 中的虛擬機器隔離](../virtual-machines/isolation.md)
  * [將專用 Azure 服務部署至虛擬網路](../virtual-network/virtual-network-for-azure-services.md)

* 若要建立在專用資源上執行的邏輯應用程式，並可存取受 Azure 虛擬網路保護的資源，您可以 [ (ISE) 建立整合服務環境 ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

  * 某些 Azure 虛擬網路會使用私人端點 ([Azure Private Link](../private-link/private-link-overview.md)) 來提供 azure PaaS 服務的存取權，例如 Azure 儲存體、Azure Cosmos DB 或 Azure SQL Database、合作夥伴服務，或裝載在 azure 上的客戶服務。 如果您的邏輯應用程式需要存取使用私人端點的虛擬網路，您必須在 ISE 內建立、部署和執行這些邏輯應用程式。

  * 若要更充分掌控 Azure 儲存體所使用的加密金鑰，您可以使用 [Azure Key Vault](../key-vault/general/overview.md)來設定、使用及管理您自己的金鑰。 這項功能也稱為「攜帶您自己的金鑰」 (BYOK) ，而您的金鑰稱為「客戶管理的金鑰」。 如需詳細資訊，請參閱 [Azure Logic Apps 中的設定客戶管理的金鑰，以將整合服務環境的待用資料加密 (ise) ](../logic-apps/customer-managed-keys-integration-service-environment.md)。

如需詳細資訊，請參閱下列主題：

* [Azure 公用雲端中的隔離](../security/fundamentals/isolation-choices.md)
* [Azure 中高度機密的 IaaS 應用程式安全性](/azure/architecture/reference-architectures/n-tier/high-security-iaas)

## <a name="next-steps"></a>下一步

* [適用于 Azure Logic Apps 的 Azure 安全性基準](../logic-apps/security-baseline.md)
* [Azure Logic Apps 的自動化部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [監視 Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md)
