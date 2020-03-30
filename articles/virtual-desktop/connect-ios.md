---
title: 從 iOS 連接到 Windows 虛擬桌面 - Azure
description: 如何使用 iOS 用戶端連接到 Windows 虛擬桌面。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3bf9ea6c68e4cbbe721705639e6c6416c0e93c9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128246"
---
# <a name="connect-with-the-ios-client"></a>與 iOS 用戶端連線

> 適用于：iOS 13.0 或更高版本。 相容 iPhone、iPad 和 iPod 觸摸。

您可以使用我們的可下載用戶端從 iOS 設備訪問 Windows 虛擬桌面資源。 本指南將告訴您如何設置 iOS 用戶端。

## <a name="install-the-ios-client"></a>安裝 iOS 用戶端

要開始使用，[請在](https://aka.ms/rdios)iOS 設備上下載並安裝用戶端。

## <a name="subscribe-to-a-feed"></a>訂閱摘要

訂閱管理員提供的源，獲取可在 iOS 設備上訪問的託管資源清單。

要訂閱源：

1. 在連接中心中，點**+** 按 ，然後點按 **"添加工作區**"。
2. 在源 URL 欄位中輸入源**URL。** 源 URL 可以是 URL 或電子郵件地址。
   - 如果您使用 URL，請使用管理員為您提供的 URL。 通常，URL 是<https://rdweb.wvd.microsoft.com>。
   - 要使用電子郵件，請輸入您的電子郵件地址。 如果管理員以這種方式配置了伺服器，則告訴用戶端搜索與您的電子郵件地址關聯的 URL。
3. 點按 **"下一步**"。
4. 出現提示時，請提供您的認證。
   - 對於**使用者名**，授予具有訪問資源許可權的使用者名。
   - 對於**密碼**，請提供與使用者名關聯的密碼。
   - 如果管理員以這種方式配置了身份驗證，則可能還會提示您提供其他因素。
5. 點選 [儲存]****。

在此之後，連接中心應顯示遠端資源。

訂閱源後，源的內容將自動定期更新。 資源可能會根據管理員所做的更改進行添加、更改或刪除。

## <a name="next-steps"></a>後續步驟

要瞭解有關如何使用 iOS 用戶端的詳細資訊，請查看[iOS 用戶端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/)文檔入門。
