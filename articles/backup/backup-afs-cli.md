---
title: 使用 Azure CLI 備份 Azure 檔案共用
description: 瞭解如何使用 Azure CLI 來備份復原服務保存庫中的 Azure 檔案共用
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76844036"
---
# <a name="back-up-azure-file-shares-with-cli"></a>使用 CLI 來備份 Azure 檔案共用

Azure 命令列介面（CLI）提供用來管理 Azure 資源的命令列體驗。 這是建立自訂自動化以使用 Azure 資源的絕佳工具。 本文詳細說明如何使用 Azure CLI 來備份 Azure 檔案共用。 您也可以透過 [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) 或在 [Azure 入口網站](backup-afs.md)中執行這些步驟。

本教學課程結束時，您將瞭解如何使用 Azure CLI 執行下列作業：

* 建立復原服務保存庫。
* 啟用 Azure 檔案共用的備份
* 觸發檔案共用的隨選備份

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本機安裝和使用 CLI，您必須執行 Azure CLI 2.0.18 版或更新版本。 若要尋找 CLI 版本， `run az --version`請。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫是一個實體，可為您提供跨所有備份專案的匯總視圖和管理功能。 執行受保護資源的備份作業時，它會在復原服務保存庫內建立復原點。 然後您可以使用其中一個復原點，將資料還原到指定的時間點。

請遵循下列步驟來建立復原服務保存庫：

1. 保存庫會放在資源群組中。 如果您沒有現有的資源群組，請使用[az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)建立一個新的。 在本教學課程中，我們會在美國東部區域建立新的資源群組*azurefiles* 。

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. 使用[az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) Cmdlet 來建立保存庫。 為保存庫指定與用於資源群組相同的位置。

    下列範例會在美國東部區域建立名為*azurefilesvault*的復原服務保存庫。

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. 指定要用於保存庫儲存體的冗余類型。 您可以使用[本機備援儲存體](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)或[異地備援儲存體](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。

    下列範例會使用[az backup vault 備份-properties set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) Cmdlet，將*azurefilesvault*的儲存體冗余選項設定為**異地備援**。

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    若要檢查是否已成功建立保存庫，您可以使用[az backup vault show](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) Cmdlet 來取得保存庫的詳細資料。 下列範例會顯示我們在上述步驟中建立之*azurefilesvault*的詳細資料。

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    輸出會類似下列回應：

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>啟用 Azure 檔案共用的備份

本節假設您已經有想要設定備份的 Azure 檔案共用。 如果您沒有帳戶，請使用[az storage share create](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create)命令來建立 Azure 檔案共用。

若要啟用檔案共用的備份，您必須建立保護原則，以定義備份作業的執行時間，以及儲存復原點的時間長度。 您可以使用[az backup policy create](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) Cmdlet 來建立備份原則。

下列範例會使用[az backup protection azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) Cmdlet，以使用*schedule 1*備份原則來啟用*afsaccount*儲存體帳戶中*azurefiles*檔案共用的備份：

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

輸出中的**name**屬性會對應至備份服務針對您的「**啟用備份**」作業所建立的工作名稱。 若要追蹤作業的狀態，請使用[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) Cmdlet。

## <a name="trigger-an-on-demand-backup-for-file-share"></a>觸發檔案共用的隨選備份

如果您想要針對檔案共用觸發隨選備份，而不是等待備份原則在排定的時間執行作業，請使用[az backup protection backup-now](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now) Cmdlet。

您必須定義下列參數，以觸發隨選備份：

* **--container-name**是主控檔案共用的儲存體帳戶名稱。 若要取得容器的**名稱**或**易記名稱**，請使用[az backup container list](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--item-name**是您想要觸發隨選備份的檔案共用名稱。 若要取出已備份專案的**名稱**或**易記名稱**，請使用[az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。
* **--保留-直到**指定要保留復原點的日期為止。 此值應以 UTC 時間格式（dd-mm-yyyy）來設定。

下列範例會針對*afsaccount*儲存體帳戶中的*azuresfiles*檔案共用觸發隨選備份，保留期為*20-01-2020*。

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

輸出中的**name**屬性會對應至備份服務針對您的「隨選備份」作業所建立的工作名稱。 若要追蹤作業的狀態，請使用[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) Cmdlet。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[使用 CLI 還原 Azure 檔案共用](restore-afs-cli.md)
* 瞭解如何[使用 CLI 管理 Azure 檔案共用 ackups](manage-afs-backup-cli.md)
