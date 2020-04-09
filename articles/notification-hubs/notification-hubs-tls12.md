---
title: 通知中心 TLS 更新
description: 瞭解 Azure 通知中心對 TLS 的支援。
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 4da96df50e961f4291029a37e883fdcf88c6c87f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885747"
---
# <a name="transport-layer-security-tls"></a>傳輸層安全性 (TLS)

為確保更高的安全級別,通知中心將在 2020 年 5 月 31 日(從 2020 年 4 月 30 日起延長)禁用對 TLS 版本 1.0 和 1.1 的支援。 這些較舊的協定提供弱加密,並且容易受到BEAST和POODLE攻擊。 部署到運行 Android 版本 5 或更高版本或 iOS 版本 5 或更高版本的裝置的應用程式不受此更改的影響,因為這些操作系統支援 TLS 1.2,用戶端和伺服器將在連接時協商協定的最高相互支援版本。

我們建議您查看使用 Azure 通知中心的所有應用程式,以確保它們使用支援 TLS 1.2 的最適用的庫和 TLS 堆疊。

## <a name="update-apps"></a>更新應用程式

您可以使用 Apple 的網路安全功能"應用傳輸安全 (ATS)" 確保 iOS 應用使用 TLS 1.2。 ATS 不能用於超過 iOS 9.0 或 macOS 10.11 的 SDK,您也可以從[Apple 的文檔](https://developer.apple.com/documentation/security/preventing_insecure_network_connections)中進一步閱讀有關它。

對於使用 SSLSocket 實例的 Android 應用程式,在每個 SSLSocket 實例上設定啟用的協定,如[set Enabled 協定 中](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D))所述。

[TLS 協定相容性](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility)支援頁上的表可幫助對應具有相容 TLS 版本的作業系統。

有關詳細資訊,請參閱[Windows 上對 TLS 協定的支援](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows)概述。

## <a name="next-steps"></a>後續步驟

- [通知中心概述](notification-hubs-push-notification-overview.md)