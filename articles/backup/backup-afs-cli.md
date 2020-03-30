---
title: 使用 Azure CLI 備份 Azure 檔共用
description: 瞭解如何使用 Azure CLI 在恢復服務保存庫中備份 Azure 檔共用
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844036"
---
# <a name="back-up-azure-file-shares-with-cli"></a>使用 CLI 備份 Azure 檔共用

Azure 命令列介面 （CLI） 提供了用於管理 Azure 資源的命令列體驗。 它是構建自訂自動化以使用 Azure 資源的絕佳工具。 本文詳細介紹了如何使用 Azure CLI 備份 Azure 檔共用。 您也可以透過 [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) 或在 [Azure 入口網站](backup-afs.md)中執行這些步驟。

在本教程結束時，您將學習如何使用 Azure CLI 執行以下操作：

* 建立復原服務保存庫。
* 為 Azure 檔共用啟用備份
* 觸發檔共用的按需備份

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本機安裝和使用 CLI，您必須執行 Azure CLI 2.0.18 版或更新版本。 要查找 CLI 版本`run az --version`，應查找 。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-a-recovery-services-vault"></a>創建恢復服務保存庫

恢復服務保存庫是一個實體，它為您提供跨所有備份項的整合視圖和管理功能。 執行受保護資源的備份作業時，它會在復原服務保存庫內建立復原點。 然後您可以使用其中一個復原點，將資料還原到指定的時間點。

按照以下步驟創建恢復服務保存庫：

1. 保存庫放置在資源組中。 如果沒有現有資源組，請創建一個使用 az 組創建 的新[資源組](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)。 在本教程中，我們在美國東部區域創建新的資源組*azure 檔*。

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. 使用[az 備份保存庫創建](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create)Cmdlet 創建保存庫。 為保存庫指定與用於資源群組相同的位置。

    下面的示例在美國東部區域創建名為*azurefilevault*的恢復服務保存庫。

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. 指定用於保存庫存儲的冗余類型。 您可以使用[本機備援儲存體](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)或[異地備援儲存體](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。

    下面的示例使用[az 備份保存庫備份屬性 set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) Cmdlet 將*azurefilevault*的存儲冗余選項設置為**異地冗余**。

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    要檢查保存庫是否成功創建，可以使用[az 備份保存庫顯示](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show)Cmdlet 來獲取保存庫的詳細資訊。 下面的示例顯示我們在上述步驟中創建的*azure 檔庫*的詳細資訊。

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    輸出將類似于以下回應：

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>為 Azure 檔共用啟用備份

本節假定您已經擁有要為其配置備份的 Azure 檔共用。 如果沒有，請使用[az 存儲共用創建](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create)命令創建 Azure 檔共用。

要啟用檔共用的備份，您需要創建一個保護原則，該策略定義備份作業何時運行以及存儲復原點的時間。 您可以使用[az 備份策略創建](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create)Cmdlet 創建備份策略。

下面的示例使用[az 備份保護啟用 azure 檔共用](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare)Cmdlet 啟用使用*計畫 1*備份策略在*afs 帳戶*存儲帳戶中的 azure*檔*共用的備份：

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

輸出中的**Name**屬性對應于**為啟用備份**操作的備份服務創建的作業的名稱。 要跟蹤作業的狀態，請使用 az[備份作業顯示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)Cmdlet。

## <a name="trigger-an-on-demand-backup-for-file-share"></a>觸發檔共用的按需備份

如果要為檔共用觸發按需備份，而不是等待備份策略在計畫的時間運行作業，請使用[az 備份保護立即備份](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now)Cmdlet。

您需要定義以下參數才能觸發按需備份：

* **--容器名稱**是託管檔共用的存儲帳戶的名稱。 若要檢索容器**的名稱**或**易記名稱**，請使用[az 備份容器清單](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)命令。
* **--項名稱**是要為其觸發按需備份的檔共用的名稱。 若要檢索備份項**的名稱**或**易記名稱**，請使用[az 備份項清單](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)命令。
* **--保留 -直到**指定日期，直到您想要保留復原點。 該值應設置為 UTC 時間格式 （dd-mm-yyyyy）。

下面的示例觸發*afs 帳戶*存儲帳戶中*Azuresfile*檔共用的按需備份，保留期至*20-01-2020*。

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

輸出中的**Name**屬性對應于備份服務為"按需備份"操作創建的作業的名稱。 要跟蹤作業的狀態，請使用[az 備份作業顯示](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)Cmdlet。

## <a name="next-steps"></a>後續步驟

* 瞭解如何使用[CLI 還原 Azure 檔共用](restore-afs-cli.md)
* 瞭解如何使用[CLI 管理 Azure 檔共用圖](manage-afs-backup-cli.md)
