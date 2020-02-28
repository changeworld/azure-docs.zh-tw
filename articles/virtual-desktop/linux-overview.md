---
title: Windows 虛擬桌面 Linux 支援-Azure
description: Windows 虛擬桌面的簡要總覽 Linux 支援。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
ms.openlocfilehash: af5ab05cb772909cd36e2e6bfe1c100a5b83841c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660031"
---
# <a name="linux-support"></a>Linux 支援

您可以使用適用于 Windows 虛擬桌面的 Linux SDK 來建立獨立 Windows 虛擬桌面用戶端。 您也可以使用它來啟用用戶端應用程式的 Windows 虛擬桌面支援。 本快速指南將說明 Linux SDK 是什麼，以及如何開始使用它。

## <a name="what-is-the-linux-sdk"></a>什麼是 Linux SDK？

您可以使用 SDK Api 來抓取資源摘要、連接到桌面或遠端應用程式會話，並使用我們的第一方用戶端支援的許多重新導向。

> [!NOTE]
> SDK 目前正在開發中。 我們將會更新本檔，其中包含可用的 SDK 存取指示。

### <a name="supported-linux-distributions"></a>支援的 Linux 發行版本

SDK 與大部分以 Ubuntu 18.04 或更新版本為基礎的作業系統相容。 如果您有不同的 Linux 散發套件，我們可以與您合作，以瞭解如何以最佳方式支援您的需求。

### <a name="feature-support"></a>功能支援

SDK 支援與桌上型電腦和遠端應用程式會話之間的多個連接。 支援下列重新導向：

| 重新導向       | 支援 |
| :---------------- | :-------: |
| 鍵盤          | &#10004;  |
| 滑鼠             | &#10004;  |
| 音訊 in          | &#10004;  |
| 音訊輸出         | &#10004;  |
| 剪貼簿（文字）  | &#10004;  |
| 剪貼簿（影像） | &#10004;  |
| 剪貼簿（檔案）  | &#10004;  |
| 智慧卡         | &#10004;  |
| 磁片磁碟機/資料夾      | &#10004;  |

SDK 也支援多個監視器顯示設定，只要您為會話選取的監視器是連續的。

當我們新增新功能和重新導向的支援時，我們將會更新這份檔。 如果您想要建議新功能和其他改善，請造訪我們的[UserVoice 頁面](https://go.microsoft.com/fwlink/?linkid=2116523)。

## <a name="get-started-with-the-linux-sdk"></a>開始使用 Linux SDK

您必須先執行下列動作，才能開發適用于 Windows 虛擬桌面的 Linux 用戶端：

1. 建立並部署 Windows 虛擬桌面環境，以供測試或實際執行使用。
2. 測試可用的第一方用戶端，以熟悉 Windows 虛擬桌面的使用者體驗。

## <a name="next-steps"></a>後續步驟

查看下列用戶端的檔：

- [Windows 桌面用戶端](connect-windows-7-and-10.md)
- [Web 用戶端](connect-web.md)
- [Android 用戶端](connect-android.md)
- [macOS 用戶端](connect-macos.md)
- [iOS 用戶端](connect-ios.md)
