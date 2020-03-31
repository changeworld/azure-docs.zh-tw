---
title: 加密部署資料
description: 瞭解為容器實例資源保留的資料加密，以及如何使用客戶管理的金鑰加密資料
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: ad232c5d9df9f6bfae3a79dbd72e2c68143be949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080355"
---
# <a name="encrypt-deployment-data"></a>加密部署資料

在雲中運行 Azure 容器實例 （ACI） 資源時，ACI 服務會收集和保留與容器相關的資料。 當此資料保留在雲中時，ACI 會自動加密這些資料。 此加密可保護您的資料，以説明滿足組織的安全和合規性承諾。 ACI 還允許您選擇使用自己的金鑰加密此資料，從而更好地控制與 ACI 部署相關的資料。

## <a name="about-aci-data-encryption"></a>關於 ACI 資料加密 

使用 256 位 AES 加密對 ACI 中的資料進行加密和解密。 它已針對所有 ACI 部署啟用，您無需修改部署或容器就可以利用此加密。 這包括有關部署的中繼資料、環境變數、傳入容器的金鑰以及容器停止後保留的日誌，以便您仍然可以看到它們。 加密不會影響容器組性能，並且加密沒有額外費用。

## <a name="encryption-key-management"></a>加密金鑰管理

您可以依賴 Microsoft 管理的金鑰來加密容器資料，也可以使用自己的金鑰管理加密。 下表比較了這些選項： 

|    |    微軟管理的金鑰     |     客戶管理的金鑰     |
|----|----|----|
|    加密/解密操作    |    Azure    |    Azure    |
|    金鑰存儲    |    微軟金鑰存儲    |    Azure 金鑰保存庫    |
|    關鍵輪換責任    |    Microsoft    |    客戶    |
|    金鑰存取    |    僅限微軟    |    微軟， 客戶    |

文檔的其餘部分介紹了使用金鑰（客戶管理的金鑰）加密 ACI 部署資料所需的步驟。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>使用客戶管理的金鑰加密資料

### <a name="create-service-principal-for-aci"></a>為 ACI 創建服務主體

第一步是確保[Azure 租戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)分配了一個服務主體，用於向 Azure 容器實例服務授予許可權。 

> [!IMPORTANT]
> 為了運行以下命令並成功創建服務主體，請確認您具有在租戶中創建服務主體的許可權。
>

以下 CLI 命令將在 Azure 環境中設置 ACI SP：

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

運行此命令的輸出應顯示已設置使用"顯示名稱"："Azure 容器實例服務"的服務主體。

如果您無法成功創建服務主體：
* 確認您具有在租戶中執行此操作的許可權
* 檢查租戶中是否存在要部署到 ACI 的服務主體。 您可以通過運行`az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9`並使用該服務主體來執行此操作

### <a name="create-a-key-vault-resource"></a>建立金鑰保存庫資源

使用[Azure 門戶](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) [CLI](https://docs.microsoft.com/azure/key-vault/quick-create-cli)或[PowerShell](https://docs.microsoft.com/azure/key-vault/quick-create-powershell)創建 Azure 金鑰保存庫。 

對於金鑰保存庫的屬性，請使用以下準則： 
* 名稱：唯一的名稱是必要項。 
* 訂用帳戶：選擇訂用帳戶。
* 在"資源組"下，選擇現有資源組，或創建新資源組並輸入資源組名稱。
* 在 [位置] 下拉式功能表中選擇位置。
* 您可以將其他選項保留為其預設值，或根據其他要求進行選擇。

> [!IMPORTANT]
> 當使用客戶管理的金鑰加密 ACI 部署範本時，建議在金鑰保存庫"虛刪除"和"不清除"上設置以下兩個屬性。 預設情況下，這些屬性不會啟用，但可以使用 PowerShell 或 Azure CLI 在新金鑰保存庫或現有金鑰保存庫上啟用。

### <a name="generate-a-new-key"></a>生成新金鑰 

創建金鑰保存庫後，導航到 Azure 門戶中的資源。 在資源邊欄選項卡的左側導航功能表上，在"設置"下，按一下 **"鍵**"。 在"鍵"的視圖中，按一下"生成/導入"以生成新金鑰。 根據您的要求，使用此金鑰和任何其他首選項的任何唯一名稱。 

![生成新金鑰](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>設置訪問策略

創建新的訪問策略，允許 ACI 服務訪問您的金鑰。

* 生成金鑰後，返回金鑰保存庫資源邊欄選項卡，在"設置"下，按一下"**訪問策略**"。
* 在金鑰保存庫的"訪問策略"頁上，按一下"**添加訪問策略**"。
* 將*金鑰許可權*設置為包括**獲取**和**取消包裝金鑰**![集金鑰許可權](./media/container-instances-encrypt-data/set-key-permissions.png)
* 對於*選擇主體*，請選擇**Azure 容器實例服務**
* 按一下底部**的"添加**" 

訪問策略現在應顯示在金鑰保存庫的訪問策略中。

![新增存取原則](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>修改 JSON 部署範本

> [!IMPORTANT]
> 使用客戶管理的金鑰加密部署資料，可在當前推出的最新 API 版本 （2019-12-01） 中提供。在部署範本中指定此 API 版本。 如果對此有任何疑問，請聯繫 Azure 支援。

設置金鑰保存庫金鑰和訪問策略後，將以下屬性添加到 ACI 部署範本。 瞭解有關使用教程中的範本部署 ACI 資源的更多資訊[：使用資源管理器範本部署多容器組](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)。 
* 在`resources`下`apiVersion`，`2019-12-01`設置為 。
* 在部署範本的容器組屬性部分下，添加 包含`encryptionProperties`以下值的 。
  * `vaultBaseUrl`：金鑰保存庫的 DNS 名稱，可在門戶中金鑰保存庫資源的概述邊欄選項卡上找到
  * `keyName`：前面生成的金鑰的名稱
  * `keyVersion`：金鑰的當前版本。 這可以通過按一下金鑰本身找到（在金鑰保存庫資源的"設置"部分的"鍵"下）
* 在容器組屬性下，添加`sku`值 的屬性`Standard`。 該`sku`屬性在 API 版本 2019-12-01 中是必需的。

以下範本程式碼片段顯示了用於加密部署資料的其他屬性：

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

下面是一個完整的範本，改編自教程中的範本[：使用資源管理器範本部署多容器組](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)。 

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

如果您在桌面上創建並編輯了範本檔，則可以通過將檔拖入雲外殼目錄來將其上載到雲外殼目錄。 

使用 [az group create][az-group-create] 命令來建立資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az group deployment create][az-group-deployment-create] 命令來部署範本。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

在幾秒內，您應該會從 Azure 收到首次回應。 部署完成後，ACI 服務保留的所有資料都將使用您提供的金鑰進行加密。

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
