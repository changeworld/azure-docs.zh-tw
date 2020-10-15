---
title: PowerShell 指令碼 - 尋找儲存體帳戶的保存庫
description: 了解如何使用 Azure PowerShell 指令碼來尋找您的儲存體帳戶註冊所在的復原服務保存庫。
ms.topic: sample
ms.date: 1/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 40859c1ea05210d27fcdcf33ba9d4f961965ea22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89075693"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>用來尋找儲存體帳戶註冊所在之復原服務保存庫的 PowerShell 指令碼

此指令碼可協助您尋找儲存體帳戶註冊所在的復原服務保存庫。

## <a name="sample-script"></a>範例指令碼

```powershell
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId
    )

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$vaults = Get-AzRecoveryServicesVault
$found = $false
foreach($vault in $vaults)
{
  Write-Verbose "Checking vault: $($vault.Id)" -Verbose
  
  $containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -FriendlyName $StorageAccountName -ResourceGroupName $ResourceGroupName -VaultId $vault.Id -Status Registered
  
  if($containers -ne $null)
  {
    $found = $True
    Write-Information "Found Storage account $StorageAccountName registered in vault: $($vault.Id)" -InformationAction Continue
    break;
  }
}

if(!$found)
{
     Write-Information "Storage account: $StorageAccountName is not registered in any vault of this subscription" -InformationAction Continue
}
```

## <a name="how-to-execute-the-script"></a>如何執行指令碼

1. 使用您選擇的名稱，將上述指令碼儲存在您的電腦上。 在此範例中，我們將其儲存為 *FindRegisteredStorageAccount.ps1*。
2. 提供下列參數以執行指令碼︰

    * **-ResourceGroupName** - 儲存體帳戶的資源群組
    * **-StorageAccountName** - 儲存體帳戶名稱
    * **-SubscriptionID** - 儲存體帳戶所屬訂用帳戶的識別碼。

下列範例會嘗試尋找 *afsaccount* 儲存體帳戶註冊所在的復原服務保存庫：

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>輸出

輸出會顯示該儲存體帳戶註冊所在之復原服務保存庫的完整路徑。 以下是範例輸出：

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>後續步驟

了解如何[從 Azure 入口網站備份 Azure 檔案共用](../backup-afs.md)
