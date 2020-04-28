---
title: 在 Azure 通知中樞中設定 Windows 推播通知服務 |Microsoft Docs
description: 瞭解如何設定 Azure 通知中樞的 Windows 推播通知服務設定。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80127316"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>在 Azure 入口網站中設定 Windows 推播通知服務設定

本文說明如何使用 Azure 入口網站來設定 Azure 通知中樞的 Windows 通知服務（WNS）設定。  

## <a name="prerequisites"></a>Prerequisites
如果您尚未建立通知中樞，立即建立一個。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Azure 通知中樞](create-notification-hub-portal.md)。 

## <a name="configure-windows-push-notification-service-wns"></a>設定 Windows 推播通知服務（WNS）

下列程式提供設定通知中樞之 Windows 推播通知服務（WNS）設定的步驟： 

1. 在 Azure 入口網站的 [**通知中樞**] 頁面上，選取左側功能表上的 [ **Windows （WNS）** ]。
2. 輸入 [套件 SID]  和 [安全性金鑰]  的值。
3. 選取 [儲存]  。

   ![顯示套件 SID 和安全性金鑰方塊的螢幕擷取畫面](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>後續步驟
如需教學課程，其中包含使用 Azure 通知中樞和 Windows 推播通知服務（WNS）將通知推送至通用 Windows 平臺應用程式的逐步指示，請參閱[使用 azure 通知中樞將通知傳送至 UWP 應用](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)程式。


