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
ms.openlocfilehash: 8c5de43ed29856451ad67e02a426b07cc34a0d54
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422421"
---
# <a name="linux-support"></a>Linux 支援

合作夥伴可以使用 Windows 虛擬桌面的 Linux SDK 構建獨立的 Windows 虛擬桌面用戶端。 您還可以使用它在用戶端應用程式上啟用 Windows 虛擬桌面支援。 本快速指南將解釋什麼是 Linux SDK 以及如何開始使用它。

## <a name="connect-with-your-linux-device"></a>與 Linux 裝置連線

以下合作夥伴已批准適用於 Linux 裝置的 Windows 虛擬桌面用戶端。

|Partner|合作夥伴文件|合作夥伴支援|
|:------|:--------------------|:--------------|
|![IGEL 徽標](./media/partners/igel.png)|[IGEL 客戶端文件](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[IGEL 支援](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>什麼是 Linux SDK?

您可以使用 SDK API 檢索資源饋送、連接到桌面或遠端應用程式作業階段,並使用我們的第一方用戶端支援的許多重定向。

> [!NOTE]
> SDK 目前正在開發中。 我們將更新本文檔,並提供訪問 SDK 的說明,當 SDK 通常可用時。

### <a name="supported-linux-distributions"></a>支援的 Linux 發行版

SDK 與基於 Ubuntu 18.04 或更高版本的大多數作業系統相容。 如果您有不同的 Linux 發行版,我們可以與您合作,瞭解如何最好地滿足您的需求。

### <a name="feature-support"></a>功能支援

SDK 支援與桌面和遠端應用程式作業階段的多個連接。 支援以下重定向:

| 重新導向       | 支援 |
| :---------------- | :-------: |
| 鍵盤          | &#10004;  |
| 滑鼠             | &#10004;  |
| 音訊          | &#10004;  |
| 音訊出         | &#10004;  |
| 剪貼簿(文字)  | &#10004;  |
| 剪貼簿(影像) | &#10004;  |
| 剪貼簿(檔案)  | &#10004;  |
| 智慧卡         | &#10004;  |
| 磁碟機/資料夾      | &#10004;  |

SDK 還支援多個監視器顯示配置,只要為會話選擇的監視器是連續的。

我們將更新本文檔,因為我們添加對新功能和重定向的支援。 如果您想建議新功能和其他改進,請造訪我們的[使用者語音頁面](https://go.microsoft.com/fwlink/?linkid=2116523)。

## <a name="get-started-with-the-linux-sdk"></a>開始使用 Linux SDK

在 Windows 虛擬桌面開發 Linux 用戶端之前,您需要執行以下操作:

1. 生成和部署用於測試或生產使用的 Windows 虛擬桌面環境。
2. 測試可用的第一方用戶端,以熟悉 Windows 虛擬桌面用戶體驗。

## <a name="next-steps"></a>後續步驟

檢視以下客戶的文件:

- [Windows 桌面用戶端](connect-windows-7-and-10.md)
- [Web 用戶端](connect-web.md)
- [Android 用戶端](connect-android.md)
- [macOS 用戶端](connect-macos.md)
- [iOS 用戶端](connect-ios.md)
