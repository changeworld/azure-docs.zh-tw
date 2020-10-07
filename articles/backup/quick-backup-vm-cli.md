---
title: 快速入門 - 使用 Azure CLI 來備份 VM
description: 在此快速入門中，了解如何建立復原服務保存庫、在 VM 上啟用保護，以及使用 Azure CLI 建立初始復原點。
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 92990df3049f7fa1074d55fc34734e13d6673cd7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91328814"
---
# <a name="back-up-a-virtual-machine-in-azure-with-the-cli"></a>使用 CLI 在 Azure 中備份虛擬機器

Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。 您可以定期建立備份以保護您的資料。 Azure 備份會建立復原點，其可儲存在異地備援復原保存庫中。 本文詳述如何使用 Azure CLI 在 Azure 中備份虛擬機器 (VM)。 您也可以透過 [Azure PowerShell](quick-backup-vm-powershell.md) 或在 [Azure 入口網站](quick-backup-vm-portal.md)中執行這些步驟。

本快速入門能夠在現有的 Azure VM 上進行備份。 如果您需要建立 VM，您可以[使用 Azure CLI 來建立 VM](../virtual-machines/linux/quick-create-cli.md)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本機安裝和使用 CLI，您必須執行 Azure CLI 2.0.18 版或更新版本。 若要知道 CLI 版本，執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫是一個邏輯容器，可儲存每個受保護資源 (例如 Azure VM) 的備份資料。 執行受保護資源的備份作業時，它會在復原服務保存庫內建立復原點。 然後您可以使用其中一個復原點，將資料還原到指定的時間點。

使用 [az backup vault create](/cli/azure/backup/vault#az-backup-vault-create) 建立復原服務保存庫。 指定與您想要保護的 VM 相同的資源群組和位置。 如果您使用了 [VM 快速入門](../virtual-machines/linux/quick-create-cli.md)，則您已建立：

- 名為 *myResourceGroup* 的資源群組、
- 名為 *myVM* 的虛擬機器、
- *eastus* 位置中的資源。

```azurecli-interactive
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

根據預設，已針對異地備援儲存體設定復原服務保存庫。 異地備援儲存體可確保您的備份資料會複寫到與主要區域距離數百英哩的次要 Azure 區域。 如果需要修改儲存體備援設定，請使用 [az backup vault backup-properties set](/cli/azure/backup/vault/backup-properties#az-backup-vault-backup-properties-set) Cmdlet。

```azurecli
az backup vault backup-properties set \
    --name myRecoveryServicesVault  \
    --resource-group myResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

## <a name="enable-backup-for-an-azure-vm"></a>啟用 Azure VM 的備份

建立要定義的保護原則：備份作業的執行時機以及復原點的儲存時間長度。 預設保護原則會每天執行備份作業並將復原點保留 30 天。 您可以使用這些預設原則值來快速保護您的 VM。 若要啟用 VM 的備份保護，請使用 [az backup protection enable-for-vm](/cli/azure/backup/protection#az-backup-protection-enable-for-vm)。 指定要保護的資源群組和 VM，然後指定要使用的原則：

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

> [!NOTE]
> 如果 VM 所在的資源群組與保存庫不同，則 myResourceGroup 會參考保存庫建立所在的資源群組。 不要提供 VM 名稱，請如下所示提供 VM 識別碼。

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm $(az vm show -g VMResourceGroup -n MyVm --query id | tr -d '"') \
    --policy-name DefaultPolicy
```

> [!IMPORTANT]
> 使用 CLI 同時啟用多個 VM 的備份時，請確定單一原則不會有超過 100 部相關聯的 VM。 這是[建議的最佳做法](./backup-azure-vm-backup-faq.md#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy)。 目前，如果有超過 100 部 VM，但已規劃在未來新增檢查，則 PowerShell 用戶端不會明確封鎖。

## <a name="start-a-backup-job"></a>開始備份作業

若要立即開始備份，而非等候預設原則在排定的時間執行此作業，請使用 [az backup protection backup-now](/cli/azure/backup/protection#az-backup-protection-backup-now)。 這第一個備份作業會建立完整復原點。 此初始備份之後的每個備份作業會建立增量復原點。 增量復原點符合儲存和時間效率，因為它只會傳輸自上次備份後所做的變更。

下列參數用於備份 VM：

- `--container-name` 是您的 VM 名稱
- `--item-name` 是您的 VM 名稱
- `--retain-until` 值應設定為您希望使用復原點的最後可用日期 (採用 UTC 時間格式 **dd-mm-yyyy**)。

下列範例會備份名為 myVM  的 VM，並將復原點的到期日設定為 2017 年 10 月 18 日：

```azurecli-interactive
az backup protection backup-now \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --retain-until 18-10-2017
```

## <a name="monitor-the-backup-job"></a>監視備份作業

若要監視備份作業的狀態，請使用 [az backup job list](/cli/azure/backup/job#az-backup-job-list)：

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

輸出會類似下列範例，其顯示的備份作業為 InProgress  ︰

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

當備份作業的「狀態」  回報 Completed  ，您的 VM 已受復原服務保護並已儲存完整復原點。

## <a name="clean-up-deployment"></a>清除部署

不再需要時，您可以停用 VM 的保護功能，移除還原點和復原服務保存庫，然後刪除資源群組和相關聯的 VM 資源。 如果您使用現有的 VM，可以略過最後的 [az group delete](/cli/azure/group#az-group-delete) 命令，讓資源群組和 VM 留在原處。

如果您要嘗試說明如何為您的 VM 還原資料的備份教學課程，請前往[後續步驟](#next-steps)。

```azurecli-interactive
az backup protection disable \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --delete-backup-data true
az backup vault delete \
    --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立復原服務保存庫、啟用 VM 的保護功能，並建立初始復原點。 若要深入了解 Azure 備份和復原服務，請繼續進行教學課程。

> [!div class="nextstepaction"]
> [備份多個 Azure VM](./tutorial-backup-vm-at-scale.md)
