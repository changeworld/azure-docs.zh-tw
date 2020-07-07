---
title: Xamarin Android 系統瀏覽器考慮（MSAL.NET） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解在 Xamarin Android 上使用系統瀏覽器搭配適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）的考慮。
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
ms.openlocfilehash: 8e37d443d75dccae8704bc1ff7856e1d24131766
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82652707"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>使用 MSAL.NET 的 Xamarin Android 系統瀏覽器考慮

本文討論當您在 Xamarin Android 上使用系統瀏覽器搭配適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）時，您應該考慮的事項。

從 MSAL.NET 2.4.0 Preview 開始，MSAL.NET 支援 Chrome 以外的瀏覽器。 它不再需要在 Android 裝置上安裝 Chrome 來進行驗證。

建議您使用支援自訂索引標籤的瀏覽器。 以下是這些瀏覽器的一些範例：

| 具有自訂索引標籤支援的瀏覽器 | 套件名稱 |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | emmx|
|Firefox | 組織 mozilla. firefox|
|Ecosia | ecosia android|
|奇異果 | kiwibrowser. 瀏覽器|
|美麗 | 美麗. 瀏覽器|

我們的測試除了可識別提供自訂索引標籤支援的瀏覽器之外，還會指出不支援自訂索引標籤的一些瀏覽器也適用于驗證。 這些瀏覽器包括 Opera、Opera 迷你、InBrowser 和 Maxthon。 

## <a name="tested-devices-and-browsers"></a>測試的裝置和瀏覽器
下表列出已針對驗證相容性進行測試的裝置和瀏覽器。

| 裝置 | 瀏覽器     |  結果  | 
| ------------- |:-------------:|:-----:|
| Huawei/一 + | Chrome\* | 通過|
| Huawei/一 + | Edge\* | 通過|
| Huawei/一 + | Firefox\* | 通過|
| Huawei/一 + | 美麗\* | 通過|
| 一個 + | Ecosia\* | 通過|
| 一個 + | 奇異果\* | 通過|
| Huawei/一 + | Opera | 通過|
| Huawei | OperaMini | 通過|
| Huawei/一 + | InBrowser | 通過|
| 一個 + | Maxthon | 通過|
| Huawei/一 + | DuckDuckGo | 使用者已取消驗證|
| Huawei/一 + | UC 瀏覽器 | 使用者已取消驗證|
| 一個 + | Dolphin | 使用者已取消驗證|
| 一個 + | CM 瀏覽器 | 使用者已取消驗證|
| Huawei/一 + | 未安裝 | AndroidActivityNotFound 例外狀況|

\*支援自訂索引標籤

## <a name="known-issues"></a>已知問題

如果使用者未在裝置上啟用瀏覽器，MSAL.NET 將會擲回 `AndroidActivityNotFound` 例外狀況。  
  - **緩和**：要求使用者在其裝置上啟用瀏覽器。 建議支援自訂索引標籤的瀏覽器。

如果驗證失敗（例如，如果使用 DuckDuckGo 啟動驗證），MSAL.NET 將會傳回 `AuthenticationCanceled MsalClientException` 。 
  - **根本問題**：不在裝置上啟用支援自訂索引標籤的瀏覽器。 以瀏覽器啟動的驗證無法完成驗證。 
  - **緩和**：要求使用者在其裝置上啟用瀏覽器。 建議支援自訂索引標籤的瀏覽器。

## <a name="next-steps"></a>後續步驟
如需詳細資訊和程式碼範例，請參閱在 Xamarin Android 和[embedded 與系統 WEB UI](msal-net-web-browsers.md#embedded-vs-system-web-ui)[上選擇內嵌網頁瀏覽器和系統流覽](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)器。  