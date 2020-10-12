---
title: Windows 7 虛擬機器 Windows 虛擬桌面 (傳統) -Azure
description: 如何在 Windows 虛擬桌面 (傳統) 環境中)  (Vm 的 Windows 7 虛擬機器問題解決問題。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1d71b4472b16372927db56b20eca4fcbde0cc625
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88005409"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop-classic"></a>針對 Windows 虛擬桌面 (傳統) 中的 Windows 7 虛擬機器進行疑難排解

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。

您可以使用本文來針對 (Vm) 設定 Windows 虛擬桌面工作階段主機虛擬機器時所遇到的問題進行疑難排解。

## <a name="known-issues"></a>已知問題

Windows 虛擬桌面的 windows 7 不支援下列功能：

- 虛擬化應用程式 (Remoteapp) 
- 時區重新導向
- 自動 DPI 縮放比例

Windows 虛擬桌面只能將 Windows 7 的完整桌面虛擬化。

雖然不支援自動 DPI 調整，但您可以在遠端桌面用戶端的圖示上按一下滑鼠右鍵，然後選取 [ **解決**]，以手動方式變更虛擬機器上的解析度。

## <a name="error-cant-access-the-remote-desktop-user-group"></a>錯誤：無法存取遠端桌面使用者群組

如果 Windows 虛擬桌面在遠端桌面使用者群組中找不到您或您的使用者認證，您可能會看到下列其中一個錯誤訊息：

- 「此使用者不是遠端桌面使用者群組的成員」
- 「您必須被授與許可權，才能透過遠端桌面服務登入」

若要修正這個錯誤，請將使用者新增至遠端桌面使用者群組：

1. 開啟 Azure 入口網站。
2. 選取您看到錯誤訊息的虛擬機器。
3. 選取 [ **執行命令**]。
4. 執行下列命令，並 `<username>` 取代為您想要新增的使用者名稱：

   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```