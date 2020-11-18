---
title: 教學課程 - 自訂動作和資源
description: 本教學課程說明如何使用 Azure 自訂提供者建立 Azure 受控應用程式。
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: f327749d1bdfb8cf2cba00cf4c5f68b4b2b77999
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379548"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>教學課程：使用自訂動作和資源建立受控應用程式

在本教學課程中，您會使用自訂動作和資源建立您自己的受控應用程式。 受控應用程式會包含在 `Overview` 頁面上的自訂動作、在 `Table of Content` 中顯示為個別功能表項目的自訂資源類型，以及自訂資源頁面上的自訂內容動作。

本教學課程包含下列步驟：

> [!div class="checklist"]
> * 撰寫用來建立受控應用程式執行個體的使用者介面定義檔案
> * 撰寫 [Azure 自訂提供者](../custom-providers/overview.md)、Azure 儲存體帳戶和 Azure 函式的部署範本
> * 撰寫具有自訂動作和資源的檢視定義成品
> * 部署受控應用程式定義
> * 部署受控應用程式的執行個體
> * 執行自訂動作並建立自訂資源

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您必須了解：

* 如何[建立及發佈受控應用程式定義](publish-service-catalog-app.md)。
* 如何[透過 Azure 入口網站部署服務類別目錄應用程式](deploy-service-catalog-quickstart.md)。
* 如何[為您的受控應用程式建立 Azure 入口網站使用者介面](create-uidefinition-overview.md)。
* [檢視定義成品](concepts-view-definition.md)功能。
* [Azure 自訂提供者](../custom-providers/overview.md)功能。

## <a name="user-interface-definition"></a>使用者介面定義

在本教學課程中，您會建立受控應用程式，且其受控資源群組將包含自訂提供者執行個體、儲存體帳戶和函式。 此範例中使用的 Azure 函式會實作 API，以處理動作和資源的自訂提供者作業。 Azure 儲存體帳戶可作為您自訂提供者資源的基本儲存體。

用來建立受控應用程式執行個體的使用者介面包含 `funcname` 和 `storagename` 輸入元素。 儲存體帳戶名稱函式名稱必須是全域唯一的。 根據預設，函式檔案會從[範例函式套件](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip)部署，但您可以在「createUiDefinition.json」 中新增套件連結的輸入元素來變更此行為：

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

此外也可在「createUiDefinition.json」 中新增輸出：

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

如需完整的「createUiDefinition.json」範例，請至[參考：使用者介面元素成品](reference-createuidefinition-artifact.md)。

## <a name="template-with-custom-provider"></a>自訂提供者的範本

若要使用自訂提供者建立受控應用程式執行個體，您必須在 **mainTemplate.json** 中定義名為 **public** 的自訂提供者資源和類型 **Microsoft.CustomProviders/resourceProviders**。 在該資源中，您會為您的服務定義資源類型和動作。 若要部署 Azure 函式和 Azure 儲存體帳戶執行個體，請分別定義 `Microsoft.Web/sites` 和 `Microsoft.Storage/storageAccounts` 類型的資源。

在本教學課程中，您將建立一個 `users` 資源類型、`ping` 自訂動作，以及將在 `users` 自訂資源的內容中執行的 `users/contextAction` 自訂動作。 請為每個資源類型和動作提供一個端點，且該端點應指向函式名稱已提供於 [createUiDefinition.json](#user-interface-definition) 中的函式。 針對資源類型請將 **routingType** 指定為 `Proxy,Cache`，並指定 `Proxy` 作為動作：

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

如需完整的 *mainTemplate.json* 範例，請至 [參考：部署範本成品](reference-main-template-artifact.md)。

## <a name="view-definition-artifact"></a>檢視定義成品

若要定義使用者介面，並使其包含受控應用程式中的自訂動作和自訂資源，您必須撰寫 **viewDefinition.json** 成品。 如需檢視定義成品的詳細資訊，請參閱 [Azure 受控應用程式中的檢視定義成品](concepts-view-definition.md)。

在本教學課程中，您會定義：
* 一個 [概觀]  頁面，其中包含工具列按鈕，代表具有基本文字輸入的自訂動作 `TestAction`。
* 一個 [使用者]  頁面，代表自訂資源類型 `users`。
* [使用者]  頁面中會在類型 `users` 的自訂資源內容中執行的自訂資源動作 `users/contextAction`。

下列範例顯示 [概觀] 頁面的檢視組態：

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

下列範例包含具有自訂資源動作的 [使用者] 資源頁面組態：

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

如需完整的 *viewDefinition.json* 範例，請至 [參考：檢視定義成品](reference-view-definition-artifact.md)。

## <a name="managed-application-definition"></a>受控應用程式定義

將下列受控應用程式成品封裝到 zip 封存檔，並將其上傳至儲存體：

* createUiDefinition.json
* mainTemplate.json
* viewDefinition.json

所有檔案都必須位於根層級。 含有成品的套件可儲存在任何儲存體中，例如 GitHub Blob 或 Azure 儲存體帳戶 Blob。 以下是可將應用程式套件上傳至儲存體帳戶的指令碼： 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

執行下列 Azure CLI 指令碼，或依照 Azure 入口網站中的步驟，部署服務類別目錄受控應用程式定義：

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在 Azure 入口網站中，選取 [所有服務]  。 在資源清單中輸入 **受控應用程式中心**，並加以選取。
2. 在 [受控應用程式中心]  上選擇 [服務類別目錄應用程式定義]  ，然後按一下 [新增]  。 
    
    ![新增服務類別目錄](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. 提供用來建立服務類別目錄定義的值：

    * 提供服務類別目錄定義的唯一 **名稱**、**顯示名稱** 和 *描述* (選擇性)。
    * 選取 [訂用帳戶]  、[資源群組]  ，以及將在其中建立應用程式定義的 [位置]  。 您可以使用 zip 套件所使用的相同資源群組，或建立新的資源群組。
    * 針對 [套件檔案 URI]  ，提供您在先前的步驟中建立之 zip 檔案的路徑。

    ![提供值](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. 當您來到 [驗證和鎖定層級] 區段時，選取 [新增授權]  。

    ![新增授權](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. 選取 Azure Active Directory 群組來管理資源，然後選取 [確定]  。

   ![新增授權群組](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. 當您已提供所有值時，選取 [建立]  。

   ![建立受控應用程式定義](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>受控應用程式執行個體

部署受控應用程式定義時，請執行下列指令碼，或依照 Azure 入口網站中的步驟，使用自訂提供者部署您的受控應用程式執行個體：

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在 Azure 入口網站中，選取 [所有服務]  。 在資源清單中輸入 **受控應用程式中心**，並加以選取。
2. 在 [受控應用程式中心]  上選擇 [服務類別目錄應用程式]  ，然後按一下 [新增]  。 

    ![新增受控應用程式](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. 在 [服務類別目錄應用程式]  頁面的搜尋方塊中，輸入服務類別目錄定義的顯示名稱。 選取在先前的步驟中建立的定義，然後按一下 [建立]  。

    ![選取服務類別目錄](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. 提供用來從服務類別目錄定義建立受控應用程式執行個體的值：

    * 選取 [訂用帳戶]  、[資源群組]  ，以及將在其中建立應用程式執行個體的 [位置]  。
    * 提供唯一的 Azure 函式名稱和 Azure 儲存體帳戶名稱。

    ![應用程式設定](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. 通過驗證後，按一下 [確定]  以部署受控應用程式的執行個體。 
    
    ![部署受控應用程式](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>自訂動作和資源

在部署服務目錄應用程式執行個體之後，您會有兩個新的資源群組。 第一個資源群組 `applicationGroup` 包含受控應用程式的執行個體，第二個資源群組 `managedResourceGroup` 會保存受控應用程式的資源，包括 **自訂提供者**。

![應用程式資源群組](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

您可以移至受控應用程式執行個體，並執行 [概觀] 頁面中的 **自訂動作**、在 [使用者] 頁面中建立 **使用者** 自訂資源，以及在自訂資源上執行 **自訂內容動作**。

* 移至 [概觀] 頁面，然後按一下 [Ping 動作] 按鈕：

![執行自訂動作](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* 移至 [使用者] 頁面，然後按一下 [新增] 按鈕。 提供用來建立資源及提交表單的輸入：

![顯示從 [使用者] 所選取 [新增] 按鈕的螢幕擷取畫面。](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* 移至 [使用者] 頁面，選取 [使用者] 資源，然後按一下 [自訂內容動作]：

![顯示已選取自訂內容動作的螢幕擷取畫面。](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>尋求協助

如果您對 Azure 受控應用程式有任何疑問，請嘗試在[堆疊溢位](https://stackoverflow.com/questions/tagged/azure-managedapps)提問。 類似的問題可能已有人詢問和回答，因此請先查看再張貼問題。 新增標記 `azure-managedapps` 以取得快速回應！

## <a name="next-steps"></a>後續步驟

若要將您的受控應用程式發佈至 Azure Marketplace，請參閱 [Marketplace 中 Azure 受控應用程式](../../marketplace/partner-center-portal/create-new-azure-apps-offer.md)。

深入了解 [Azure 自訂提供者](../custom-providers/overview.md)。
