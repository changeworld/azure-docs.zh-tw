---
title: 自訂 Windows 虛擬桌面使用者的源 - Azure
description: 如何使用 PowerShell Cmdlet 自訂 Windows 虛擬桌面使用者的源。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 24a295d220cfaa7efe2fdc0d4eee53bb5c409708
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128076"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>適用於 Windows 虛擬桌面使用者的自訂摘要

您可以自訂來源，以便遠端應用和遠端桌面資源以可識別的方式顯示給使用者。

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。 之後，請執行下列 Cmdlet 來登入您的帳戶：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>自訂遠端應用的顯示名稱

您可以通過設置易記名稱來更改已發佈的 RemoteApp 的顯示名稱。 預設情況下，易記名稱與 RemoteApp 程式的名稱相同。

要檢索應用組的已發佈遠端應用清單，請運行以下 PowerShell Cmdlet：

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![突出顯示了 PowerShell Cmdlet get-RDSRemoteApp 的螢幕截圖，突出顯示了名稱和易記名稱。](media/get-rdsremoteapp.png)

要將易記名稱分配給 RemoteApp，請運行以下 PowerShell Cmdlet：

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![突出顯示了 PowerShell Cmdlet Set-RDSRemoteApp 的螢幕截圖，其中突出顯示了名稱和新的易記名稱。](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>自訂遠端桌面的顯示名稱

您可以通過設置易記名稱來更改已發佈的遠端桌面的顯示名稱。 如果通過 PowerShell 手動創建主機池和桌面應用組，則預設易記名稱為"會話桌面"。 如果通過 GitHub Azure 資源管理器範本或 Azure 應用商店產品庫創建了主機池和桌面應用組，則預設易記名稱與主機池名稱相同。

要檢索遠端桌面資源，請運行以下 PowerShell Cmdlet：

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![突出顯示了 PowerShell Cmdlet get-RDSRemoteApp 的螢幕截圖，突出顯示了名稱和易記名稱。](media/get-rdsremotedesktop.png)

要為遠端桌面資源配置易記名稱，請運行以下 PowerShell Cmdlet：

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![突出顯示了 PowerShell Cmdlet Set-RDSRemoteApp 的螢幕截圖，其中突出顯示了名稱和新的易記名稱。](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>後續步驟

現在，您已為使用者自訂了源，您可以登錄到 Windows 虛擬桌面用戶端以將其測試出來。為此，請繼續連接到 Windows 虛擬桌面操作：
    
 * [從 Windows 10 或 Windows 7 連線](connect-windows-7-and-10.md)
 * [從網頁瀏覽器連線](connect-web.md) 
