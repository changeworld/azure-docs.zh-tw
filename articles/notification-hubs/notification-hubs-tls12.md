---
title: 通知中樞 TLS 更新
description: 瞭解 Azure 通知中樞中的 TLS 支援。
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/29/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87a3627d7820f9f456ac08e2f20b70af961f817e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87084240"
---
# <a name="transport-layer-security-tls"></a>傳輸層安全性 (TLS)

為了確保較高的安全性等級，通知中樞會在 **12 月 31** 日停用 TLS 1.0 和1.1 的支援，2020 (從 2020) 延長。 這些較舊的通訊協定提供弱式密碼編譯，而且容易受到怪獸和貴賓攻擊的影響。 如果應用程式部署到執行 Android 第5版或更新版本的裝置，或 iOS 版本5或更新版本，則不會受到這項變更的影響，因為這些作業系統支援 TLS 1.2，而且用戶端和伺服器會在連線時協商通訊協定的最高互相支援版本。

建議您複習所有使用 Azure 通知中樞的應用程式，以確保它們使用最適用的程式庫和支援 TLS 1.2 的 TLS 堆疊。

## <a name="update-apps"></a>更新應用程式

您可以使用名為「應用程式傳輸安全性 (ATS) 的 Apple 網路安全性功能，確保您的 iOS 應用程式使用 TLS 1.2。 ATS 不能用於 iOS 9.0 或 macOS 10.11 之前的 Sdk，您可以從 [Apple 的檔](https://developer.apple.com/documentation/security/preventing_insecure_network_connections)進一步瞭解它。

針對使用 SSLSocket 實例的 Android 應用程式，請在每個 SSLSocket 實例上設定啟用的通訊協定，如 [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D))中所述。

[Tls 通訊協定相容性](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility)支援頁面上的表格可協助您對應具有相容 TLS 版本的作業系統。

如需詳細資訊，請參閱 [Windows 上的 TLS 通訊協定支援](/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows)的總覽。

## <a name="next-steps"></a>接下來的步驟

- [通知中樞總覽](notification-hubs-push-notification-overview.md)
