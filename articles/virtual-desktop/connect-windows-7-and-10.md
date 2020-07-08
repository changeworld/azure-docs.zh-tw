---
title: 連接到 Windows 虛擬桌面 Windows 10 或 7-Azure
description: 如何使用 Windows 桌面用戶端連接到 Windows 虛擬桌面。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d333b2f8bbb660e9fd71339159d942c0520f4f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85201543"
---
# <a name="connect-with-the-windows-desktop-client"></a>與 Windows 桌面用戶端連線

> 適用于： Windows 7、Windows 10 和 Windows 10 IoT 企業版

您可以使用 Windows 桌面用戶端，存取 Windows 7、Windows 10 和 Windows 10 IoT 企業版裝置上的 Windows 虛擬桌面資源。

>[!NOTE]
>Windows 用戶端會自動預設為 Windows 虛擬桌面秋季2019版。 不過，如果用戶端偵測到使用者也有 Azure Resource Manager 資源，它會自動新增資源，或通知使用者其可用。

> [!IMPORTANT]
> Windows 虛擬桌面不支援 RemoteApp 和桌面連線 (RADC) 用戶端或遠端桌面連線 (MSTSC) 用戶端。

> [!IMPORTANT]
> Windows 虛擬桌面目前不支援來自 Windows Store 的遠端桌面用戶端。 未來版本將增加此用戶端的支援。

## <a name="install-the-windows-desktop-client"></a>安裝 Windows 桌面用戶端

選擇符合您 Windows 版本的用戶端：

- [Windows 64 位元](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 位元](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

您可以為目前使用者安裝用戶端，這不需要系統管理員權限，或者您的系統管理員可以安裝和設定用戶端，讓裝置上的所有使用者都可以存取它。

安裝後，您可藉由搜尋**遠端桌面**，從 [開始] 功能表啟動用戶端。

## <a name="subscribe-to-a-feed"></a>訂閱摘要

藉由訂閱系統管理員所提供的摘要，取得可供您使用的受控資源清單。訂閱會將資源提供給您的本機電腦。

若要訂閱摘要：

1. 開啟 Windows 桌面用戶端。
2. 選取主頁面上的 [**訂閱**]，以連線至服務並抓取您的資源。
3. 出現提示時，使用您的使用者帳戶登入。

成功登入之後，您應該會看到您可以存取的資源清單。

您可以透過兩種方法的其中一種來啟動資源。

- 在用戶端的主頁面上，按兩下資源加以啟動。
- 像平常從 [開始] 功能表中的其他應用程式一樣啟動資源。
  - 您也可以在搜尋列中搜尋應用程式。

訂閱摘要之後，摘要的內容會定期自動更新。 根據系統管理員所做的變更，可能會新增、變更或移除資源。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何使用 Windows 桌面用戶端，請參閱[開始使用 Windows 桌面用戶端](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)。
