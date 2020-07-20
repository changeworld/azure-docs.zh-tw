---
title: 建立自訂 Azure Resource Manager 角色並指派給服務主體-Azure
description: 本文提供有關如何使用 Azure CLI 在 IoT Edge 上建立自訂 Azure Resource Manager 角色並指派給服務主體以進行即時影片分析的指導方針。
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: be317ac1e86fd38c72b87734909004a64dc2938b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260511"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>建立自訂 Azure Resource Manager 角色並指派給服務主體

IoT Edge 模組實例上的即時影片分析需要有效的 Azure 媒體服務帳戶，才能正常運作。 IoT Edge 模組上的即時影片分析和 Azure 媒體服務帳戶之間的關聯性是透過一組模組對應項屬性來建立。 其中一個對應項屬性是[服務主體](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)，可讓模組實例與媒體服務帳戶進行通訊並觸發必要的作業。 為了盡可能減少潛在的誤用及/或從 edge 裝置意外產生的資料，此服務主體應具有最低許可權。

本文說明使用 Azure Cloud Shell 建立自訂 Azure Resource Manager 角色，然後用來建立服務主體的步驟。

## <a name="prerequisites"></a>必要條件  

本文的必要條件如下：

* 具有擁有者訂用帳戶的 Azure 訂用帳戶。
* 具有許可權可建立應用程式並將服務主體指派給角色的 Azure Active Directory。

檢查您的帳戶是否具有足夠的權限，最簡單的方式是透過入口網站。 請參閱[檢查必要的權限](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

## <a name="overview"></a>總覽  

我們將逐步執行建立自訂角色，並以下列順序連結服務主體的步驟：

1. 建立媒體服務帳戶（如果還沒有的話）。
1. 建立服務主體。
1. 建立具有有限許可權的自訂 Azure Resource Manager 角色。
1. 使用所建立的自訂角色「限制」服務主體許可權。
1. 執行簡單的測試，以查看我們是否能夠成功限制服務主體。
1. 捕捉將在 IoT Edge 部署資訊清單中使用的參數。

### <a name="create-a-media-services-account"></a>建立媒體服務帳戶  

如果您沒有媒體服務帳戶，請使用下列步驟來建立一個。

1. 流覽至 [ [Cloud Shell](https://shell.azure.com/)]。
1. 在 [shell] 視窗左側的下拉式選單中，選取 [Bash] 做為您的環境

    ![Bash](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. 使用下列命令範本，將您的 Azure 訂用帳戶設定為預設帳戶：
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. 建立[資源群組](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)和[儲存體帳戶](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)。
1. 現在，請在 Cloud Shell 中使用下列命令範本來建立 Azure 媒體服務帳戶：

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>建立服務主體  

我們現在會建立新的服務主體，並將其連結至您的媒體服務帳戶。

如果沒有任何驗證參數，密碼型驗證會與您的服務主體的隨機密碼搭配使用。 在 Cloud Shell 中，使用下列命令範本：

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

    請務必複製此值 (此值無法擷取)。 如果您忘記密碼，[請重設服務主體認證](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#reset-credentials)。
1. AppId 和租使用者金鑰分別會在輸出中顯示為 "AadClientId" 和 "AadTenantId"。 它們會在服務主體驗證中使用。 請記錄其值，但這些值可以隨時透過 [az ad sp list](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) 來擷取。

### <a name="create-a-custom-role-definition"></a>建立自訂角色定義  

若要建立自訂角色，以下是您應該遵循的步驟：

1. 在您的本機系統上建立角色定義 JSON 檔案，並將下列文字儲存在檔案中。 
    1. 以您的 Azure 訂用帳戶識別碼取代 < yourSubscriptionId>
    1. 此角色唯一允許的動作包括：
        * listContainerSas –協助模組列出儲存體容器 Url 與共享存取簽章（SAS），以上傳和下載資產內容。
        * 寫入資產–協助模組建立或更新任何資產
        * listEdgePolicies –列出適用于 edge 裝置的原則  
        
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

    成功執行命令時，您會看到下列輸出：
    
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

若要新增角色指派，您需要服務主體的 objectId，而您想要將您剛才建立的自訂角色指派給它。

在 Cloud Shell 中使用下列命令來取得 objectId：

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>`可以從 [[建立服務主體](#create-service-principal)] 步驟的輸出中抓取。

上述命令會列印出服務主體的 objectId。 

```
“objectId” : “<yourObjectId>”,
```

使用[az role 指派 create 命令](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)範本，將自訂角色與服務主體連結：

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

參數：

|參數|說明| 
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

### <a name="confirm-that-role-assignment-happened"></a>確認已發生角色指派

若要確認服務主體現在已與我們剛才建立的自訂角色連結，請執行下列命令：

```
az role assignment list  --assignee < objectId>
```

結果應該如下所示：

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
 
尋找 "roleDefinitionName"，並查看其值設定為 "LVAEdge User"。 

這會確認我們已連結自訂使用者角色與用於應用程式的服務主體。

### <a name="test-the-service-principal-rbac"></a>測試服務主體 RBAC  

1. 使用服務主體登入。 為此，我們需要3項資訊，讓 Azure Active Directory 授與我們可從 [[建立服務主體](#create-service-principal)] 步驟的輸出中取得的適當存取權杖：
    1. AadClientID 
    1. AadSecret
    1. AadTenantId
1. 現在，讓我們使用下列命令範本嘗試登入：
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  現在，讓我們藉由嘗試建立資源群組來確定失敗，來查看登入是否受限於具有「LVAEdge 使用者」角色的服務主體。 在 Cloud Shell 中執行下列命令︰

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    此命令應該會失敗，而且看起來會像這樣：
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>後續步驟  

請注意本文中的下列值。 您需要這些值，才能在 IoT Edge 模組上設定即時影片分析的對應項屬性，請參閱模組對應項[JSON 架構](module-twin-configuration-schema.md)。

| 本文中的變數|IoT Edge 上即時影片分析的對應項屬性名稱|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|AadTenantId |  aadTenantId|
|AadClientId    |aadServicePrincipalAppId|
