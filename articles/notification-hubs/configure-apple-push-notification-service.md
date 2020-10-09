---
title: 在 Azure 通知中樞中設定 Apple Push Notification Service |Microsoft Docs
description: 瞭解如何使用 Apple Push Notification Service (APNS) 設定來設定 Azure 通知中樞。
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 63c7e0c9569428b55420911f253deee52ce440cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85255393"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>在 Azure 入口網站中設定通知中樞的 Apple Push Notification Service 設定

本文說明如何使用 Azure 入口網站設定 Azure 通知中樞的 Apple Push Notification Service (APNS) 設定。

## <a name="prerequisites"></a>必要條件

如果您尚未建立通知中樞，立即建立一個。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Azure 通知中樞](create-notification-hub-portal.md)。

## <a name="configure-apple-push-notification-service"></a>設定 Apple Push Notification Service

下列程式可讓您設定通知中樞的 Apple Push Notification Service (APNS) 設定的步驟：

1. 在 Azure 入口網站的 [ **通知中樞** ] 頁面上，選取左側功能表上的 [ **Apple (APNS) ** ]。

1. 針對 [驗證模式]，選取 [憑證] 或 [權杖]。

   - 如果您選取 [憑證]：
      - 選取檔案圖示，然後選取您要上傳的 [.p12] 檔案。
      - 輸入密碼。
      - 選取 [沙箱]  模式。 或者，若要傳送推播通知給從市集購買應用程式的使用者，請選取 [生產] 模式。

     ![Azure 入口網站中的 APNS 憑證設定螢幕擷取畫面](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   - 如果您選取 [權杖]：
      - 輸入 [金鑰識別碼]、[搭售方案識別碼]、[小組識別碼] 和 [權杖] 的值。
      - 選取 [沙箱]  模式。 或者，若要傳送推播通知給從市集購買應用程式的使用者，請選取 [生產] 模式。

     ![Azure 入口網站中的 APNS 權杖設定螢幕擷取畫面](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>後續步驟

如需有關將通知傳送至 iOS 裝置之逐步指示的教學課程，請參閱下列文章： [使用 Azure 通知中樞將推播通知傳送至 ios 應用程式](ios-sdk-get-started.md)。
