---
title: 教學課程：使用 CLI 管理備份的 SAP Hana DB
description: 在本教學課程中，您將了解如何使用 Azure CLI 來管理在 Azure VM 上執行的已備份 SAP Hana 資料庫。
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 2c088c27a678a4541cbba3c4c43c9cd830c60ff0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "79222455"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>教學課程：使用 Azure CLI 在 Azure VM 中管理 SAP Hana 資料庫

Azure CLI 可用來從命令列或透過指令碼建立和管理 Azure 資源。 本文件將詳細說明如何在 Azure VM 上管理已備份的 SAP Hana 資料庫 - 全部都使用 Azure CLI。 您也可以使用 [Azure 入口網站](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)來執行這些步驟。

使用 [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) 來執行 CLI 命令。

完成本教學課程時，您將能夠：

> [!div class="checklist"]
>
> * 監視備份和還原作業
> * 保護新增至 SAP Hana 執行個體的新資料庫
> * 變更原則
> * 停止保護
> * 繼續保護

如果您已使用 [使用 CLI 在 Azure 中備份 SAP Hana 資料庫](tutorial-sap-hana-backup-cli.md)來備份 SAP Hana 資料庫，則您使用的是下列資源：

* 名為 saphanaResourceGroup  的資源群組
* 名為 saphanaVault  的保存庫
* 名為 VMAppContainer;Compute;saphanaResourceGroup;saphanaVM  的受保護容器
* 名為 saphanadatabase;hxe;hxe  的已備份資料庫/項目
* westus2  區域中的資源

Azure CLI 可讓您輕鬆地管理在 Azure VM 上執行且使用 Azure 備份進行備份的 SAP Hana 資料庫。 本教學課程會詳細說明每個管理作業。

## <a name="monitor-backup-and-restore-jobs"></a>監視備份和還原作業

若要監視已完成或目前正在執行的作業 (備份或還原)，請使用 [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) Cmdlet。 CLI 也可讓您[暫停目前執行中的工作](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop)或[等候作業完成](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)。

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

輸出會看起來類似這樣：

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>變更原則

若要變更 SAP Hana 備份設定的基礎原則，請使用 [az backup policy set](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-set) Cmdlet。 此 Cmdlet 中的 name 參數會參考我們想要變更其原則的備份項目。 在本教學課程中，我們會將 SAP Hana 資料庫 saphanadatabase;hxe;hxe  的原則取代為新的原則 newsaphanaPolicy  。 您可以使用 [az backup policy create](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) Cmdlet 來建立新的原則。

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

輸出看起來應該會像下面這樣：

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>保護新增至 SAP Hana 執行個體的新資料庫

[向復原服務保存庫註冊 SAP Hana 執行個體](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance)會自動探索此執行個體上的所有資料庫。

不過，如果之後有新的資料庫新增至 SAP Hana 執行個體，則應使用 [az backup protectable-item initialize](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-initialize)Cmdlet。 此 Cmdlet 會探索新增的新資料庫。

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

然後使用 [az backup protectable-item list](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) Cmdlet 來列出在您 SAP Hana 執行個體上探索到的所有資料庫。 不過，這份清單會排除已設定備份的資料庫。 一旦探索到要備份的資料庫之後，請參閱[在 SAP Hana 資料庫上啟用備份](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database)。

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

此清單中上會顯示您要備份的新資料庫，如下所示：

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>停止 SAP Hana 資料庫的保護

您可以用幾種方式來停止保護 SAP Hana 資料庫：

* 停止所有未來的備份作業並刪除所有復原點。
* 停止所有未來的備份作業但保留復原點不變。

如果您選擇保留復原點，請記住下列詳細資料：

* 所有復原點會永遠保持不變，而停止保護但保留資料時，所有清除作業都應該停止。
* 您需支付受保護執行個體和已使用儲存體的費用。
* 如果您刪除資料來源，但沒有停止備份，新的備份將會失敗。

讓我們深入查看每一種停止保護的方式。

### <a name="stop-protection-with-retain-data"></a>停止使用保留資料來進行保護

若要停止保護但保留資料，請使用 [az backup protection disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) Cmdlet。

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

輸出看起來應該會像下面這樣：

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

若要檢查此作業的狀態，請使用 [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) Cmdlet。

### <a name="stop-protection-without-retain-data"></a>停止保護且不保留資料

若要停止保護且不保留資料，請使用 [az backup protection disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) Cmdlet。

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

輸出看起來應該會像下面這樣：

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

若要檢查此作業的狀態，請使用 [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) Cmdlet。

## <a name="resume-protection"></a>繼續保護

當您停止保護 SAP Hana 資料庫但保留資料時，您可以在稍後恢復保護。 如果您沒有保留備份的資料，就無法恢復保護。

若要恢復保護，請使用 [az backup protection resume](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) Cmdlet。

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

輸出看起來應該會像下面這樣：

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

若要檢查此作業的狀態，請使用 [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) Cmdlet。

## <a name="next-steps"></a>後續步驟

* 若要了解如何使用 Azure 入口網站備份在 Azure VM 中執行的 SAP Hana 資料庫，請參閱[在 Azure VM 上備份 SAP Hana 資料庫](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

* 若要了解如何使用 Azure 入口網站管理在 Azure VM 中執行的已備份 SAP Hana 資料庫，請參閱[在 Azure VM 上管理備份的 SAP Hana 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
