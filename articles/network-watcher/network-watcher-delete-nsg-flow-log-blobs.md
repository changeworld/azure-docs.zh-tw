---
title: 刪除 Azure 網路觀察程式中網路安全性群組流日誌的存儲 Blob |微軟文檔
description: 本文介紹如何刪除 Azure 網路觀察程式中超出其保留原則期間之外的網路安全性群組流日誌存儲 Blob。
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2019
ms.author: damendo
ms.openlocfilehash: 6d535bcc2e0831baae658796f76c8087d74c6a85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587204"
---
# <a name="delete-network-security-group-flow-log-storage-blobs-in-network-watcher"></a>刪除網路觀察程式中的網路安全性群組流日誌存儲 Blob

目前有一個問題是，系統不會根據保留原則設定從 Blob 儲存體自動刪除網路監看員的[網路安全性群組 (NSG) 流量記錄](network-watcher-nsg-flow-logging-overview.md)。 現在，您必須運行 PowerShell 腳本才能手動從存儲帳戶中刪除流日誌，如本文所述。

## <a name="run-powershell-script-to-delete-nsg-flow-logs"></a>運行 PowerShell 腳本以刪除 NSG 流日誌
 
複製以下腳本並將其保存到當前工作目錄等位置。 

```powershell
# This powershell script deletes all NSG flow log blobs that should not be retained anymore as per configured retention policy.
# While configuring NSG flow logs on Azure portal, the user configures the retention period of NSG flow log blobs in
# their storage account (in days).
# This script reads all blobs and deletes blobs that are not to be retained (outside retention window)
# if the retention days are zero; all blobs are retained forever and hence no blobs are deleted.
#
#

param (
        [string] [Parameter(Mandatory=$true)]  $SubscriptionId,
        [string] [Parameter(Mandatory=$true)]  $Location,
        [switch] [Parameter(Mandatory=$false)] $Confirm
    )

Login-AzAccount

$SubId = Get-AzSubscription| Where-Object {$_.Id.contains($SubscriptionId.ToLower())}

if ($SubId.Count -eq 0)
{
    Write-Error 'The SubscriptionId does not exist' -ErrorAction Stop
}

Set-AzContext -SubscriptionId $SubscriptionId

$NsgList = Get-AzNetworkSecurityGroup | Where-Object {$_.Location -eq $Location}
$NW = Get-AzNetworkWatcher | Where-Object {$_.Location -eq $Location}

$FlowLogsList = @()
foreach ($Nsg in $NsgList)
{
    # Query Flow Log Status which are enabled
    $NsgFlowLog = Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $Nsg.Id | Where-Object {$_.Enabled -eq "True"}
    if ($NsgFlowLog.Count -gt 0)
    {
        $FlowLogsList +=  $NsgFlowLog
        Write-Output ('Enabled NSG found: ' +  $NsgFlowLog.TargetResourceId)
    }
}

foreach ($Psflowlog in $FlowLogsList)
{
    $RetentionDays = $Psflowlog.RetentionPolicy.Days
    if ($RetentionDays -le 0)
    {
        continue
    }

    $Strings = $Psflowlog.StorageId -split '/'
    $RGName = $Strings[4]
    $StorageAccountName = $Strings[-1]

    $Key = (Get-AzStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName).Value[1]
    $StorageAccount = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $Key

    $ContainerName = 'insights-logs-networksecuritygroupflowevent'  
    $BLobsList = Get-AzStorageBlob -Container $ContainerName -Context $StorageAccount.Context

    $TargetBLobsList = $BLobsList | Where-Object {$_.Name.contains($Psflowlog.TargetResourceId.ToUpper())}

    $RetentionDate = Get-Date
    $RetentionDate = $RetentionDate.AddDays(-1*$RetentionDays)
    $RetentionDateInUTC = $RetentionDate.ToUniversalTime()

    foreach ($Blob in $TargetBLobsList)
    {
        $BlobLastModifietedDTinUTC = [datetime]$Blob.LastModified.UtcDateTime

        if ($BlobLastModifietedDTinUTC -ge  $RetentionDateInUTC)
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> RETAINED')
            continue
        }

        if ($Confirm)
        {
            Write-Output (Blob to be deleted: $Blob.Name)
            $Confirmation = Read-Host "Are you sure you want to remove this blob (Y/N)?"
        }

        if ((-not $Confirm) -or ($Confirmation -eq 'Y'))
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> DELETED')
            Remove-AzStorageBlob -Container $ContainerName -Context $StorageAccount.Context -Blob $Blob.Name
        }
        else
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> RETAINED')
        }
    }
}

Write-Output ('Retention policy for all NSGs evaluated and completed successfully')
```

1. 根據需要在腳本中輸入以下參數：
   - **訂閱 Id** [必需]：要從中刪除 NSG 流日誌 blob 的訂閱 ID。
   - **位置**[必需]：要為其刪除 NSG 流日誌 blob 的 NSG 區域_的位置字串_。 您可以在 Azure 門戶或[GitHub](https://github.com/Azure/azure-extensions-cli/blob/beb3d3fe984cfa9c7798cb11a274c5337968cbc5/regions.go#L23)上查看此資訊。
   - **確認**[可選]：如果要手動確認刪除每個存儲 Blob，請傳遞確認標誌。

1. 運行保存的腳本，如以下示例所示，其中指令檔保存為**Delete-NsgFlowLogsBlobs.ps1**：
   ```
   .\Delete-NsgFlowLogsBlobs.ps1 -SubscriptionId <subscriptionId> -Location  <location> -Confirm
   ```
    
## <a name="next-steps"></a>後續步驟
- 客戶可以使用[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)或[Azure 自動化](https://azure.microsoft.com/services/automation/)自動運行腳本
- 要瞭解有關 NSG 日誌記錄的更多資訊，請參閱[網路安全性群組 （NSG） 的 Azure 監視器日誌](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。

