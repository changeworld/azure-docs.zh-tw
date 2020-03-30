---
title: 在 Azure 通知中心配置 Microsoft 推送通知服務 |微軟文檔
description: 瞭解如何為 Azure 通知中心配置 Microsoft 推送通知服務設置。
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
ms.openlocfilehash: 99f29e7910fe6070c6202f6a936173455f979732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127332"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>在 Azure 門戶中配置 Microsoft 推送通知服務 （MPNS） 設置

本文介紹如何使用 Azure 門戶為 Azure 通知中心配置 Microsoft 推送通知服務 （MPNS） 設置。 

## <a name="prerequisites"></a>Prerequisites
如果您尚未建立通知中樞，立即建立一個。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Azure 通知中樞](create-notification-hub-portal.md)。 

## <a name="configure-microsoft-push-notification-service-mpns"></a>配置微軟推送通知服務 （MPNS）

以下過程為您提供了為通知中心配置 Microsoft 推送通知服務 （MPNS） 設置的步驟： 

1. 在 Azure 門戶中，在**通知中心**頁上，選擇左側功能表上的**Windows Phone （MPNS）。**
1. 啟用未經驗證或已驗證的推播通知：

   a. 要啟用未身份驗證的推送通知，請選擇 **"啟用未身份驗證的推送** > **保存**"。

      ![顯示如何啟用未經驗證的推播通知的螢幕擷取畫面](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. 啟用已驗證推播通知：
      * 在工具列上選取 [上傳憑證]****。
      * 選取檔案圖示，然後選取憑證檔案。
      * 輸入憑證的密碼。
      * 選取 [確定]****。
      * 在 [Windows Phone (MPNS)]**** 頁面上，選取 [儲存]****。

## <a name="next-steps"></a>後續步驟
有關使用 Azure 通知中心和 Microsoft 推送通知服務 （MPNS） 將通知推送到 Windows Phone 設備的分步說明的教程，請參閱[通過使用通知中心將通知推送到 Windows Phone 應用](notification-hubs-windows-mobile-push-notifications-mpns.md)。

