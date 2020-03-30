---
title: 在 Azure 通知中心配置 Apple 推送通知服務 |微軟文檔
description: 瞭解如何使用 Apple 推送通知服務 （APNS） 設置配置 Azure 通知中心。
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
ms.openlocfilehash: eb1122ba3de0002507589d3e607d1e39d905c308
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127523"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>為 Azure 門戶中的通知中心配置 Apple 推送通知服務設置

本文介紹如何使用 Azure 門戶為 Azure 通知中心配置 Apple 推送通知服務 （APNS） 設置。 

## <a name="prerequisites"></a>Prerequisites
如果您尚未建立通知中樞，立即建立一個。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Azure 通知中樞](create-notification-hub-portal.md)。 

## <a name="configure-apple-push-notification-service"></a>配置 Apple 推送通知服務

以下過程為您提供了為通知中心配置 Apple 推送通知服務 （APNS） 設置的步驟：

1. 在 Azure 門戶中，在**通知中心**頁上，選擇左側功能表上的**Apple （APNS）。**

1. 針對 [驗證模式]****，選取 [憑證]**** 或 [權杖]****。

   a. 如果您選取 [憑證]****：
   * 選取檔案圖示，然後選取您要上傳的 [.p12]** 檔案。
   * 輸入密碼。
   * 選取 [沙箱]**** 模式。 或者，若要傳送推播通知給從市集購買應用程式的使用者，請選取 [生產]**** 模式。

     ![Azure 入口網站中的 APNS 憑證設定螢幕擷取畫面](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. 如果您選取 [權杖]****：

   * 輸入**金鑰識別碼、** 捆綁**ID、****團隊 ID**和**權杖**的值。
   * 選取 [沙箱]**** 模式。 或者，若要傳送推播通知給從市集購買應用程式的使用者，請選取 [生產]**** 模式。

     ![Azure 入口網站中的 APNS 權杖設定螢幕擷取畫面](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>後續步驟
有關將通知推送到 iOS 設備的分步說明的教程，請參閱以下文章：[使用通知中心和 APNS 將通知推送到 iOS 設備](notification-hubs-ios-apple-push-notification-apns-get-started.md)
