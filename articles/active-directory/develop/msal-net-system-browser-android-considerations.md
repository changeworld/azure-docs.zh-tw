---
title: Xamarin Android 系統瀏覽器注意事項 （MSAL.NET） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解在 Xamarin Android 上使用系統瀏覽器與 Microsoft 身份驗證庫用於 .NET （MSAL.NET） 的注意事項。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad26a4d619a7984f08a8decc87f9339adae47cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132601"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Xamarin Android 系統瀏覽器使用MSAL.NET

本文討論了在 Xamarin Android 上使用 Xamarin Android 上的系統瀏覽器以及用於 .NET （MSAL.NET）的 Microsoft 身份驗證庫時應考慮的事項。

從 MSAL.NET 2.4.0 預覽版開始，MSAL.NET支援 Chrome 以外的瀏覽器。 它不再需要在 Android 設備上安裝 Chrome 進行身份驗證。

我們建議您使用支援自訂選項卡的瀏覽器。 下面是這些瀏覽器的一些示例：

| 支援自訂選項卡的瀏覽器 | 套件名稱 |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.微軟.emmx|
|Firefox | org.mozilla.火狐|
|埃西亞 | com.ecosia.android|
|獼猴桃 | com.kiwi瀏覽器.瀏覽器|
|勇敢 | com.brave.瀏覽器|

除了識別支援自訂選項卡的瀏覽器外，我們的測試還表明，一些不支援自訂選項卡的瀏覽器也可用於身份驗證。 這些瀏覽器包括歌劇，歌劇迷你，瀏覽器和Maxthon。 

## <a name="tested-devices-and-browsers"></a>測試設備和瀏覽器
下表列出了已測試身份驗證相容性的設備和瀏覽器。

| 裝置 | 瀏覽器     |  結果  | 
| ------------- |:-------------:|:-----:|
| 華為/一* | Chrome\* | 通過|
| 華為/一* | Edge\* | 通過|
| 華為/一* | Firefox\* | 通過|
| 華為/一* | 勇敢\* | 通過|
| 一個* | 埃西亞\* | 通過|
| 一個* | 獼猴桃\* | 通過|
| 華為/一* | Opera | 通過|
| Huawei | 歌劇迷你 | 通過|
| 華為/一* | 瀏覽器中 | 通過|
| 一個* | 馬克斯森 | 通過|
| 華為/一* | 鴨鴨果 | 使用者取消身份驗證|
| 華為/一* | UC 瀏覽器 | 使用者取消身份驗證|
| 一個* | 海豚 | 使用者取消身份驗證|
| 一個* | CM 瀏覽器 | 使用者取消身份驗證|
| 華為/一* | 未安裝 | Android 活動未發現異常|

\*支援自訂選項卡

## <a name="known-issues"></a>已知問題

如果使用者在設備上未啟用瀏覽器，MSAL.NET將引發`AndroidActivityNotFound`異常。  
  - **緩解**：要求使用者在其設備上啟用瀏覽器。 建議支援自訂選項卡的瀏覽器。

如果身份驗證失敗（例如，如果使用 DuckDuckGo 啟動身份驗證），MSAL.NET將`AuthenticationCanceled MsalClientException`返回 。 
  - **根問題**：設備上未啟用支援自訂選項卡的瀏覽器。 使用無法完成身份驗證的瀏覽器啟動身份驗證。 
  - **緩解**：要求使用者在其設備上啟用瀏覽器。 建議支援自訂選項卡的瀏覽器。

## <a name="next-steps"></a>後續步驟
有關詳細資訊和代碼示例，請參閱在 Xamarin Android 和[嵌入式與系統 Web UI](msal-net-web-browsers.md#embedded-vs-system-web-ui)[上的嵌入式 Web 瀏覽器和系統瀏覽器之間進行選擇](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)。  