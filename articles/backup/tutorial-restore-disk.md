---
title: 教學課程 - 使用 Azure CLI 來還原 VM
description: 了解如何還原磁碟，並在 Azure 中使用備份與復原服務建立 VM。
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 861c911e84c9de02467d443751902e71d2504422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324975"
---
# <a name="restore-a-vm-with-azure-cli"></a>使用 Azure CLI 來還原 VM

Azure 備份會建立復原點，並儲存在異地備援復原保存庫。 當您從復原點還原時，可以還原整個 VM 或個別檔案。 本文說明如何使用 CLI 還原完整的 VM。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
>
> * 列出和選取復原點
> * 從復原點還原磁碟
> * 從還原的磁碟建立 VM

如需使用 PowerShell 還原磁碟及建立已復原 VM 的相關資訊，請參閱[使用 PowerShell 備份及還原 Azure VM](backup-azure-vms-automation.md#restore-an-azure-vm)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.0.18 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="prerequisites"></a>Prerequisites

本教學課程需要已使用 Azure 備份保護的 Linux VM。 若要模擬意外刪除 VM 和復原程序，您可以在復原點從磁碟建立 VM。 如果您需要已使用 Azure 備份保護的 Linux VM，請參閱[使用 CLI 在 Azure 中備份虛擬機器](quick-backup-vm-cli.md)。

## <a name="backup-overview"></a>備份概觀

當 Azure 起始備份時，VM 上的備份擴充功能會建立時間點快照集。 此備份擴充功能會在要求第一個備份時安裝在 VM 上。 如果進行備份時 VM 不在執行中，Azure 備份也可以建立基礎儲存體的快照集。

根據預設，Azure 備份會建立檔案系統一致的快照集。 Azure 備份建立快照集之後，資料會傳輸至復原服務保存庫。 為了能更有效率，Azure 備份只會找出並傳輸自上次備份之後有變更的資料區塊。

資料傳輸完畢後，系統會移除快照集並建立復原點。

## <a name="list-available-recovery-points"></a>列出可用的復原點

若要還原磁碟，您可選取復原點作為復原資料的來源。 由於預設原則會每天建立復原點並保留 30 天，您可保留一組復原點，讓您選取特定的時間點進行復原。

若要查看可用的復原點清單，請使用 [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list)。 復原點**名稱**用來復原磁碟。 在本教學課程中，我們需要最近可用的復原點。 `--query [0].name` 參數可選取最近的復原點名稱，如下所示：

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --backup-management-type AzureIaasVM \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>還原 VM 磁碟

> [!IMPORTANT]
> 強烈建議使用 Az CLI 2.0.74 版或更新版本，以取得快速還原的所有優點，包括受控磁碟還原。 如果您一律使用最新版本，則建議您這麼做。

### <a name="managed-disk-restore"></a>受控磁碟還原

如果備份的 VM 具有受控磁碟，且其目的是要從復原點還原受控磁碟，您必須先提供 Azure 儲存體帳戶。 此儲存體帳戶會用來儲存 VM 組態和部署範本，以便後續用來從還原的磁碟部署 VM。 然後，您也可以為要還原的受控磁碟提供目標資源群組。

1. 若要建立儲存體帳戶，請使用 [az storage account create](/cli/azure/storage/account#az-storage-account-create)。 儲存體帳戶名稱必須全部小寫，並且是全域唯一的。 以自己的唯一名稱取代 *mystorageaccount*：

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. 使用 [az backup restore restore-disks](/cli/azure/backup/restore#az-backup-restore-restore-disks) 從您的復原點還原磁碟。 以您在上一個命令中建立的儲存體帳戶名稱取代 *mystorageaccount*。 以您在先前 [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) 命令的輸出中取得的復原點名稱取代 *myRecoveryPointName*。 ***也請提供要將受控磁碟還原到其中的目標資源群組***。

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --target-resource-group targetRG
    ```

    > [!WARNING]
    > 若未提供**目標資源群組**，則受控磁碟將會以非受控磁碟的形式還原至指定的儲存體帳戶。 這對還原時間會有明顯的影響，因為還原磁碟所花費的時間完全取決於指定的儲存體帳戶。 只有在指定 target-resource-group 參數時，您才能享有「立即還原」的好處。 如果想要將受控磁碟還原為非受控，則不要提供 **target-resource-group** 參數，而是改為提供 **restore-as-unmanaged-disk** 參數，如下所示。 此參數從 az 3.4.0 版之後開始提供。

    ```azurecli-interactive
    az backup restore restore-disks \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --storage-account mystorageaccount \
    --rp-name myRecoveryPointName \
    --restore-as-unmanaged-disk
    ```

這會將受控磁碟當做非受控磁碟來還原至指定的儲存體帳戶，而不會利用「立即」還原功能。 在未來的 CLI 版本中，您必須提供 **target-resource-group** 參數或 **restore-as-unmanaged-disk** 參數。

### <a name="unmanaged-disks-restore"></a>非受控磁碟還原

如果備份的 VM 具有非受控磁碟，且其目的是要從復原點還原磁碟，您必須先提供 Azure 儲存體帳戶。 此儲存體帳戶會用來儲存 VM 組態和部署範本，以便後續用來從還原的磁碟部署 VM。 根據預設，非受控磁碟會還原至其原始儲存體帳戶。 如果想要將所有非受控磁碟還原至單一位置，則也可以使用指定的儲存體帳戶作為這些磁碟的暫存位置。

在其他步驟中，已還原的磁碟用來建立 VM。

1. 若要建立儲存體帳戶，請使用 [az storage account create](/cli/azure/storage/account#az-storage-account-create)。 儲存體帳戶名稱必須全部小寫，並且是全域唯一的。 以自己的唯一名稱取代 *mystorageaccount*：

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. 使用 [az backup restore restore-disks](/cli/azure/backup/restore#az-backup-restore-restore-disks) 從您的復原點還原磁碟。 以您在上一個命令中建立的儲存體帳戶名稱取代 *mystorageaccount*。 以您在先前 [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) 命令的輸出中取得的復原點名稱取代 *myRecoveryPointName*：

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

如前所述，非受控磁碟會還原至其原始儲存體帳戶。 這樣可以提供最理想的還原效能。 但若所有非受控磁碟都必須還原至指定的儲存體帳戶，請使用相關的旗標，如下所示。

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](/cli/azure/backup/job#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

輸出會類似下列範例，其顯示的還原作業為 InProgress  ︰

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

當還原作業的 [狀態]  報告 [已完成]  時，表示所需的資訊 (VM 組態和部署範本) 已還原至儲存體帳戶。

## <a name="create-a-vm-from-the-restored-disk"></a>從還原的磁碟建立 VM

最後一個步驟是從還原的磁碟建立 VM。 您可以使用下載到指定儲存體帳戶的部署範本來建立 VM。

### <a name="fetch-the-job-details"></a>提取作業詳細資料

結果工作詳細資料提供可查詢和部署的範本 URI。 使用 job show 命令，可針對觸發的還原作業取得更多詳細資料。

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

此查詢的輸出會提供所有詳細資料，但我們只對儲存體帳戶內容感興趣。 我們可以使用 Azure CLI 的[查詢功能](/cli/azure/query-azure-cli)來提取相關的詳細資料

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>提取部署範本

此範本位於客戶的儲存體帳戶和指定的容器下，因此無法直接存取。 我們需要完整的 URL (以及暫時的 SAS 權杖)，才能存取此範本。

首先，請從作業詳細資料中擷取範本 Blob URI

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

範本 Blob URI 會採用此格式，並擷取範本名稱

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

因此，上述範例中的範本名稱將是 ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json```，而容器名稱為 ```myVM-daa1931199fd4a22ae601f46d8812276```

現在，取得此容器和範本的 SAS 權杖，詳細說明請見[此處](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-cli#provide-sas-token-during-deployment)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>部署範本以建立 VM

現在，請部署範本以建立 VM，如[此處](../azure-resource-manager/templates/deploy-cli.md)所說明。

```azurecli-interactive
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

若要確認已從復原的磁碟建立 VM，請使用 [az vm list](/cli/azure/vm#az-vm-list) 列出資源群組中的 VM，如下所示：

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已從復原點還原磁碟，並從磁碟建立 VM。 您已了解如何︰

> [!div class="checklist"]
>
> * 列出和選取復原點
> * 從復原點還原磁碟
> * 從還原的磁碟建立 VM

前進到下一個教學課程，了解如何從復原點還原個別的檔案。

> [!div class="nextstepaction"]
> [在 Azure 中將檔案還原到虛擬機器](tutorial-restore-files.md)
