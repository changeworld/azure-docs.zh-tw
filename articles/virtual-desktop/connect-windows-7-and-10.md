---
title: 連接到 Windows 虛擬桌面 Windows 10 或 7 - Azure
description: 如何使用 Windows 桌面用戶端連接到 Windows 虛擬桌面。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6436059cc8b3637edfc2a146d0aab3e2beae6a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154333"
---
# <a name="connect-with-the-windows-desktop-client"></a>與 Windows 桌面用戶端連線

> 適用于：Windows 7、Windows 10 和 Windows 10 IoT 企業

您可以使用 Windows 桌面用戶端存取 Windows 7、Windows 10 和 Windows 10 IoT 企業版設備上的 Windows 虛擬桌面資源。

> [!IMPORTANT]
> Windows 虛擬桌面不支援遠端應用和桌面連接 （RADC） 用戶端或遠端桌面連線 （MSTSC） 用戶端。

> [!IMPORTANT]
> Windows 虛擬桌面當前不支援 Windows 應用商店中的遠端桌面用戶端。 將在將來的版本中添加對此用戶端的支援。

## <a name="install-the-windows-desktop-client"></a>安裝 Windows 桌面用戶端

選擇符合您 Windows 版本的用戶端：

- [視窗 64 位](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 位元](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

您可以為目前使用者安裝用戶端，這不需要系統管理員權限，或者您的系統管理員可以安裝和設定用戶端，讓裝置上的所有使用者都可以存取它。

安裝後，您可藉由搜尋**遠端桌面**，從 [開始] 功能表啟動用戶端。

## <a name="subscribe-to-a-feed"></a>訂閱摘要

通過訂閱管理員提供的源，獲取可供您使用的託管資源清單。訂閱使資源在本地 PC 上可用。

要訂閱源：

1. 打開 Windows 桌面用戶端。
2. 在主頁上選擇 **"訂閱**"以連接到服務並檢索資源。
3. 出現提示時，使用您的使用者帳戶登入。

成功登錄後，應看到可以訪問的資源的清單。

您可以通過兩種方法之一啟動資源。

- 從用戶端的主頁中，按兩下資源以啟動它。
- 像通常從"開始"功能表中啟動其他應用一樣啟動資源。
  - 您還可以在搜索欄中搜索應用。

訂閱源後，源的內容將自動定期更新。 資源可能會根據管理員所做的更改進行添加、更改或刪除。

## <a name="next-steps"></a>後續步驟

要瞭解有關如何使用 Windows 桌面用戶端的詳細資訊，請查看 Windows[桌面用戶端入門](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)。
