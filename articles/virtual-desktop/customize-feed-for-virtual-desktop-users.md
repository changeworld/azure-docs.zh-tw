---
title: 為 Windows 虛擬桌面使用者自訂摘要-Azure
description: 如何使用 PowerShell Cmdlet 為 Windows 虛擬桌面使用者自訂摘要。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9c2ad99a59e548ff7793455dac21748dd057c5fc
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86248691"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>適用於 Windows 虛擬桌面使用者的自訂摘要

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 2020 年春季更新版。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 2019 年秋季版，請參閱[這篇文章](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md)。
>
> Windows 虛擬桌面 2020 年春季更新版目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以自訂摘要，讓 RemoteApp 和遠端桌面資源以可辨識的方式顯示給使用者。

## <a name="prerequisites"></a>必要條件

本文假設您已下載並安裝 Windows 虛擬桌面 PowerShell 模組。 如果您尚未這麼做，請依照[設定 PowerShell 模組](powershell-module.md)中的指示進行。

## <a name="customize-the-display-name-for-a-remoteapp"></a>自訂 RemoteApp 的顯示名稱

您可以藉由設定易記名稱來變更已發佈 RemoteApp 的顯示名稱。 根據預設，易記名稱與 RemoteApp 程式的名稱相同。

若要取得應用程式群組的已發佈 Remoteapp 清單，請執行下列 PowerShell Cmdlet：

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

若要為 RemoteApp 指派易記名稱，請使用必要的參數執行下列 Cmdlet：

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

例如，假設您使用下列範例 Cmdlet 取出了目前的應用程式：

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

輸出看起來會像這樣：

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

若要確認您已成功更新好記名稱，請執行此 Cmdlet：

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

Cmdlet 應該會提供下列輸出：

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>自訂遠端桌面的顯示名稱

您可以藉由設定易記名稱來變更已發佈之遠端桌面的顯示名稱。 如果您透過 PowerShell 手動建立主機集區和桌面應用程式群組，則預設的易記名稱為「會話桌面」。 如果您透過 GitHub Azure Resource Manager 範本或 Azure Marketplace 供應專案建立了主機集區和桌面應用程式群組，則預設的易記名稱會與主機集區名稱相同。

若要取出遠端桌面資源，請執行下列 PowerShell Cmdlet：

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

若要為遠端桌面資源指派易記名稱，請執行下列 PowerShell Cmdlet：

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>在 Azure 入口網站中自訂顯示名稱

您可以使用 Azure 入口網站設定易記名稱，以變更已發佈之遠端桌面的顯示名稱。

1. 在 <https://portal.azure.com> 登入 Azure 入口網站。

2. 搜尋**Windows 虛擬桌面**。

3. 在 [服務] 下，選取 [ **Windows 虛擬桌面**]。

4. 在 [Windows 虛擬桌面] 頁面上，選取畫面左側的 [**應用程式群組**]，然後選取您要編輯之應用程式群組的名稱。

5. 選取畫面左側功能表中的 [**應用程式**]。

6. 選取您要更新的應用程式，然後輸入新的**顯示名稱**。

7. 選取 [儲存]。 您編輯的應用程式現在應該會顯示已更新的名稱。

## <a name="next-steps"></a>後續步驟

既然您已自訂使用者的摘要，您可以登入 Windows 虛擬桌面的用戶端來進行測試。若要這麼做，請繼續進行連線至 Windows 虛擬桌面的作法：

 * [與 Windows 10 或 Windows 7 連接](connect-windows-7-10.md)
 * [與 Web 用戶端連線](connect-web.md)
 * [與 Android 用戶端連線](connect-android.md)
 * [與 iOS 用戶端連線](connect-ios.md)
 * [與 macOS 用戶端連線](connect-macos.md)
