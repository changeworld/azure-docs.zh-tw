---
title: Azure 通知中樞和 Google Firebase 雲端通訊 (FCM) 遷移
description: 說明 Azure 通知中樞如何解決 Google GCM 以 FCM 遷移。
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80127030"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Azure 通知中樞和 Google Firebase 雲端訊息遷移

## <a name="current-state"></a>目前狀態

當 Google 宣佈從 Google 雲端通訊進行遷移時 (GCM) Firebase 雲端通訊 (FCM) ，例如，我們必須調整傳送通知至 Android 裝置的方式，以配合變更。

我們已更新服務後端，然後視需要將更新發佈至我們的 API 和 Sdk。 在我們的實施中，我們決定維持與現有 GCM 通知架構的相容性，以將客戶的影響降至最低。 這表示我們目前使用 FCM 舊版模式中的 FCM，將通知傳送至 Android 裝置。 最後，我們想要新增真正的 FCM 支援，包括新功能和裝載格式。 這是較長期的變更，而目前的遷移著重于維持與現有應用程式和 Sdk 的相容性。 您可以在應用程式中使用 GCM 或 FCM Sdk (以及我們的 SDK) ，我們會確定已正確傳送通知。

某些客戶最近收到來自 Google 警告的電子郵件，其中會使用 GCM 端點來通知應用程式。 這只是警告，而且沒有任何問題，您的應用程式的 Android 通知仍會傳送至 Google 進行處理，且 Google 仍會處理這些通知。 某些在其服務設定中明確指定 GCM 端點的客戶仍在使用已被取代的端點。 我們已識別出此缺口，並正在解決 Google 傳送電子郵件時的問題。

我們取代了已淘汰的端點，並已部署修正。

## <a name="going-forward"></a>往後

Google 的 FCM 常見問題指出您不需要採取任何動作。 在 [FCM 常見問題](https://developers.google.com/cloud-messaging/faq)中，Google 說「用戶端 SDK 和 GCM 權杖將繼續無限期地運作。 不過，除非您遷移至 FCM，否則您將無法在 Android 應用程式中以最新版本的 Google Play Services 為目標。

如果您的應用程式使用 GCM 程式庫，請繼續並依照 Google 的指示，在您的應用程式中升級至 FCM 程式庫。 我們的 SDK 與任何一項相容，因此只要您是最新的 SDK 版本) ，就不需要在我們的 (更新應用程式中的任何資料。

## <a name="questions-and-answers"></a>問題和回答

以下是我們從客戶聽到的一些常見問題解答：

**問：** (Google 目前的截止日期是否符合截止日期時，我需要採取什麼動作？這可能會變更) ？

**答：** 什麼。 我們將維持與現有 GCM 通知架構的相容性。 您的 GCM 金鑰將繼續正常運作，如同您的應用程式所使用的任何 GCM Sdk 和程式庫一般。

如果您決定升級至 FCM Sdk 和程式庫以利用新功能，則您的 GCM 金鑰仍可運作。 您可以視需要切換到使用 FCM 索引鍵，但請確定您是在建立新的 Firebase 專案時，將 Firebase 新增至現有的 GCM 專案。 這可保證您的客戶執行的繼承應用程式，仍使用 GCM Sdk 和程式庫的回溯相容性。

如果您要建立新的 FCM 專案，但未附加至現有的 GCM 專案，則在使用新的 FCM 秘密更新通知中樞之後，您將無法將通知推播至目前的應用程式安裝，因為新的 FCM 金鑰沒有舊的 GCM 專案的連結。

**問：** 為什麼我會收到這封電子郵件，告訴您使用舊的 GCM 端點？ 我該怎麼做？

**答：** 什麼。 我們已遷移至新的端點並即將完成，因此不需要進行任何變更。 沒有任何已中斷的專案，我們的一個遺漏端點只會導致來自 Google 的警告訊息。

**問：** 如何轉換至新的 FCM Sdk 和程式庫，而不會中斷現有的使用者？

答：任何時間升級。 Google 尚未宣告現有 GCM Sdk 和程式庫的任何淘汰。 為確保您不會中斷推播通知給現有的使用者，請確定您建立的新 Firebase 專案與現有的 GCM 專案相關聯。 這可確保新的 Firebase 秘密適用于使用 GCM Sdk 和程式庫執行繼承應用程式的使用者，以及具有 FCM Sdk 和程式庫的應用程式新使用者。

**問：** 我何時可以針對我的通知使用新的 FCM 功能和架構？

**答：** 一旦我們將更新發佈到我們的 API 和 Sdk 之後，請隨時掌握，我們預期會在接下來的幾個月內有一些東西。
