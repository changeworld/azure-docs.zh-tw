---
title: 將推送通知添加到 iOS
description: 了解如何使用 Azure 行動應用程式將推播通知傳送至 iOS 應用程式。
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: ad58ee158a2e1842c1b41db281bdd9f04b9b2ca4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461499"
---
# <a name="add-push-notifications-to-your-ios-app"></a>將推播通知新增至您的 iOS 應用程式

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>總覽

在本教學課程中，您會將推播通知新增至 [iOS 快速入門]專案，以便在每次插入一筆記錄時傳送推播通知至裝置。

如果您不要使用下載的快速入門伺服器專案，則需要推播通知擴充套件。 如需詳細資訊，請參閱[使用適用於 Azure Mobile Apps 的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) 指南。

[iOS 模擬器不支援推送通知](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html)。 您需要實體 iOS 裝置和 [Apple Developer Program 成員資格](https://developer.apple.com/programs/ios/)。

## <a name="configure-notification-hub"></a><a name="configure-hub"></a>設定通知中樞

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-app-for-push-notifications"></a><a id="register"></a>註冊應用程式以取得推播通知

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>設定 Azure 來傳送推播通知

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-backend-to-send-push-notifications"></a><a id="update-server"></a>更新後端程式碼以傳送推播通知

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a name="add-push-notifications-to-app"></a><a id="add-push"></a>向應用添加推送通知

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a name="test-push-notifications"></a><a id="test"></a>測試推播通知

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a name="more"></a><a id="more"></a>更多資訊

* ：範本可讓您彈性地傳送跨平台推播和當地語系化推播。 [如何使用適用於 Azure 行動應用程式的 iOS 用戶端程式庫](app-service-mobile-ios-how-to-use-client-library.md#templates) 一文，說明如何註冊範本。

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[iOS 快速入門]: app-service-mobile-ios-get-started.md
