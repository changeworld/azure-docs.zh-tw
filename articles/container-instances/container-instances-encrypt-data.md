---
title: 加密部署資料
description: 瞭解針對您的容器實例資源保存的資料加密，以及如何使用客戶管理的金鑰來加密資料
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 1b73ce5c994231a1c7b2f26ad702f2ad5880ba44
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686271"
---
# <a name="encrypt-deployment-data"></a>加密部署資料

當執行 Azure 容器實例 (ACI) 雲端中的資源時，ACI 服務會收集和保存與您的容器相關的資料。 當資料保存在雲端時，ACI 會自動將此資料加密。 此加密可保護您的資料，以協助您符合組織的安全性和合規性承諾。 ACI 也可讓您選擇使用自己的金鑰來加密此資料，讓您更能控制與 ACI 部署相關的資料。

## <a name="about-aci-data-encryption"></a>關於 ACI 資料加密 

ACI 中的資料會使用256位 AES 加密進行加密和解密。 它會針對所有 ACI 部署啟用，而且您不需要修改您的部署或容器來利用此加密。 這包括有關部署的中繼資料、環境變數、傳入容器的金鑰，以及在您的容器停止後保存的記錄，讓您仍然可以看到它們。 加密不會影響您的容器群組效能，而且加密沒有額外的成本。

## <a name="encryption-key-management"></a>加密金鑰管理

您可以依賴 Microsoft 管理的金鑰來加密容器資料，也可以使用您自己的金鑰來管理加密。 下表比較這些選項： 

|    |    Microsoft 管理的金鑰     |     客戶管理的金鑰     |
|----|----|----|
|    **加密/解密作業**    |    Azure    |    Azure    |
|    **金鑰儲存體**    |    Microsoft 金鑰存放區    |    Azure Key Vault    |
|    **金鑰輪替責任**    |    Microsoft    |    客戶    |
|    **金鑰存取**    |    僅限 Microsoft    |    Microsoft，客戶    |

本檔的其餘部分涵蓋使用金鑰 (客戶管理的金鑰) 來加密 ACI 部署資料時所需的步驟。 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>使用客戶管理的金鑰來加密資料

### <a name="create-service-principal-for-aci"></a>建立 ACI 的服務主體

第一個步驟是確定您的 [azure 租](../active-directory/develop/quickstart-create-new-tenant.md) 使用者已指派服務主體，以授與 Azure 容器實例服務的許可權。 

> [!IMPORTANT]
> 若要執行下列命令並成功建立服務主體，請確認您有權在您的租使用者中建立服務主體。
>

下列 CLI 命令會在您的 Azure 環境中設定 ACI SP：

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

執行此命令的輸出應該會顯示已使用 "displayName"： "Azure Container Instance Service" 設定的服務主體。

如果您無法成功建立服務主體：
* 確認您有權在您的租使用者中這麼做
* 檢查您的租使用者中是否已有服務主體可部署至 ACI。 您可以改為執行 `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` 並使用該服務主體來執行此動作

### <a name="create-a-key-vault-resource"></a>建立金鑰保存庫資源

使用 [Azure 入口網站](../key-vault/general/quick-create-portal.md)、 [Azure CLI](../key-vault/general/quick-create-cli.md)或 [Azure PowerShell](../key-vault/general/quick-create-powershell.md)建立 Azure Key Vault。

針對金鑰保存庫的屬性，請使用下列指導方針： 
* Name：唯一名稱是必要項。 
* 訂用帳戶：選擇訂用帳戶。
* 在 [資源群組] 底下，選擇現有的資源群組，或建立新的資源群組，然後輸入資源組名。
* 在 [位置] 下拉式功能表中選擇位置。
* 您可以將其他選項保留為預設值，或根據其他需求進行挑選。

> [!IMPORTANT]
> 使用客戶管理的金鑰來加密 ACI 部署範本時，建議您在金鑰保存庫、虛刪除和請勿清除下列兩個屬性。 這些屬性預設不會啟用，但可使用 PowerShell 或 Azure CLI 在新的或現有的金鑰保存庫上啟用。

### <a name="generate-a-new-key"></a>產生新的金鑰 

建立金鑰保存庫之後，請流覽至 Azure 入口網站中的資源。 在資源分頁的左側導覽功能表上，按一下 [設定] 下的 [ **金鑰**]。 在 [索引鍵] 的 [查看] 上，按一下 [產生/匯入] 以產生新的金鑰。 針對此金鑰使用任何唯一的名稱，並根據您的需求使用任何其他喜好設定。 

![產生新的金鑰](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>設定存取原則

建立新的存取原則，以允許 ACI 服務存取您的金鑰。

* 一旦產生金鑰之後，請回到您的金鑰保存庫資源分頁，在 [設定] 底下，按一下 [ **存取原則**]。
* 在金鑰保存庫的 [存取原則] 頁面上，按一下 [ **新增存取原則**]。
* 設定 *金鑰許可權* ，以包含 **取得** 和解除包裝 **金鑰** ![ 集金鑰許可權](./media/container-instances-encrypt-data/set-key-permissions.png)
* 針對 [*選取主體*]，選取 [ **Azure 容器實例服務**]
* 按一下底部的 [ **新增** ] 

存取原則現在應該會顯示在金鑰保存庫的存取原則中。

![新增存取原則](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>修改您的 JSON 部署範本

> [!IMPORTANT]
> 使用客戶管理的金鑰來加密部署資料，可在目前推出的最新 API 版本 (2019-12-01) 中取得。在您的部署範本中指定此 API 版本。 如果您有任何問題，請與 Azure 支援人員聯繫。

設定金鑰保存庫金鑰和存取原則之後，請將下列屬性新增至 ACI 部署範本。 在 [教學課程：使用 Resource Manager 範本部署多容器群組](./container-instances-multi-container-group.md)中，深入瞭解如何使用範本部署 ACI 資源。 
* 在底下 `resources` ，將設定 `apiVersion` 為 `2019-12-01` 。
* 在部署範本的 [容器群組屬性] 區段下，新增 `encryptionProperties` 包含下列值的：
  * `vaultBaseUrl`：您可以在入口網站中金鑰保存庫資源的總覽分頁上找到金鑰保存庫的 DNS 名稱。
  * `keyName`：稍早產生的金鑰名稱
  * `keyVersion`：索引鍵的目前版本。 在金鑰保存庫資源的 [設定] 區段中，按一下 [金鑰] 下的 [金鑰]，即可找到此 () 
* 在容器群組屬性下，新增 `sku` 具有值的屬性 `Standard` 。 `sku`API 版本2019-12-01 中需要屬性。

下列範本程式碼片段顯示加密部署資料的這些額外屬性：

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

以下是從 [教學課程：使用 Resource Manager 範本部署多容器群組](./container-instances-multi-container-group.md)中範本所調整的完整範本。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "encryptionProperties": {
            "vaultBaseUrl": "https://example.vault.azure.net",
            "keyName": "acikey",
            "keyVersion": "xxxxxxxxxxxxxxxx"
        },
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>部署資源

如果您在桌面上建立和編輯範本檔案，您可以將檔案拖曳到 Cloud Shell 目錄，將它上傳到您的目錄。 

使用 [az group create][az-group-create] 命令來建立資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az deployment group create][az-deployment-group-create] 命令部署範本。

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

在幾秒內，您應該會從 Azure 收到首次回應。 部署完成後，ACI 服務保存的所有相關資料都會以您提供的金鑰進行加密。

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group/#az-deployment-group-create
