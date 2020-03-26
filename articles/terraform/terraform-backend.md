---
title: 教學課程 - 在 Azure 儲存體中儲存 Terraform 狀態
description: 在 Azure 儲存體中儲存 Terraform 狀態的簡介。
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 1cc475e5070b21a7ea96585f2183c07d258acdc5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75708419"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>教學課程：在 Azure 儲存體中儲存 Terraform 狀態

Terraform 狀態用來與 Terraform 設定協調已部署的資源。 Terraform 可透過狀態來了解要新增、更新或刪除哪些 Azure 資源。 根據預設，Terraform 狀態會在執行 `terraform apply` 命令時儲存在本機中。 基於下列原因，此設定並不是理想的做法：

- 本機狀態在小組或共同作業環境中無法正常運作。
- Terraform 狀態可能包含機密資訊。
- 將狀態儲存在本機會增加意外刪除的機率。

Terraform 支援在遠端存放庫中保存狀態。 這類支援後端的其中一個是 Azure 儲存體。 本文件將說明如何針對此用途來設定及使用 Azure 儲存體。

## <a name="configure-storage-account"></a>設定儲存體帳戶

您使用 Azure 儲存體作為後端之前，您必須建立儲存體帳戶。 可以透過 Azure 入口網站、PowerShell、Azure CLI 或 Terraform 本身建立儲存體帳戶。 使用下列範例，透過 Azure CLI 設定儲存體帳戶。

```azurecli
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

記下儲存體帳戶名稱、容器名稱和儲存體存取金鑰。 設定遠端狀態時需要使用這些值。

## <a name="configure-state-back-end"></a>設定狀態後端

系統會在您執行 `terraform init` 命令時設定 Terraform 狀態後端。 設定狀態後端需要下列資料：

- **storage_account_name**：Azure 儲存體帳戶的名稱。
- **container_name**：Blob 容器的名稱。
- **金鑰**：要建立的狀態存放區檔案名稱。
- **access_key**：儲存體存取金鑰

這些值都可以在 Terraform 設定檔或命令列中指定。 我們建議您針對 `access_key` 值使用環境變數。 使用環境變數可防止系統將金鑰寫入至磁碟。

使用 Azure 儲存體存取金鑰的值建立名為 `ARM_ACCESS_KEY` 的環境變數。

```bash
export ARM_ACCESS_KEY=<storage access key>
```

若要進一步保護 Azure 儲存體帳戶存取金鑰，請將它儲存在 Azure Key Vault 中。 接著便可使用類似下列的命令來設定環境變數。 如需 Azure Key Vault 的詳細資訊，請參閱 [Azure Key Vault 文件](../key-vault/quick-create-cli.md)。

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

若要將 Terraform 設定為使用後端，必須執行下列步驟：
- 包含類型為 `azurerm` 的 `backend` 設定區塊。
- 將 `storage_account_name` 值新增至設定區塊。
- 將 `container_name` 值新增至設定區塊。
- 將 `key` 值新增至設定區塊。

下列範例會設定 Terraform 後端及建立 Azure 資源群組。

```hcl
terraform {
  backend "azurerm" {
    resource_group_name   = "tstate"
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

使用下列步驟來初始化設定：

1. 執行 `terraform init` 命令。
1. 執行 `terraform apply` 命令。

您現在可以在 Azure 儲存體 Blob 中找到狀態檔案。

## <a name="state-locking"></a>狀態鎖定

Azure 儲存體 Blob 會在執行寫入狀態的任何作業之前自動鎖定。 此模式可防止並行的狀態作業，並行作業可能會導致損毀。 

如需詳細資訊，請參閱 Terraform 文件中的[「狀態鎖定」](https://www.terraform.io/docs/state/locking.html)。

透過 Azure 入口網站或其他 Azure 管理工具檢查 Blob 時，您可以看到鎖定狀態。

![帶有鎖定圖示的 Azure Blob](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>待用加密

儲存在 Azure Blob 中的資料會先經過加密，再進行保存。 如有需要，Terraform 會從後端擷取狀態，並將其儲存在本機記憶體中。 使用此模式時，狀態永遠不會寫入本機磁碟。

如需 Azure 儲存體加密的詳細資訊，請參閱[待用資料的 Azure 儲存體服務加密](../storage/common/storage-service-encryption.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [深入了解如何使用 Azure 中的 Terraform](/azure/terraform)
