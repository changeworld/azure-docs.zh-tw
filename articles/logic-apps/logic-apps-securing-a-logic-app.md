---
title: 保障存取和資料的安全性
description: 安全存取輸入、輸出、以要求為基礎的觸發程式、執行歷程記錄、管理工作，以及存取中的其他資源 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 47b9c0f89cb3db1610b8e3d98f408283c6ff9980
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116923"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>保護 Azure Logic Apps 中的存取和資料

若要控制存取和保護 Azure Logic Apps 中的資料，您可以在下欄區域中設定安全性：

* [存取以要求為基礎的觸發程式](#secure-triggers)
* [存取邏輯應用程式作業](#secure-operations)
* [執行歷程記錄輸入和輸出的存取權](#secure-run-history)
* [存取參數輸入](#secure-action-parameters)
* [存取從邏輯應用程式呼叫的服務和系統](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>存取以要求為基礎的觸發程式

如果您的邏輯應用程式使用以要求為基礎的觸發程式，例如[要求](../connectors/connectors-native-reqres.md)或[Webhook](../connectors/connectors-native-webhook.md)觸發程式，則您可以限制存取權，讓只有經過授權的用戶端可以呼叫您的邏輯應用程式。 邏輯應用程式收到的所有要求，都會使用安全通訊端層 (SSL) 通訊協定加密並保護。

以下是您可以保護此觸發程式類型之存取的方式：

* [產生共用存取簽章](#sas)
* [限制輸入 IP 位址](#restrict-inbound-ip-addresses)
* [新增 Azure Active Directory OAuth 或其他安全性](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>產生共用存取簽章（SAS）

邏輯應用程式上的每個要求端點在端點的 URL 中都有[共用存取簽章（SAS）](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) ，其遵循此格式：

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

每個 URL 都包含 `sp`、`sv`和 `sig` 查詢參數，如下表所述：

| 查詢參數 | 描述 |
|-----------------|-------------|
| `sp` | 指定允許的 HTTP 方法所要使用的許可權。 |
| `sv` | 指定要用於產生簽章的 SAS 版本。 |
| `sig` | 指定要用來驗證觸發程式存取權的簽章。 此簽章的產生方式是在所有 URL 路徑和屬性上使用 SHA256 演算法搭配秘密存取金鑰。 永不公開或發佈，此金鑰會保持加密狀態，並與邏輯應用程式一起儲存。 您的邏輯應用程式只會針對包含使用祕密金鑰建立之有效簽章的觸發程序進行授權。 |
|||

如需使用 SAS 保護存取權的詳細資訊，請參閱本主題中的下列各節：

* [重新產生存取金鑰](#access-keys)
* [建立即將到期的回呼 URL](#expiring-urls)
* [使用主要或次要金鑰建立 URL](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>重新產生存取金鑰

若要隨時產生新的安全存取金鑰，請使用 Azure REST API 或 Azure 入口網站。 所有先前產生的使用舊金鑰的 Url 都會失效，且不再有觸發邏輯應用程式的授權。 您在重新產生之後取得的 Url 會使用新的存取金鑰進行簽署。

1. 在  [Azure 入口網站](https://portal.azure.com)中，開啟具有您想要重新產生之金鑰的邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定] 下，選取 [存取金鑰]。

1. 選取您要重新產生的金鑰，然後完成此程式。

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>建立即將過期的回呼 Url

如果您與其他人共用以要求為基礎之觸發程式的端點 URL，您可以產生使用特定金鑰並具有到期日期的回呼 Url。 如此一來，您就可以根據特定時間範圍，順暢地變換金鑰或限制觸發邏輯應用程式的存取。 若要指定 URL 的到期日，請使用[Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers)，例如：

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在主體中，使用 JSON 日期字串來包含 `NotAfter`屬性。 此屬性會傳回只在 `NotAfter` 日期與時間之前有效的回呼 URL。

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>使用主要或次要祕密金鑰建立 URL

當您產生或列出以要求為基礎之觸發程式的回呼 Url 時，您可以指定要用來簽署 URL 的金鑰。 若要產生以特定金鑰簽署的 URL，請使用[Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers)，例如：

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在本文中，將屬性 `KeyType` 包含為 `Primary` 或 `Secondary`。 此屬性會傳回使用指定安全金鑰簽署的 URL。

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>限制輸入 IP 位址

除了共用存取簽章（SAS），您可能會想要特別限制可以呼叫邏輯應用程式的用戶端。 例如，如果您使用 Azure API 管理來管理要求端點，您可以限制邏輯應用程式僅接受來自 API 管理實例之 IP 位址的要求。

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>限制 Azure 入口網站中的輸入 IP 範圍

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定] 底下，選取 [工作流程設定]。

1. 在 [存取控制設定] > [允許的輸入 IP 位址] 底下，選取 [特定 IP 範圍]。

1. 在 [觸發程序的 IP 範圍] 底下，指定觸發程序可接受的 IP 位址範圍。

   有效的 IP 範圍使用這些格式：*x.x.x.x/x* 或 *x.x.x.x-x.x.x.x*

如果您想要讓邏輯應用程式僅觸發為嵌套邏輯應用程式，請從 [**允許的輸入 IP 位址**] 清單中，只選取 [**其他 Logic Apps**]。 此選項會將空陣列寫入您的邏輯應用程式資源。 如此一來，只有來自 Logic Apps 服務（父邏輯應用程式）的呼叫才能觸發嵌套邏輯應用程式。

> [!NOTE]
> 不論 IP 位址為何，您仍然可以使用 `/triggers/<trigger-name>/run` 透過 Azure REST API 或透過 API 管理來執行具有要求型觸發程序的邏輯應用程式。 不過，此案例仍然需要對 Azure REST API 進行驗證。 所有事件都會出現在 Azure Audit 記錄中。 請確定您已適當地設定存取控制原則。

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>限制 Azure Resource Manager 範本中的輸入 IP 範圍

如果您[使用 Resource Manager 範本自動部署邏輯應用程式](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，您可以在邏輯應用程式的資源定義中使用 `accessControl` 區段與 `triggers` 區段來指定 IP 範圍，例如：

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

### <a name="add-azure-active-directory-oauth-or-other-security"></a>新增 Azure Active Directory OAuth 或其他安全性

若要將更多授權通訊協定新增至邏輯應用程式，請考慮使用[AZURE API 管理](../api-management/api-management-key-concepts.md)服務。 此服務可協助您將邏輯應用程式公開為 API，並為任何端點提供豐富的監視、安全性、原則和檔。 API 管理可以公開您邏輯應用程式的公用或私用端點。 若要授與此端點的存取權，您可以使用[Azure Active Directory OAuth](#azure-active-directory-oauth-authentication)、[用戶端憑證](#client-certificate-authentication)或其他安全性標準來授權該端點的存取權。 當 API 管理收到要求時，服務會傳送要求至您的邏輯應用程式，也會在過程中進行任何必要的轉換或限制。 若要只讓 API 管理觸發邏輯應用程式，您可以使用邏輯應用程式的輸入 IP 範圍設定。

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>存取邏輯應用程式作業

您只能允許特定的使用者或群組執行特定工作，例如管理、編輯和觀看邏輯應用程式。 若要控制其許可權，請使用[Azure 角色型存取控制（RBAC）](../role-based-access-control/role-assignments-portal.md) ，讓您可以將自訂或內建角色指派給 Azure 訂用帳戶中的成員：

* [邏輯應用程式參與者](../role-based-access-control/built-in-roles.md#logic-app-contributor)：可讓您管理邏輯應用程式，但無法變更其存取。

* [邏輯應用程式操作員](../role-based-access-control/built-in-roles.md#logic-app-operator)：可讓您讀取、啟用及停用邏輯應用程式，但無法編輯或更新它們。

若要防止變更或刪除邏輯應用程式，您可以使用 [Azure 資源鎖定](../azure-resource-manager/management/lock-resources.md)。 這項功能可防止其他人變更或刪除生產資源。

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>執行歷程記錄資料的存取權

執行邏輯應用程式期間，所有資料都會在傳輸期間使用傳輸層安全性（TLS [）和待用](../security/fundamentals/encryption-atrest.md)[加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)。 當您的邏輯應用程式完成執行時，您可以查看該回合的歷程記錄，包括與每個動作的狀態、持續時間、輸入和輸出一起執行的步驟。 此豐富的詳細資料可讓您深入瞭解邏輯應用程式的執行方式，以及您可以從何處開始針對發生的任何問題進行疑難排解。

當您查看邏輯應用程式的執行歷程記錄時，Logic Apps 會驗證您的存取權，然後提供每次執行之要求和回應的輸入和輸出連結。 不過，針對處理任何密碼、秘密、金鑰或其他機密資訊的動作，您會想要防止其他人查看和存取該資料。 例如，如果您的邏輯應用程式從[Azure Key Vault](../key-vault/key-vault-overview.md)取得要在驗證 HTTP 動作時使用的密碼，您就會想要從 view 中隱藏該秘密。

若要在邏輯應用程式的執行歷程記錄中控制輸入和輸出的存取權，您有下列選項：

* [依 IP 位址範圍限制存取](#restrict-ip)。

  此選項可讓您根據特定 IP 位址範圍的要求來保護對執行歷程記錄的存取。

* [使用混淆來隱藏執行歷程記錄中的資料](#obfuscate)。

  在許多觸發程式和動作中，您可以從邏輯應用程式的執行歷程記錄隱藏其輸入、輸出或兩者。

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>依 IP 位址範圍限制存取

您可以限制對邏輯應用程式執行歷程記錄中的輸入和輸出的存取，如此一來，只有來自特定 IP 位址範圍的要求才能看到該資料。 例如，若要封鎖任何人存取輸入和輸出，請指定 IP 位址範圍，例如 `0.0.0.0-0.0.0.0`。 只有具備系統管理員許可權的人員可以移除此限制，這可讓您對邏輯應用程式的資料進行「即時」存取的可能性。 您可以使用 Azure 入口網站或在用於邏輯應用程式部署的 Azure Resource Manager 範本中，指定要限制的 IP 範圍。

#### <a name="restrict-ip-ranges-in-azure-portal"></a>限制 Azure 入口網站中的 IP 範圍

1. 在 Azure 入口網站的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定] 底下，選取 [工作流程設定]。

1. 在 [存取控制設定] > [允許的輸入 IP 位址] 底下，選取 [特定 IP 範圍]。

1. 在 [內容的 IP 範圍] 底下，指定可存取輸入和輸出內容的 IP 位址範圍。 

   有效的 IP 範圍使用這些格式：*x.x.x.x/x* 或 *x.x.x.x-x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>限制 Azure Resource Manager 範本中的 IP 範圍

如果您[使用 Resource Manager 範本自動部署邏輯應用程式](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，您可以在邏輯應用程式的資源定義中使用 `accessControl` 區段與 `contents` 區段來指定 IP 範圍，例如：

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

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>使用混淆來隱藏執行歷程記錄中的資料

許多觸發程式和動作都具有從邏輯應用程式的執行歷程記錄中隱藏輸入、輸出或兩者的設定。 當您使用這些設定來保護這項資料時，以下是一些[要審查的考慮事項](#obfuscation-considerations)。

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>在設計工具中保護輸入和輸出

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

   ![在邏輯應用程式設計工具中開啟邏輯應用程式](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. 在您要保護資料的觸發程式或動作上，選取省略號（ **...** ）按鈕，然後選取 [**設定**]。

   ![開啟觸發程式或動作設定](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. 開啟**安全輸入**、**保護輸出**或兩者。 完成之後，選取 [完成]。

   ![開啟安全的輸入或輸出](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   動作或觸發程式現在會在標題列中顯示鎖定圖示。

   ![動作或觸發程式標題列顯示鎖定圖示](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   代表先前動作之受保護輸出的權杖也會顯示鎖定圖示。 例如，當您從動態內容清單中選取要在動作中使用的輸出時，該權杖會顯示鎖定圖示。

   ![為受保護的輸出選取權杖](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. 邏輯應用程式執行之後，您可以查看該執行的歷程記錄。

   1. 在邏輯應用程式的 **[總覽**] 窗格中，選取您要查看的執行。

   1. 在 [**邏輯應用程式執行**] 窗格中，展開您想要檢查的動作。

      如果您選擇保護輸入和輸出，那些值現在會顯示為隱藏。

      ![執行歷程記錄中隱藏的輸入和輸出](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>在程式碼視圖中保護輸入和輸出

在基礎觸發程式或動作定義中，使用下列其中一個或兩個值來新增或更新 `runtimeConfiguration.secureData.properties` 陣列：

* `"inputs"`：保護執行歷程記錄中的輸入。
* `"outputs"`：保護執行歷程記錄中的輸出。

當您使用這些設定來保護這項資料時，以下是一些[要審查的考慮事項](#obfuscation-considerations)。

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

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>隱藏輸入和輸出時的考慮

* 當您保護觸發程式或動作的輸入或輸出時，Logic Apps 不會將受保護的資料傳送至 Azure Log Analytics。 此外，您也無法將[追蹤的屬性](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)新增至該觸發程式或動作以進行監視。

* [用於處理工作流程歷程記錄的 LOGIC APPS API](https://docs.microsoft.com/rest/api/logic/)不會傳回安全的輸出。

* 若要保護來自保護輸入或明確使用安全輸出之動作的輸出，請手動開啟該動作中的**安全輸出**。

* 請確定您在下游動作中開啟**安全輸入**或**安全輸出**，其中您預期執行歷程記錄會保護該資料。

  **安全輸出設定**

  當您以手動方式開啟觸發程式或動作中的**安全輸出**時，Logic Apps 會在執行歷程記錄中保護這些輸出。 如果下游動作明確使用這些受保護的輸出做為輸入，Logic Apps 會在執行歷程記錄中隱藏此動作的輸入，但*不會啟用*動作的「**安全輸入**」設定。

  ![受保護的輸出做為輸入和下游對大部分動作的影響](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  撰寫、剖析 JSON 和回應動作只有**安全輸入**設定。 開啟時，此設定也會隱藏這些動作的輸出。 如果這些動作明確使用上游保護的輸出做為輸入，Logic Apps 會隱藏這些動作的輸入和輸出，但*不會啟用*這些動作的**安全輸入**設定。 如果下游動作明確使用撰寫、剖析 JSON 或回應動作中的隱藏輸出做為輸入，Logic Apps*不會隱藏此下游動作的輸入或輸出*。

  ![受保護的輸出作為具有對特定動作之下游影響的輸入](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **安全輸入設定**

  當您以手動方式開啟觸發程式或動作中的**安全輸入**時，Logic Apps 會在執行歷程記錄中保護這些輸入。 如果下游動作明確使用該觸發程式或動作的可見輸出做為輸入，Logic Apps 會在執行歷程記錄中隱藏此下游動作的輸入，但*不會*在此動作中啟用**安全輸入**，也不會隱藏此動作的輸出。

  ![受保護的輸入和下游對大部分動作的影響](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  如果撰寫、剖析 JSON 和回應動作明確使用具有受保護輸入之觸發程式或動作的可見輸出，Logic Apps 會隱藏這些動作的輸入和輸出，但*不會啟用*這些動作的**安全輸入**設定。 如果下游動作明確使用撰寫、剖析 JSON 或回應動作中的隱藏輸出做為輸入，Logic Apps*不會隱藏此下游動作的輸入或輸出*。

  ![受保護的輸入和下游對特定動作的影響](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>存取參數輸入

如果您在不同的環境之間部署，請考慮將工作流程定義中的值參數化，這會根據這些環境而有所不同。 如此一來，您可以藉由使用[Azure Resource Manager 範本](../azure-resource-manager/templates/overview.md)來部署邏輯應用程式、藉由定義安全的參數來保護機密資料，以及使用[參數](../azure-resource-manager/templates/parameter-files.md)檔案，透過[範本的參數](../azure-resource-manager/templates/template-parameters.md)將該資料當做個別輸入傳遞，以避免硬式編碼的資料。

例如，如果您使用[Azure Active Directory OAuth](#azure-active-directory-oauth-authentication)來驗證 HTTP 動作，您可以定義並保護接受用於驗證之用戶端識別碼和用戶端密碼的參數。 若要在邏輯應用程式中定義這些參數，請使用邏輯應用程式的工作流程定義中的 `parameters` 區段，並 Resource Manager 範本進行部署。 若要隱藏編輯邏輯應用程式或查看執行歷程記錄時不想顯示的參數值，請使用 `securestring` 或 `secureobject` 類型來定義參數，並視需要使用編碼方式。 具有此類型的參數不會隨資源定義一起傳回，而且在部署之後查看資源時無法存取。 若要在執行時間期間存取這些參數值，請使用工作流程定義內的 `@parameters('<parameter-name>')` 運算式。 此運算式只會在執行時間評估，並由[工作流程定義語言](../logic-apps/logic-apps-workflow-definition-language.md)所描述。

> [!NOTE]
> 如果您在要求標頭或本文中使用參數，當您查看邏輯應用程式的執行歷程記錄和傳出 HTTP 要求時，可能會顯示該參數。 請確定您也會據以設定您的內容存取原則。 您也可以使用[混淆](#obfuscate)來隱藏執行歷程記錄中的輸入和輸出。 授權標頭絕對不會透過輸入或輸出來顯示。 如此一來如果該處有使用密碼，就無法擷取密碼。

如需詳細資訊，請參閱本主題中的下列各節：

* [保護工作流程定義中的參數](#secure-parameters-workflow)
* [使用混淆來隱藏執行歷程記錄中的資料](#obfuscate)

如果您[使用 Resource Manager 範本自動部署邏輯應用程式](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，您可以使用 `securestring` 和 `secureobject` 類型，定義在部署時評估的安全[範本參數](../azure-resource-manager/templates/template-parameters.md)。 若要定義範本參數，請使用您範本的最上層 `parameters` 區段，這是獨立的，不同于您的工作流程定義的 `parameters` 區段。 若要提供範本參數的值，請使用個別的[參數](../azure-resource-manager/templates/parameter-files.md)檔。

例如，如果您使用秘密，您可以定義和使用安全的範本參數，在部署時從[Azure Key Vault](../key-vault/key-vault-overview.md)取出這些秘密。 接著，您可以在參數檔案中參考金鑰保存庫和密碼。 如需詳細資訊，請參閱下列主題：

* [使用 Azure Key Vault 在部署時傳遞敏感值](../azure-resource-manager/templates/key-vault-parameter.md)
* 在本主題稍後的[Azure Resource Manager 範本中保護參數](#secure-parameters-deployment-template)

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>保護工作流程定義中的參數

若要保護邏輯應用程式的工作流程定義中的機密資訊，請使用受保護的參數，以便在您儲存邏輯應用程式之後看不到這項資訊。 例如，假設您有 HTTP 動作需要基本驗證，這會使用使用者名稱和密碼。 在工作流程定義中，`parameters` 區段會使用 `securestring` 類型來定義 `basicAuthPasswordParam` 和 `basicAuthUsernameParam` 參數。 接著，動作定義會參考 `authentication` 區段中的這些參數。

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

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Azure Resource Manager 範本中的安全參數

邏輯應用程式的[Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)有多個 `parameters` 區段。 若要保護密碼、金鑰、秘密和其他機密資訊，請使用 `securestring` 或 `secureobject` 類型，在範本層級和工作流程定義層級定義受保護的參數。 然後您可以將這些值儲存在[Azure Key Vault](../key-vault/key-vault-overview.md)中，並使用[參數](../azure-resource-manager/templates/parameter-files.md)檔案來參考金鑰保存庫和密碼。 然後，您的範本會在部署時抓取該資訊。 如需詳細資訊，請參閱[使用 Azure Key Vault 在部署時傳遞敏感值](../azure-resource-manager/templates/key-vault-parameter.md)。

以下是有關這些 `parameters` 區段的詳細資訊：

* 在範本的最上層，`parameters` 區段會定義範本在*部署*時使用之值的參數。 例如，這些值可以包含特定部署環境的連接字串。 然後您可以將這些值儲存在個別的[參數](../azure-resource-manager/templates/parameter-files.md)檔案中，這樣會讓變更這些值變得更容易。

* 在邏輯應用程式的資源定義內部，但在您的工作流程定義外，`parameters` 區段會指定工作流程定義參數的值。 在本節中，您可以使用參考範本參數的範本運算式來指派這些值。 這些運算式會在部署時進行評估。

* 在您的工作流程定義內，`parameters` 區段會定義邏輯應用程式在執行時間使用的參數。 接著，您可以在邏輯應用程式的工作流程中，使用在執行時間評估的工作流程定義運算式來參考這些參數。

這個範例範本具有多個使用 `securestring` 類型的安全參數定義：

| 參數名稱 | 描述 |
|----------------|-------------|
| `TemplatePasswordParam` | 此範本參數會接受密碼，然後傳遞至工作流程定義的 `basicAuthPasswordParam` 參數 |
| `TemplateUsernameParam` | 此範本參數會接受使用者名稱，然後傳遞至工作流程定義的 `basicAuthUserNameParam` 參數 |
| `basicAuthPasswordParam` | 針對 HTTP 動作中的基本驗證接受密碼的工作流程定義參數 |
| `basicAuthUserNameParam` | 在 HTTP 動作中接受基本驗證使用者名稱的工作流程定義參數 |
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

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>存取從邏輯應用程式呼叫的服務和系統

以下是您可以保護接收來自邏輯應用程式之呼叫或要求之端點的一些方法：

* 將驗證新增至輸出要求。

  當您使用以 HTTP 為基礎的觸發程式或動作來進行撥出電話（例如 HTTP、HTTP + Swagger 或 Webhook）時，您可以將驗證新增至邏輯應用程式所傳送的要求。 例如，您可以使用下列驗證類型：

  * [基本驗證](#basic-authentication)

  * [用戶端憑證驗證](#client-certificate-authentication)

  * [Active Directory OAuth 驗證](#azure-active-directory-oauth-authentication)

  * [受控識別驗證](#managed-identity-authentication)
  
  如需詳細資訊，請參閱本主題稍後的[將驗證新增至輸出呼叫](#add-authentication-outbound)。

* 限制來自邏輯應用程式 IP 位址的存取。

  從邏輯應用程式對端點的所有呼叫，都是源自以您的邏輯應用程式區域為基礎的特定指定 IP 位址。 您可以新增篩選，以只接受來自那些 IP 位址的要求。 若要取得這些 IP 位址，請參閱[Azure Logic Apps 的限制和](logic-apps-limits-and-config.md#configuration)設定。

* 保護內部部署系統的連接。

  Azure Logic Apps 提供與下列服務的整合，以提供安全可靠的內部部署通訊。

  * 內部部署資料閘道

    Azure Logic Apps 中的許多受控連接器都會提供與內部部署系統（例如檔案系統、SQL、SharePoint 和 DB2）的安全連線。 閘道會在加密通道上經過 Azure 服務匯流排傳送來自內部部署來源的資料。 源自閘道代理程式的所有流量都是安全輸出流量。 了解[內部部署資料閘道的運作方式](logic-apps-gateway-install.md#gateway-cloud-service)。

  * 透過 Azure API 管理連接

    [Azure API 管理](../api-management/api-management-key-concepts.md)提供內部部署連線選項，例如站對站虛擬私人網路和 ExpressRoute 整合，以對內部部署系統提供安全的 Proxy 和通訊。 從邏輯應用程式設計工具的邏輯應用程式工作流程中，您可以選取 API 管理所公開的 API，以提供對內部部署系統的快速存取。

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>將驗證新增至輸出呼叫

HTTP 和 HTTPS 端點支援各種類型的驗證。 根據您用來進行輸出呼叫的觸發程式或動作，或存取這些端點的要求，您可以從不同的驗證類型範圍中選取。 若要確定您會保護邏輯應用程式所處理的任何機密資訊，請使用受保護的參數，並視需要將資料編碼。 如需使用及保護參數的詳細資訊，請參閱[參數輸入的存取權](#secure-action-parameters)。

> [!NOTE]
> 在邏輯應用程式設計工具中，您可以在某些觸發程式和動作上隱藏**驗證**屬性，以指定驗證類型。 若要讓屬性出現在這些情況下，請在 [觸發程式] 或 [動作] 上，開啟 [**加入新的參數**] 清單，然後選取 [**驗證**]。 如需詳細資訊，請參閱[使用受控識別來驗證存取權](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)。

| 驗證類型 | 受下列產品支援 |
|---------------------|--------------|
| [基本](#basic-authentication) | Azure API 管理，Azure App Services，HTTP，HTTP + Swagger，HTTP Webhook |
| [用戶端憑證](#client-certificate-authentication) | Azure API 管理，Azure App Services，HTTP，HTTP + Swagger，HTTP Webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API 管理，Azure App 服務，Azure Functions，HTTP，HTTP + Swagger，HTTP Webhook |
| [原始](#raw-authentication) | Azure API 管理，Azure App 服務，Azure Functions，HTTP，HTTP + Swagger，HTTP Webhook |
| [受控身分識別](#managed-identity-authentication) | Azure API 管理，Azure App 服務，Azure Functions，HTTP，HTTP + Swagger，HTTP Webhook |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>基本驗證

如果 [[基本](../active-directory-b2c/secure-rest-api-dotnet-basic-auth.md)] 選項可供使用，請指定下列屬性值：

| 屬性（設計工具） | 屬性（JSON） | 必要項 | 值 | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| **驗證** | `type` | 是 | Basic | 要使用的驗證類型 |
| **使用者名稱** | `username` | 是 | <*使用者名稱*>| 用來驗證存取目標服務端點的使用者名稱 |
| **密碼** | `password` | 是 | <*密碼*> | 用來驗證存取目標服務端點的密碼 |
||||||

當您使用[安全的參數](#secure-action-parameters)來處理和保護機密資訊時（例如，在[用於自動化部署的 Azure Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中），您可以在執行時間使用運算式來存取這些參數值。 這個 HTTP 動作定義範例會將驗證 `type` 指定為 `Basic`，並使用[parameters （）](../logic-apps/workflow-definition-language-functions-reference.md#parameters)函式來取得參數值：

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

如果 [[用戶端憑證](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)] 選項可供使用，請指定下列屬性值：

| 屬性（設計工具） | 屬性（JSON） | 必要項 | 值 | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| **驗證** | `type` | 是 | **用戶端憑證** <br>或 <br>`ClientCertificate` | 用於安全通訊端層 (SSL) 用戶端憑證的驗證類型。 雖支援自我簽署憑證，但不支援 SSL 自我簽署憑證。 |
| **Pfx** | `pfx` | 是 | <*編碼-pfx-檔案內容*> | Base64 編碼的個人資訊交換 (PFX) 檔案內容 <p><p>若要將 PFX 檔案轉換成 base64 編碼格式，您可以依照下列步驟使用 PowerShell： <p>1. 將憑證內容儲存至變數： <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. 使用 `ToBase64String()` 函式來轉換憑證內容，並將該內容儲存至文字檔： <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **密碼** | `password`| 否 | <*pfx-檔案> 的密碼* | 用於存取 PFX 檔案的密碼 |
|||||

當您使用[安全的參數](#secure-action-parameters)來處理和保護機密資訊時（例如，在[用於自動化部署的 Azure Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中），您可以在執行時間使用運算式來存取這些參數值。 這個 HTTP 動作定義範例會將驗證 `type` 指定為 `ClientCertificate`，並使用[parameters （）](../logic-apps/workflow-definition-language-functions-reference.md#parameters)函式來取得參數值：

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

如需使用用戶端憑證驗證來保護服務安全的詳細資訊，請參閱下列主題：

* [在 Azure API 管理中使用用戶端憑證驗證保護 Api](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [在 Azure API 管理中使用用戶端憑證驗證來保護後端服務](../api-management/api-management-howto-mutual-certificates.md)
* [使用用戶端憑證保護您的 RESTfuL 服務](../active-directory-b2c/secure-rest-api-dotnet-certificate-auth.md)
* [應用程式驗證的憑證認證](../active-directory/develop/active-directory-certificate-credentials.md)
* [在 Azure App Service 中的應用程式程式碼中使用 SSL 憑證](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Azure Active Directory OAuth 驗證

如果[Active Directory OAuth](../active-directory/develop/about-microsoft-identity-platform.md)選項可供使用，請指定下列屬性值：

| 屬性（設計工具） | 屬性（JSON） | 必要項 | 值 | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| **驗證** | `type` | 是 | **Active Directory OAuth** <br>或 <br>`ActiveDirectoryOAuth` | 要使用的驗證類型。 Logic Apps 目前遵循[OAuth 2.0 通訊協定](../active-directory/develop/v2-overview.md)。 |
| **頒發** | `authority` | 否 | <*URL-for-authority-token-issuer*> | 提供驗證權杖的授權單位 URL。 根據預設，此值為 `https://login.windows.net`。 |
| **出租** | `tenant` | 是 | <*tenant-ID*> | Azure AD 租用戶的租用戶識別碼 |
| **目標對象** | `audience` | 是 | <*resource-to-authorize*> | 您希望用於授權的資源，例如，`https://management.core.windows.net/` |
| **用戶端識別碼** | `clientId` | 是 | <*client-ID*> | 要求授權的應用程式用戶端識別碼 |
| **認證類型** | `credentialType` | 是 | Certificate <br>或 <br>祕密 | 用戶端用來要求授權的認證類型。 這個屬性和值不會出現在邏輯應用程式的基礎定義中，而是會決定針對所選認證類型所顯示的屬性。 |
| **祕密** | `secret` | 是，但僅適用于 "Secret" 認證類型 | <*用戶端密碼*> | 要求授權用的用戶端密碼 |
| **Pfx** | `pfx` | 是，但僅適用于 "Certificate" 認證類型 | <*編碼-pfx-檔案內容*> | Base64 編碼的個人資訊交換 (PFX) 檔案內容 |
| **密碼** | `password` | 是，但僅適用于 "Certificate" 認證類型 | <*pfx-檔案> 的密碼* | 用於存取 PFX 檔案的密碼 |
|||||

當您使用[安全的參數](#secure-action-parameters)來處理和保護機密資訊時（例如，在[用於自動化部署的 Azure Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中），您可以在執行時間使用運算式來存取這些參數值。 這個 HTTP 動作定義範例會將驗證 `type` 指定為 `ActiveDirectoryOAuth`，將認證類型指定為 `Secret`，並使用[parameters （）](../logic-apps/workflow-definition-language-functions-reference.md#parameters)函式來取得參數值：

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

### <a name="raw-authentication"></a>原始驗證

如果**原始**選項可用，當您必須使用未遵循[OAuth 2.0 通訊協定](https://oauth.net/2/)的[驗證](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml)配置時，就可以使用此驗證類型。 使用此類型時，您會手動建立與傳出要求一起傳送的授權標頭值，並在觸發程式或動作中指定該標頭值。

例如，以下是遵循[OAuth 1.0 通訊協定](https://tools.ietf.org/html/rfc5849)的 HTTPS 要求範例標頭：

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

在支援原始驗證的觸發程式或動作中，指定下列屬性值：

| 屬性（設計工具） | 屬性（JSON） | 必要項 | 值 | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| **驗證** | `type` | 是 | Raw | 要使用的驗證類型 |
| **ReplTest1** | `value` | 是 | <*授權-標頭-值*> | 要用於驗證的授權標頭值 |
||||||

當您使用[安全的參數](#secure-action-parameters)來處理和保護機密資訊時（例如，在[用於自動化部署的 Azure Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中），您可以在執行時間使用運算式來存取這些參數值。 這個 HTTP 動作定義範例會將驗證 `type` 指定為 `Raw`，並使用[parameters （）](../logic-apps/workflow-definition-language-functions-reference.md#parameters)函式來取得參數值：

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

如果 [[受控識別](../active-directory/managed-identities-azure-resources/overview.md)] 選項可用，則邏輯應用程式可以使用系統指派的身分識別或*單一*手動建立的使用者指派身分識別，來驗證其他 Azure Active Directory （Azure AD）租使用者中資源的存取權，而不需要登入。 Azure 會為您管理此身分識別，並協助保護您的認證，因為您不需要提供或輪替使用祕密。 深入瞭解[支援受控識別以進行 Azure AD 驗證的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

1. 在您的邏輯應用程式可以使用受控識別之前，請遵循在[Azure Logic Apps 中使用受控識別來驗證對 Azure 資源的存取](../logic-apps/create-managed-service-identity.md)中的步驟。 這些步驟會在您的邏輯應用程式上啟用受控識別，並設定該身分識別對目標 Azure 資源的存取權。

1. 在 Azure 函式可以使用受控識別之前，請先[啟用 Azure 函式的驗證](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)。

1. 在您要使用受控識別的觸發程式或動作中，指定下列屬性值：

   | 屬性（設計工具） | 屬性（JSON） | 必要項 | 值 | 描述 |
   |---------------------|-----------------|----------|-------|-------------|
   | **驗證** | `type` | 是 | **受控身分識別** <br>或 <br>`ManagedServiceIdentity` | 要使用的驗證類型 |
   | **受控身分識別** | `identity` | 是 | * **系統指派的受控識別** <br>或 <br>`SystemAssigned` <p><p>* <*使用者指派的身分識別名稱*> | 要使用的受控識別 |
   | **目標對象** | `audience` | 是 | <*目標-資源識別碼*> | 您想要存取之目標資源的資源識別碼。 <p>例如，`https://storage.azure.com/` 會使存取權杖對所有儲存體帳戶都是有效的。 不過，您也可以指定根服務 URL，例如特定儲存體帳戶的 `https://fabrikamstorageaccount.blob.core.windows.net`。 <p>**注意**：某些觸發程式或動作中可能隱藏了**物件**屬性。 若要顯示此屬性，請在 [觸發程式] 或 [動作] 中開啟 [**加入新的參數**] 清單，然後選取 [**物件**]。 <p><p>**重要**事項：請確定此目標資源識別碼*完全符合*Azure AD 預期的值，包括任何必要的尾端斜線。 因此，所有 Azure Blob 儲存體帳戶的 `https://storage.azure.com/` 資源識別碼都需要尾端斜線。 不過，特定儲存體帳戶的資源識別碼不需要尾端斜線。 若要尋找這些資源識別碼，請參閱[支援 Azure AD 的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。 |
   |||||

   當您使用[安全的參數](#secure-action-parameters)來處理和保護機密資訊時（例如，在[用於自動化部署的 Azure Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中），您可以在執行時間使用運算式來存取這些參數值。 這個 HTTP 動作定義範例會將驗證 `type` 指定為 `ManagedServiceIdentity`，並使用[parameters （）](../logic-apps/workflow-definition-language-functions-reference.md#parameters)函式來取得參數值：

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

* [自動化 Azure Logic Apps 的部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)  
* [監視 Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [診斷邏輯應用程式失敗和問題](../logic-apps/logic-apps-diagnosing-failures.md)  
* [自動化邏輯應用程式部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
