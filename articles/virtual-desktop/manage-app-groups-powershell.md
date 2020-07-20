---
title: 管理 Windows 虛擬桌面 PowerShell 的應用程式群組-Azure
description: 如何使用 PowerShell 管理 Windows 虛擬桌面應用程式群組。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c286a3795cc7cb4c1925ff06b3da19952e7f0b43
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85209329"
---
# <a name="manage-app-groups-using-powershell"></a>使用 PowerShell 管理應用程式群組

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 2020 年春季更新版。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 2019 年秋季版，請參閱[這篇文章](./virtual-desktop-fall-2019/manage-app-groups-2019.md)。
>
> Windows 虛擬桌面 2020 年春季更新版目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

為新的 Windows 虛擬桌面主機集區建立的預設應用程式群組，也會發佈完整桌面。 此外，您可以為主機集區建立一或多個 RemoteApp 應用程式群組。 遵循本教學課程來建立 RemoteApp 應用程式群組及發佈個別 [開始]  功能表應用程式。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立 RemoteApp 群組。
> * 授與 RemoteApp 程式的存取權。

## <a name="prerequisites"></a>必要條件

本文假設您已遵循[設定 powershell 模組](powershell-module.md)中的指示來設定 powershell 模組，並登入您的 Azure 帳戶。

## <a name="create-a-remoteapp-group"></a>建立 RemoteApp 群組

若要使用 PowerShell 建立 RemoteApp 群組：

1. 執行下列 PowerShell Cmdlet 來建立新的空白 RemoteApp 應用程式群組。

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. (選用) 若要確認已建立應用程式群組，您可以執行下列 Cmdlet 來查看主機集區的所有應用程式群組清單。

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. 執行下列 Cmdlet 來取得主機集區的虛擬機器映像上的 [開始]  功能表應用程式清單。 請記下 **FilePath**、**IconPath**、**IconIndex** 的值，以及您要發佈的應用程式的其他重要資訊。

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more
   ```

   輸出應該會顯示所有 [開始] 功能表項目，格式如下：

   ```powershell
   AppAlias            : access
   CommandLineArgument :
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access
   Name                : 0301RAG/Access
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems

   AppAlias            : charactermap
   CommandLineArgument :
   FilePath            : C:\windows\system32\charmap.exe
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\windows\system32\charmap.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map
   Name                : 0301RAG/Character Map
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   ```

4. 執行下列 Cmdlet 以根據 `AppAlias` 安裝應用程式。 當您執行執行步驟 3 的輸出時，`AppAlias` 隨即顯示。

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

5. (選擇性) 執行下列 Cmdlet 將新的 RemoteApp 程式發佈到步驟 1 中建立的應用程式群組。

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

6. 若要確認已發佈應用程式，請執行下列 Cmdlet。

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. 針對您要對此應用程式群組發佈的每個應用程式，重複步驟 1–5。
8. 執行下列 Cmdlet 來授與使用者存取應用程式群組中 RemoteApp 程式的權限。

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

## <a name="next-steps"></a>後續步驟

如果您從我們的教學課程進入此操作指南，請參閱[建立主機集區以驗證服務更新](create-validation-host-pool.md)。 您可以使用驗證主機集區來監視服務更新，再將其推展到生產環境。