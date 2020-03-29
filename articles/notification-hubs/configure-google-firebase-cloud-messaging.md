---
title: 在 Azure 通知中心配置 Google 火庫雲消息 |微軟文檔
description: 瞭解如何使用 Google Firebase 雲消息設置配置 Azure 通知中心。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127476"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>為 Azure 門戶中的通知中心配置 Google Firebase 設置

本文介紹如何使用 Azure 門戶為 Azure 通知中心配置 Google Firebase 雲消息傳遞 （FCM） 設置。  

## <a name="prerequisites"></a>Prerequisites
如果您尚未建立通知中樞，立即建立一個。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Azure 通知中樞](create-notification-hub-portal.md)。 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>配置谷歌火庫雲消息 （FCM）

以下過程為您提供了為通知中心配置 Google Firebase 雲消息 （FCM） 設置的步驟： 

1. 在 Azure 門戶中，在**通知中心**頁上，選擇左側功能表上的**Google （GCM/FCM）。** 
2. 針對您稍早儲存的 FCM 專案，貼上 **API 金鑰**。 
3. 選取 [儲存]****。 

   ![顯示如何設定 Google FCM 的通知中樞的螢幕擷取畫面](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>後續步驟
有關使用 Azure 通知中心和 Google Firebase 雲消息推送通知到 Android 設備的分步說明的教程，[請參閱使用通知中心和 Google FCM 將通知推送到 Android 設備](notification-hubs-android-push-notification-google-fcm-get-started.md)。

