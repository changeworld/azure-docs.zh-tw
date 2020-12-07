---
title: 自動化 Azure Migrate 中的無代理程式 VMware 遷移
description: 說明如何使用腳本，在 Azure Migrate 中遷移大量的 VMware Vm。
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: cdae1fe13f8e08cb6b817f8ec6431c77013020d7
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754261"
---
# <a name="scale-migration-of-vmware-vms"></a>調整 VMware Vm 的規模 

本文可協助您瞭解如何使用腳本，將大量的 VMware 虛擬機器遷移 (Vm) 使用無代理程式方法。 若要調整遷移，您可以使用 [Azure Migrate PowerShell 模組](./tutorial-migrate-vmware-powershell.md)。 

Azure Migrate VMware 遷移自動化腳本可在 GitHub 上的 [Azure PowerShell 範例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) 存放庫下載。 您可以使用這些腳本，將 VMware Vm 遷移至使用無代理程式遷移方法的 Azure。 這些腳本中使用的 Azure Migrate PowerShell 命令記載于 [此處](./tutorial-migrate-vmware-powershell.md)。

## <a name="current-limitations"></a>目前的限制
- 這些腳本支援使用所有磁片來遷移 VMware Vm。 如果您想要選擇性地複寫連接至 VMware VM 的磁片，您可以更新腳本。 
- 腳本支援使用評估建議。 如果未使用評定建議，則連接到 VMware VM 的所有磁片都會遷移至相同的受控磁片類型 (Standard 或 Premium) 。 如果您想要使用多個具有相同 VM 的受控磁片類型，您可以更新腳本

## <a name="prerequisites"></a>先決條件

- [完成探索教學課程](tutorial-discover-vmware.md)，準備 Azure 和 VMware 以進行移轉。
- 建議您先完成第二個教學課程以[評估 VMware VM](./tutorial-assess-vmware-azure-vm.md)，再將其遷移至 Azure。
- 您有 Azure PowerShell `Az` 模組。 如果您需要安裝或升級 Azure PowerShell，請依照此[安裝和設定 Azure PowerShell 的指南](/powershell/azure/install-az-ps)操作

## <a name="install-azure-migrate-powershell-module"></a>安裝 Azure Migrate PowerShell 模組

Azure Migrate PowerShell 模組可供預覽。 您必須使用下列命令來安裝 PowerShell 模組。 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="csv-input-file"></a>CSV 輸入檔
當您完成所有必要條件之後，您必須建立一個 CSV 檔案，其中包含您想要遷移的每個來源 VM 的資料。 所有指令碼是設計成在相同的 CSV 檔案上運作。 範例 CSV 範本可以在指令碼資料夾中取得，供您參考。 Csv 檔案是可設定的，因此您可以使用評量建議，甚至指定是否不會針對特定的 VM 觸發特定的作業。 

> [!NOTE]
> 您可以使用相同的 csv 檔案，在多個 Azure Migrate 專案中遷移 Vm。

### <a name="csv-file-schema"></a>CSV 檔案架構

**欄標題** | **說明**
--- | ---
AZMIGRATEPROJECT_SUBSCRIPTION_ID | 提供 Azure Migrate 專案訂用帳戶識別碼。
AZMIGRATEPROJECT_RESOURCE_GROUP_NAME | 提供 Azure Migrate 資源組名。
AZMIGRATEPROJECT_NAME | 提供您要在其中遷移伺服器之 Azure Migrate 專案的名稱。 
SOURCE_MACHINE_NAME | 在 Azure Migrate 專案中，為探索到的 VM 提供易記名稱 (顯示名稱) 。
AZMIGRATEASSESSMENT_NAME | 提供需要用來進行遷移的評定名稱。
AZMIGRATEGROUP_NAME | 提供用於 Azure Migrate 評量的組名。
TARGET_RESOURCE_GROUP_NAME | 提供 VM 需要遷移至的 Azure 資源組名。
TARGET_VNET_NAME| 提供已遷移 VM 應使用的 Azure 虛擬網路名稱。
TARGET_SUBNET_NAME | 提供已遷移 VM 應使用之目標虛擬網路中的子網名稱。 如果保留空白，則會使用「預設」子網。
TARGET_MACHINE_NAME | 提供遷移的 VM 應該在 Azure 中使用的名稱。 如果保留空白，則會使用來源電腦名稱稱。  
TARGET_MACHINE_SIZE | 提供 VM 應該在 Azure 中使用的 SKU。 若要將 VM 遷移至 Azure 中 D2_v2 VM，請在此欄位中將值指定為 "Standard_D2_v2"。 如果您使用評量，則會根據評估建議來衍生此值。
LICENSE_TYPE | 如果您想要使用 Windows Server Vm 的 Azure Hybrid Benefit，請指定。 使用 "WindowsServer" 值來利用 Azure Hybrid Benefit。 否則保留空白或使用 "NoLicenseType"。
OS_DISK_ID | 提供要遷移之 VM 的 OS 磁片識別碼。 要使用的磁片識別碼是使用 Get-AzMigrateServer Cmdlet 抓取之磁片的唯一識別碼 (UUID) 屬性。 如果未提供任何值，腳本將會使用 VM 的第一個磁片作為 OS 磁片。
TARGET_DISKTYPE | 提供要用於 Azure 中 VM 之所有磁片的磁片類型。 適用于高階受控磁片的 ' Premium_LRS '，標準 SSD 磁片為 ' StandardSSD_LRS '，而 ' Standard_LRS ' 則使用標準 HDD 磁片。 如果您選擇使用評量，則腳本會針對 VM 的每個磁片使用建議的磁片類型來排定優先順序。 如果您未使用評量或指定任何值，則腳本預設會使用標準 HDD 磁片。    
AVAILABILITYZONE_NUMBER | 指定要用於已遷移 VM 的可用性區域編號。 如果您不想要使用可用性區域，您可以將其保留空白。 
AVAILABILITYSET_NAME | 指定要用於已遷移 VM 的可用性設定組名稱。 如果您不想要使用可用性設定組，可以將此空白留白。
TURNOFF_SOURCESERVER | 如果您想要在遷移時關閉來源 VM，請指定 ' Y '。 否則請使用 ' N '。 如果保留空白，則腳本會假設值為 ' N '。
TESTMIGRATE_VNET_NAME | 指定要用於測試遷移的虛擬網路名稱。
UPDATED_TARGET_RESOURCE_GROUP_NAME | 如果您想要更新資源群組以供 Azure 中已遷移的 VM 使用，請指定 Azure 資源群組的名稱，否則請保留空白。 
UPDATED_TARGET_VNET_NAME | 如果您想要更新要供 Azure 中已遷移 VM 使用的虛擬網路，請指定 Azure 虛擬網路的名稱，否則請保留空白。
UPDATED_TARGET_MACHINE_NAME | 如果您想要更新 Azure 中已遷移 VM 所使用的名稱，請指定要使用的新名稱，否則請保留空白。
UPDATED_TARGET_MACHINE_SIZE | 如果您想要更新 Azure 中已遷移 VM 所使用的 SKU，請指定要使用的新 SKU，否則請保留空白。
UPDATED_AVAILABILITYZONE_NUMBER | 如果您想要更新可用性區域，以供 Azure 中已遷移的 VM 使用，請指定要使用的新可用性區域，否則請保留空白。
UPDATED_AVAILABILITYSET_NAME | 如果您想要更新 Azure 中已遷移 VM 所使用的可用性設定組，請指定要使用的新可用性設定組，否則請保留空白。
UPDATE_NIC1_ID | 指定要更新之 NIC 的識別碼。 如果保留空白，則腳本會假設值為探索到之 VM 的第一個 NIC。 如果您不想要更新 VM 的 NIC，請將包含 NIC 名稱的所有欄位保留空白。 
UPDATED_TARGET_NIC1_SELECTIONTYPE | 指定此 NIC 要使用的值。 使用「主要」、「次要」或「DoNotCreate」來指定此 NIC 應該是主要、次要或不應該在已遷移的 VM 上建立。 您只能將一個 NIC 指定為 VM 的主要 NIC。 如果您不想要更新，請保留空白。
UPDATED_TARGET_NIC1_SUBNET_NAME | 指定要用於已遷移 VM 上 NIC 的子網名稱。 如果您不想要更新，請保留空白。
UPDATED_TARGET_NIC1_IP | 如果您想要使用靜態 IP，請指定要供已遷移 VM 上的 NIC 使用的 IPv4 位址。 如果您想要自動指派 IP，請使用「自動」。 如果您不想要更新，請保留空白。
UPDATE_NIC2_ID | 指定要更新之 NIC 的識別碼。 如果保留空白，則腳本會假設值為探索到之 VM 的第二個 NIC。 如果您不想要更新 VM 的 NIC，請將包含 NIC 名稱的所有欄位保留空白。
UPDATED_TARGET_NIC2_SELECTIONTYPE | 指定此 NIC 要使用的值。 使用「主要」、「次要」或「DoNotCreate」來指定此 NIC 應該是主要、次要或不應該在已遷移的 VM 上建立。 您只能將一個 NIC 指定為 VM 的主要 NIC。 如果您不想要更新，請保留空白。
UPDATED_TARGET_NIC2_SUBNET_NAME | 指定要用於已遷移 VM 上 NIC 的子網名稱。 如果您不想要更新，請保留空白。
UPDATED_TARGET_NIC2_IP | 如果您想要使用靜態 IP，請指定要供已遷移 VM 上的 NIC 使用的 IPv4 位址。 如果您想要自動指派 IP，請使用「自動」。 如果您不想要更新，請保留空白。
OK_TO_UPDATE | 使用 ' Y ' 指出當您執行 AzMigrate_UpdateMachineProperties 腳本時，是否需要更新 VM 屬性。 請使用 ' N '，否則請保留空白。
OK_TO_MIGRATE | 使用 ' Y ' 指出當您執行 AzMigrate_StartMigration 腳本時，是否應遷移 VM。 如果您不想要遷移 VM，請使用 ' N ' 或保留空白。 
OK_TO_USE_ASSESSMENT | 使用 ' Y ' 來指出當您執行 AzMigrate_StartReplication 腳本時，VM 是否應使用評估建議來開始複寫。 這會覆寫 csv 檔案中的 TARGET_MACHINE_SIZE 和 TARGET_DISKTYPE 值。 如果您不想要使用評量建議，請使用 ' N ' 或保留空白。
OK_TO_TESTMIGRATE | 當您執行 AzMigrate_StartTestMigration 腳本時，請使用 ' Y ' 來指出 VM 是否應遷移測試。 如果您不想要測試遷移 VM，請使用 ' N ' 或保留空白。 
OK_TO_RETRIEVE_REPLICATIONSTATUS | 使用 ' Y ' 指出當您執行 AzMigrate_ReplicationStatus 腳本時，是否應更新 VM 的複寫狀態。 如果您不想要更新複寫狀態，請使用 ' N ' 或保留空白。
OK_TO_CLEANUP | 使用 ' Y ' 指出當您執行 AzMigrate_StopReplication 腳本時，是否要清除 VM 的複寫。 請使用 ' N '，否則請保留空白。
OK_TO_TESTMIGRATE_CLEANUP | 使用 ' Y ' 指出當您執行 AzMigrate_CleanUpTestMigration 腳本時，是否要清除 VM 的測試遷移。 請使用 ' N '，否則請保留空白。


## <a name="script-execution"></a>指令碼執行

CSV 準備就緒之後，您就可以執行下列步驟來遷移您的內部部署 VMware Vm。

**步驟 #** | **腳本名稱** | **說明**
--- | --- | ---
1 | AzMigrate_StartReplication.ps1 | 針對 csv 中列出的所有 Vm 啟用複寫，腳本會建立 CSV 輸出和記錄檔以進行疑難排解。
2 | AzMigrate_ReplicationStatus.ps1 | 檢查複寫的狀態，腳本會建立 csv 輸出，其中包含每個 VM 的狀態和記錄檔以進行疑難排解。
3 | AzMigrate_UpdateMachineProperties.ps1 | 一旦 Vm 完成初始複寫，請使用此腳本來更新 VM 的目標屬性， (計算和網路屬性) 。 腳本會建立 CSV 輸出，其中包含每個 VM 的作業詳細資料。
4 | AzMigrate_StartTestMigration.ps1 |  針對 csv 中列出的所有 Vm 啟動測試容錯移轉，這些 Vm 已針對測試遷移進行設定。 腳本會建立 CSV 輸出，其中包含每個 VM 的作業詳細資料。
5 | AzMigrate_CleanUpTestMigration.ps1 | 當您手動驗證已容錯移轉的 Vm 之後，請使用此腳本來清除 csv 中列出的所有 Vm 的測試容錯移轉 Vm，這些 Vm 已設定為測試遷移清除。 腳本會建立 CSV 輸出，其中包含每個 VM 的作業詳細資料。
6 | AzMigrate_StartMigration.ps1 | 針對 csv 中列出的所有 Vm （已設定要進行遷移）開始遷移。 腳本會建立 CSV 輸出，其中包含每個 VM 的作業詳細資料。
7 | AzMigrate_StopReplication.ps1 | 在 VM 成功遷移之後停止 VM 的複寫，或者，如果您因為其他原因而想要取消複寫。 腳本會建立 CSV 輸出，其中包含每個 VM 的作業詳細資料。


下列腳本是由其他腳本針對所有 Azure Migrate 作業（例如啟用複寫、開始測試遷移、更新 VM 屬性等）叫用。 確定所有腳本都存在於相同的資料夾/路徑中。 

**步驟 #** | **腳本名稱** | **說明**
--- | --- | ---
1 | AzMigrate_Shared.ps1 | 包含函式的通用腳本，可透過 API) 、探索到的 Vm 和複寫 Vm 來取得 (的評量屬性。 
2 | AzMigrate_CSV_Processor.ps1 | 一般腳本，包含用於 csv 檔案作業的函式，包括載入、讀取和列印記錄檔。 
3 | AzMigrate_Logger.ps1 | 針對 Azure Migrate automation 作業產生記錄檔時所叫用的一般腳本。 記錄檔的格式會是 log.Scriptname.Datetime.txt。

除了上述以外，此資料夾還包含 AzMigrate_Template.ps1，其中包含用來建立不同 Azure Migrate 作業之自訂腳本的基本架構架構。 

### <a name="script-execution-syntax"></a>腳本執行語法

一旦您下載了腳本，就可以依照下列方式執行腳本。

如果您想要使用 Input.csv 檔案來執行腳本，以啟動 Vm 的複寫，請使用下列語法。 

```azurepowershell
".\AzMigrate_StartReplication.ps1" .\Input.csv 
```

若要深入瞭解如何使用 Azure PowerShell 來遷移具有 Azure Migrate 的 VMware Vm，請遵循 [本教學](./tutorial-migrate-vmware-powershell.md)課程。