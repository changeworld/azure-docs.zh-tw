---
title: 教學課程 - 使用 CLI 在 Azure 上備份 SAP Hana DB
description: 在本教學課程中，您將了解如何使用 Azure CLI 將執行於 Azure VM 上的 SAP Hana 資料庫備份至 Azure 備份復原服務保存庫。
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: f11e01c6af18cac956d58b9c692d7b57c8fe653a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324955"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>教學課程：使用 Azure CLI 在 Azure VM 中備份 SAP Hana 資料庫

Azure CLI 可用來從命令列或透過指令碼建立和管理 Azure 資源。 本文件將詳細說明如何備份 SAP Hana 資料庫和觸發隨選備份 - 全部都使用 Azure CLI。 您也可以使用 [Azure 入口網站](./backup-azure-sap-hana-database.md)來執行這些步驟。

本文件假設您已在 Azure VM 上安裝 SAP Hana 資料庫。 (您也可以[使用 Azure CLI建立 VM](../virtual-machines/linux/quick-create-cli.md))。 完成本教學課程時，您將能夠：

> [!div class="checklist"]
>
> * 建立復原服務保存庫
> * 註冊 SAP Hana 執行個體並探索其中的資料庫
> * 在 SAP Hana 資料庫上啟用備份
> * 觸發隨選備份

請查看[我們目前支援的 SAP Hana 案例](./sap-hana-backup-support-matrix.md#scenario-support)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本機安裝和使用 CLI，您必須執行 Azure CLI xx.xxx.x 版或更新版本。 若要知道 CLI 版本，執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫是一個邏輯容器，可儲存每個受保護資源 (例如 Azure VM 或 Azure VM 上執行的 SQL 或 HANA 資料庫等工作負載) 的備份資料。 執行受保護資源的備份作業時，它會在復原服務保存庫內建立復原點。 然後您可以使用其中一個復原點，將資料還原到指定的時間點。

使用 [az backup vault create](/cli/azure/backup/vault#az-backup-vault-create) 建立復原服務保存庫。 指定與您想要保護的 VM 相同的資源群組和位置。 了解如何透過此 [VM 快速入門](../virtual-machines/linux/quick-create-cli.md)來使用 Azure CLI 建立 VM。

針對本教學課程，我們將使用下列項目：

* 名為 saphanaResourceGroup 的資源群組
* 名為 saphanaVM 的 VM
* westus2 位置中的資源。

我們將會建立名為 saphanaVault 的保存庫。

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

根據預設，已針對異地備援儲存體設定復原服務保存庫。 異地備援儲存體可確保您的備份資料會複寫到與主要區域距離數百英哩的次要 Azure 區域。 如果需要修改儲存體備援設定，請使用 [az backup vault backup-properties set](/cli/azure/backup/vault/backup-properties#az-backup-vault-backup-properties-set) Cmdlet。

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

若要查看您的保存庫是否已成功建立，請使用 [az backup vault list](/cli/azure/backup/vault#az-backup-vault-list) Cmdlet。 您將看見下列回應：

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>註冊並保護 SAP Hana 執行個體

對於要由 Azure 服務探索的 SAP Hana 執行個體 (已安裝 SAP Hana 的 VM)，SAP Hana 機器上必須執行[預先註冊指令碼](https://aka.ms/scriptforpermsonhana)。 執行指令碼之前，請務必先滿足所有[必要條件](./tutorial-backup-sap-hana-db.md#prerequisites)。 若要深入了解指令碼的用途，請參閱[預先註冊指令碼的功能](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)一節。

執行指令碼之後，即可向先前建立的復原服務保存庫註冊 SAP Hana 執行個體。 若要註冊執行個體，請使用 [az backup container register](/cli/azure/backup/container#az-backup-container-register) Cmdlet。 VMResourceId 是您為了安裝 SAP Hana 所建立的 VM 資源識別碼。

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>如果 VM 所在的資源群組與保存庫不同，則 saphanaResourceGroup 會參考保存庫建立所在的資源群組。

註冊 SAP Hana 執行個體會自動探索其目前所有的資料庫。 不過，若要探索未來可能新增的任何新資料庫，請參閱[探索新增至已註冊 SAP Hana 執行個體的新資料庫](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance)一節。

若要檢查是否已成功向保存庫註冊 SAP Hana 執行個體，請使用 [az backup container list](/cli/azure/backup/container#az-backup-container-list) Cmdlet。 您將看見下列回應：

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> 上述輸出中的 "name" 資料行會參考容器名稱。 下一節將使用此容器名稱來啟用備份並觸發這些備份。 在此案例中，該名稱為 VMAppContainer;Compute;saphanaResourceGroup;saphanaVM。

## <a name="enable-backup-on-sap-hana-database"></a>在 SAP Hana 資料庫上啟用備份

針對您在上一個步驟中註冊的 SAP Hana 執行個體，您可以使用 [az backup protectable-item list](/cli/azure/backup/protectable-item#az-backup-protectable-item-list) Cmdlet 來列出從中探索到的所有資料庫。

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

您應該會在這份清單中找到您要備份的資料庫，如下所示：

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

如您在上述輸出中所見，SAP Hana 系統的 SID 是 HXE。 在本教學課程中，我們將為位於 hxehost 伺服器上的 saphanadatabase;hxe;hxe 資料庫設定備份。

為了保護和設定資料庫上的備份 (一次一個)，我們會使用 [az backup protection enable-azurewl](/cli/azure/backup/protection#az-backup-protection-enable-for-azurewl) Cmdlet。 提供您要使用的原則名稱。 若要使用 CLI 建立原則，請使用 [az backup policy create](/cli/azure/backup/policy#az-backup-policy-create) Cmdlet。 針對本教學課程，我們將使用 sapahanaPolicy原則。

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name saphanadatabase;hxe;hxe  \
    --protectable-item-type SAPHANADatabse \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

您可以使用 [az backup job list](/cli/azure/backup/job#az-backup-job-list) Cmdlet 來檢查上述備份設定是否已完成。 輸出將如下所示：

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

[az backup job list](/cli/azure/backup/job#az-backup-job-list) Cmdlet 會列出所有已執行或目前正在受保護資料庫上執行的備份作業 (已排定或隨選的備份)，還會列出註冊、設定備份、和刪除備份資料。

>[!NOTE]
>在 Azure VM 中執行 SAP HANA 資料庫備份時，Azure 備份不會自動調整日光節約時間變更。
>
>請視需要手動修改原則。

## <a name="trigger-an-on-demand-backup"></a>觸發隨選備份

上一節詳述如何設定已排定的備份，而本節將討論如何觸發隨選備份。 我們會使用 [az backup protection backup-now](/cli/azure/backup/protection#az-backup-protection-backup-now) Cmdlet 來執行此動作。

>[!NOTE]
> 隨選備份的保留原則取決於資料庫的基礎保留原則。

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

輸出將如下所示：

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

回應會提供您作業名稱。 使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) Cmdlet，即可透過此作業名稱來追蹤作業狀態。

>[!NOTE]
>除了排定完整或差異備份之外，這些備份現在也可以手動觸發。 記錄備份會由 SAP Hana 在內部自動觸發和管理。
>
> Azure 備份目前不支援增量備份。

## <a name="next-steps"></a>後續步驟

* 若要了解如何使用 CLI 在 Azure VM 中還原 SAP Hana 資料庫，請繼續進行教學課程 - [使用 CLI 在 Azure VM 中還原 SAP Hana 資料庫](tutorial-sap-hana-restore-cli.md)

* 若要了解如何使用 Azure 入口網站備份在 Azure VM 中執行的 SAP Hana 資料庫，請參閱[在 Azure VM 上備份 SAP Hana 資料庫](./backup-azure-sap-hana-database.md)
