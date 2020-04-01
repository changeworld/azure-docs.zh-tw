---
title: 將 Azure VM 移至新訂閱或資源群組
description: 使用 Azure 資源管理員將虛擬機器移動到新的資源組或訂閱。
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: df34268b7741f76621c290e9979cf24d828ddc09
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478660"
---
# <a name="move-guidance-for-virtual-machines"></a>移動虛擬機器指南

本文介紹了當前不支援的方案以及使用備份移動虛擬機的步驟。

## <a name="scenarios-not-supported"></a>不支援的機制

尚未支援下列案例：

* 無法將可用區域中的託管磁碟移到其他訂閱。
* 無法移動具有標準 SKU 負載均衡器或標準 SKU 公共 IP 的虛擬機器規模集。
* 從 Marketplace 資源建立且附加方案的虛擬機器無法在資源群組或訂用帳戶之間移動。 取消在當前訂閱中的虛擬機預配,並在新訂閱中再次部署。
* 當您未移動虛擬網路中的所有資源時,無法將現有虛擬網路中的虛擬機器移動到新訂閱。
* 低優先順序虛擬機和低優先順序虛擬機器規模集無法跨資源組或訂閱移動。
* 可用性集中的虛擬機無法單獨移動。

## <a name="virtual-machines-with-azure-backup"></a>具有 Azure 備份虛擬機器

要移動配置 Azure 備份的虛擬機,必須從保管庫中刪除還原點。

如果為虛擬機啟用[了軟刪除](../../../backup/backup-azure-security-feature-cloud.md),則在保留這些還原點時無法移動虛擬機器。 [關閉軟刪除](../../../backup/backup-azure-security-feature-cloud.md#disabling-soft-delete)或刪除還原點後等待 14 天。

### <a name="portal"></a>入口網站

1. 選擇配置為備份的虛擬機器。

1. 在左側窗格中,選擇 **「備份**」。。

1. 選擇 **「停止備份**」。

1. 選擇 **「刪除回資料**」。

1. 刪除完成後,您可以將保管庫和虛擬機器移動到目標訂閱。 移動後,您可以繼續備份。

### <a name="powershell"></a>PowerShell

* 尋找您虛擬機器的位置。
* 尋找具有下列命名模式的資源群組：`AzureBackupRG_<location of your VM>_1`，例如 AzureBackupRG_westus2_1
* 如果是在 PowerShell 中，請使用 `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` Cmdlet
* 尋找類型為 `Microsoft.Compute/restorePointCollections` 且命名模式為 `AzureBackup_<name of your VM that you're trying to move>_###########` 的資源
* 刪除此資源。 此作業只會刪除立即復原點，而不會刪除保存庫中備份的資料。

### <a name="azure-cli"></a>Azure CLI

* 尋找您虛擬機器的位置。
* 尋找具有下列命名模式的資源群組：`AzureBackupRG_<location of your VM>_1`，例如 AzureBackupRG_westus2_1
* 如果是在 CLI 中，請使用 `az resource list -g AzureBackupRG_<location of your VM>_1`
* 尋找類型為 `Microsoft.Compute/restorePointCollections` 且命名模式為 `AzureBackup_<name of your VM that you're trying to move>_###########` 的資源
* 刪除此資源。 此作業只會刪除立即復原點，而不會刪除保存庫中備份的資料。

## <a name="next-steps"></a>後續步驟

* 如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](../move-resource-group-and-subscription.md)。

* 如需移動復原服務保存庫以進行備份的相關資訊，請參閱[復原服務限制](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)。
