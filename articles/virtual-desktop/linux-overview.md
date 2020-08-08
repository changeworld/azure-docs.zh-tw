---
title: Windows 虛擬桌面 Linux 支援-Azure
description: Windows 虛擬桌面的簡要總覽 Linux 支援。
author: Heidilohr
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bd3cc6c5220e2e84cbbd30b29b8034f53c813f1e
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008690"
---
# <a name="linux-support"></a>Linux 支援

您可以使用我們的 Linux 瘦用戶端合作夥伴提供的下列支援用戶端，從您的 Linux 裝置存取 Windows 虛擬桌面資源。 我們正與許多合作夥伴合作，在更多以 Linux 為基礎的作業系統和裝置上啟用支援的 Windows 虛擬桌面用戶端。 如果您想要在此處未列出的 Linux 平臺上 Windows 虛擬桌面支援，請在[UserVoice 頁面](https://remotedesktop.uservoice.com/forums/923035-remote-desktop-support-on-linux)上讓我們知道。

## <a name="connect-with-your-linux-device"></a>與您的 Linux 裝置連線

下列合作夥伴已核准適用于 Linux 裝置的 Windows 虛擬桌面用戶端。

|Partner|合作夥伴檔|合作夥伴支援|
|:------|:--------------------|:--------------|
|![IGEL 標誌](./media/partners/igel.png)|[IGEL 用戶端檔](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[IGEL 支援](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>什麼是 Linux SDK？

Linux 瘦用戶端合作夥伴可以使用 Windows 虛擬桌面 Linux SDK Api 來抓取資源摘要、連接到桌面或遠端應用程式會話，並使用我們的第一方用戶端支援的許多重新導向。 SDK 與大部分以 Ubuntu 18.04 或更新版本為基礎的作業系統相容。

### <a name="feature-support"></a>功能支援

SDK 支援與桌上型電腦和遠端應用程式會話之間的多個連接。 支援下列重新導向：

| 重新導向       | 支援 |
| :---------------- | :-------: |
| 鍵盤          | &#10004;  |
| 滑鼠             | &#10004;  |
| 音訊 in          | &#10004;  |
| 音訊輸出         | &#10004;  |
| 剪貼簿 (文字)   | &#10004;  |
| 剪貼簿 (影像)  | &#10004;  |
| 剪貼簿 (檔案)   | &#10004;  |
| 智慧卡         | &#10004;  |
| 磁片磁碟機/資料夾      | &#10004;  |

SDK 也支援多個監視器顯示設定，只要您為會話選取的監視器是連續的。

當我們新增新功能和重新導向的支援時，我們將會更新這份檔。 如果您想要建議新功能和其他改善，請造訪我們的[UserVoice 頁面](https://go.microsoft.com/fwlink/?linkid=2116523)。

## <a name="next-steps"></a>後續步驟

查看下列用戶端的檔：

- [Windows 桌面用戶端](connect-windows-7-10.md)
- [Web 用戶端](connect-web.md)
- [Android 用戶端](connect-android.md)
- [macOS 用戶端](connect-macos.md)
- [iOS 用戶端](connect-ios.md)
