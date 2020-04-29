---
title: 在您的行動應用程式中使用 Visual Studio App Center 和 Azure 通知中樞推播通知的重要性
description: 瞭解您可以用來與行動應用程式使用者互動的服務，例如 Visual Studio App Center。
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: bc59857cdb843dfff54e69f2226f13cfe70df8f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80235338"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>藉由傳送推播通知與應用程式使用者互動

無論您是建立取用者或企業應用程式，都希望使用者主動使用您的應用程式。 有時候您會想要分享即時新聞、遊戲更新、令人興奮的供應專案、產品更新或任何其他與使用者相關的資訊。 若要增加與您的使用者互動，並讓他們回到您的應用程式，您需要能夠即時與使用者通訊的方法。

推播通知提供快速且有效率的方式，與您的應用程式使用者進行通訊。 您可以在正確的時間與您的使用者連線，並在行動裝置上的快顯專案或對話方塊中通知他們所需的資訊，不論他們正在做什麼。

## <a name="value-of-push-notifications"></a>推播通知的值
推播通知可為使用者和企業提供價值。

企業可以：
- 在支援的平臺上，于適當時間傳送訊息，直接與使用者通訊。
- 將即時更新和提醒傳送給使用者，鼓勵他們定期與應用程式互動，藉此提升使用者參與度。
- 使用已下載應用程式但未使用它再次變成作用中的使用者，保留使用者和 reengage。
- 藉由分析使用者行為、分割使用者，以及根據行動推播通知來運用行銷活動，以提高轉換率。
- 將推播訊息和及時更新傳送給使用者，以推廣產品和服務。
- 藉由傳送個人化推播訊息來為使用者設定目標。

針對應用程式使用者，推播通知：
- 即時提供價值和資訊。
- 提醒使用者使用應用程式。

使用下列服務在您的行動應用程式中啟用推播通知。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
使用[App Center Push](/appcenter/push/)時，您可以將目標訊息傳送給 IOS、Android 和 Windows 使用者，而不需要管理使用推播通知服務（PNS）將通知傳送至裝置的程式。 這項服務以 Azure 通知中樞為基礎，藉由提供強大的儀表板，消除與推播通知相關的複雜性。

**主要功能**
- 將推播通知傳送至各種平臺上的行動裝置。
- 使用通知將資料傳送至應用程式、向使用者顯示訊息，或由應用程式觸發動作。
- 使用通知目標來執行下列動作： 
    - 將訊息廣播到所有已註冊的裝置。
    - 根據裝置資訊和自訂屬性，將通知傳送給觀眾。
    - 將通知傳送給特定使用者。
    - 將通知傳送至特定裝置。
- 在 App Center 入口網站提供的推播、裝置和錯誤上使用豐富的遙測。
- 取得適用于 iOS、Android、macOS、Xamarin、React Native、Unity 和 Cordova 的平臺支援。

**參考**
- [使用 Visual Studio App Center 註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [開始使用 App Center Push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure 通知中心
[通知中樞](/azure/notification-hubs/notification-hubs-push-notification-overview)提供容易使用和相應放大的推播引擎。 您可以使用它來傳送通知到任何平臺，以及從雲端或內部部署的任何後端。

**主要功能**
- 從雲端或內部部署的任何後端，將推播通知傳送至任何平臺。
- 使用單一 API 呼叫，將快速廣播推送至數百萬個行動裝置。
- 依據客戶、語言和位置量身打造推播通知。
- 動態定義及通知客戶區段。
- 立即擴充到數百萬個行動裝置。
- 取得適用于 iOS、Android、Windows、Kindle 和百度的平臺支援。
        
**參考**
- [Azure 入口網站](https://portal.azure.com) 
- [開始使用 Azure 通知中樞](/azure/notification-hubs/)
- [快速入門](/azure/notification-hubs/create-notification-hub-portal)
- [範例](/azure/notification-hubs/samples)
