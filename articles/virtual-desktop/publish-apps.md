---
title: 在 Windows 虛擬桌面中發佈內建應用程式-Azure
description: 如何在 Windows 虛擬桌面中發佈內建應用程式。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ad2dd398e5eddd7cbb56b4f293a8a67a0df85316
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291400"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>在 Windows 虛擬桌面中發佈內建應用程式

>[!IMPORTANT]
>此內容適用于具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用 Windows 虛擬桌面（傳統）而不 Azure Resource Manager 物件，請參閱[這篇文章](./virtual-desktop-fall-2019/publish-apps-2019.md)。

本文將告訴您如何在 Windows 虛擬桌面環境中發佈應用程式。

## <a name="publish-built-in-apps"></a>發佈內建應用程式

若要發佈內建應用程式：

1. 連接到主機集區中的其中一個虛擬機器。
2. 遵循[本文中的](/powershell/module/appx/get-appxpackage?view=win10-ps/)指示，取得您想要發佈之應用程式的**PackageFamilyName** 。
3. 最後，執行下列 Cmdlet，並 `<PackageFamilyName>` 取代為您在上一個步驟中找到的**PackageFamilyName** ：

   ```powershell
   New-AzWvdApplication -Name <applicationname> -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -FilePath "shell:appsFolder\<PackageFamilyName>!App" -CommandLineSetting <Allow|Require|DoNotAllow> -IconIndex 0 -IconPath <iconpath> -ShowInPortal:$true
   ```

>[!NOTE]
> Windows 虛擬桌面只支援使用以開頭的安裝位置來發行應用程式 `C:\Program Files\WindowsApps` 。

## <a name="update-app-icons"></a>更新應用程式圖示

在您發行應用程式之後，它會有預設的 Windows 應用程式圖示，而不是其一般圖示圖片。 若要將圖示變更為其一般圖示，請將您想要的圖示影像放在網路共用上。 支援的影像格式為 PNG、BMP、GIF、JPG、JPEG 和 .ICO。

## <a name="publish-microsoft-edge"></a>發佈 Microsoft Edge

您用來發佈 Microsoft Edge 的流程與其他應用程式的發佈流程稍有不同。 若要使用預設首頁發佈 Microsoft Edge，請執行此 Cmdlet：

```powershell
New-AzWvdApplication -Name -ResourceGroupName -ApplicationGroupName -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" -CommandLineSetting <Allow|Require|DoNotAllow> -iconPath "C:\Windows\SystemApps\Microsoft.MicrosoftEdge_8wekyb3d8bbwe\microsoftedge.exe" -iconIndex 0 -ShowInPortal:$true
```

## <a name="next-steps"></a>後續步驟

- 深入瞭解如何設定摘要，以組織為[Windows 虛擬桌面使用者的自訂](customize-feed-for-virtual-desktop-users.md)摘要中的使用者顯示應用程式的方式。
- 瞭解[設定 MSIX 應用程式](app-attach.md)連結上的 MSIX 應用程式附加功能。

