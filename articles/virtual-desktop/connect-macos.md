---
title: 從 macOS 連接到 Windows 虛擬桌面 - Azure
description: 如何使用 macOS 用戶端連接到 Windows 虛擬桌面。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 673c20355e9ad4abf2c8318b3eb2ebbcc36ed8fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128240"
---
# <a name="connect-with-the-macos-client"></a>與 macOS 用戶端連線

> 適用于： macOS 10.12 或更高版本

您可以使用我們的可下載用戶端從 macOS 設備訪問 Windows 虛擬桌面資源。 本指南將告訴您如何設置用戶端。

## <a name="install-the-client"></a>安裝用戶端

要開始使用，[請在](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) macOS 設備上下載並安裝用戶端。

## <a name="subscribe-to-a-feed"></a>訂閱摘要

訂閱管理員提供的源，獲取 macOS 設備上可用的託管資源清單。

要訂閱源：

1. 在主頁上選擇 **"添加源"** 以連接到服務並檢索資源。
2. 輸入摘要 URL。 這可以是 URL 或電子郵件地址：
   - 如果您使用 URL，請使用管理員為您提供的 URL。 通常，URL 是<https://rdweb.wvd.microsoft.com>。
   - 要使用電子郵件，請輸入您的電子郵件地址。 如果管理員以這種方式配置了伺服器，則告訴用戶端搜索與您的電子郵件地址關聯的 URL。
3. 選擇 **"訂閱**"。
4. 出現提示時，使用您的使用者帳戶登入。

登錄後，應看到可用資源的清單。

訂閱源後，源的內容將自動定期更新。 資源可能會根據管理員所做的更改進行添加、更改或刪除。

## <a name="next-steps"></a>後續步驟

要瞭解有關 macOS 用戶端的詳細資訊，請查看[macOS 用戶端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/)文檔入門。
