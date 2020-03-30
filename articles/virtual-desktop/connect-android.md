---
title: 從 Android 連接到 Windows 虛擬桌面 - Azure
description: 如何使用 Android 用戶端連接到 Windows 虛擬桌面。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d2990c82efbcdac7d453f920301787b8c83db1e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295368"
---
# <a name="connect-with-the-android-client"></a>與 Android 用戶端連線

> 適用于：Android 4.1 及更高版本，Chromebook 配備 ChromeOS 53 及更高版本。

>[!NOTE]
> 從 Android 用戶端存取 Windows 虛擬桌面資源的功能當前在預覽版中可用。

您可以使用我們的可下載用戶端從 Android 設備訪問 Windows 虛擬桌面資源。 您還可以在支援 Google Play 商店的 Chromebook 設備上使用 Android 用戶端。 本指南將告訴您如何設置 Android 用戶端。

## <a name="install-the-android-client"></a>安裝 Android 用戶端

要開始，請在 Android 設備上[下載](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx)並安裝用戶端。

## <a name="subscribe-to-a-feed"></a>訂閱摘要

訂閱管理員提供的源，獲取您可以在 Android 設備上訪問的託管資源清單。

要訂閱源：

1. 在連接中心中，點**+** 按 ，然後點按 **"遠端資源源**"。
2. 在源 URL 欄位中輸入源**URL。** 源 URL 可以是 URL 或電子郵件地址。
   - 如果您使用 URL，請通常<https://rdweb.wvd.microsoft.com>使用管理員為您提供的 URL。
   - 要使用電子郵件，請輸入您的電子郵件地址。 如果管理員以這種方式配置了伺服器，則用戶端將搜索與您的電子郵件地址關聯的 URL。
3. 點按**下一個**。
4. 出現提示時，請提供您的認證。
   - 對於**使用者名**，授予具有訪問資源許可權的使用者名。
   - 對於**密碼**，請提供與使用者名關聯的密碼。
   - 如果管理員以這種方式配置了身份驗證，則可能還會提示您提供其他因素。

訂閱後，連接中心應顯示遠端資源。

訂閱源後，源的內容將自動定期更新。 資源可能會根據管理員所做的更改進行添加、更改或刪除。

## <a name="next-steps"></a>後續步驟

要瞭解有關如何使用 Android 用戶端的詳細資訊，請查看 Android[用戶端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/)入門。
