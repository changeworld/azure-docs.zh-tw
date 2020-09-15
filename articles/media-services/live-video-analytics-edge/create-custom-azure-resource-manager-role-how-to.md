---
title: 建立自訂 Azure Resource Manager 角色並指派給服務主體-Azure
description: 本文提供的指引，說明如何建立自訂 Azure Resource Manager 角色，並在 IoT Edge 使用 Azure CLI 指派給 Live Video Analytics 的服務主體。
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: a780ecbbf2530b15984c596281c4aa7e4f5dd520
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526573"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>建立自訂 Azure Resource Manager 角色並指派給服務主體

IoT Edge 模組實例上的即時影片分析需要使用中的 Azure 媒體服務帳戶，才能正常運作。 IoT Edge 模組上的即時影片分析和 Azure 媒體服務帳戶之間的關聯性，是透過一組模組對應項屬性來建立。 其中一個對應項屬性是 [服務主體](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) ，可讓模組實例與媒體服務帳戶進行通訊，並觸發必要的作業。 若要將潛在的誤用及/或意外的資料暴露從邊緣裝置降至最低，此服務主體應具有最少的許可權。

本文說明使用 Azure Cloud Shell 建立自訂 Azure Resource Manager 角色的步驟，然後用來建立服務主體。

## <a name="prerequisites"></a>必要條件  

本文的必要條件如下：

* 具有擁有者訂用帳戶的 Azure 訂用帳戶。
* 有許可權可建立應用程式，並將服務主體指派給角色的 Azure Active Directory。

檢查您的帳戶是否具有足夠的權限，最簡單的方式是透過入口網站。 請參閱[檢查必要的權限](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)。

## <a name="overview"></a>概觀  

我們將探討建立自訂角色的步驟，並以下列順序連結至服務主體：

1. 建立媒體服務帳戶（如果您還沒有的話）。
1. 建立服務主體。
1. 建立具有有限許可權的自訂 Azure Resource Manager 角色。
1. 使用建立的自訂角色「限制」服務主體許可權。
1. 執行簡單的測試，以查看我們是否能夠成功限制服務主體。
1. 捕捉將用於 IoT Edge 部署資訊清單中的參數。

### <a name="create-a-media-services-account"></a>建立媒體服務帳戶  

如果您沒有媒體服務帳戶，請使用下列步驟建立一個。

1. 流覽至 [Cloud Shell](https://shell.azure.com/)。
1. 在 [shell] 視窗左側的下拉式清單中，選取 [Bash] 作為您的環境

    ![螢幕 capturs 會顯示從 shell 視窗選取的 Bash。](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. 使用下列命令範本，將您的 Azure 訂用帳戶設定為預設帳戶：
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. 建立 [資源群組](/cli/azure/group?view=azure-cli-latest#az-group-create) 和 [儲存體帳戶](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)。
1. 現在，在 Cloud Shell 中使用下列命令範本來建立 Azure 媒體服務帳戶：

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>建立服務主體  

我們現在會建立新的服務主體，並將它連結至您的 Media Service 帳戶。

如果沒有任何驗證參數，密碼型驗證就會與您的服務主體的隨機密碼搭配使用。 在 Cloud Shell 中，使用下列命令範本：

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

此命令會產生如下所示的回應：

```
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "<yourServicePrincipalPassword>",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": " <yourAMSAccountName >",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "Central US",
  "ResourceGroup": " <yourResouceGroup >",
  "SubscriptionId": "<yourSubscriptionId>"
}

```
1. 具有密碼驗證之服務主體的輸出會包含密碼金鑰，在此案例中為 "AadSecret" 參數。 

    請務必複製此值 (此值無法擷取)。 如果您忘記密碼，[請重設服務主體認證](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#reset-credentials)。
1. AppId 和租使用者金鑰分別會以 "AadClientId" 和 "AadTenantId" 的形式出現在輸出中。 它們用於服務主體驗證。 請記錄其值，但這些值可以隨時透過 [az ad sp list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) 來擷取。

### <a name="create-a-custom-role-definition"></a>建立自訂角色定義  

若要建立自訂角色，以下是您應該遵循的步驟：

1. 在您的本機系統上建立角色定義 JSON 檔案，並將下列文字儲存在檔案中。 
    1. 以您的 Azure 訂用帳戶識別碼取代 < >yoursubscriptionid>
    1. 此角色唯一允許的動作為：
        * listContainerSas –協助模組列出具有共用存取簽章的儲存體容器 Url (SAS) ，以便上傳及下載資產內容。
        * 寫入資產–協助模組建立或更新任何資產
        * listEdgePolicies –列出套用至 edge 裝置的原則  
        
        ```
        {
          "Name": "LVAEdge User",
          "IsCustom": true,
          "Description": "Can create assets, view list of containers and view Edge policies",
          "Actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action"
          ],
          "NotActions": [],
          "DataActions": [],
          "NotDataActions": [],
          "AssignableScopes": [
            "/subscriptions/<yourSubscriptionId>"
          ]
        }
        ```  
          
1. 建立之後，請執行下列命令範本，在訂用帳戶中建立新的角色定義：
    
    ```
    az role definition create --role-definition "<location of the Role Definition JSON file >"
    ```

    成功執行命令之後，您會看到下列輸出：
    
    ```
    {
      "assignableScopes": [
      "/subscriptions/<yourSubscriptionId>"
      ],
      "description": "Can create assets, view list of containers and view Edge policies",
      "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<unique name>",
      "name": "<unique name>",
      "permissions": [
        {
          "actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action",
          ],
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": " LVAEdge User ",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

### <a name="create-role-assignment"></a>建立角色指派  

若要加入角色指派，您需要您想要指派您剛才建立之自訂角色的服務主體 objectId。

在 Cloud Shell 中使用下列命令來取得 objectId：

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>` 可以從「 [建立服務主體](#create-service-principal) 」步驟的輸出中取出。

上述命令會列印出服務主體的 objectId。 

```
“objectId” : “<yourObjectId>”,
```

使用 [az role 指派 create 命令](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) 範本將自訂角色連結至服務主體：

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

參數：

|參數|描述| 
|---|---|
|--角色 |自訂角色名稱或識別碼。 在我們的案例中：「LVAEdge 使用者」。|
|--受託人-物件識別碼|您將使用之服務主體的物件識別碼。|

結果看起來會像這樣：

```
{
  "canDelegate": null,
  "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleAssignments/<yourCustomRoleId>",
  "name": "<yourCustomRoleId>",
  "principalId": "<yourServicePrincipalId>",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<yourRoleDefinitionId>",
  "scope": "/subscriptions/<yourSubscriptionId>",
  "type": "Microsoft.Authorization/roleAssignments"
} 
```

### <a name="confirm-that-role-assignment-happened"></a>確認角色指派發生

若要確認服務主體現在已連結到剛才建立的自訂角色，請執行下列命令：

```
az role assignment list  --assignee < objectId>
```

結果看起來應該像這樣：

```
[
  {
    "canDelegate": null,
    "id": "/subscriptions/xxx/providers/Microsoft.Authorization/roleAssignments/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "principalId": "<yourServicePrincipalID>",
    "principalName": "<yourServicePrincipalName>",
    "principalType": "ServicePrincipal",
    "roleDefinitionId": "/subscriptions/xxx/providers/Microsoft.Authorization/roleDefinitions/zzz",
    "roleDefinitionName": "LVAEdge User",
    "scope": "/subscriptions/<yourSubscription ID>",
    "type": "Microsoft.Authorization/roleAssignments
  }
]  
```
 
尋找 "roleDefinitionName"，並查看其值是否設定為 "LVAEdge User"。 

這會確認我們已將自訂使用者角色連結至應用程式所使用的服務主體。

### <a name="test-the-service-principal-rbac"></a>測試服務主體 RBAC  

1. 使用服務主體登入。 為此，我們需要三項資訊，讓 Azure Active Directory 將可從「 [建立服務主體](#create-service-principal) 」步驟的輸出中取得的適當存取權杖授與我們：
    1. AadClientID 
    1. AadSecret
    1. AadTenantId
1. 現在，讓我們嘗試使用下列命令範本登入：
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  現在，讓我們來看看登入是否限制為具有「LVAEdge 使用者」角色的服務主體，方法是嘗試建立資源群組，以確保其失敗。 在 Cloud Shell 中執行下列命令︰

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    此命令應該會失敗，並看起來像這樣：
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>後續步驟  

請注意本文中的下列值。 您需要這些值，才能在 IoT Edge 模組上設定即時影片分析的對應項屬性，請參閱模組對應項 [JSON 架構](module-twin-configuration-schema.md)。

| 本文中的變數|IoT Edge 上的即時影片分析對應項屬性名稱|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|AadTenantId |  aadTenantId|
|AadClientId    |aadServicePrincipalAppId|
