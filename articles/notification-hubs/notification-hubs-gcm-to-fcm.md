---
title: Azure 通知中樞和 Google Firebase 雲端通訊（FCM）遷移
description: 說明 Azure 通知中樞如何將 Google GCM 定址以 FCM 遷移。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80127030"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Azure 通知中樞和 Google Firebase 雲端訊息遷移

## <a name="current-state"></a>目前狀態

當 Google 宣佈從 Google 雲端通訊（GCM）遷移至 Firebase 雲端通訊（FCM）時，推播服務（如我們）必須調整我們將通知傳送至 Android 裝置以因應變更的方式。

我們已更新服務後端，然後視需要將更新發佈至我們的 API 和 Sdk。 隨著我們的實行，我們決定維護與現有 GCM 通知架構的相容性，以將客戶的影響降至最低。 這表示我們目前使用 FCM 傳統模式的 FCM，將通知傳送至 Android 裝置。 最後，我們想要新增 FCM 的真正支援，包括新功能和承載格式。 這是較長期的變更，而目前的遷移著重于維持與現有應用程式和 Sdk 的相容性。 您可以在應用程式中使用 GCM 或 FCM Sdk （連同我們的 SDK），並確定已正確傳送通知。

某些客戶最近收到有關使用 GCM 端點進行通知之應用程式的 Google 警告電子郵件。 這只是警告，而且不會有任何中斷-您的應用程式的 Android 通知仍會傳送至 Google 進行處理，而 Google 仍然會處理它們。 某些已明確在其服務設定中指定 GCM 端點的客戶仍在使用已淘汰的端點。 我們已識別出此缺口，並正努力在 Google 傳送電子郵件時解決問題。

我們已取代該已淘汰的端點，並已部署修正程式。

## <a name="going-forward"></a>往後

Google 的 FCM 常見問題指出您不需要採取任何動作。 在[FCM 常見問題](https://developers.google.com/cloud-messaging/faq)中，Google 說「用戶端 SDK 和 GCM 權杖將會無限期地繼續工作。 不過，除非您遷移至 FCM，否則您將無法以 Android 應用程式中的最新 Google Play Services 版本為目標。

如果您的應用程式使用 GCM 程式庫，請繼續進行，並遵循 Google 的指示升級至您應用程式中的 FCM 程式庫。 我們的 SDK 與這兩者相容，因此您不需要在我們的應用程式中更新任何專案（前提是您已使用我們的 SDK 版本保持最新狀態）。

## <a name="questions-and-answers"></a>問題和回答

以下是我們從客戶聽到的常見問題的解答：

**問：** 我需要執行哪些動作才能與截止日期相容（Google 的目前截止日期可能是29，而且可能會變更）？

**答：** 這裡. 我們會維持與現有 GCM 通知架構的相容性。 您的 GCM 金鑰會繼續正常運作，就像您的應用程式所使用的任何 GCM Sdk 和程式庫一樣。

如果您決定要升級至 FCM Sdk 和程式庫以利用新功能，您的 GCM 金鑰仍然可以使用。 如有需要，您可以切換為使用 FCM 金鑰，但在建立新的 Firebase 專案時，請確定您已將 Firebase 新增至現有的 GCM 專案。 這可保證與執行繼承應用程式的客戶回溯相容性，但仍會使用 GCM Sdk 和程式庫。

如果您要建立新的 FCM 專案，而不是附加至現有的 GCM 專案，一旦您使用新的 FCM 秘密來更新通知中樞，就無法將通知推送至目前的應用程式安裝，因為新的 FCM 金鑰沒有舊 GCM 專案的連結。

**問：** 為什麼我會收到有關使用舊 GCM 端點的這封電子郵件？ 我該怎麼做？

**答：** 這裡. 我們已遷移至新的端點，即將完成，因此不需要進行任何變更。 沒有任何東西會中斷，我們的一個錯過的端點只會導致來自 Google 的警告訊息。

**問：** 如何轉換至新的 FCM Sdk 和程式庫而不會中斷現有的使用者？

答：隨時升級。 Google 尚未宣佈現有的 GCM Sdk 和程式庫已淘汰。 為確保您不會將推播通知分解到現有的使用者，請確定您是在建立與現有 GCM 專案相關聯的新 Firebase 專案時。 這可確保新的 Firebase 秘密適用于使用 GCM Sdk 和程式庫執行繼承應用程式的使用者，以及使用 FCM Sdk 和程式庫的應用程式新使用者。

**問：** 何時可以將新的 FCM 功能和架構用於我的通知？

**答：** 一旦我們將更新發佈至我們的 API 和 Sdk 之後，請繼續進行調整-我們預期您會在未來幾個月內有一些專案。
