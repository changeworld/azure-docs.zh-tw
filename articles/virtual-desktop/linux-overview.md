---
title: Windows 虛擬桌面 Linux 支援 - Azure
description: 概述 Linux 對 Windows 虛擬桌面的支援。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 967fd1fa182b7c8e581fd74cc287c5a6ba0e4038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127835"
---
# <a name="linux-support"></a>Linux 支援

您可以使用 Windows 虛擬桌面的 Linux SDK 構建獨立的 Windows 虛擬桌面用戶端。 您還可以使用它在用戶端應用程式上啟用 Windows 虛擬桌面支援。 本快速指南將解釋什麼是 Linux SDK 以及如何開始使用它。

## <a name="what-is-the-linux-sdk"></a>什麼是 Linux SDK？

您可以使用 SDK API 檢索資源饋送、連接到桌面或遠端應用程式會話，並使用我們的第一方用戶端支援的許多重定向。

> [!NOTE]
> SDK 目前正在開發中。 我們將更新本文檔，並提供訪問 SDK 的說明（當它可用時）。

### <a name="supported-linux-distributions"></a>支援的 Linux 發行版本

SDK 與基於 Ubuntu 18.04 或更高版本的大多數作業系統相容。 如果您有不同的 Linux 發行版本，我們可以與您合作，瞭解如何最好地滿足您的需求。

### <a name="feature-support"></a>功能支援

SDK 支援與桌面和遠端應用程式會話的多個連接。 支援以下重定向：

| 重新導向       | 支援 |
| :---------------- | :-------: |
| 鍵盤          | &#10004;  |
| 滑鼠             | &#10004;  |
| 音訊          | &#10004;  |
| 音訊出         | &#10004;  |
| 剪貼簿（文本）  | &#10004;  |
| 剪貼簿（圖像） | &#10004;  |
| 剪貼簿（檔）  | &#10004;  |
| 智慧卡         | &#10004;  |
| 磁碟機/資料夾      | &#10004;  |

SDK 還支援多個監視器顯示配置，只要為會話選擇的監視器是連續的。

我們將更新本文檔，因為我們添加對新功能和重定向的支援。 如果您想建議新功能和其他改進，請訪問我們的[使用者語音頁面](https://go.microsoft.com/fwlink/?linkid=2116523)。

## <a name="get-started-with-the-linux-sdk"></a>開始使用 Linux SDK

在為 Windows 虛擬桌面開發 Linux 用戶端之前，您需要執行以下操作：

1. 生成和部署用於測試或生產使用的 Windows 虛擬桌面環境。
2. 測試可用的第一方用戶端，以熟悉 Windows 虛擬桌面使用者體驗。

## <a name="next-steps"></a>後續步驟

查看以下客戶的文檔：

- [Windows 桌面用戶端](connect-windows-7-and-10.md)
- [Web 用戶端](connect-web.md)
- [Android 用戶端](connect-android.md)
- [macOS 用戶端](connect-macos.md)
- [iOS 用戶端](connect-ios.md)
