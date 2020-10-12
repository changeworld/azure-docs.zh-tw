---
title: 在 Azure 通知中樞中設定 Google Firebase 雲端通訊 |Microsoft Docs
description: 瞭解如何使用 Google Firebase 雲端通訊設定來設定 Azure 通知中樞。
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 0da103c11e2412108535ca322917632f5d95559d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87760833"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>在 Azure 入口網站中設定通知中樞的 Google Firebase 設定

本文說明如何使用 Azure 入口網站，為 Azure 通知中樞設定 Google Firebase 雲端通訊 (FCM) 設定。  

## <a name="prerequisites"></a>必要條件

如果您尚未建立通知中樞，立即建立一個。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Azure 通知中樞](create-notification-hub-portal.md)。

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>設定 Google Firebase 雲端通訊 (FCM) 

下列程式說明為通知中樞設定 Google Firebase 雲端通訊 (FCM) 設定的步驟：

1. 在 Azure 入口網站的 [ **通知中樞** ] 頁面上，選取左側功能表上的 [ **Google (GCM/FCM) ** 。
2. 針對您稍早儲存的 FCM 專案，貼上 **API 金鑰**。
3. 選取 [儲存]。

   ![顯示如何設定 Google FCM 的通知中樞的螢幕擷取畫面](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>後續步驟

如需使用 Azure 通知中樞和 Google Firebase 雲端通訊將通知傳送至 Android 裝置的逐步指示教學課程，請參閱 [使用通知中樞和 GOOGLE FCM 將推播通知傳送至 android 裝置](notification-hubs-android-push-notification-google-fcm-get-started.md)。
