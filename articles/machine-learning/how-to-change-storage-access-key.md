---
title: 變更儲存體帳戶存取金鑰
titleSuffix: Azure Machine Learning
description: 瞭解如何變更工作區所使用之 Azure 儲存體帳戶的存取金鑰。 Azure Machine Learning 會使用 Azure 儲存體帳戶來儲存資料和模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/19/2020
ms.openlocfilehash: 1cb9600393fe50226da1efcef550cbb8b26f7f14
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321583"
---
# <a name="regenerate-storage-account-access-keys"></a>重新產生儲存體帳戶存取金鑰
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何變更 Azure Machine Learning 所使用之 Azure 儲存體帳戶的存取金鑰。 Azure Machine Learning 可以使用儲存體帳戶來儲存資料或定型模型。

基於安全考慮，您可能需要變更 Azure 儲存體帳戶的存取金鑰。 當您重新產生存取金鑰時，必須將 Azure Machine Learning 更新為使用新的金鑰。 Azure Machine Learning 可能會同時針對模型儲存體和資料存放區使用儲存體帳戶。

> [!IMPORTANT]
> 使用資料存放區已註冊的認證會儲存在與工作區相關聯的 Azure Key Vault 中。 如果您已為您的 Key Vault 啟用虛[刪除](https://docs.microsoft.com/azure/key-vault/general/overview-soft-delete)，請務必遵循這篇文章來更新認證。 取消註冊資料存放區，並以相同名稱重新註冊，將會失敗。

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 如需詳細資訊，請參閱[建立工作區](how-to-manage-workspace.md)文章。

* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* [AZURE MACHINE LEARNING CLI 擴充](reference-azure-machine-learning-cli.md)功能。

> [!NOTE]
> 本檔中的程式碼片段已透過 Python SDK 的版本1.0.83 進行測試。

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>需要更新的內容

儲存體帳戶可供 Azure Machine Learning 工作區使用（將記錄、模型、快照集等）和資料存放區。 更新工作區的程式是單一 Azure CLI 命令，而且可以在更新儲存體金鑰後執行。 更新資料存放區的程式較牽涉，而且需要探索哪些資料存放區目前正在使用儲存體帳戶，然後再重新註冊。

> [!IMPORTANT]
> 使用 Azure CLI 和使用 Python 的資料存放區同時更新工作區。 僅更新其中一個或另一個不足夠，而且可能會導致錯誤，直到兩者都更新為止。

若要探索您的資料存放區所使用的儲存體帳戶，請使用下列程式碼：

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

此程式碼會尋找使用 Azure 儲存體的任何已註冊資料存放區，並列出下列資訊：

* 資料存放區名稱：儲存體帳戶註冊所在的資料存放區名稱。
* 儲存體帳戶名稱： Azure 儲存體帳戶的名稱。
* 容器：此註冊所使用之儲存體帳戶中的容器。

它也會指出資料存放區是否適用于 Azure Blob 或 Azure 檔案共用，因為有不同的方法可重新註冊每種類型的資料存放區。

如果您計畫在重新產生存取金鑰的儲存體帳戶中存在專案，請儲存資料存放區名稱、儲存體帳戶名稱和容器名稱。

## <a name="update-the-access-key"></a>更新存取金鑰

若要更新 Azure Machine Learning 以使用新的金鑰，請使用下列步驟：

> [!IMPORTANT]
> 執行所有步驟，使用 CLI 來更新工作區，並使用 Python 資料存放區。 只更新其中一個或其他可能會導致錯誤，直到兩者都更新為止。

1. 重新產生金鑰。 如需重新產生存取金鑰的詳細資訊，請參閱[管理儲存體帳戶存取金鑰](../storage/common/storage-account-keys-manage.md)。 儲存新的金鑰。

1. Azure Machine Learning 工作區會自動同步處理新的金鑰，並在一小時後開始使用它。 若要強制工作區立即同步至新的金鑰，請使用下列步驟：

    1. 若要使用下列 Azure CLI 命令登入包含您工作區的 Azure 訂用帳戶：

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. 若要更新工作區以使用新的金鑰，請使用下列命令。 `myworkspace`將取代為您的 Azure Machine Learning 工作區名稱，並 `myresourcegroup` 將取代為包含工作區的 Azure 資源組名。

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        此命令會自動為工作區所使用的 Azure 儲存體帳戶同步處理新的金鑰。

1. 您可以透過 SDK 或[Azure Machine Learning studio](https://ml.azure.com)，重新註冊使用儲存體帳戶的資料存放區。
    1. **若要透過 PYTHON SDK 重新註冊資料存放區**，請使用 [必須更新的[內容](#whattoupdate)] 區段中的值，以及步驟1中包含下列程式碼的索引鍵。 
    
        由於 `overwrite=True` 指定了，此程式碼會覆寫現有的註冊，並將其更新為使用新的金鑰。
    
        ```python
        # Re-register the blob container
        ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                                  datastore_name='your datastore name',
                                                  container_name='your container name',
                                                  account_name='your storage account name',
                                                  account_key='new storage account key',
                                                  overwrite=True)
        # Re-register file shares
        ds_file = Datastore.register_azure_file_share(workspace=ws,
                                              datastore_name='your datastore name',
                                              file_share_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
        
        ```
    
    1. **若要透過 studio 重新註冊資料存放區**，請從 studio 的左窗格中選取 [**資料存放區**]。 
        1. 選取您想要更新的資料存放區。
        1. 選取左上方的 [**更新認證**] 按鈕。 
        1. 使用步驟1中的新存取金鑰來填入表單，然後按一下 [**儲存**]。
        
            如果您要更新**預設資料**存放區的認證，請完成此步驟，並重複步驟2b 以重新同步處理新的金鑰與工作區的預設資料存放區。 

## <a name="next-steps"></a>後續步驟

如需註冊資料存放區的詳細資訊，請參閱 [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) 類別參考。
