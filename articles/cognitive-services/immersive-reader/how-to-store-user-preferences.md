---
title: 儲存使用者喜好設定
titleSuffix: Azure Cognitive Services
description: 本文將說明如何儲存使用者的喜好設定。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: beb053551dc1fa28672c488b31dfb29ca3b53651
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486958"
---
# <a name="how-to-store-user-preferences"></a>如何儲存使用者喜好設定

本文示範如何儲存使用者的喜好設定。 這項功能是用來儲存使用者喜好設定的替代方法，這種情況不需要使用 cookie，也不可行。

## <a name="how-to-enable-storing-user-preferences"></a>如何啟用儲存使用者喜好設定

`options`參數包含 `onPreferencesChanged` 回呼。 每當使用者變更其喜好設定時，就會呼叫這個函式（例如，變更文字大小、主題色彩、字型等等）。 `value`參數包含字串，表示使用者目前的喜好設定。 這個字串可以使用您偏好的任何機制來儲存。

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>如何將使用者喜好設定載入沉浸式讀取器

使用參數將使用者的喜好設定傳遞給沉浸式讀取器 `preferences` 。 儲存和載入使用者喜好設定的簡單範例如下：

```typescript
let userPreferences = null;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
    }
};
```

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK 參考](./reference.md)