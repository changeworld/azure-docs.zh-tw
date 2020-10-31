---
title: 沈浸式閱讀程式 SDK 版本資訊
titleSuffix: Azure Cognitive Services
description: 深入瞭解沈浸式閱讀程式 JavaScript SDK 的新功能。
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 10/12/2020
ms.author: dylankil
ms.openlocfilehash: 6b041916c6fa446732e95d49391d9ead63eb1b17
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133498"
---
# <a name="immersive-reader-javascript-sdk-release-notes"></a>沈浸式閱讀程式 JavaScript SDK 版本資訊

## <a name="version-110"></a>1.1.0 版

此版本包含新功能、安全性弱點修正、bug 修正、程式碼範例的更新，以及設定選項。

#### <a name="new-features"></a>新功能

* 在不同的瀏覽器和裝置上啟用儲存和載入使用者喜好設定
* 啟用設定預設顯示選項
* 新增選項來設定翻譯語言、啟用 word 翻譯，以及在啟動時啟用檔轉譯沈浸式閱讀程式
* 新增透過選項設定大聲朗讀的支援
* 新增停用第一次執行體驗的功能
* 新增 UWP 的 ImmersiveReaderView

#### <a name="improvements"></a>改善項目

* 更新 Android 程式碼範例 HTML，以使用最新的 SDK
* 更新啟動回應以傳回已處理的字元數
* 更新程式碼範例以使用 v 1.1。0
* 不允許在已載入時呼叫 launchAsync
* 藉由略過資料不是字串的訊息來檢查是否有不正確內容
* 將呼叫包裝到 if 子句中的視窗，以檢查瀏覽器支援的承諾

#### <a name="fixes"></a>修正

* 藉由從 .gitignore 移除 yarn 來修正 dependabot
* 在快速入門中將 pug 升級為 v 3.0.0 來修正安全性弱點-nodejs 程式碼範例
* 藉由升級 Jest 和 TypeScript 套件來修正多個安全性弱點
* 藉由將 Microsoft.identitymodel 升級為 v 5.2.0 來修正安全性弱點

<br>

## <a name="version-100"></a>1\.0.0 版

此版本包含重大變更、新功能、程式碼範例的增強功能，以及 bug 修正。

#### <a name="breaking-changes"></a>重大變更

* 需要 Azure AD 的權杖和子域，以及淘汰在舊版中使用的權杖。
* 將 CookiePolicy 設定為停用。 預設會停用使用者喜好設定的保留。 除非 CookiePolicy 設為 enabled，否則讀取器每次都會以預設設定啟動。

#### <a name="new-features"></a>新功能

* 新增支援以啟用或停用 cookie
* 新增 Android Kotlin 快速入門程式碼範例
* 新增 Android JAVA 快速入門程式碼範例
* 新增 Node.js 快速入門程式碼範例

#### <a name="improvements"></a>改善項目

* 更新 Node.js advanced README.md
* 將 Python 程式碼範例從 advanced 變更為快速入門
* 將 iOS Swift 程式碼範例移至 js/範例
* 更新程式碼範例以使用1.0.0 版

#### <a name="fixes"></a>修正

* 修正 Node.js advanced code 範例
* 新增 advanced-csharp 的遺失檔案-多重資源
* 從超連結移除 en-us

<br>

## <a name="version-003"></a>版本0.0。3

此版本包含新功能、程式碼範例的增強功能、安全性弱點修正，以及 bug 修正。

#### <a name="new-features"></a>新功能

* 新增 iOS Swift 程式碼範例
* 新增示範如何使用多個資源的 c # advanced code 範例 
* 新增支援以停用全螢幕切換功能
* 新增支援以隱藏沈浸式閱讀程式應用程式結束按鈕
* 新增主機應用程式在結束沈浸式閱讀程式時可使用的回呼函式
* 更新程式碼範例以使用 Azure Active Directory Authentication

#### <a name="improvements"></a>改善項目

* 更新 c # advanced code 範例以包含 Word 檔
* 更新程式碼範例以使用 v 0.0。3

#### <a name="fixes"></a>修正

* 將 lodash 所升級至版本4.17.14 以修正安全性弱點
* 更新 c # MSAL 程式庫以修正安全性弱點
* 升級 mixin-深入至版本1.3.2 以修正安全性弱點
* 升級 jest、webpack 和 webpack-使用易受攻擊版本的設定值和 mixin-深層來修正安全性弱點的 cli

<br>

## <a name="version-002"></a>版本0.0。2

此版本包含新功能、程式碼範例的增強功能、安全性弱點修正，以及 bug 修正。

#### <a name="new-features"></a>新功能

* 新增 Python advanced code 範例
* 新增 JAVA 快速入門程式碼範例
* 新增簡單程式碼範例

#### <a name="improvements"></a>改善項目

* 將 CoNtext.resourcename 重新命名為 cogSvcsSubdomain
* 將秘密移出程式碼並使用環境變數
* 更新程式碼範例以使用 v 0.0。2

#### <a name="fixes"></a>修正

* 修正沈浸式閱讀程式按鈕協助工具 bug
* 修正中斷的滾動
* 將 handlebars 封裝升級至版本4.1.2 以修正安全性弱點
* 修正 SDK 單元測試中的 bug
* 修正 JavaScript Internet Explorer 11 相容性錯誤
* 更新 SDK url

<br>

## <a name="version-001"></a>版本0.0。1

沈浸式閱讀程式 JavaScript SDK 的初始版本。

* 新增沈浸式閱讀程式 JavaScript SDK
* 新增支援以指定 UI 語言
* 新增 timeout 以判斷 launchAsync 函式何時應失敗，併發生逾時錯誤
* 新增支援以指定沈浸式閱讀程式 iframe 的 z-索引
* 新增支援以使用 web 程式標籤，而非 iframe，以與 Chrome 應用程式相容
* 新增 SDK 單元測試
* 新增 Node.js advanced code 範例
* 新增 c # advanced code 範例
* 新增 c # 快速入門程式碼範例
* 新增套件設定、Yarn 和其他組建檔案
* 新增 git 設定檔
* 將 README.md 檔案新增至程式碼範例和 SDK
* 新增 MIT 授權
* 新增參與者指示
* 新增靜態圖示按鈕 SVG 資產

## <a name="next-steps"></a>後續步驟

開始使用沈浸式閱讀程式：

* 請參閱[沈浸式閱讀程式用戶端程式庫參考](./reference.md)
* 探索 [GitHub 上的沈浸式閱讀程式用戶端程式庫](https://github.com/microsoft/immersive-reader-sdk)
