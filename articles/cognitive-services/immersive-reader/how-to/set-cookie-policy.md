---
title: 設定沉浸式讀取器 Cookie 原則
titleSuffix: Azure Cognitive Services
description: 本文將說明如何設定沉浸式讀取器的 cookie 原則。
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.custom: devx-track-javascript
ms.openlocfilehash: 6bc55416c335f370151e54d2b09ccf00bd799fdb
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406889"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>如何設定沉浸式讀取器的 cookie 原則

沉浸式讀取者預設會停用 cookie 使用方式。 如果您啟用 cookie 的使用方式，則沉浸式讀取者可能會使用 cookie 來維護使用者喜好設定並追蹤功能的使用方式。 如果您在沉浸式讀取器中啟用 cookie 使用方式，請考慮歐盟 Cookie 合規性政策的需求。 主機應用程式必須負責根據歐盟 Cookie 合規性原則來取得任何必要的使用者同意。

您可以透過沉浸式讀取器[選項](../reference.md#options)來設定 cookie 原則。

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