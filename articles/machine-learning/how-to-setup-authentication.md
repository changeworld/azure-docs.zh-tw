---
title: 設定驗證
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 中設定各種資源和工作流程的驗證。 在服務內設定和使用驗證的方法有很多種，範圍從簡單的 UI 型驗證，到用於開發或測試用途，到完整 Azure Active Directory 服務主體驗證。
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: fcaa7a0c44851d6b48b40b01af4c8ec992c330b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79283533"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>設定 Azure Machine Learning 資源和工作流程的驗證
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何在 Azure Machine Learning 中設定各種資源和工作流程的驗證。 有多種方法可以向服務進行驗證，範圍從簡單的 UI 型驗證，到用於開發或測試目的，到完整 Azure Active Directory 服務主體驗證。 本文也會說明 web 服務驗證運作方式的差異，以及如何向 Azure Machine Learning REST API 進行驗證。

本 how to 說明如何執行下列工作：

* 使用互動式 UI 驗證進行測試/開發
* 設定服務主體驗證
* 向您的工作區進行驗證
* 取得適用于 Azure Machine Learning REST API 的 OAuth 2.0 持有人類型權杖
* 瞭解 web 服務驗證

如需 Azure Machine Learning 內的安全性和驗證的一般總覽，請參閱[概念一文](concept-enterprise-security.md)。

## <a name="prerequisites"></a>先決條件

* 建立[Azure Machine Learning 工作區](how-to-manage-workspace.md)。
* [設定您的開發環境](how-to-configure-environment.md)以安裝 Azure Machine Learning SDK，或使用已安裝 SDK 的[Azure Machine Learning 筆記本 VM](concept-azure-machine-learning-architecture.md#compute-instance) 。

## <a name="interactive-authentication"></a>互動式驗證

這項服務的檔中，大部分的範例都是使用 Jupyter 筆記本中的互動式驗證，做為測試和示範的簡單方法。 這是測試您所建立之內容的輕量方式。 有兩個函式呼叫會自動提示您使用以 UI 為基礎的驗證流程。

`from_config()`呼叫函式將會發出提示。

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

`from_config()`函式會尋找 JSON 檔案，其中包含您的工作區連接資訊。 您也可以使用此`Workspace`函式明確地指定連接詳細資料，這也會提示您進行互動式驗證。 這兩個呼叫都是相等的。

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

如果您有多個租使用者的存取權，您可能需要匯入類別，並明確定義您的目標租使用者。 呼叫的函式`InteractiveLoginAuthentication`也會提示您登入，類似上述的呼叫。

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

對於測試和學習很有用，互動式驗證將無法協助您建立自動或無周邊的工作流程。 設定服務主體驗證是使用 SDK 之自動化程式的最佳方法。

## <a name="set-up-service-principal-authentication"></a>設定服務主體驗證

若要啟用與特定使用者登入分離的驗證，這是必要的程式，可讓您向自動化工作流程中的 Azure Machine Learning Python SDK 進行驗證。 服務主體驗證也可讓您向[REST API 進行驗證](#azure-machine-learning-rest-api-auth)。

若要設定服務主體驗證，請先在 Azure Active Directory 中建立應用程式註冊，然後將您的應用程式角色型存取權授與您的 ML 工作區。 完成此安裝程式最簡單的方式是透過 Azure 入口網站中的[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 。 在您登入入口網站之後，請`>_`按一下您名稱附近頁面右上方的圖示，以開啟命令介面。

如果您的 Azure 帳戶中尚未使用 cloud shell，您必須建立儲存體帳戶資源，以儲存任何寫入的檔案。 一般而言，此儲存體帳戶會產生可忽略的每月成本。 此外，如果您先前未使用下列命令，請安裝機器學習服務延伸模組。

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> 您必須是訂用帳戶的系統管理員，才能執行下列步驟。

接下來，執行下列命令來建立服務主體。 為它命名，在此案例中為**ml 驗證**。

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

輸出將會是與下列類似的 JSON。 請記下`clientId`、 `clientSecret`和`tenantId`欄位，因為本文中的其他步驟將會用到它們。

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

接下來，執行下列命令以取得您剛建立之服務主體的詳細資料，並使用`clientId`上述的值做為`--id`參數的輸入。

```azurecli-interactive
az ad sp show --id your-client-id
```

以下是來自命令的 JSON 輸出的簡單範例。 請記下`objectId`欄位，因為您需要它的值，才能進行下一個步驟。

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

接下來，使用下列命令，將您的服務主體存取權指派給您的 machine learning 工作區。 您將會分別需要您的`-w`工作區名稱，以及其和`-g`參數的資源組名。 針對`--user`參數，使用上一個`objectId`步驟中的值。 `--role`參數可讓您設定服務主體的存取角色，一般而言，您將使用**擁有**者或**參與者**。 兩者都具有現有資源（如計算叢集和資料存放區）的寫入權限，但只有**擁有**者可以布建這些資源。 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

此呼叫不會產生任何輸出，但您現在已為您的工作區設定服務主體驗證。

## <a name="authenticate-to-your-workspace"></a>向您的工作區進行驗證

現在您已啟用服務主體驗證，您可以在 SDK 中驗證您的工作區，而不需要實際以使用者身分登入。 使用`ServicePrincipalAuthentication`類別的函式，並使用您從上一個步驟中所獲得的值做為參數。 `tenant_id`參數會對應至`tenantId`上述的， `service_principal_id`並對應`clientId`至， `service_principal_password`並對應`clientSecret`至。

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

`sp`變數現在會保留您在 SDK 中直接使用的驗證物件。 一般來說，將上述使用的識別碼/秘密儲存在環境變數中是個不錯的主意，如下列程式碼所示。

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

針對在 Python 中執行並使用 SDK 的自動化工作流程，您可以在大部分的情況下使用此物件來進行驗證。 下列程式碼會使用您剛才建立的 auth 物件，向您的工作區進行驗證。

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Azure Machine Learning REST API auth

在上述步驟中建立的服務主體也可以用來向 Azure Machine Learning [REST API](https://docs.microsoft.com/rest/api/azureml/)進行驗證。 您會使用 Azure Active Directory 的[用戶端認證授與流程](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)，允許服務對服務呼叫在自動化工作流程中進行無周邊驗證。 範例是以 Python 和 node.js 中的[ADAL 程式庫](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)來執行，但是您也可以使用任何支援 OpenID connect 1.0 的開放原始碼程式庫。 

> [!NOTE]
> MSAL 是比 ADAL 更新的程式庫，但您無法使用 MSAL 的用戶端認證來進行服務對服務驗證，因為它主要是適用于與特定使用者系結互動/UI 驗證的用戶端程式庫。 我們建議使用如下所示的 ADAL，以 REST API 建立自動化工作流程。

### <a name="nodejs"></a>Node.js

使用下列步驟，使用 node.js 來產生驗證權杖。 在您的環境中`npm install adal-node`，執行。 然後，從您`tenantId`在`clientId`上述步驟`clientSecret`中建立的服務主體，使用您的、和，做為下列腳本中相符變數的值。

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

變數`tokenResponse`是物件，其中包含權杖和相關聯的中繼資料，例如到期時間。 權杖的有效時間為1小時，而且可以重新整理，方法是再次執行相同的呼叫以抓取新的權杖。 以下是範例回應。

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

使用`accessToken`屬性來提取驗證權杖。 如需如何使用權杖進行 API 呼叫的範例，請參閱[REST API 檔](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)。

### <a name="python"></a>Python 

使用下列步驟，使用 Python 來產生驗證權杖。 在您的環境中`pip install adal`，執行。 然後，從您`tenantId`在`clientId`上述步驟`clientSecret`中建立的服務主體，使用您的、和，做為下列腳本中適當變數的值。

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

變數`token_response`是字典，其中包含權杖和相關聯的中繼資料，例如到期時間。 權杖的有效時間為1小時，而且可以重新整理，方法是再次執行相同的呼叫以抓取新的權杖。 以下是範例回應。

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

使用`token_response["accessToken"]`提取驗證權杖。 如需如何使用權杖進行 API 呼叫的範例，請參閱[REST API 檔](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)。

## <a name="web-service-authentication"></a>Web 服務驗證

Azure Machine Learning 中的 Web 服務所使用的驗證模式與上述內容不同。 向已部署的 web 服務進行驗證的最簡單方式是使用以**金鑰為基礎的驗證**，它會產生不需要重新整理的靜態承載類型驗證金鑰。 如果您只需要對已部署的 web 服務進行驗證，則不需要設定服務主體驗證，如上所示。

Azure Kubernetes Service 上部署的 Web 服務預設會*啟用*金鑰型驗證。 Azure 容器實例已部署的服務預設會*停用*金鑰型驗證，但是您可以在建立 ACI `auth_enabled=True`web 服務時設定來啟用它。 以下範例會建立已啟用金鑰型驗證的 ACI 部署設定。

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

接著，您可以使用`Model`類別，在部署中使用自訂 ACI 設定。

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

若要提取驗證金鑰，請`aci_service.get_keys()`使用。 若要重新產生金鑰，請`regen_key()`使用函數並傳遞**主要**或**次要**。

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Web 服務也支援以權杖為基礎的驗證，但僅適用于 Azure Kubernetes Service 部署。 如需驗證的詳細資訊，請參閱使用 web 服務的操作[說明](how-to-consume-web-service.md)。

### <a name="token-based-web-service-authentication"></a>以權杖為基礎的 web 服務驗證

當您啟用 web 服務的權杖驗證時，使用者必須向 web 服務出示 Azure Machine Learning 的 JSON Web 權杖才能存取它。 權杖會在指定的時間範圍之後過期，而且需要重新整理才能繼續進行呼叫。

* 當您部署到 Azure Kubernetes Service 時，**預設會停用**權杖驗證。
* 當您部署至 Azure 容器實例時，**不支援**權杖驗證。

若要控制權杖驗證，請`token_auth_enabled`在建立或更新部署時使用參數。

如果已啟用權杖驗證，您可以使用`get_token`方法來取出 JSON Web TOKEN （JWT）和該權杖的到期時間：

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 您必須在權杖的`refresh_by`時間之後要求新的權杖。 如果您需要在 Python SDK 以外重新整理權杖，其中一個選項是使用 REST API 搭配服務主體驗證定期進行`service.get_token()`呼叫，如先前所述。
>
> 強烈建議您在與 Azure Kubernetes Service 叢集相同的區域中建立您的 Azure Machine Learning 工作區。 
>
> 若要使用權杖進行驗證，web 服務會呼叫您的 Azure Machine Learning 工作區建立所在的區域。 如果您的工作區區域無法使用，您將無法提取 web 服務的權杖，即使您的叢集與工作區位於不同的區域也一樣。 結果是，除非您的工作區區域再次可供使用，否則 Azure AD 驗證無法使用。 
>
> 此外，您的叢集區域和工作區區域之間的距離愈大，提取權杖所需的時間就越長。

## <a name="next-steps"></a>後續步驟

* [定型及部署影像分類模型](tutorial-train-models-with-aml.md)。
* [使用部署為 web 服務的 Azure Machine Learning 模型](how-to-consume-web-service.md)。
