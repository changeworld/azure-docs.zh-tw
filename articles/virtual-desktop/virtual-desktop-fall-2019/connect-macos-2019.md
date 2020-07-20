---
title: 從 macOS 連接到 Windows 虛擬桌面-Azure
description: 如何使用 macOS 用戶端連接到 Windows 虛擬桌面。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 024a1ab1a7fef58bd5fd8f9e7e0fc743a4ecee71
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213154"
---
# <a name="connect-with-the-macos-client"></a>與 macOS 用戶端連線

> 適用于： macOS 10.12 或更新版本

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 2019 年秋季版本。 如果您嘗試管理 2020 年春季版更新中引進的 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../connect-macos.md)。

您可以使用可下載的用戶端，從 macOS 裝置存取 Windows 虛擬桌面資源。 本指南將告訴您如何設定用戶端。

## <a name="install-the-client"></a>安裝用戶端

若要開始使用，請在您的 macOS 裝置上[下載](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12)並安裝用戶端。

## <a name="subscribe-to-a-feed"></a>訂閱摘要

訂閱系統管理員提供給您的摘要，以取得可供您在 macOS 裝置上使用的受控資源清單。

若要訂閱摘要：

1. 選取主頁面上的 [**新增工作區**]，以連線至服務並抓取您的資源。
2. 輸入摘要 URL。 這可以是 URL 或電子郵件地址：
   - 如果您使用 URL，請使用系統管理員提供給您的帳戶。 此 URL 通常是 <https://rdweb.wvd.microsoft.com>。
   - 若要使用電子郵件，請輸入您的電子郵件地址。 這會告訴用戶端搜尋與您的電子郵件地址相關聯的 URL (如果系統管理員是這樣設定伺服器的話)。
3. 選取 [新增]。
4. 出現提示時，使用您的使用者帳戶登入。

登入之後，您應該會看到可用資源的清單。

訂閱摘要之後，摘要的內容便會定期自動更新。 根據系統管理員所做的變更，可能會新增、變更或移除資源。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 macOS 用戶端，請參閱[開始使用 macOS 用戶端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/)檔。