---
title: Xamarin Android 系統瀏覽器考慮 (MSAL.NET) |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解使用適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) 在 Xamarin Android 上使用系統瀏覽器的考慮。
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
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4230a194fb18587a209c100a39b0924e6170502d
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063462"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>使用 MSAL.NET 的 Xamarin Android 系統瀏覽器考慮

本文討論當您使用 Xamarin Android 上的系統瀏覽器搭配適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) 時應考慮的事項。

從 MSAL.NET 2.4.0 Preview 開始，MSAL.NET 支援 Chrome 以外的瀏覽器。 它不再需要在 Android 裝置上安裝 Chrome 來進行驗證。

建議您使用支援自訂索引標籤的瀏覽器。 以下是這些瀏覽器的一些範例：

| 具有自訂索引標籤支援的瀏覽器 | 套件名稱 |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | emmx|
|Firefox | 組織 mozilla|
|Ecosia | ecosia android|
|獼猴桃 | kiwibrowser 瀏覽器|
|勇敢 | 美麗瀏覽器|

除了識別提供自訂索引標籤支援的瀏覽器以外，我們的測試也會指出一些不支援自訂索引標籤的瀏覽器也適用于驗證。 這些瀏覽器包括 Opera、Opera 迷你、InBrowser 和 Maxthon。 

## <a name="tested-devices-and-browsers"></a>測試的裝置和瀏覽器
下表列出已測試過驗證相容性的裝置和瀏覽器。

| 裝置 | 瀏覽器     |  結果  | 
| ------------- |:-------------:|:-----:|
| Huawei/One + | 鉻\* | 通過|
| Huawei/One + | Edge\* | 通過|
| Huawei/One + | Firefox\* | 通過|
| Huawei/One + | 勇敢\* | 通過|
| 一 + | Ecosia\* | 通過|
| 一 + | 獼猴桃\* | 通過|
| Huawei/One + | Opera | 通過|
| Huawei | OperaMini | 通過|
| Huawei/One + | InBrowser | 通過|
| 一 + | Maxthon | 通過|
| Huawei/One + | DuckDuckGo | 使用者已取消驗證|
| Huawei/One + | UC 瀏覽器 | 使用者已取消驗證|
| 一 + | 海豚 | 使用者已取消驗證|
| 一 + | CM 瀏覽器 | 使用者已取消驗證|
| Huawei/One + | 未安裝 | AndroidActivityNotFound 例外狀況|

\* 支援自訂索引標籤

## <a name="known-issues"></a>已知問題

如果使用者未在裝置上啟用瀏覽器，MSAL.NET 會擲回 `AndroidActivityNotFound` 例外狀況。  
  - **風險降低**：要求使用者在其裝置上啟用瀏覽器。 建議支援自訂索引標籤的瀏覽器。

如果驗證失敗 (例如，如果驗證以 DuckDuckGo) 啟動，MSAL.NET 將會傳回 `AuthenticationCanceled MsalClientException` 。 
  - **根本問題**：裝置上未啟用支援自訂索引標籤的瀏覽器。 以無法完成驗證的瀏覽器啟動驗證。 
  - **風險降低**：要求使用者在其裝置上啟用瀏覽器。 建議支援自訂索引標籤的瀏覽器。

## <a name="next-steps"></a>後續步驟
如需詳細資訊和程式碼範例，請參閱在 Xamarin Android 和[embedded 與 system WEB UI](msal-net-web-browsers.md#embedded-vs-system-web-ui)[上，選擇內嵌的網頁瀏覽器和系統瀏覽器](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)。  