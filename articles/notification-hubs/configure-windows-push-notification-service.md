---
title: 在 Azure 通知中心配置 Windows 推送通知服務 |微軟文檔
description: 瞭解如何為 Azure 通知中心配置 Windows 推送通知服務設置。
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
ms.openlocfilehash: 73304e191242725c80204efb132c26aede9ce7e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127316"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>在 Azure 門戶中配置 Windows 推送通知服務設置

本文介紹如何使用 Azure 門戶為 Azure 通知中心配置 Windows 通知服務 （WNS） 設置。  

## <a name="prerequisites"></a>Prerequisites
如果您尚未建立通知中樞，立即建立一個。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Azure 通知中樞](create-notification-hub-portal.md)。 

## <a name="configure-windows-push-notification-service-wns"></a>配置 Windows 推送通知服務 （WNS）

以下過程提供了為通知中心配置 Windows 推送通知服務 （WNS） 設置的步驟： 

1. 在 Azure 門戶中，在**通知中心**頁上，選擇左側功能表上的**Windows （WNS）。**
2. 輸入 [套件 SID]**** 和 [安全性金鑰]**** 的值。
3. 選取 [儲存]****。

   ![顯示套件 SID 和安全性金鑰方塊的螢幕擷取畫面](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>後續步驟
有關使用 Azure 通知中心和 Windows 推送通知服務 （WNS） 將通知推送到通用 Windows 平臺應用程式的分步說明的教程，請參閱[使用 Azure 通知中心將通知發送到 UWP 應用](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)。


