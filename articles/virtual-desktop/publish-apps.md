---
title: 在 Windows 虛擬桌面中發佈內置應用 - Azure
description: 如何在 Windows 虛擬桌面中發佈內置應用。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a697c9a62e52e82a550969e1852abd1489ed59b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127750"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>在 Windows 虛擬桌面中發佈內置應用

本文將介紹如何在 Windows 虛擬桌面環境中發佈應用。

## <a name="publish-built-in-apps"></a>發佈內建應用程式

要發佈內置應用，可以：

1. 連接到主機池中的虛擬機器之一。
2. 按照[本文](/powershell/module/appx/get-appxpackage?view=win10-ps/)中的說明獲取要發佈的應用程式的**包家庭名稱**。
3. 最後，運行以下 Cmdlet，`<PackageFamilyName>`替換為您在上一步中找到的**包家庭名稱**：
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Windows 虛擬桌面僅支援發佈安裝位置以`C:\Program Files\Windows Apps`開始的應用。

## <a name="update-app-icons"></a>更新應用圖示

發佈應用後，它將具有預設的 Windows 應用圖示，而不是其常規圖示圖片。 要將圖示更改為其常規圖示，可以將所需的圖示圖像放在網路共用上。 支援的圖像格式包括 PNG、BMP、GIF、JPG、JPEG 和 ICO。

## <a name="publish-microsoft-edge"></a>發佈微軟邊緣

用於發佈 Microsoft Edge 的過程與其他應用的發佈過程略有不同。 要使用預設首頁發佈 Microsoft Edge，請運行此 Cmdlet：

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>後續步驟

- 瞭解如何配置源以在[Windows 虛擬桌面使用者自訂來源](customize-feed-for-virtual-desktop-users.md)中組織如何為使用者顯示應用。
- 在[設置 MSIX 應用附加](app-attach.md)時瞭解有關 MSIX 應用附加功能。

