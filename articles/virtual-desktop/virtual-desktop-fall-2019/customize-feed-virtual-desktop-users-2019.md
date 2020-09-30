---
title: 自訂 Windows 虛擬桌面 (傳統) 使用者的摘要-Azure
description: 如何使用 PowerShell Cmdlet 為 Windows 虛擬桌面 (傳統) 使用者自訂摘要。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cd7496690ec88fbe4297386c32d1b8a2c3234577
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540756"
---
# <a name="customize-feed-for-windows-virtual-desktop-classic-users"></a>自訂 Windows 虛擬桌面 (傳統) 使用者的摘要

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../customize-feed-for-virtual-desktop-users.md)。

您可以自訂摘要，讓 RemoteApp 和遠端桌面資源以可辨識的方式顯示給您的使用者。

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。 之後，請執行下列 Cmdlet 來登入您的帳戶：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>自訂 RemoteApp 的顯示名稱

您可以藉由設定易記名稱，來變更已發行 RemoteApp 的顯示名稱。 根據預設，易記名稱與 RemoteApp 程式的名稱相同。

若要取得應用程式群組的已發佈 Remoteapp 清單，請執行下列 PowerShell Cmdlet：

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![已反白顯示 [名稱] 和 [FriendlyName] 的 PowerShell Cmdlet RDSRemoteApp 的螢幕擷取畫面，以自訂顯示名稱。](../media/get-rdsremoteapp.png)

若要將易記名稱指派給 RemoteApp，請執行下列 PowerShell Cmdlet：

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![已反白顯示 [名稱] 和 [新增 FriendlyName] 的 PowerShell Cmdlet 設定-RDSRemoteApp 的螢幕擷取畫面，以自訂顯示名稱。](../media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>自訂遠端桌面的顯示名稱

您可以藉由設定易記名稱，來變更已發佈之遠端桌面的顯示名稱。 如果您透過 PowerShell 手動建立主機集區和桌面應用程式群組，預設的易記名稱就是「會話桌面」。 如果您透過 GitHub Azure Resource Manager 範本或 Azure Marketplace 供應專案建立主機集區和桌面應用程式群組，預設的易記名稱會與主機集區名稱相同。

若要取得遠端桌面資源，請執行下列 PowerShell Cmdlet：

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![已反白顯示 [名稱] 和 [FriendlyName] 的 PowerShell Cmdlet RDSRemoteApp 螢幕擷取畫面。](../media/get-rdsremotedesktop.png)

若要為遠端桌面資源指派易記名稱，請執行下列 PowerShell Cmdlet：

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![已反白顯示 [名稱] 和 [新增 FriendlyName] 的 PowerShell Cmdlet 設定-RDSRemoteApp 的螢幕擷取畫面。](../media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>後續步驟

既然您已自訂使用者的摘要，您可以登入 Windows 虛擬桌面用戶端來進行測試。若要這樣做，請繼續進行「連線到 Windows 虛擬桌面」操作說明：

 * [從 Windows 10 或 Windows 7 連線](connect-windows-7-10-2019.md)
 * [從網頁瀏覽器連線](connect-web-2019.md)
