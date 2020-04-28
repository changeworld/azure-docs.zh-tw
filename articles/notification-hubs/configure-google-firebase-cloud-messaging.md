---
title: 在 Azure 通知中樞中設定 Google Firebase 雲端通訊 |Microsoft Docs
description: 瞭解如何使用 Google Firebase 雲端通訊設定來設定 Azure 通知中樞。
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 1adbce654bc5c057270df9a874911731a0135034
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80127476"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>在 Azure 入口網站中設定通知中樞的 Google Firebase 設定

本文說明如何使用 Azure 入口網站來設定 Azure 通知中樞的 Google Firebase 雲端通訊（FCM）設定。  

## <a name="prerequisites"></a>Prerequisites
如果您尚未建立通知中樞，立即建立一個。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Azure 通知中樞](create-notification-hub-portal.md)。 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>設定 Google Firebase 雲端通訊（FCM）

下列程式提供設定通知中樞的 Google Firebase 雲端通訊（FCM）設定的步驟： 

1. 在 Azure 入口網站的 [**通知中樞**] 頁面上，選取左側功能表上的 [ **Google （GCM/FCM）** ]。 
2. 針對您稍早儲存的 FCM 專案，貼上 **API 金鑰**。 
3. 選取 [儲存]  。 

   ![顯示如何設定 Google FCM 的通知中樞的螢幕擷取畫面](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>後續步驟
如需教學課程，其中包含使用 Azure 通知中樞和 Google Firebase 雲端通訊將通知推送至 Android 裝置的逐步指示，請參閱[使用通知中樞和 GOOGLE FCM 將通知推送至 android 裝置](notification-hubs-android-push-notification-google-fcm-get-started.md)。

