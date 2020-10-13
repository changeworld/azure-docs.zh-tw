---
title: 自訂 Windows 虛擬桌面使用者的摘要-Azure
description: 如何使用 PowerShell Cmdlet 自訂 Windows 虛擬桌面使用者的摘要。
author: Heidilohr
ms.topic: how-to
ms.date: 09/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: e47486f29537cb948aaae7cf17e97bae14b60700
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90084289"
---
# <a name="customize-the-feed-for-windows-virtual-desktop-users"></a>自訂 Windows 虛擬桌面使用者的摘要

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 (傳統版)，請參閱[這篇文章](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md)。

您可以自訂摘要，讓 RemoteApp 和遠端桌面資源以可辨識的方式顯示給您的使用者。

## <a name="prerequisites"></a>必要條件

本文假設您已下載並安裝 Windows 虛擬桌面 PowerShell 模組。 如果您尚未這麼做，請依照 [設定 PowerShell 模組](powershell-module.md)中的指示進行。

## <a name="customize-the-display-name-for-a-remoteapp"></a>自訂 RemoteApp 的顯示名稱

您可以藉由設定易記名稱，來變更已發行 RemoteApp 的顯示名稱。 根據預設，易記名稱與 RemoteApp 程式的名稱相同。

若要取得應用程式群組的已發佈 Remoteapp 清單，請執行下列 PowerShell Cmdlet：

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

若要將易記名稱指派給 RemoteApp，請使用必要的參數來執行下列 Cmdlet：

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

例如，假設您使用下列範例 Cmdlet 取出目前的應用程式：

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

輸出會如下所示：

```powershell
CommandLineArgument :
CommandLineSetting  : DoNotAllow
Description         :
FilePath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
FriendlyName        : Microsoft Word
IconContent         : {0, 0, 1, 0…}
IconHash            : --iom0PS6XLu-EMMlHWVW3F7LLsNt63Zz2K10RE0_64
IconIndex           : 0
IconPath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
Id                  : /subscriptions/<subid>/resourcegroups/0301RG/providers/Microsoft.DesktopVirtualization/applicationgroups/0301RAG/applications/Microsoft Word
Name                : 0301RAG/Microsoft Word
ShowInPortal        : False
Type                : Microsoft.DesktopVirtualization/applicationgroups/applications
```
若要更新易記名稱，請執行此 Cmdlet：

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe"
```

若要確認您已成功更新好記的名稱，請執行此 Cmdlet：

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

Cmdlet 應提供下列輸出：

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>自訂遠端桌面的顯示名稱

您可以藉由設定易記名稱，來變更已發佈之遠端桌面的顯示名稱。 如果您透過 PowerShell 手動建立主機集區和桌面應用程式群組，預設的易記名稱就是「會話桌面」。 如果您透過 GitHub Azure Resource Manager 範本或 Azure Marketplace 供應專案建立主機集區和桌面應用程式群組，預設的易記名稱會與主機集區名稱相同。

若要取得遠端桌面資源，請執行下列 PowerShell Cmdlet：

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

若要為遠端桌面資源指派易記名稱，請執行下列 PowerShell Cmdlet：

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>在 Azure 入口網站中自訂顯示名稱

您可以使用 Azure 入口網站設定易記名稱，來變更已發佈之遠端桌面的顯示名稱。

1. 在 <https://portal.azure.com> 登入 Azure 入口網站。

2. 搜尋 **Windows 虛擬桌面**。

3. 在 [服務] 下，選取 [ **Windows 虛擬桌面**]。

4. 在 [Windows 虛擬桌面] 頁面上，選取畫面左側的 [ **應用程式群組** ]，然後選取您要編輯的應用程式組名。  (例如，如果您想要編輯桌面應用程式群組的顯示名稱，請選取名為 **desktop**的應用程式群組 ) 

5. 選取畫面左側功能表中的 [ **應用程式** ]。

6. 選取您要更新的應用程式，然後輸入新的 **顯示名稱**。

7. 選取 [儲存]****。 您所編輯的應用程式現在應該會顯示已更新的名稱。

## <a name="next-steps"></a>後續步驟

既然您已自訂使用者的摘要，您可以登入 Windows 虛擬桌面用戶端來進行測試。若要這樣做，請繼續進行「連線到 Windows 虛擬桌面」操作說明：

 * [使用 Windows 10 或 Windows 7 連接](connect-windows-7-10.md)
 * [與 Web 用戶端連線](connect-web.md)
 * [與 Android 用戶端連線](connect-android.md)
 * [與 iOS 用戶端連線](connect-ios.md)
 * [與 macOS 用戶端連線](connect-macos.md)
