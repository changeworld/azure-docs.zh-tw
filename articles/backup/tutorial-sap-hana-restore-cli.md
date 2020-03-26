---
title: 教學課程 - 使用 CLI 在 Azure 上還原 SAP HANA DB
description: 在本教學課程中，您將了解如何使用 Azure CLI 從 Azure 備份復原服務保存庫還原執行於 Azure VM 上的 SAP HANA 資料庫。
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75470403"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>教學課程：使用 Azure CLI 在 Azure VM 中還原 SAP HANA 資料庫

Azure CLI 可用來從命令列或透過指令碼建立和管理 Azure 資源。 本文件將詳細說明如何使用 Azure CLI 在 Azure VM 上還原已備份的 SAP HANA 資料庫。 您也可以使用 [Azure 入口網站](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)來執行這些步驟。

使用 [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) 來執行 CLI 命令。

完成本教學課程時，您將能夠：

> [!div class="checklist"]
>
> * 檢視所備份資料庫的還原點
> * 還原資料庫

本教學課程假設您擁有在 Azure VM 上執行的 SAP HANA 資料庫，且會使用 Azure 備份來加以備份。 如果您已使用[使用 CLI 在 Azure 中備份 SAP Hana 資料庫](tutorial-sap-hana-backup-cli.md)來備份 SAP Hana 資料庫，則您使用的是下列資源：

* 名為 saphanaResourceGroup  的資源群組
* 名為 saphanaVault  的保存庫
* 名為 VMAppContainer;Compute;saphanaResourceGroup;saphanaVM  的受保護容器
* 名為 saphanadatabase;hxe;hxe  的已備份資料庫/項目
* westus2  區域中的資源

## <a name="view-restore-points-for-a-backed-up-database"></a>檢視所備份資料庫的還原點

若要檢視資料庫的所有復原點清單，請使用 [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) Cmdlet，如下所示：

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

復原點清單看起來會像下面這樣：

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

如您所見，上述清單包含三個復原點：各自用於完整、差異和記錄備份。

>[!NOTE]
>您也可以使用 [az backup recoverypoint show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) Cmdlet，來檢視每個未中斷記錄備份鏈結的起點和終點。

## <a name="prerequisites-to-restore-a-database"></a>用以還原資料庫的必要條件

在還原資料庫之前，請先確定您符合下列必要條件：

* 您只能將資料庫還原到相同區域中的 SAP HANA 執行個體
* 目標執行個體必須使用相同的來源保存庫進行註冊
* Azure 備份無法在相同的 VM 上識別出兩個不同的 SAP HANA 執行個體。 因此，您無法在相同 VM 上將資料從某個執行個體還原到另一個執行個體。

## <a name="restore-a-database"></a>還原資料庫

Azure 備份可以還原在 Azure VM 上執行的 SAP HANA 資料庫，如下所示：

* 使用記錄備份還原至特定日期或時間 (可精確到秒)。 Azure 備份會根據選取的時間，自動決定還原所需的適當完整差異備份和記錄備份鏈結。
* 還原至特定的完整或差異備份以還原至特定復原點。

若要還原資料庫，請使用 [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) Cmdlet，這需要某個復原設定物件來作為其中一個輸入。 這個物件可使用 [az backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) Cmdlet 來加以產生。 復原設定物件包含用來執行還原的所有詳細資料。 其中一個詳細資料是還原模式 – **OriginalWorkloadRestore** 或 **AlternateWorkloadRestore**。

>[!NOTE]
> **OriginalWorkloadRestore** - 將資料還原至與原始來源相同的 SAP HANA 執行個體。 此選項會覆寫原始資料庫。 <br>
> **AlternateWorkloadRestore** - 將資料庫還原至替代位置，並保留原始來源資料庫。

## <a name="restore-to-alternate-location"></a>還原至替代位置

若要將資料庫還原至替代位置，請使用 **AlternateWorkloadRestore** 作為還原模式。 接著，您必須選擇還原點，這可以是先前的時間點或任何先前的還原點。

在本教學課程中，您將還原到先前的還原點。 [檢視資料庫的還原點清單](#view-restore-points-for-a-backed-up-database)，然後選擇要作為還原目標的還原點。 本教學課程會使用名稱為 7660777527047692711  的還原點。

讓我們使用上述還原點名稱和還原模式，透過 [az backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) Cmdlet 來建立復原設定物件。 讓我們看看此 Cmdlet 中的其餘參數各自代表什麼：

* **--target-item-name** 這是所還原的資料庫將會使用的名稱。 在本案例中，我們使用的名稱為 restored_database  。
* **--target-server-name** 這是已成功向復原服務保存庫完成註冊，並且和所要還原的資料庫位於相同區域的 SAP HANA 伺服器所具有的名稱。 在本教學課程中，我們會將資料庫還原到我們已保護的相同 SAP HANA 伺服器，其名稱為 hxehost  。
* **--target-server-type** 若要還原 SAP HANA 資料庫，則必須使用 **SapHanaDatabase**。

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

上述查詢的回應將會是如下所示的復原設定物件：

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

現在，為了還原資料庫，請執行 [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) Cmdlet。 為了使用此命令，我們將輸入上述 json 輸出，其已儲存至名為 recoveryconfig.json  的檔案中。

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

輸出將看起來如下：

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

回應會提供您作業名稱。 使用 [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) Cmdlet，即可透過此作業名稱來追蹤作業狀態。

## <a name="restore-and-overwrite"></a>還原並覆寫資料庫

為了還原到原始位置，我們將使用 **OrignialWorkloadRestore** 作為還原模式。 接著，您必須選擇還原點，這可以是先前的時間點或任何先前的還原點。

在本教學課程中，我們選擇先前的時間點 “28-11-2019-09:53:00” 來作為還原目標。 您可以使用下列格式來提供此還原點：dd-mm-yyyy, dd-mm-yyyy-hh:mm:ss。 為了選擇有效的還原目標時間點，請使用 [az backup recoverypoint show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) Cmdlet，以列出未中斷記錄鏈結備份的間隔。

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

上述查詢的回應將會是如下所示的復原設定物件：

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

現在，為了還原資料庫，請執行 [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) Cmdlet。 為了使用此命令，我們將輸入上述 json 輸出，其已儲存至名為 recoveryconfig.json  的檔案中。

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

輸出將看起來如下：

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

回應會提供您作業名稱。 使用 [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) Cmdlet，即可透過此作業名稱來追蹤作業狀態。

## <a name="next-steps"></a>後續步驟

* 若要了解如何使用 Azure CLI 來管理所備份的 SAP HANA 資料庫，請繼續進行教學課程[使用 CLI 管理 Azure VM 中的 SAP HANA 資料庫](tutorial-sap-hana-backup-cli.md)

* 若要了解如何使用 Azure 入口網站來還原在 Azure VM 中執行的 SAP HANA 資料庫，請參閱[在 Azure VM 上還原 SAP Hana 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
