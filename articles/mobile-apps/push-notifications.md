---
title: 使用視覺化工作室應用中心和 Azure 通知中心移動應用中的推送通知的重要性
description: 瞭解可用於與移動應用程式使用者互動的視覺化工作室應用中心等服務。
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: bc59857cdb843dfff54e69f2226f13cfe70df8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235338"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>通過發送推送通知與應用程式使用者互動

無論您是在構建消費者應用程式還是企業應用程式，都希望使用者主動使用您的應用程式。 有時，您希望與使用者共用突發新聞、遊戲更新、令人興奮的優惠、產品更新或任何其他相關資訊。 要增加與使用者的參與度，並讓他們返回到您的應用程式，您需要一種與使用者進行即時通信的方法。

推送通知提供了一種與應用程式使用者進行通信的快速高效方式。 您可以在正確的時間聯繫使用者並通知使用者所需的資訊，通常使用行動裝置上的彈出式專案或對話方塊，無論他們在做什麼。

## <a name="value-of-push-notifications"></a>推送通知的值
推送通知為使用者和企業提供價值。

企業可以：
- 通過在受支援的平臺上在正確的時間發送消息，直接與使用者通信。
- 通過向使用者發送即時更新和提醒，鼓勵他們定期與應用程式互動，提高使用者參與度。
- 保留使用者並重新與下載應用程式的非活動使用者重新互動，但不要使用它再次處於活動狀態。
- 通過分析使用者行為、細分使用者和利用基於移動推送通知的廣告系列來提高轉化率。
- 通過向使用者發送推送消息和及時更新來推廣產品和服務。
- 通過發送個人化的推送消息來定位使用者。

對於應用程式使用者，推送通知：
- 即時提供價值和資訊。
- 提醒使用者使用該應用程式。

使用以下服務在移動應用中啟用推送通知。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
使用[應用中心推送](/appcenter/push/)，您可以將目標消息發送給 iOS、Android 和 Windows 使用者，而無需使用推送通知服務 （PNS） 管理向設備發送通知的過程。 此服務構建在 Azure 通知中心之上，通過提供功能強大的儀表板，消除了手動推送通知的複雜性。

**主要功能**
- 跨各種平臺向行動裝置發送推送通知。
- 使用通知將資料發送到應用程式、向使用者顯示消息或觸發應用程式的操作。
- 使用通知目標： 
    - 將消息廣播到所有已註冊設備。
    - 根據設備資訊和自訂屬性向訪問群體發送通知。
    - 向特定使用者發送通知。
    - 向特定設備發送通知。
- 使用應用中心門戶中可用的推送、設備和錯誤的豐富遙測資料。
- 獲得對 iOS、Android、macOS、Xamarin、反應原生、團結和科爾多瓦的平臺支援。

**引用**
- [使用視覺化工作室應用中心註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [開始應用中心推送](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure 通知中心
[通知中心](/azure/notification-hubs/notification-hubs-push-notification-overview)提供了便於使用和橫向擴展的推送引擎。 您可以使用它向任何平臺以及雲或本地的任何後端發送通知。

**主要功能**
- 從任何後端、雲或本地向任何平臺發送推送通知。
- 通過單個 API 呼叫，快速廣播推送到數百萬個行動裝置。
- 按客戶、語言和位置定制推送通知。
- 動態定義和通知客戶細分。
- 即時擴展到數百萬台行動裝置。
- 獲得 iOS、Android、Windows、Kindle 和百度的平臺支援。
        
**引用**
- [Azure 門戶](https://portal.azure.com) 
- [開始使用 Azure 通知中樞](/azure/notification-hubs/)
- [快速入門](/azure/notification-hubs/create-notification-hub-portal)
- [樣品](/azure/notification-hubs/samples)
