---
title: 使用 Azure CLI 備份 Azure 檔案共用
description: 瞭解如何使用 Azure CLI 在復原服務保存庫中備份 Azure 檔案共用
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 12d258a3242530745cc8ce31afae18f622323488
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91293249"
---
# <a name="back-up-azure-file-shares-with-cli"></a>使用 CLI 備份 Azure 檔案共用

Azure 命令列介面 (CLI) 提供用來管理 Azure 資源的命令列體驗。 它是建立自訂自動化以使用 Azure 資源的絕佳工具。 本文將詳細說明如何使用 Azure CLI 來備份 Azure 檔案共用。 您也可以透過 [Azure PowerShell](./backup-azure-afs-automation.md) 或在 [Azure 入口網站](backup-afs.md)中執行這些步驟。

本教學課程結束時，您將瞭解如何使用 Azure CLI 來執行下列作業：

* 建立復原服務保存庫
* 啟用 Azure 檔案共用的備份
* 觸發檔案共用的隨選備份

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本機安裝和使用 CLI，您必須執行 Azure CLI 2.0.18 版或更新版本。 若要尋找 CLI 版本，請 `run az --version` 。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫是一個實體，可讓您跨所有備份專案提供合併的查看和管理功能。 執行受保護資源的備份作業時，它會在復原服務保存庫內建立復原點。 然後您可以使用其中一個復原點，將資料還原到指定的時間點。

遵循下列步驟來建立復原服務保存庫：

1. 保存庫會放置在資源群組中。 如果您沒有現有的資源群組，請建立一個新的資源群組，其中包含 [az group create](/cli/azure/group#az-group-create) 。 在本教學課程中，我們會在美國東部區域建立新的資源群組 *azurefiles* 。

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

1. 使用 [az backup vault create](/cli/azure/backup/vault#az-backup-vault-create) Cmdlet 來建立保存庫。 為保存庫指定與用於資源群組相同的位置。

    下列範例會在美國東部區域建立名為 *azurefilesvault* 的復原服務保存庫。

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>啟用 Azure 檔案共用的備份

本節假設您已有想要設定備份的 Azure 檔案共用。 如果您沒有帳戶，請使用 [az storage share create](/cli/azure/storage/share#az-storage-share-create) 命令來建立 Azure 檔案共用。

若要啟用檔案共用的備份，您必須建立保護原則來定義備份作業的執行時間，以及復原點的儲存時間長度。 您可以使用 [az backup policy create](/cli/azure/backup/policy#az-backup-policy-create) Cmdlet 來建立備份原則。

下列範例會使用[az backup protection azurefileshare](/cli/azure/backup/protection#az-backup-protection-enable-for-azurefileshare) Cmdlet，在*afsaccount*儲存體帳戶中使用*排程 1*備份原則來啟用*azurefiles*檔案共用的備份：

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

輸出中的 **name** 屬性會對應至備份服務為您 **啟用備份** 作業所建立的作業名稱。 若要追蹤作業的狀態，請使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) Cmdlet。

## <a name="trigger-an-on-demand-backup-for-file-share"></a>觸發檔案共用的隨選備份

如果您想要觸發檔案共用的隨選備份，而不是等到備份原則在排程的時間執行作業，請使用 [az backup protection backup-now](/cli/azure/backup/protection#az-backup-protection-backup-now) Cmdlet。

您需要定義下列參數，以觸發隨選備份：

* **--容器-名稱** 是裝載檔案共用的儲存體帳戶名稱。 若要取出容器的 **名稱** 或 **易記名稱** ，請使用 [az backup container list](/cli/azure/backup/container#az-backup-container-list) 命令。
* **--專案-名稱** 是您要觸發隨選備份的檔案共用名稱。 若要取出備份專案的 **名稱** 或 **易記名稱** ，請使用 [az backup item list](/cli/azure/backup/item#az-backup-item-list) 命令。
* **--保留-直到** 指定您要保留復原點的日期。 值應該以 UTC 時間格式設定 (dd-mm-yyyy) 。

下列範例會在*afsaccount*儲存體帳戶中觸發*azurefiles*檔案共用的隨選備份，保留期限為*20-01-2020*。

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

輸出中的 **name** 屬性會對應至備份服務針對您的「隨選備份」作業所建立的作業名稱。 若要追蹤作業的狀態，請使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) Cmdlet。

## <a name="next-steps"></a>後續步驟

* 瞭解如何 [使用 CLI 還原 Azure 檔案共用](restore-afs-cli.md)
* 瞭解如何 [使用 CLI 管理 Azure 檔案共用備份](manage-afs-backup-cli.md)
