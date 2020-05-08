---
title: Windows 7 虛擬機器 Windows 虛擬桌面-Azure
description: 如何解決 Windows 虛擬桌面環境中 Windows 7 虛擬機器（Vm）的問題。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 74f2e22bcc9d75070e4f7af304f92d9c5640ca7a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615275"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Windows 虛擬桌面的 Windows 7 虛擬機器問題疑難排解

>[!IMPORTANT]
>此內容適用于不支援 Azure Resource Manager Windows 虛擬桌面物件的秋季2019版。

使用本文來針對設定 Windows 虛擬桌面工作階段主機虛擬機器（Vm）時所發生的問題進行疑難排解。

## <a name="known-issues"></a>已知問題

Windows 虛擬桌面電腦上的 windows 7 不支援下列功能：

- 虛擬化應用程式（Remoteapp）
- 時區重新導向
- 自動 DPI 縮放比例

Windows 虛擬桌面只能虛擬化 Windows 7 的完整桌上型電腦。

雖然不支援自動 DPI 縮放比例，但您可以在遠端桌面用戶端的圖示上按一下滑鼠右鍵，然後選取 [**解決**]，以手動方式變更虛擬機器上的解析度。

## <a name="error-cant-access-the-remote-desktop-user-group"></a>錯誤：無法存取遠端桌面使用者群組

如果 Windows 虛擬桌面在遠端桌面使用者群組中找不到您或您的使用者認證，您可能會看到下列其中一個錯誤訊息：

- 「此使用者不是遠端桌面使用者群組的成員」
- 「您必須透過遠端桌面服務授與您登入的許可權」

若要修正此錯誤，請將使用者新增至遠端桌面使用者群組：

1. 開啟 Azure 入口網站。
2. 選取您看到的錯誤訊息所在的虛擬機器。
3. 選取 [**執行命令**]。
4. 執行下列命令，並`<username>`將取代為您想要新增的使用者名稱：
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```