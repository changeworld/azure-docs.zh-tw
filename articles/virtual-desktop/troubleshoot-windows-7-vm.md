---
title: Windows 7 虛擬機器 Windows 虛擬桌面 - Azure
description: 如何解決 Windows 虛擬桌面環境中 Windows 7 虛擬機器 （VM） 的問題。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a2ff3f6fa9896e45ecd6ab40d40d46a046edf1cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127381"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Windows 虛擬桌面的 Windows 7 虛擬機器問題疑難排解

使用本文可解決配置 Windows 虛擬桌面工作階段主機虛擬機器 （VM） 時遇到的問題。

## <a name="known-issues"></a>已知問題

Windows 虛擬桌面上的 Windows 7 不支援以下功能：

- 虛擬化應用程式（遠端應用）
- 時區重定向
- 自動 DPI 縮放

Windows 虛擬桌面只能虛擬化 Windows 7 的完整桌面。

雖然不支援自動 DPI 縮放，但您可以通過按右鍵遠端桌面用戶端中的圖示並選擇 **"解析度**"來手動更改虛擬機器上的解析度。

## <a name="error-cant-access-the-remote-desktop-user-group"></a>錯誤：無法訪問遠端桌面使用者組

如果 Windows 虛擬桌面在遠端桌面使用者組中找不到您或使用者的憑據，您可能會看到以下錯誤訊息之一：

- "此使用者不是遠端桌面使用者組的成員"
- "您必須被授予通過遠端桌面服務登錄的許可權"

要修復此錯誤，將使用者添加到遠端桌面使用者組：

1. 開啟 Azure 入口網站。
2. 選擇您看到錯誤訊息的虛擬機器。
3. 選擇 **"運行命令**"。
4. 運行以下命令，`<username>`替換為要添加的使用者的名稱：
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```