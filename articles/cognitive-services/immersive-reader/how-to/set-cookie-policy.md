---
title: 設置沉浸式閱讀器 Cookie 策略
titleSuffix: Azure Cognitive Services
description: 本文將向您展示如何為沉浸式閱讀器設置 Cookie 策略。
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946282"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>如何為沉浸式閱讀器設置 Cookie 策略

預設情況下，沉浸式閱讀器將禁用 Cookie 使用。 如果您啟用 Cookie 使用，則沉浸式閱讀器可能會使用 Cookie 來維護使用者首選項並跟蹤功能使用方式。 如果您在沉浸式閱讀器中啟用 Cookie 使用，請考慮歐盟 Cookie 合規政策的要求。 根據歐盟 Cookie 合規政策，主機應用程式有責任獲得任何必要的使用者同意。

Cookie 策略可以通過沉浸式閱讀器[選項](../reference.md#options)進行設置。 有關詳細資訊[，請參閱 CookiePolicy 枚舉](../reference.md#cookiepolicy-enum)。

## <a name="enable-cookie-usage"></a>啟用 Cookie 使用

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>禁用 Cookie 使用

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>後續步驟

* 檢視 [Node.js 快速入門](../quickstart-nodejs.md)，以查看您還可以使用 Node.js 透過沈浸式閱讀程式 SDK 執行哪些作業
* 檢視 [Python 教學課程](../tutorial-python.md)，以查看您還可以使用 Python 透過沈浸式閱讀程式 SDK 執行哪些作業
* 查看[Swift 教程](../tutorial-ios-picture-immersive-reader.md)，瞭解使用 Swift 的沉浸式讀取器 SDK 還可以執行哪些操作
* 探索[沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](../reference.md)