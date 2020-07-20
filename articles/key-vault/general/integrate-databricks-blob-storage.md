---
title: 使用 Azure Databricks 和 Azure Key Vault 存取 Azure Blob 儲存體
description: 在本教學課程中，您將了解如何使用儲存在 Azure Key Vault 中的秘密，從 Azure Databricks 存取 Azure Blob 儲存體
author: taytran0
ms.author: t-trtr
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: 50ea93dea62b6e4e333e58550b2750897c7e0a77
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988238"
---
# <a name="tutorial-access-azure-blob-storage-using-azure-databricks-and-azure-key-vault"></a>教學課程：使用 Azure Databricks 和 Azure Key Vault 存取 Azure Blob 儲存體

在本教學課程中，您將了解如何使用儲存在 Azure Key Vault 中的秘密，從 Azure Databricks 存取 Azure Blob 儲存體。 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 利用 Azure CLI 建立儲存體帳戶和 Blob 容器
> * 建立 Key Vault 並設定祕密
> * 建立 Azure Databricks 工作區並新增 Key Vault 秘密範圍
> * 從 Azure Databricks 工作區存取您的 Blob 容器

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

開始本教學課程之前，請先安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)。

## <a name="create-a-storage-account-and-blob-container-with-azure-cli"></a>利用 Azure CLI 建立儲存體帳戶和 Blob 容器

您必須先建立一般用途的儲存體帳戶，才能使用 blob。 如果您沒有[資源群組](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)，請在執行命令之前建立一個。 下列命令會建立並顯示儲存體容器的中繼資料。 複製**識別碼**。

```azurecli
az storage account create --name contosoblobstorage5 --resource-group contosoResourceGroup --location eastus --sku Standard_ZRS --encryption-services blob
```

![上述命令的主控台輸出。 識別碼會以綠色反白顯示，讓終端使用者可以看到此識別碼。](../media/databricks-command-output-1.png)

您必須先將[儲存體 Blob 資料參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) 角色指派給自己，才可以建立容器來上傳 blob。 在此範例中，系統會將角色指派給您先前建立的儲存體帳戶。

```azurecli
az role assignment create --role "Storage Blob Data Contributor" --assignee t-trtr@microsoft.com --scope "/subscriptions/885e24c8-7a36-4217-b8c9-eed31e110504/resourceGroups/contosoResourceGroup5/providers/Microsoft.Storage/storageAccounts/contosoblobstorage5
```

既然您已將角色指派給儲存體帳戶，就可以為您的 blob 建立容器。

```azurecli
az storage container create --account-name contosoblobstorage5 --name contosocontainer5 --auth-mode login
```

一旦建立了容器，您就可以將 blob (您選擇的檔案) 上傳至該容器。 在此範例中，會上傳含 helloworld 的 .txt 檔案。

```azurecli
az storage blob upload --account-name contosoblobstorage5 --container-name contosocontainer5 --name helloworld --file helloworld.txt --auth-mode login
```

列出容器中的 blob，以驗證容器是否具有這些 blob。

```azurecli
az storage blob list --account-name contosoblobstorage5 --container-name contosocontainer5 --output table --auth-mode login
```

![上述命令的主控台輸出。 其會顯示剛才儲存在容器中的檔案。](../media/databricks-command-output-2.png)

使用下列命令，取得儲存體容器的 **key1** 值。 複製此值。

```azurecli
az storage account keys list -g contosoResourceGroup5 -n contosoblobstorage5
```

![上述命令的主控台輸出。 key1 的值會在綠色方塊中反白顯示。](../media/databricks-command-output-3.png)

## <a name="create-a-key-vault-and-set-a-secret"></a>建立 Key Vault 並設定祕密

您將使用下列命令來建立 Key Vault。 此命令也會顯示 Key Vault 的中繼資料。 複製**識別碼** 和 **vaultUri**。

```azurecli
az keyvault create --name contosoKeyVault10 --resource-group contosoResourceGroup5 --location eastus
```

![影像](../media/databricks-command-output-4.png)
![上述命令的主控台輸出。 識別碼和 vaultUri 均會以綠色反白顯示，讓使用者可以看到這兩者。](../media/databricks-command-output-5.png)

若要建立祕密，請使用下列命令。 將祕密的值設定為您儲存體帳戶中的 **key1** 值。

```azurecli
az keyvault secret set --vault-name contosoKeyVault10 --name storageKey --value "value of your key1"
```

## <a name="create-an-azure-databricks-workspace-and-add-key-vault-secret-scope"></a>建立 Azure Databricks 工作區並新增 Key Vault 秘密範圍

此區段無法透過命令列完成。 請遵循此[指南](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault#create-an-azure-databricks-workspace-and-add-a-secret-scope)。 您必須存取 [Azure 入口網站](https://ms.portal.azure.com/#home)，才能執行下列動作：

1. 建立您的 Azure Databricks 資源
1. 啟動您的工作區
1. 建立 Azure Key Vault 支援的秘密範圍

## <a name="access-your-blob-container-from-azure-databricks-workspace"></a>從 Azure Databricks 工作區存取您的 Blob 容器

此區段無法透過命令列完成。 請遵循此[指南](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault#access-your-blob-container-from-azure-databricks)。 您必須使用 Azure Databricks 工作區，才能執行下列動作：

1. 建立**新叢集**
1. 建立**新的 Notebook**
1. 填寫 Python 指令碼中的對應欄位
1. 執行 Python 指令碼

```python
dbutils.fs.mount(
source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
mount_point = "/mnt/<mount-name>",
extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})

df = spark.read.text("/mnt/<mount-name>/<file-name>")

df.show()
```

## <a name="next-steps"></a>後續步驟

確定您的 Key Vault 可以復原：
> [!div class="nextstepaction"]
> [清除資源](https://docs.microsoft.com/azure/azure-resource-manager/management/delete-resource-group?tabs=azure-powershell)
