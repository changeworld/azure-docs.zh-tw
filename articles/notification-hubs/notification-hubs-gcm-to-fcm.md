---
title: Azure 通知中心和 Google 火庫雲消息 （FCM） 遷移
description: 描述 Azure 通知中心如何解決 Google GCM 到 FCM 遷移。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127030"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Azure 通知中心和 Google 火庫雲消息遷移

## <a name="current-state"></a>目前狀態

當 Google 宣佈從 Google 雲消息 （GCM） 遷移到 Firebase 雲消息 （FCM） 時，像我們這樣的推送服務必須調整我們向 Android 設備發送通知的方式以適應更改。

我們更新了我們的服務後端，然後根據需要發佈了 API 和 SDK 的更新。 通過我們的實施，我們決定保持與現有 GCM 通知架構的相容性，以儘量減少客戶影響。 這意味著我們目前在 FCM 舊模式下使用 FCM 向 Android 設備發送通知。 最終，我們希望添加對 FCM 的真正支援，包括新功能和有效負載格式。 這是一個長期的變化，當前的遷移側重于保持與現有應用程式和 SDK 的相容性。 您可以在應用中使用 GCM 或 FCM SDK（以及我們的 SDK），並確保通知發送正確。

一些客戶最近收到來自 Google 的電子郵件，警告使用 GCM 終結點的應用程式進行通知。 這只是一個警告，沒有什麼被打破 - 你的應用程式的Android通知仍然發送到谷歌處理，谷歌仍然處理他們。 一些在其服務配置中顯式指定 GCM 終結點的客戶仍在使用棄用終結點。 我們已經發現了這個差距，並在谷歌發送電子郵件時正在努力解決這個問題。

我們替換了已棄用的終結點，並部署了修復程式。

## <a name="going-forward"></a>展望未來

谷歌的FCM常見問題解答說，你不必做任何事情。 在[FCM常見問題解答](https://developers.google.com/cloud-messaging/faq)中，谷歌表示"用戶端 SDK 和 GCM 權杖將繼續無限期工作。 但是，除非您遷移到 FCM，否則您將無法在 Android 應用中定位最新版本的 Google Play 服務。

如果您的應用使用 GCM 庫，請繼續按照 Google 的說明升級到應用中的 FCM 庫。 我們的 SDK 與任一版本相容，因此您不必更新我們方應用中的任何內容（只要您與我們的 SDK 版本保持最新）。

## <a name="questions-and-answers"></a>問與答

以下是我們從客戶那裡聽到的常見問題的一些答案：

**問：** 我需要做什麼才能在截止日期前相容（谷歌當前的截止日期是5月29日，可能會改變）？

**答：** 什麼。 我們將保持與現有 GCM 通知架構的相容性。 您的 GCM 金鑰將繼續正常工作，應用程式使用的任何 GCM SDK 和庫也將照常工作。

如果您決定升級到 FCM SDK 和庫以利用新功能，則 GCM 金鑰仍然有效。 如果需要，可以切換到使用 FCM 金鑰，但在創建新的 Firebase 專案時，請確保將 Firebase 添加到現有 GCM 專案。 這將保證與運行舊版本的應用且仍使用 GCM SDK 和庫的客戶向後相容。

如果要創建新的 FCM 專案，但未附加到現有的 GCM 專案，一旦使用新的 FCM 金鑰更新通知中心，您將失去將通知推送到當前應用安裝的能力，因為新的 FCM 金鑰沒有指向舊 GCM 的連結專案。

**問：** 為什麼我收到有關正在使用的舊 GCM 終結點的電子郵件？ 我該怎麼做？

**答：** 什麼。 我們一直在遷移到新的終結點，並將很快完成，因此無需更改。 沒有什麼被打破，我們一個錯過的終端只是引起了來自谷歌的警告訊息。

**問：** 如何在不中斷現有使用者的情況下過渡到新的 FCM SDK 和庫？

答：隨時升級。 谷歌尚未宣佈任何對現有GCM SDK和圖書館的棄用。 為了確保不會中斷對現有使用者的推送通知，請確保在創建新的 Firebase 專案時，您要與現有的 GCM 專案關聯。 這將確保新的 Firebase 機密適用于使用 GCM SDK 和庫運行應用舊版本的使用者，以及使用 FCM SDK 和庫的應用的新使用者。

**問：** 何時可以使用新的 FCM 功能和架構來進行通知？

**答：** 發佈 API 和 SDK 的更新後，請繼續關注我們 - 我們希望在未來幾個月內為您提供一些服務。
