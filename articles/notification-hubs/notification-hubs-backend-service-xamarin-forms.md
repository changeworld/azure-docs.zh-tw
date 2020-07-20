---
title: 透過後端服務使用 Azure 通知中樞將推播通知傳送至 Xamarin.Forms 應用程式 | Microsoft Docs
description: 了解如何將通知推送至透過後端服務使用 Azure 通知中樞的 Xamarin.Forms 應用程式。
author: mikeparker104
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: miparker
ms.openlocfilehash: 5175edfd438edb527f6873c87b948f8ff0701cf1
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86166326"
---
# <a name="tutorial-send-push-notifications-to-xamarinforms-apps-using-azure-notification-hubs-via-a-backend-service"></a>教學課程：透過後端服務使用 Azure 通知中樞將推播通知傳送至 Xamarin.Forms 應用程式  

[![下載範例](./media/notification-hubs-backend-service-xamarin-forms/download.png) 下載範例](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md) \(英文\)

在本教學課程中，您將使用 [Azure 通知中樞](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview)將通知推送至以 **Android** 和 **iOS** 為目標的 [Xamarin.Forms](https://dotnet.microsoft.com/apps/xamarin/xamarin-forms) 應用程式。  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

本教學課程將引導您完成下列步驟：

> [!div class="checklist"]
>
> * [設定推播通知服務和 Azure 通知中樞。](#set-up-push-notification-services-and-azure-notification-hub)
> * [建立 ASP.NET Core Web API 後端應用程式。](#create-an-aspnet-core-web-api-backend-application)
> * [建立跨平台的 Xamarin.Forms 應用程式。](#create-a-cross-platform-xamarinforms-application)
> * [設定用於推播通知的原生 Android 專案。](#configure-the-native-android-project-for-push-notifications)
> * [設定用於推播通知的原生 iOS 專案。](#configure-the-native-ios-project-for-push-notifications)
> * [測試解決方案。](#test-the-solution)

## <a name="prerequisites"></a>必要條件

若要繼續，您需要：

* [Azure 訂用帳戶](https://portal.azure.com)，您可以在其中建立和管理資源。
* 已安裝 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) 的 Mac (或執行 [Visual Studio 2019](https://visualstudio.microsoft.com/vs) 的電腦，並搭配**使用 .NET 進行行動裝置開發**的工作負載)。
* 在 **Android** (實體或模擬器裝置) 或 **iOS** (僅限實體裝置) 上執行應用程式的功能。

針對 Android，您必須具有：

* 由開發人員解除鎖定的實體裝置或模擬器 (執行已安裝 Google Play Services 的 API 26 和更新版本)。

針對 iOS，您必須具有：

* 作用中的 [Apple 開發人員帳戶](https://developer.apple.com)。
* [向您的開發人員帳戶註冊](https://help.apple.com/developer-account/#/dev40df0d9fa)的實體 iOS 裝置 (執行 iOS 13.0 和更新版本)。
* 在您**金鑰鏈**中安裝的 **.p12**[開發憑證](https://help.apple.com/developer-account/#/dev04fd06d56)，以便您[在實體裝置上執行應用程式](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca)。

> [!NOTE]
> iOS 模擬器不支援遠端通知，因此在 iOS 上探索此範例時需要實體裝置。 不過，您不需要在 **Android** 和 **iOS** 上執行應用程式，也能完成本教學課程。

您可以遵循此第一原則範例中的步驟，而不需要先前的經驗。 不過，熟悉下列各方面對您有益。

* [Apple 開發人員入口網站](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Azure 通知中樞](notification-hubs-push-notification-overview.md)
* [Google Firebase 主控台](https://console.firebase.google.com/u/0/)
* [Xamarin](https://dotnet.microsoft.com/apps/xamarin) 和 [Xamarin.Forms](https://dotnet.microsoft.com/apps/xamarin/xamarin-forms)

提供的步驟適用於 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)，但可以使用 [Visual Studio 2019](https://visualstudio.microsoft.com/vs) 來繼續。

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>設定推播通知服務和 Azure 通知中樞

在本節中，您會設定 **[Firebase 雲端通訊 (FCM)](https://firebase.google.com/docs/cloud-messaging)** 和 **[Apple Push Notification Services (APNS)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** 。 接著，您可以建立並設定通知中樞，以使用這些服務。

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>建立 ASP.NET Core Web API 後端應用程式

在本節中，您會建立 [ASP.NET Core Web API](https://dotnet.microsoft.com/apps/aspnet/apis) 後端，以處理[裝置註冊](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration)並將通知傳送至 Xamarin.Forms 行動應用程式。

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-xamarinforms-application"></a>建立跨平台的 Xamarin.Forms 應用程式

在本節中，您會建立以跨平台方式執行推播通知的 [Xamarin.Forms](https://dotnet.microsoft.com/apps/xamarin/xamarin-forms) 行動應用程式。

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create Xamarin.Forms application](../../includes/notification-hubs-backend-service-sample-app-xamarin-forms.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>設定用於推播通知的原生 Android 專案

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-xamarin-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>設定用於推播通知的原生 iOS 專案

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-xamarin-ios.md)]

## <a name="test-the-solution"></a>測試解決方案

您現在可以透過後端服務測試傳送通知。

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>後續步驟

您現在應該已有基本的 Xamarin.Forms 應用程式，可透過後端服務連線到通知中樞，並且可以傳送和接收通知。

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>疑難排解

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>相關連結

* [Azure 通知中樞概觀](notification-hubs-push-notification-overview.md)
* [安裝 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* [在 Windows 上安裝 Xamarin](https://docs.microsoft.com/xamarin/get-started/installation/windows)
* [適用於後端作業的通知中樞 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [GitHub 上的通知中樞 SDK](https://github.com/Azure/azure-notificationhubs)
* [向應用程式後端註冊](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [註冊管理](notification-hubs-push-notification-registration-management.md)
* [使用標記](notification-hubs-tags-segment-push-message.md)
* [使用自訂範本](notification-hubs-templates-cross-platform-push-messages.md)
