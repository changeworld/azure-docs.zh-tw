---
title: Xamarin Android 系統瀏覽器考慮（MSAL.NET） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解在 Xamarin Android 上使用系統瀏覽器搭配適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）時的特定考慮。
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
ms.openlocfilehash: c144c6dd090669ca16c03050cbb8b59ff0cc224f
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084583"
---
#  <a name="xamarin-android-system-browser-considerations-with-msalnet"></a>MSAL.NET 的 Xamarin Android 系統瀏覽器考慮

本文討論在 Xamarin Android 上使用系統瀏覽器搭配適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）時的特定考慮。

從 MSAL.NET 2.4.0-preview 開始，MSAL.NET 支援 Chrome 以外的瀏覽器，且不再需要在 Android 裝置上安裝 Chrome 來進行驗證。

建議您使用支援自訂索引標籤的瀏覽器，例如：

| 具有自訂索引標籤支援的瀏覽器 | 封裝名稱 |
|------| ------- |
|Chrome | .com. chrome|
|Microsoft Edge | emmx|
|Firefox | 組織 mozilla. firefox|
|Ecosia | ecosia android|
|奇異果 | kiwibrowser. 瀏覽器|
|美麗 | 美麗. 瀏覽器|

除了具有自訂索引標籤支援的瀏覽器，根據我們的測試，有些不支援自訂索引標籤的瀏覽器也適用于驗證： Opera、Opera 迷你、InBrowser 和 Maxthon。 如需詳細資訊，請參閱[資料表以取得測試結果](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested)。

## <a name="known-issues"></a>已知問題

- 如果使用者未在裝置上啟用瀏覽器，MSAL.NET 將會擲回 `AndroidActivityNotFound` 例外狀況。 
  - **緩和**：通知使用者他們應該在其裝置上啟用瀏覽器（最好是具有自訂索引標籤的帳戶）。

- 如果驗證失敗（例如 驗證會以 DuckDuckGo 啟動），MSAL.NET 會傳回 `AuthenticationCanceled MsalClientException`。 
  - **根本問題**：裝置上未啟用具有自訂索引標籤支援的瀏覽器。 以替代的瀏覽器啟動驗證，但無法完成驗證。 
  - **緩和**：通知使用者他們應該在其裝置上安裝瀏覽器（最好是具有自訂索引標籤支援的應用程式）。

## <a name="devices-and-browsers-tested"></a>已測試的裝置和瀏覽器
下表列出已測試過的裝置和瀏覽器。

| | 瀏覽器&ast;     |  結果  | 
| ------------- |:-------------:|:-----:|
| Huawei/一 + | Chrome&ast; | 通過|
| Huawei/一 + | Edge&ast; | 通過|
| Huawei/一 + | Firefox&ast; | 通過|
| Huawei/一 + | 美麗&ast; | 通過|
| 一個 + | Ecosia&ast; | 通過|
| 一個 + | 奇異果&ast; | 通過|
| Huawei/一 + | Opera | 通過|
| Huawei | OperaMini | 通過|
| Huawei/一 + | InBrowser | 通過|
| 一個 + | Maxthon | 通過|
| Huawei/一 + | DuckDuckGo | 使用者已取消驗證|
| Huawei/一 + | UC 瀏覽器 | 使用者已取消驗證|
| 一個 + | Dolphin | 使用者已取消驗證|
| 一個 + | CM 瀏覽器 | 使用者已取消驗證|
| Huawei/一 + | 未安裝 | AndroidActivityNotFound ex|

&ast; 支援自訂索引標籤

## <a name="next-steps"></a>後續步驟
如需有關在 Xamarin Android 上使用系統瀏覽器的程式碼片段和其他資訊，請閱讀本[指南](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)。  