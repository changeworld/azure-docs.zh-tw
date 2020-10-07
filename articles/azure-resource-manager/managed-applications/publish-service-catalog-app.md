---
title: 發佈服務類別目錄受控應用程式
description: 示範如何建立 Azure 受控應用程式，以供組織成員使用。
author: tfitzmac
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 04/14/2020
ms.author: tomfitz
ms.openlocfilehash: 342fa722d704933f22cec00a46d11ccc38fc6e4d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91650641"
---
# <a name="quickstart-create-and-publish-a-managed-application-definition"></a>快速入門：建立及發佈受控應用程式定義

本快速入門將簡介如何使用 [Azure 受控應用程式](overview.md)。 您可以建立及發佈受控應用程式，以供組織成員使用。

若要將受控應用程式發佈至您的服務類別目錄，您必須：

* 建立範本，該範本會定義要與受控應用程式一起部署的資源。
* 部署受控應用程式時，定義入口網站的使用者介面元素。
* 建立包含必要範本檔案的 _.zip_ 套件。
* 決定需要存取使用者訂用帳戶中的資源群組之使用者、群組或應用程式。
* 建立指向 _.zip_ 套件並要求存取身分識別的受控應用程式定義。

## <a name="create-the-arm-template"></a>建立 ARM 範本

每個受控應用程式定義都包含名為 _mainTemplate.json_ 的檔案。 您可以在其中定義要部署的 Azure 資源。 此範本與一般 ARM 範本不同。

建立名為 _mainTemplate.json_ 的檔案。 名稱會區分大小寫。

將下列 JSON 新增至檔案。 它會定義用來建立儲存體帳戶的參數，並且指定儲存體帳戶的屬性。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountNamePrefix": {
      "type": "string"
    },
    "storageAccountType": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageEndpoint": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
    }
  }
}
```

儲存 _mainTemplate.json_ 檔案。

## <a name="define-your-create-experience"></a>定義您的建立體驗

身為發行者，您可以定義用來建立受控應用程式的入口網站體驗。 _createUiDefinition.json_ 檔案會產生入口網站介面。 您可以使用 [控制元素](create-uidefinition-elements.md) 定義使用者提供每個參數輸入的方式，包括下拉式清單、文字方塊和密碼方塊。

建立名為 _createUiDefinition.json_ 的檔案 (此名稱會區分大小寫)

將下列啟動器 JSON 新增至檔案，並加以儲存。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "storageConfig",
        "label": "Storage settings",
        "subLabel": {
          "preValidation": "Configure the infrastructure settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Storage settings",
        "elements": [
          {
            "name": "storageAccounts",
            "type": "Microsoft.Storage.MultiStorageAccountCombo",
            "label": {
              "prefix": "Storage account name prefix",
              "type": "Storage account type"
            },
            "defaultValue": {
              "type": "Standard_LRS"
            },
            "constraints": {
              "allowedTypes": [
                "Premium_LRS",
                "Standard_LRS",
                "Standard_GRS"
              ]
            }
          }
        ]
      }
    ],
    "outputs": {
      "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
      "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
      "location": "[location()]"
    }
  }
}
```

如需詳細資訊，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。

## <a name="package-the-files"></a>封裝檔案

將兩個檔案新增至名為 _app.zip_ 的 _.zip_ 檔案。 這兩個檔案必須位於 _.zip_ 檔案的根層級。 如果您將這些檔案放在資料夾中，當建立受控應用程式時，您會收到錯誤，指出必要的檔案不存在。

將套件上傳至可從中取用它的可存取位置。 您需要提供儲存體帳戶的唯一名稱。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name storageGroup -Location eastus

$storageAccount = New-AzStorageAccount `
  -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent `
  -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name storageGroup --location eastus

az storage account create \
    --name mystorageaccount \
    --resource-group storageGroup \
    --location eastus \
    --sku Standard_LRS \
    --kind StorageV2

az storage container create \
    --account-name mystorageaccount \
    --name appcontainer \
    --public-access blob

az storage blob upload \
    --account-name mystorageaccount \
    --container-name appcontainer \
    --name "app.zip" \
    --file "D:\myapplications\app.zip"

```

---

## <a name="create-the-managed-application-definition"></a>建立受控應用程式定義

### <a name="create-an-azure-active-directory-user-group-or-application"></a>建立 Azure Active Directory 使用者群組或應用程式

下一個步驟是選取要代表客戶管理資源的使用者群組、使用者或應用程式。 此身分識別會根據指派的角色，取得受控資源群組的權限。 角色可以是任何內建的 Azure 角色，例如擁有者或參與者。 若要建立新的 Active Directory 使用者群組，請參閱[在 Azure Active Directory 中建立群組和新增成員](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

您需要使用者群組的物件識別碼，以便用於管理資源。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
groupid=$(az ad group show --group mygroup --query objectId --output tsv)
```

---

### <a name="get-the-role-definition-id"></a>取得角色定義識別碼

接下來，您需要的角色定義識別碼，是您想要授與使用者、使用者群組或應用程式存取權的 Azure 內建角色。 您通常會使用「擁有者」或「參與者」或「讀取者」角色。 下列命令會顯示如何取得「擁有者」角色的角色定義識別碼：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
ownerid=$(az role definition list --name Owner --query [].name --output tsv)
```

---

### <a name="create-the-managed-application-definition"></a>建立受控應用程式定義

如果您尚未有可儲存受控應用程式定義的資源群組，請立即建立一個：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

---

現在，建立受控應用程式定義資源。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
blob=$(az storage blob url --account-name mystorageaccount --container-name appcontainer --name app.zip --output tsv)

az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$groupid:$ownerid" \
  --package-file-uri "$blob"
```

---

命令完成時，您的資源群組中會有受控應用程式定義。

上述範例中使用的部分參數：

* **資源群組**：資源群組的名稱，其中會建立受控應用程式定義。
* **鎖定層級**：放在受控資源群組的鎖定類型。 它可以避免客戶在此資源群組上執行非預期的作業。 目前唯一支援的鎖定等級是 ReadOnly。 指定 ReadOnly 時，客戶只能讀取受控資源群組中存在的資源。 獲取受控資源群組存取權的發行者身分識別免除鎖定。
* **authorizations**：描述用來授與權限給受控資源群組的主體識別碼及角色定義識別碼。 它是以 `<principalId>:<roleDefinitionId>` 格式來指定。 如果需要多個值，請在表單中指定它們：`<principalId1>:<roleDefinitionId1>,<principalId2>:<roleDefinitionId2>`。 值之間以逗號分隔。
* **套件檔案 URI**：該位置有包含必要檔案的 _.zip_ 套件。

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>以您自備的儲存體存放受控應用程式定義

您可以選擇將受控應用程式定義儲存在您於在建立期間提供的儲存體帳戶中，以全面管理其位置和存取權而符合法規需求。

> [!NOTE]
> 「自備儲存體」僅支援受控應用程式定義的 ARM 範本或 REST API 部署。

### <a name="select-your-storage-account"></a>選取您的儲存體帳戶

您必須[建立儲存體帳戶](../../storage/common/storage-account-create.md)，用以保存用於服務類別目錄的受控應用程式定義。

複製儲存體帳戶的資源識別碼。 稍後在部署定義時將會用到這項資料。

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>在您的儲存體帳戶中設定「設備資源提供者」的角色指派

您必須先為**設備資源提供者**角色授與參與者權限，使其能夠將定義檔案寫入至您儲存體帳戶的容器，受控應用程式定義才可部署至您的儲存體帳戶。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的儲存體帳戶。
1. 選取 [存取控制 (IAM)]，以顯示儲存體帳戶的存取控制設定。 選取 [角色指派] 索引標籤，以查看角色指派的清單。
1. 在 [新增角色指派] 視窗中，選取 [參與者] 角色。
1. 在 [存取權指派對象為] 欄位中，選取 [Azure AD 使用者、群組或服務主體]。
1. 在 [選取] 底下搜尋**設備資源提供者**角色，並加以選取。
1. 儲存角色指派。

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>使用 ARM 範本部署受控應用程式定義

使用下列 ARM 範本，在以您自己的儲存體帳戶儲存及維護定義檔案的服務類別目錄，將您已封裝的受控應用程式部署為新的受控應用程式定義：

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "applicationName": {
      "type": "string",
      "metadata": {
        "description": "Managed Application name"
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "definitionStorageResourceID": {
      "type": "string",
      "metadata": {
        "description": "Storage account resource ID for where you're storing your definition"
      }
    },
    "_artifactsLocation": {
      "type": "string",
      "metadata": {
        "description": "The base URI where artifacts required by this template are located."
      }
    }
  },
  "variables": {
    "lockLevel": "None",
    "description": "Sample Managed application definition",
    "displayName": "Sample Managed application definition",
    "managedApplicationDefinitionName": "[parameters('applicationName')]",
    "packageFileUri": "[parameters('_artifactsLocation')]",
    "defLocation": "[parameters('definitionStorageResourceID')]",
    "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
    "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Solutions/applicationDefinitions",
      "apiVersion": "2020-08-21-preview",
      "name": "[variables('managedApplicationDefinitionName')]",
      "location": "[parameters('location')]",
      "properties": {
        "lockLevel": "[variables('lockLevel')]",
        "description": "[variables('description')]",
        "displayName": "[variables('displayName')]",
        "packageFileUri": "[variables('packageFileUri')]",
        "storageAccountId": "[variables('defLocation')]"
      }
    }
  ],
  "outputs": {}
}
```

我們已將名為 `storageAccountId` 的新屬性新增至 `applicationDefinitions` 屬性，並提供您要用來儲存定義的儲存體帳戶識別碼作為其值：

您可以確認應用程式定義檔案是否已儲存於您在名為 `applicationDefinitions` 的容器中提供的儲存體帳戶內。

> [!NOTE]
> 若要提升安全性，您可以建立受控應用程式定義，並將其儲存在[已啟用加密的 Azure 儲存體帳戶 Blob](../../storage/common/storage-service-encryption.md) 中。 定義內容會透過儲存體帳戶的加密選項進行加密。 只有具備檔案權限的使用者，才能在服務類別目錄中看到該定義。

## <a name="make-sure-users-can-see-your-definition"></a>確定使用者可以看到您的定義

您可以存取受控應用程式定義，但您想確保您組織中的其他使用者可以存取它。 至少在定義上將讀者角色授與給他們。 他們可能已從訂用帳戶或資源群組繼承此存取層級。 若要檢查誰可以存取定義和新增使用者或群組，請參閱[使用 Azure 入口網站新增或移除 Azure 角色指派](../../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>後續步驟

您已發佈受控應用程式定義。 現在，深入了解如何部署該定義的執行個體。

> [!div class="nextstepaction"]
> [快速入門：部署服務目錄應用程式](deploy-service-catalog-quickstart.md)
