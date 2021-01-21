---
title: 使用 CLI 建立、查看和管理 Azure 事件方格系統主題
description: 本文說明如何使用 Azure CLI 來建立、查看和刪除系統主題。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: c1c847c7f25e3a656b798e186a408e560b9ee9e6
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633217"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>使用 Azure CLI 建立、查看和管理事件方格系統主題
本文說明如何使用 Azure CLI 建立和管理系統主題。 如需系統主題的總覽，請參閱 [系統主題](system-topics.md)。

## <a name="install-extension-for-azure-cli"></a>安裝 Azure CLI 擴充功能
對於 Azure CLI，您需要[事件方格擴充功能](/cli/azure/azure-cli-extensions-list)。

在 Cloud Shell：

- 如果您先前已安裝此延伸模組，請加以更新： `az extension update -n eventgrid`
- 如果您先前未安裝此延伸模組，請加以安裝：  `az extension add -n eventgrid`

針對本機安裝：

1. [安裝 Azure CLI](/cli/azure/install-azure-cli)。 使用 `az --version` 檢查，以確定所使用的是最新版本。
2. 卸載舊版的延伸模組： `az extension remove -n eventgrid`
3. 安裝 eventgrid 延伸模組 `az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>建立系統主題

- 若要先在 Azure 來源上建立系統主題，然後建立該主題的事件訂用帳戶，請參閱下列參考主題：
    - [az eventgrid system-主題 create](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-create)

        ```azurecli-interactive
        # Get the ID of the Azure source (for example: Azure Storage account)
        storageid=$(az storage account show \
                --name <AZURE STORAGE ACCOUNT NAME> \
                --resource-group <AZURE RESOURCE GROUP NAME> \
                    --query id --output tsv)
    
        # Create the system topic on the Azure source (example: Azure Storage account)
        az eventgrid system-topic create \
            -g <AZURE RESOURCE GROUP NAME> \
            --name <SPECIFY SYSTEM TOPIC NAME> \
            --location <LOCATION> \
            --topic-type microsoft.storage.storageaccounts \
            --source $storageid
        ```           

        如需 `topic-type` 可用來建立系統主題的值清單，請執行下列命令。 這些主題類型值代表支援建立系統主題的事件來源。 請忽略 `Microsoft.EventGrid.Topics` `Microsoft.EventGrid.Domains` 清單中的和。 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [az eventgrid system-主題事件-訂用帳戶建立](/cli/azure/ext/eventgrid/eventgrid/system-topic/event-subscription#ext-eventgrid-az-eventgrid-system-topic-event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- 若要建立系統主題 (在建立 Azure 來源的事件訂用帳戶時隱含) ，請使用 [az eventgrid 事件訂](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) 用帳戶建立方法。 以下為範例：
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    如需逐步指示的教學課程，請參閱 [訂閱儲存體帳戶](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account)。

## <a name="view-all-system-topics"></a>查看所有系統主題
若要查看所選系統主題的所有系統主題和詳細資料，請使用下列命令：

- [az eventgrid 系統-主題清單](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [az eventgrid system-主題 show](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>刪除系統主題
若要刪除系統主題，請使用下列命令： 

- [az eventgrid system-主題 delete](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>下一步
若要深入瞭解 Azure 事件方格所支援的系統主題和主題類型，請參閱 [Azure 事件方格中的系統主題](system-topics.md) 一節。 