---
title: 設定驗證
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中為各種資源和工作流設置和配置身份驗證。 在服務中配置和使用身份驗證的方法有多種，從用於開發或測試目的的簡單基於 UI auth 到完整的 Azure Active Directory 服務主體身份驗證。
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: fcaa7a0c44851d6b48b40b01af4c8ec992c330b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283533"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>為 Azure 機器學習資源和工作流設置身份驗證
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何在 Azure 機器學習中為各種資源和工作流設置和配置身份驗證。 有多種方法可以對服務進行身份驗證，從用於開發或測試目的的簡單基於 UI 的身份驗證到完整的 Azure 活動目錄服務主體身份驗證。 本文還介紹了 Web 服務身份驗證的工作方式以及如何對 Azure 機器學習 REST API 進行身份驗證的差異。

本操作操作說明如何執行以下任務：

* 使用互動式 UI 身份驗證進行測試/開發
* 設置服務主體身份驗證
* 驗證到您的工作區
* 獲取用於 Azure 機器學習 REST API 的 OAuth2.0 無記名類型權杖
* 瞭解 Web 服務身份驗證

有關 Azure 機器學習中安全性和身份驗證的概述，請參閱[概念文章](concept-enterprise-security.md)。

## <a name="prerequisites"></a>Prerequisites

* 創建[Azure 機器學習工作區](how-to-manage-workspace.md)。
* [將開發環境配置為](how-to-configure-environment.md)安裝 Azure 機器學習 SDK，或使用已安裝 SDK 的[Azure 機器學習筆記本 VM。](concept-azure-machine-learning-architecture.md#compute-instance)

## <a name="interactive-authentication"></a>互動式身份驗證

此服務文件中的大多數示例使用 Jupyter 筆記本中的互動式身份驗證作為測試和演示的簡單方法。 這是測試要構建的內容的羽量級方法。 有兩個函式呼叫將自動提示您使用基於 UI 的身份驗證流。

調用函數`from_config()`將發出提示。

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

該`from_config()`函數查找包含工作區連接資訊的 JSON 檔。 您還可以使用`Workspace`建構函式顯式指定連接詳細資訊，該建構函式還會提示互動式身份驗證。 兩個調用都是等效的。

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

如果有權訪問多個租戶，則可能需要導入類並顯式定義要定位的租戶。 調用建構函式`InteractiveLoginAuthentication`也會提示您登錄類似于上述調用。

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

互動式身份驗證雖然可用於測試和學習，但無助于您構建自動化或無頭工作流。 對於使用 SDK 的自動化進程，設置服務主體身份驗證是最佳方法。

## <a name="set-up-service-principal-authentication"></a>設置服務主體身份驗證

此過程對於啟用與特定使用者登錄分離的身份驗證是必需的，這允許您在自動化工作流中對 Azure 機器學習 Python SDK 進行身份驗證。 服務主體身份驗證還允許您對[REST API 進行身份驗證](#azure-machine-learning-rest-api-auth)。

要設置服務主體身份驗證，請先在 Azure 活動目錄中創建應用註冊，然後授予應用基於角色的 ML 工作區存取權限。 完成此設置的最簡單方法是通過 Azure 門戶中的[Azure 雲外殼](https://azure.microsoft.com/features/cloud-shell/)。 登錄門戶後，按一下靠近您姓名的`>_`頁面右上角的圖示以打開 shell。

如果以前未在 Azure 帳戶中使用雲外殼，則需要創建存儲帳戶資源以存儲寫入的任何檔。 通常，此存儲帳戶每月的成本可以忽略不計。 此外，如果您以前沒有使用以下命令，請安裝機器學習擴展。

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> 您必須是訂閱上的管理員才能執行以下步驟。

接下來，運行以下命令以創建服務主體。 給它一個名字，在這種情況下**ml-auth。**

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

輸出將是類似于下面的 JSON。 記下`clientId`和`clientSecret``tenantId`欄位，因為本文中的其他步驟需要它們。

```json
{
    "clientId": "your-client-id",
    "clientSecret": "your-client-secret",
    "subscriptionId": "your-sub-id",
    "tenantId": "your-tenant-id",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com",
    "activeDirectoryGraphResourceId": "https://graph.windows.net",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net"
}
```

接下來，運行以下命令，獲取剛剛創建的服務主體的詳細資訊，使用上面`clientId`的值作為參數的`--id`輸入。

```azurecli-interactive
az ad sp show --id your-client-id
```

下面是來自命令的 JSON 輸出的簡化示例。 記下該`objectId`欄位，因為下一步需要其值。

```json
{
    "accountEnabled": "True",
    "addIns": [],
    "appDisplayName": "ml-auth",
    ...
    ...
    ...
    "objectId": "your-sp-object-id",
    "objectType": "ServicePrincipal"
}
```

接下來，使用以下命令將服務主體存取權限分配給機器學習工作區。 您將分別需要工作區名稱及其資源組名稱`-w`和`-g`參數。 對於參數`--user`，請使用上一`objectId`步驟中的值。 該`--role`參數允許您設置服務主體的訪問角色，並且通常您將使用**擁有者**或**參與者**。 兩者都具有對現有資源（如計算群集和資料存儲）的寫入存取權限，但只有**擁有者**才能預配這些資源。 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

此調用不生成任何輸出，但現在您已為工作區設置了服務主體身份驗證。

## <a name="authenticate-to-your-workspace"></a>對工作區進行身份驗證

現在，您已經啟用了服務主體身份驗證，您可以在 SDK 中對工作區進行身份驗證，而無需以使用者身份實際登錄。 使用`ServicePrincipalAuthentication`類建構函式，並使用從前面的步驟中獲得的值作為參數。 參數`tenant_id``tenantId`從上面映射到`service_principal_id`、映射到`clientId`和`service_principal_password`映射到 。 `clientSecret`

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

該`sp`變數現在包含您直接在 SDK 中使用的身份驗證物件。 通常，最好將上述 id/機密存儲在環境變數中，如以下代碼所示。

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

對於在 Python 中運行並主要使用 SDK 的自動化工作流，在大多數情況下，可以按"按"的方式使用此物件進行身份驗證。 以下代碼使用您剛剛創建的身份驗證物件對工作區進行身份驗證。

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Azure 機器學習 REST API auth

在上述步驟中創建的服務主體也可用於對 Azure 機器學習[REST API](https://docs.microsoft.com/rest/api/azureml/)進行身份驗證。 您可以使用 Azure 活動目錄[用戶端憑據授予流](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)，該流允許在自動工作流中進行無頭身份驗證的服務到服務調用。 這些示例在 Python 和 Node.js 中使用[ADAL 庫](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)實現，但您也可以使用支援 OpenID Connect 1.0 的任何開源庫。 

> [!NOTE]
> MSAL.js 是比 ADAL 更新的庫，但無法使用 MSAL.js 的用戶端憑據進行服務到服務身份驗證，因為它主要是用於綁定到特定使用者的互動式/UI 身份驗證的用戶端庫。 我們建議使用 ADAL，如下所示，使用 REST API 構建自動化工作流。

### <a name="nodejs"></a>Node.js

使用以下步驟使用 Node.js 生成身份驗證權杖。 在您的環境中，運行`npm install adal-node`。 然後，使用`tenantId`在`clientId``clientSecret`上述步驟中創建的服務主體作為以下腳本中匹配變數的值。

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

變數`tokenResponse`是包含權杖和相關中繼資料（如過期時間）的物件。 權杖的有效期為 1 小時，可以通過再次運行同一調用來檢索新權杖來刷新。 下面是一個示例回應。

```javascript
{ 
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id' 
}
```

使用`accessToken`屬性獲取身份驗證權杖。 有關如何使用權杖進行 API 呼叫的示例，請參閱[REST API 文檔](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)。

### <a name="python"></a>Python 

使用以下步驟使用 Python 生成身份驗證權杖。 在您的環境中，運行`pip install adal`。 然後，使用`tenantId`在`clientId``clientSecret`上述步驟中創建的 服務主體作為以下腳本中相應變數的值。

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

變數`token_response`是包含權杖和相關中繼資料（如過期時間）的字典。 權杖的有效期為 1 小時，可以通過再次運行同一調用來檢索新權杖來刷新。 下面是一個示例回應。

```python
{
    'tokenType': 'Bearer', 
    'expiresIn': 3599, 
    'expiresOn': '2019-12-17 19:47:15.150205', 
    'resource': 'https://management.azure.com/', 
    'accessToken': 'random-oauth-token', 
    'isMRRT': True, 
    '_clientId': 'your-client-id', 
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

用於`token_response["accessToken"]`獲取身份驗證權杖。 有關如何使用權杖進行 API 呼叫的示例，請參閱[REST API 文檔](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)。

## <a name="web-service-authentication"></a>Web 服務身份驗證

Azure 機器學習中的 Web 服務使用的身份驗證模式與上述模式不同。 對部署的 Web 服務進行身份驗證的最簡單方法是使用**基於金鑰的身份驗證**，這將生成不需要刷新的靜態承載類型身份驗證金鑰。 如果只需要對已部署的 Web 服務進行身份驗證，則無需設置服務原則身份驗證，如上所述。

部署在 Azure Kubernetes 服務上的 Web 服務預設*啟用*了基於金鑰的 auth。 預設情況下，Azure 容器實例部署的服務已*禁用*基於金鑰的 auth，但可以通過在創建 ACI Web 服務時設置`auth_enabled=True`來啟用它。 下面是創建啟用基於金鑰 au 的 ACI 部署配置的示例。

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

然後，您可以使用`Model`類在部署中使用自訂 ACI 配置。

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

要獲取 auth 鍵，`aci_service.get_keys()`請使用 。 要重新生成鍵，請使用`regen_key()`函數並傳遞**主**項或**輔助**。

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Web 服務還支援基於權杖的身份驗證，但僅適用于 Azure Kubernetes 服務部署。 有關身份驗證的其他資訊，請參閱使用 Web 服務的["方法"。](how-to-consume-web-service.md)

### <a name="token-based-web-service-authentication"></a>基於權杖的 Web 服務身份驗證

為 Web 服務啟用權杖身份驗證時，使用者必須向 Web 服務顯示 Azure 機器學習 JSON Web 權杖才能訪問它。 權杖將在指定的時間範圍後過期，需要刷新才能繼續調用。

* 預設情況下，當您部署到 Azure 庫伯奈斯服務時，權杖身份驗證將**禁用**。
* 部署到 Azure 容器實例時**不支援**權杖身份驗證。

要控制權杖身份驗證，請在`token_auth_enabled`創建或更新部署時使用 參數。

如果啟用了權杖身份驗證，`get_token`則可以使用 方法檢索 JSON Web 權杖 （JWT） 和該權杖的過期時間：

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 您需要在權杖`refresh_by`之後請求新權杖。 如果需要刷新 Python SDK 之外的權杖，一個選項是使用 REST API 與服務主體身份驗證一起定期`service.get_token()`進行調用，如前所述。
>
> 我們強烈建議您在同一區域創建 Azure 機器學習工作區，與 Azure Kubernetes 服務群集相同。 
>
> 要使用權杖進行身份驗證，Web 服務將調用創建 Azure 機器學習工作區的區域。 如果工作區的區域不可用，則即使群集與工作區位於其他區域，您也無法為 Web 服務獲取權杖。 結果是 Azure AD 身份驗證不可用，直到工作區的區域再次可用。 
>
> 此外，群集的區域和工作區區域之間的距離越大，獲取權杖所花的時間就越長。

## <a name="next-steps"></a>後續步驟

* [訓練和部署圖像分類模型](tutorial-train-models-with-aml.md)。
* [使用部署為 Web 服務的 Azure 機器學習模型](how-to-consume-web-service.md)。
