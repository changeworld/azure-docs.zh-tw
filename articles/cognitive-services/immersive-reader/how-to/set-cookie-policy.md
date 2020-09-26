---
title: 設定沈浸式閱讀程式 Cookie 原則
titleSuffix: Azure Cognitive Services
description: 本文將說明如何設定沈浸式閱讀程式的 cookie 原則。
services: cognitive-services
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 02901e6b4a75257b2cda8dee84dbe3438dc15c18
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332367"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>如何設定沈浸式閱讀程式的 cookie 原則

根據預設，沈浸式閱讀程式會停用 cookie 的使用方式。 如果您啟用 cookie 使用方式，則沈浸式閱讀程式可能會使用 cookie 來維護使用者喜好設定，並追蹤功能的使用方式。 如果您在沈浸式閱讀程式中啟用 cookie 使用方式，請考慮採用 EU Cookie 合規性原則的需求。 主機應用程式會負責根據歐盟 Cookie 的合規性政策取得任何必要的使用者同意。

您可以透過沈浸式閱讀程式 [選項](../reference.md#options)設定 cookie 原則。

## <a name="enable-cookie-usage"></a>啟用 Cookie 使用方式

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>停用 Cookie 使用方式

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>後續步驟

* 檢視 [Node.js 快速入門](../quickstarts/client-libraries.md?pivots=programming-language-nodejs)，以查看您還可以使用 Node.js 透過沈浸式閱讀程式 SDK 執行哪些作業
* 檢視 [Android 教學課程](../tutorial-android.md)，以查看您還可以使用適用於 Android 的 Java 或 Kotlin 透過沈浸式閱讀程式 SDK 執行哪些作業
* 檢視 [iOS 教學課程](../tutorial-ios.md)，以查看您還可以使用適用於 iOS 的 Swift 透過沈浸式閱讀程式 SDK 執行哪些作業
* 檢視 [Python 教學課程](../tutorial-python.md)，以查看您還可以使用 Python 透過沈浸式閱讀程式 SDK 執行哪些作業
* 探索[沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](../reference.md)