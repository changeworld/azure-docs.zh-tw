---
title: 保護 Azure Functions
description: 了解如何讓您的函式程式碼更安全地在 Azure 中執行，以免遭受常見的攻擊。
ms.date: 4/13/2020
ms.topic: conceptual
ms.openlocfilehash: 9bec32c4c3d8005ef0d3c9fc5732785a5fa19a0c
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87850707"
---
# <a name="securing-azure-functions"></a>保護 Azure Functions

在許多方面，對無伺服器函式進行安全開發、部署和作業的規劃，與任何 Web 架構或雲端裝載的應用程式非常相似。 [Azure App Service](../app-service/index.yml) 提供適用於函式應用程式的裝載基礎結構。 此文章可為您提供執行函式程式碼的安全性策略，以及 App Service 如何協助保護您的函式。 

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

如需遵循 [Azure 安全性基準測試](../security/benchmarks/overview.md)的一組安全性建議，請參閱[適用於 Azure Functions 的 Azure 安全性基準](security-baseline.md)。

## <a name="secure-operation"></a>安全作業 

此節將引導您以盡可能安全的方式來設定和執行函式應用程式。 

### <a name="security-center"></a>資訊安全中心

資訊安全中心會在入口網站中與您的函式應用程式整合。 其免費提供可能與設定相關之安全性弱點的快速評量。 在專用方案中執行的函式應用程式也可以使用資訊安全中心的即時安全性功能，但需支付額外費用。 若要深入了解，請參閱[保護您的 Azure App Service Web 應用程式和 API](../security-center/security-center-app-services.md)。 

### <a name="log-and-monitor"></a>記錄和監視

偵測攻擊的方法之一是透過活動監視活動和記錄分析。 Functions 會與 Application Insights 整合，以收集函式應用程式的記錄、效能和錯誤資料。 Application Insights 會自動偵測效能異常，並隨附強大的分析工具，以協助您診斷問題及了解如何使用您的函式。 若要深入了解，請參閱[監視 Azure Functions](functions-monitoring.md)。

Functions 也會與 Azure 監視器記錄整合，讓您能夠將函式應用程式記錄與系統事件合併，以便分析。 您可以使用診斷設定，將函式平台記錄和計量的資料流匯出設定到您選擇的目的地，例如 Logs Analytics 工作區。 若要深入了解，請參閱[使用 Azure 監視器記錄監視 Azure Functions](functions-monitor-log-analytics.md)。 

針對企業層級的威脅偵測和回應自動化，將您的記錄和事件串流處理到 Logs Analytics 工作區。 接著，您可以將 Azure Sentinel 連線到此工作區。 若要深入了解，請參閱[什麼是 Azure Sentinel](../sentinel/overview.md)。  

如需更多對於可檢視性的安全性建議，請參閱[適用於 Azure Functions 的 Azure 安全性基準](security-baseline.md#logging-and-monitoring)。 

### <a name="require-https"></a>需要 HTTPS

根據預設，用戶端可以使用 HTTP 或 HTTPS 連線到函式端點。 您應該將 HTTP 重新導向至 HTTPS，因為 HTTPS 會使用 SSL/TLS 通訊協定來提供安全連線，這兩者都已加密且通過驗證。 若要了解做法，請參閱[強制使用 HTTPS](../app-service/configure-ssl-bindings.md#enforce-https)。

當您需要 HTTPS 時，應該也需要最新的 TLS 版本。 若要了解做法，請參閱[強制使用 TLS 版本](../app-service/configure-ssl-bindings.md#enforce-tls-versions)。

如需詳細資訊，請參閱[保護連線 (TSL)](../app-service/overview-security.md#https-and-certificates)。

### <a name="function-access-keys"></a>函式存取金鑰

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

#### <a name="system-key"></a>系統金鑰 

特定的延伸模組可能需要系統管理的金鑰，才能存取 Webhook 端點。 系統金鑰是針對由內部元件所呼叫之延伸模組特定的函式端點所設計。 例如，[事件方格觸發程序](functions-bindings-event-grid-trigger.md)要求訂用帳戶在呼叫觸發程序端點時使用系統金鑰。 Durable Functions 也會使用系統金鑰來呼叫[長期工作延伸模組 API](durable/durable-functions-http-api.md)。 

系統金鑰的範圍會由此延伸模組決定，但通常適用於整個函式應用程式。 系統金鑰只能由特定的延伸模組建立，而且您無法明確設定其值。 如同其他金鑰，您可以從入口網站或使用金鑰 API 來產生新的金鑰值。

#### <a name="keys-comparison"></a>金鑰比較

下表將比較各種存取金鑰的用途：

| 動作                                        | 影響範圍                    | 有效金鑰         |
|-----------------------------------------------|--------------------------|--------------------|
| 執行函式                            | 特定函式        | 函式           |
| 執行函式                            | 任何函式             | 函式或主機   |
| 呼叫管理員端點                        | 函式應用程式             | 主機 (僅限主機) |
| 呼叫長期工作延伸模組 API              | 函式應用程式<sup>1</sup> | 系統<sup>2</sup> |
| 呼叫延伸模組特定的 Webhook (內部) | 函式應用程式<sup>1</sup> | 系統<sup>2</sup> |

<sup>1</sup>由延伸模組決定的範圍。  
<sup>2</sup>由延伸模組設定的特定名稱。

若要深入了解存取金鑰，請參閱 [HTTP 觸發程序繫結文章](functions-bindings-http-webhook-trigger.md#obtaining-keys)。


#### <a name="secret-repositories"></a>秘密存放庫

根據預設，金鑰會儲存在設定所提供之帳戶的 Blob 儲存體容器中 `AzureWebJobsStorage` 。 您可以使用特定的應用程式設定來覆寫此行為，並將金鑰儲存在不同的位置。

|Location  |設定 | 值 | 描述  |
|---------|---------|---------|---------|
|不同的儲存體帳戶     |  `AzureWebJobsSecretStorageSas`       | `<BLOB_SAS_URL` | 根據提供的 SAS URL，將金鑰儲存在第二個儲存體帳戶的 Blob 儲存體中。 金鑰會先經過加密，再使用您的函式應用程式特有的秘密加以儲存。 |
|檔案系統   | `AzureWebJobsSecretStorageType`   |  `files`       | 金鑰會保存在檔案系統上，並在使用您的函式應用程式特有的秘密儲存之前加密。 |
|Azure 金鑰保存庫  | `AzureWebJobsSecretStorageType`<br/>`AzureWebJobsSecretStorageKeyVaultName` | `keyvault`<br/>`<VAULT_NAME>` | 保存庫必須具有對應至主機資源之系統指派受控識別的存取原則。 存取原則應將下列秘密許可權授與識別： `Get` 、 `Set` 、 `List` 和 `Delete` 。 <br/>在本機執行時，會使用開發人員身分識別，而且設定必須位於檔案中的[local.settings.js](functions-run-local.md#local-settings-file)。 | 
|Kubernetes 秘密  |`AzureWebJobsSecretStorageType`<br/>`AzureWebJobsKubernetesSecretName` (選擇性) | `kubernetes`<br/>`<SECRETS_RESOURCE>` | 只有在 Kubernetes 中執行函數執行時間時才支援。 若 `AzureWebJobsKubernetesSecretName` 未設定，則會將存放庫視為唯讀。 在此情況下，必須在部署前產生值。 當部署至 Kubernetes 時，Azure Functions Core Tools 會自動產生值。|

### <a name="authenticationauthorization"></a>驗證/授權

雖然函式金鑰可以針對不必要的存取提供一些緩和措施，但真正保護函式端點的唯一方式，就是對存取您函式的用戶端實作正面驗證。 接著，您可以根據身分識別進行授權決策。  

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

### <a name="permissions"></a>權限

如同任何應用程式或服務，目標是以最低的可能權限來執行您的函式應用程式。 

#### <a name="user-management-permissions"></a>使用者管理權限

函數支援內建的[azure 角色型存取控制， (AZURE RBAC) ](../role-based-access-control/overview.md)。 函式支援的 Azure 角色為[參與者](../role-based-access-control/built-in-roles.md#contributor)、[擁有](../role-based-access-control/built-in-roles.md#owner)者和[讀者](../role-based-access-control/built-in-roles.md#owner)。 

權限會在函式應用層級生效。 您必須具備參與者角色，才能執行大部分函式應用程式層級的工作。 只有擁有者角色可以刪除函式應用程式。 

#### <a name="organize-functions-by-privilege"></a>依權限組織函式 

儲存在應用程式設定中的連接字串和其他認證，會為函式應用程式中的所有函式提供與相關聯資源中相同的權限集。 請考慮藉由將未使用那些認證的函式移至個別函式應用程式，來將可存取特定認證的函式數目降至最低。 您一律可以使用[函式鏈結](/learn/modules/chain-azure-functions-data-using-bindings/)之類的技術，在不同函式應用程式的函式之間傳遞資料。  

#### <a name="managed-identities"></a>受控身分識別

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

如需詳細資訊，請參閱[如何使用 App Service 和 Azure Functions 的受控身分識別](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json)。

#### <a name="restrict-cors-access"></a>限制 CORS 存取

[跨原始來源資源共用 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) \(英文\) 是一種方式，讓在另一個網域中執行的 Web 應用程式能夠對您的 HTTP 觸發程序端點提出要求。 App Service 提供內建支援，可在 HTTP 要求中處理所需的 CORS 標題。 CORS 規則定義於函式應用程式層級上。  

儘管使用萬用字元來讓所有網站都能存取您的端點，這一點很吸引人。 但是，這會失去 CORS 的用意，也就是協助防止跨網站指令碼攻擊。 請改為針對必須存取您端點之每個 Web 應用程式的網域，新增個別的 CORS 項目。 

### <a name="managing-secrets"></a>管理密碼 

若要能夠連線到各種服務且資源必須執行您的程式碼，函式應用程式就必須能夠存取祕密 (例如連接字串和服務金鑰)。 此節說明如何儲存函式所需的祕密。

絕對不要將祕密儲存於函式程式碼中。 

#### <a name="application-settings"></a>應用程式設定

根據預設，您會將函式應用程式和繫結所使用的連接字串及祕密儲存為應用程式設定。 這讓您的函式程式碼及該函式所使用的各種繫結能夠使用這些認證。 應用程式設定 (金鑰) 名稱可用來擷取實際值，也就是祕密。 

例如，每個函式應用程式都需要一個相關聯且由執行階段使用的儲存體帳戶。 根據預設，此儲存體帳戶的連線會儲存在名為 `AzureWebJobsStorage` 的應用程式設定中。

應用程式設定和連接字串會以加密方式儲存在 Azure 中。 只有在應用程式啟動時，才會將其解密，然後插入至應用程式的處理序記憶體。 加密金鑰會定期輪替。 如果您想要改為管理祕密的安全儲存體，應用程式設定就應該改為參考 Azure Key Vault。 

#### <a name="key-vault-references"></a>Key Vault 參考

儘管對大部分函式而言應用程式設定就已足夠，但您可能想要在多個服務之間共用相同的祕密。 在此情況下，重複儲存祕密會導致更多潛在的弱點。 一種更安全的方法是使用集中式祕密儲存服務，並使用對此服務的參考，而不是祕密本身。      

[Azure Key Vault](../key-vault/general/overview.md) 是提供集中式祕密管理的服務，可完整控制存取原則和稽核歷程記錄。 您可以在應用程式設定中，使用 Key Vault 參考來取代連接字串或金鑰。 若要深入了解，請參閱[使用 App Service 和 Azure Functions 的 Key Vault 參考](../app-service/app-service-key-vault-references.md?toc=%2fazure%2fazure-functions%2ftoc.json)。

### <a name="set-usage-quotas"></a>設定使用量配額

請考慮在取用量方案中執行之函式上設定使用量配額。 當您在函式應用程式中為函式的總執行次數設定每日 GB-s 限制時，一旦達到該限制，就會停止執行。 這可能有助於緩和惡意程式碼執行您函式的情況。 若要了解如何估計函式的取用量，請參閱[估計取用量方案成本](functions-consumption-costs.md)。 

### <a name="data-validation"></a>資料驗證

函式所使用的觸發程序和繫結不會提供任何額外的資料驗證。 您的程式碼必須驗證接收自觸發程序或輸入繫結的所有資料。 如果上游服務遭到入侵，您不希望未經驗證的輸入流經您的函式。 例如，如果您的函式將來自 Azure 儲存體佇列的資料儲存於關聯式資料庫中，您就必須驗證該資料並將命令參數化，以避免受到 SQL 插入式攻擊。 

請勿假設傳入您函式的資料已經過驗證或處理。 確認寫入至輸出繫結的資料有效，也是個不錯的主意。 

### <a name="handle-errors"></a>處理錯誤

儘管這看似基本，但在您的函式中撰寫良好的錯誤處理很重要。 未處理的錯誤會往上累積到主機，並由執行階段處理。 不同的繫結會以不同方式進行錯誤的處理。 若要深入了解，請參閱 [Azure Functions 錯誤處理](functions-bindings-error-pages.md)。

### <a name="disable-remote-debugging"></a>停用遠端偵錯

除非您正主動對函式進行偵錯，否則，需確定已停用遠端偵錯。 您可以在入口網站內函式應用程式 [設定] 的 [一般設定] 索引標籤中停用遠端偵錯。 

### <a name="restrict-cors-access"></a>限制 CORS 存取

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

請勿在允許的來源清單中使用萬用字元， 而是改為列出您希望從中取得要求的特定網域。

### <a name="store-data-encrypted"></a>儲存加密的資料

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="secure-deployment"></a>安全部署

Azure Functions 工具整合，可讓您輕鬆地將區域函式專案程式碼發佈至 Azure。 在考慮 Azure Functions 拓撲的安全性時，請務必了解部署的運作方式。   

### <a name="deployment-credentials"></a>部署認證

App Service 部署需要一組部署認證。 這些部署認證可用來保護您的函式應用程式部署。 部署認證會由 App Service 平台管理，並在待用時加密。 

有兩種部署認證：

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

目前，部署認證不支援 Key Vault。 若要深入了解管理部署認證，請參閱[設定 Azure App Service 的部署認證](../app-service/deploy-configure-credentials.md)。

### <a name="disable-ftp"></a>停用 FTP

根據預設，每個函式應用程式均已啟用 FTP 端點。 您可以使用部署認證來存取 FTP 端點。 

不建議使用 FTP 來部署函式程式碼。 FTP 部署是手動的，而且需要您同步處理觸發程序。 若要深入了解，請參閱 [FTP 部署](functions-deployment-technologies.md#ftp)。 

當您不打算使用 FTP 時，應該在入口網站中加以停用。 如果您選擇使用 FTP，則應該[強制使用 FTPS](../app-service/deploy-ftp.md#enforce-ftps)。

### <a name="secure-the-scm-endpoint"></a>保護 SCM 端點

每個函式應用程式都有一個由進階工具 (Kudu) 服務所使用的對應 `scm` 服務端點，可供部署和其他 App Service [網站延伸模組](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) \(英文\) 使用。 函式應用程式的 SCM 端點一律是格式為 `https://<FUNCTION_APP_NAME.scm.azurewebsites.net>` 的 URL。 當您使用網路隔離來保護函式時，也必須考慮此端點。 

藉由擁有個別的 SCM 端點，您可以控制在虛擬網路中隔離或執行之函式應用程式的部署和其他進階工具功能。 SCM 端點支援基本驗證 (使用部署認證)，以及透過您 Azure 入口網站認證進行的單一登入。 若要深入了解，請參閱[存取 Kudu 服務](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) \(英文\)。 

### <a name="continuous-security-validation"></a>持續安全性驗證

由於必須將安全性視為開發程序中的每個步驟，因此，也可以在持續部署環境中實作安全性驗證。 這有時稱為 DevSecOps。 針對您的部署管線使用 Azure DevOps，可讓您在部署程序中整合驗證。 如需詳細資訊，請參閱[了解如何將持續安全性驗證新增至您的 CI/CD 管線](/azure/devops/migrate/security-validation-cicd-pipeline)。  

## <a name="network-security"></a>網路安全性

限制對函式應用程式的網路存取，可讓您控制可存取函式端點的人員。 Functions 會利用 App Service 基礎結構，讓您的函式可以在不使用可透過網際網路路由傳送之位址的情況下存取資源，或限制對函式端點的網際網路存取。 若要深入了解這些網路功能選項，請參閱 [Azure Functions 網路功能選項](functions-networking-options.md)。

### <a name="set-access-restrictions"></a>設定存取限制

存取限制可讓您定義允許/拒絕規則的清單，以控制對應用程式的流量。 規則會依照優先順序進行評估。 如果未定義任何規則，則您的應用程式將接受來自任意位址的流量。 若要深入了解，請參閱 [Azure App Service 存取限制 #](../app-service/app-service-ip-restrictions.md?toc=%2fazure%2fazure-functions%2ftoc.json)。

### <a name="private-site-access"></a>私人網站存取

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

### <a name="deploy-your-function-app-in-isolation"></a>以隔離方式部署函式應用程式

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

### <a name="use-a-gateway-service"></a>使用閘道服務

閘道服務 (例如 [Azure 應用程式閘道](../application-gateway/overview.md)和 [Azure Front Door](../frontdoor/front-door-overview.md)) 可讓您設定 Web 應用程式防火牆 (WAF)。 WAF 規則可用來監視或封鎖偵測到的攻擊，為您的函式提供額外保護層。 若要設定 WAF，您的函式應用程式必須在 ASE 中或使用私人端點 (預覽) 來執行。 若要深入了解，請參閱[使用私人端點](../app-service/networking/private-endpoint.md)。    

## <a name="next-steps"></a>後續步驟

+ [適用於 Azure Functions 的 Azure 安全性基準](security-baseline.md)
+ [Azure Functions 診斷](functions-diagnostics.md)
