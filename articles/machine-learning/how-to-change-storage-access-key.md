---
title: 更改存儲帳戶訪問金鑰
titleSuffix: Azure Machine Learning
description: 瞭解如何更改工作區使用的 Azure 存儲帳戶的訪問金鑰。 Azure 機器學習使用 Azure 存儲帳戶來存儲資料和模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: f1541c177cea2d223a5e7df576d95fab7eafb310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296928"
---
# <a name="regenerate-storage-account-access-keys"></a>重新生成存儲帳戶訪問金鑰
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何更改 Azure 機器學習使用的 Azure 存儲帳戶的訪問金鑰。 Azure 機器學習可以使用存儲帳戶來存儲資料或經過訓練的模型。

出於安全考慮，您可能需要更改 Azure 存儲帳戶的訪問金鑰。 重新生成訪問金鑰時，必須更新 Azure 機器學習才能使用新金鑰。 Azure 機器學習可能同時將存儲帳戶用於模型存儲和資料存儲。

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 有關詳細資訊，請參閱[創建工作區](how-to-manage-workspace.md)一文。

* [Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* [Azure 機器學習 CLI 擴展](reference-azure-machine-learning-cli.md)。

> [!NOTE]
> 本文檔中的程式碼片段使用 Python SDK 的版本 1.0.83 進行了測試。

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>需要更新的內容

存儲帳戶可以由 Azure 機器學習工作區（存儲日誌、模型、快照等）和資料存儲使用。 更新工作區的過程是單個 Azure CLI 命令，可以在更新存儲金鑰後運行。 更新資料存儲的過程涉及更多，需要發現哪些資料存儲當前正在使用存儲帳戶，然後重新註冊它們。

> [!IMPORTANT]
> 同時使用 Azure CLI 和使用 Python 更新資料存儲。 僅更新一個或另一個是不夠的，並且可能會導致錯誤，直到更新兩個。

要發現資料存儲使用的存儲帳戶，請使用以下代碼：

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

此代碼查找使用 Azure 存儲的任何已註冊資料存儲，並列出以下資訊：

* 資料存儲名稱：存儲帳戶註冊下的資料存儲的名稱。
* 存儲帳戶名稱：Azure 存儲帳戶的名稱。
* 容器：此註冊使用的存儲帳戶中的容器。

它還指示資料存儲是針對 Azure Blob 還是 Azure 檔共用，因為需要重新註冊每種類型的資料存儲的方法也不同。

如果計畫為其重新生成訪問金鑰的存儲帳戶存在條目，則保存資料存儲名稱、存儲帳戶名稱和容器名稱。

## <a name="update-the-access-key"></a>更新訪問金鑰

要更新 Azure 機器學習以使用新金鑰，請使用以下步驟：

> [!IMPORTANT]
> 執行所有步驟，使用 CLI 更新工作區和使用 Python 的資料存儲。 只更新一個或另一個可能會導致錯誤，直到兩者都更新。

1. 重新生成金鑰。 有關重新生成訪問金鑰的資訊，請參閱[管理存儲帳戶訪問金鑰](../storage/common/storage-account-keys-manage.md)。 保存新金鑰。

1. 要更新工作區以使用新鍵，請使用以下步驟：

    1. 要使用以下 Azure CLI 命令登錄到包含工作區的 Azure 訂閱，請執行以下操作：

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. 要更新工作區以使用新鍵，請使用以下命令。 替換為`myworkspace`Azure 機器學習工作區名稱，然後替換為`myresourcegroup`包含工作區的 Azure 資源組的名稱。

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        此命令會自動同步工作區使用的 Azure 存儲帳戶的新金鑰。

1. 要重新註冊使用存儲帳戶的資料存儲，請使用["需要更新的內容](#whattoupdate)"部分的值以及步驟 1 中的包含以下代碼的鍵：

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

    由於`overwrite=True`已指定，此代碼將覆蓋現有註冊並對其進行更新以使用新金鑰。

## <a name="next-steps"></a>後續步驟

有關註冊資料存儲的詳細資訊，請參閱[`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)類引用。
