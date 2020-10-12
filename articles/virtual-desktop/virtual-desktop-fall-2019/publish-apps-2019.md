---
title: 在 Windows 虛擬桌面 (傳統) 中發佈內建應用程式-Azure
description: 如何在 Windows 虛擬桌面中發佈內建應用程式 (傳統) 。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 80cd1a4c92441fb17ce0a66814ff0a39a92fb287
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88005562"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop-classic"></a>在 Windows 虛擬桌面 (傳統) 中發佈內建應用程式

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../publish-apps.md)。

本文將告訴您如何在 Windows 虛擬桌面環境中發佈應用程式。

## <a name="publish-built-in-apps"></a>發佈內建應用程式

若要發佈內建應用程式：

1. 連接至主機集區中的其中一部虛擬機器。
2. 遵循[本文中的](/powershell/module/appx/get-appxpackage?view=win10-ps/)指示，取得您想要發佈的應用程式**PackageFamilyName** 。
3. 最後，執行下列 Cmdlet，並以 `<PackageFamilyName>` 您在上一個步驟中找到的 **PackageFamilyName** 取代：

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Windows 虛擬桌面僅支援使用開頭為的安裝位置來發行應用程式 `C:\Program Files\Windows Apps` 。

## <a name="update-app-icons"></a>更新應用程式圖示

在您發佈應用程式之後，它會有預設的 Windows 應用程式圖示，而不是一般圖示圖片。 若要將圖示變更為一般圖示，請將您想要的圖示影像放在網路共用上。 支援的影像格式為 PNG、BMP、GIF、JPG、JPEG 和 .ICO。

## <a name="publish-microsoft-edge"></a>發佈 Microsoft Edge

您用來發佈 Microsoft Edge 的程式與其他應用程式的發佈程式稍有不同。 若要使用預設首頁發佈 Microsoft Edge，請執行下列 Cmdlet：

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge"
```

## <a name="next-steps"></a>後續步驟

- 瞭解如何設定摘要來組織針對 [Windows 虛擬桌面使用者的自訂](customize-feed-virtual-desktop-users-2019.md)摘要使用者顯示應用程式的方式。
- 瞭解 [設定 MSIX app 附加](../app-attach.md)的 MSIX 應用程式附加功能。

