---
title: 通知中心 TLS 更新
description: 瞭解 Azure 通知中心對 TLS 的支援。
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87309e20efd9d6f8bd1a659451e5a603e6b95bc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908523"
---
# <a name="transport-layer-security-tls"></a>傳輸層安全性 (TLS)

為確保更高的安全級別，通知中心將在 2020 年 4 月 30 日禁用對 TLS 版本 1.0 和 1.1 的支援。 這些較舊的協定提供弱加密，並且容易受到 BEAST 和 POODLE 攻擊。 部署到運行 Android 版本 5 或更高版本或 iOS 版本 5 或更高版本的設備的應用程式不受此更改的影響，因為這些作業系統支援 TLS 1.2，並且用戶端和伺服器將協商最高相互支援的版本連接時的協定。

我們建議您查看使用 Azure 通知中心的所有應用程式，以確保它們使用支援 TLS 1.2 的最適用的庫和 TLS 堆疊。

## <a name="update-apps"></a>更新應用程式

您可以使用 Apple 的網路安全功能"應用傳輸安全 （ATS）"確保 iOS 應用使用 TLS 1.2。 ATS 不能用於超過 iOS 9.0 或 macOS 10.11 的 SDK，您也可以從[Apple 的文檔](https://developer.apple.com/documentation/security/preventing_insecure_network_connections)中進一步閱讀有關它。

對於使用 SSLSocket 實例的 Android 應用程式，在每個 SSLSocket 實例上設置啟用的協定，如[set Enabled 協定 中](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D))所述。

[TLS 協定相容性](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility)支援頁上的表可説明映射具有相容 TLS 版本的作業系統。

有關詳細資訊，請參閱[Windows 上對 TLS 協定的支援](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows)概述。

## <a name="next-steps"></a>後續步驟

- [通知中心概述](notification-hubs-push-notification-overview.md)