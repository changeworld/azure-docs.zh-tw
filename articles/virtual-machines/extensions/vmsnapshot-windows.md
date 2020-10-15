---
title: 適用於 Azure 備份的 VM Snapshot Windows 擴充功能
description: 使用 VM Snapshot 擴充功能，從 Azure Backup 採用與虛擬機器備份一致的應用程式
services: backup, virtual-machines-windows
documentationcenter: ''
author: trinadhkotturu
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 10/15/2020
ms.author: trinadhk
ms.openlocfilehash: d771c6dd114b252c070a0e56ba3ab23c597ad5aa
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92088851"
---
# <a name="vm-snapshot-windows-extension-for-azure-backup"></a>適用於 Azure 備份的 VM Snapshot Windows 擴充功能

Azure 備份支援將工作負載從內部部署備份至雲端，以及將雲端資源備份至復原服務保存庫。 Azure 備份使用 VM Snapshot 擴充功能採用與 Azure 虛擬機器備份一致的應用程式，而不需要關閉 VM。 Microsoft 將 VM Snapshot 擴充功能當作 Azure 備份服務的一部分發行並提供支援。 Azure 備份將會在第一次排程備份觸發的啟用後備份時，安裝擴充功能。 本文件詳述適用於 VM Snapshot 擴充功能所支援的平台、組態和部署選項。

VMSnapshot 延伸模組只會出現在非受控 Vm 的 Azure 入口網站中。

## <a name="prerequisites"></a>必要條件

### <a name="operating-system"></a>作業系統
如需支援的作業系統清單，請參閱 [Azure 備份支援的作業系統](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 顯示 VM Snapshot 擴充功能的結構描述。 此擴充功能需要工作識別碼 (用來識別 VM 上觸發快照集的備份作業)、狀態 Blob URI (寫入快照集作業狀態所在位置)、排程的快照集開始時間、記錄 Blob URI (寫入對應至快照集工作的記錄所在位置)、objstr (表示 VM 磁碟和中繼資料)。  由於這些設定應該被視為敏感性資料，因此應該儲存在受保護的設定組態中。 Azure VM 擴充功能保護的設定資料會經過加密，只會在目標虛擬機器上解密。 請注意，建議僅在備份作業進行時，從 Azure 備份服務傳遞這些設定。

```json
{
  "type": "extensions",
  "name": "VMSnapshot",
  "location":"<myLocation>",
  "properties": {
    "publisher": "Microsoft.Azure.RecoveryServices",
    "type": "VMSnapshot",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "locale":"<location>",
      "taskId":"<taskId used by Azure Backup service to communicate with extension>",
      "commandToExecute": "snapshot",
      "commandStartTimeUTCTicks": "<scheduled start time of the snapshot task>",
      "vmType": "microsoft.compute/virtualmachines"
    },
    "protectedSettings": {
      "objectStr": "<blob SAS uri representation of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 | 資料類型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | 字串 |
| commandStartTimeUTCTicks | 6.36458E+17 | 字串 |
| locale | zh-tw | 字串 |
| objectStr | sas uri 陣列的編碼 - "blobSASUri": ["https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmwin1404ltsc201652903941.vhd?sv=2014-02-14&sr=b&sig=TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna8461.blob.core.windows.net\/vhds\/vmwin1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&sig=5S0A6YDWvVwqPAkzWXVy%2BS%2FqMwzFMbamT5upwx05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna8461.blob.core.windows.net\/bootdiagnostics-vmwintu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmwin1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r%2BC%2BNIAork%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmwin1404ltsc-20170705-124311.vhd?sv=2014-02-14&sr=b&sig=ZUM9d28Mvvm%2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw"] | 字串 |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | 字串 |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | 字串 |



## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 不過，將 VM Snapshot 擴充功能新增至虛擬機器的建議方式是在虛擬機器上啟用備份。 這可以透過 Resource Manager 範本來達成。  在 [Azure 快速啟動資源庫](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/)上可找到在虛擬機器上啟用備份的範例 Resource Manager 範本。


## <a name="azure-cli-deployment"></a>Azure CLI 部署

您可以使用 Azure CLI，在虛擬機器上啟用備份。 啟用後備份，第一個排程備份作業將會在 VM 上安裝 VM Snapshot 擴充功能。

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

使用 Azure CLI，就可以從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看指定 VM 的擴充功能部署狀態，請使用 Azure CLI 執行下列命令。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

擴充功能執行輸出會記錄至下列檔案︰

```
C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot
```

### <a name="error-codes-and-their-meanings"></a>錯誤碼及其意義

疑難排解資訊可以在 [Azure VM 備份疑難排解指南](../../backup/backup-azure-vms-troubleshoot.md)上找到。

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
