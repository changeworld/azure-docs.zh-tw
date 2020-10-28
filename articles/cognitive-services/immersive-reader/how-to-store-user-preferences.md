---
title: 儲存使用者喜好設定
titleSuffix: Azure Cognitive Services
description: 本文將示範如何儲存使用者的喜好設定。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 31c1ef8d75b4c12e4dd6a360852feb27857ac412
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636539"
---
# <a name="how-to-store-user-preferences"></a>如何儲存使用者喜好設定

本文示範如何透過 [-喜好](./reference.md#options)設定和 [-onPreferencesChanged](./reference.md#options)沈浸式閱讀程式 SDK 選項，儲存使用者的 UI 設定，正式稱為 **使用者喜好** 設定。

當 [CookiePolicy](./reference.md#cookiepolicy-options) SDK 選項設定為 [ *已啟用* ] 時，沈浸式閱讀程式的應用程式會將 **使用者喜好** 設定儲存 (文字大小、主題色彩、字型等等，在 cookie 中) （特定瀏覽器和裝置的本機）。 每次使用者在相同的瀏覽器和裝置上啟動沈浸式閱讀程式時，將會以使用者在該裝置上的最後一個會話中的喜好設定來開啟。 不過，如果使用者在不同的瀏覽器或裝置上開啟沈浸式閱讀程式，設定一開始會以沈浸式閱讀程式的預設設定來設定，而使用者必須再次設定其喜好設定，並針對他們使用的每個裝置設定其喜好設定。 `-preferences`和 `-onPreferencesChanged` 沈浸式閱讀程式 SDK 選項提供一個方法，讓應用程式在各種瀏覽器和裝置上漫遊使用者的喜好設定，讓使用者在使用應用程式的任何地方都具有一致的體驗。

首先，藉由在 `-onPreferencesChanged` 啟動沈浸式閱讀程式應用程式時提供回呼 SDK 選項，沈浸式閱讀程式會在 `-preferences` 每次使用者于沈浸式閱讀程式會話期間變更其喜好設定時，將字串傳回主機應用程式。 然後，主機應用程式會負責在自己的系統中儲存使用者喜好設定。 然後，當相同的使用者再次啟動沈浸式閱讀程式時，主應用程式可以從儲存體取出該使用者的喜好設定，並 `-preferences` 在啟動沈浸式閱讀程式應用程式時，提供它們作為字串 SDK 選項，以便還原使用者的喜好設定。

這項功能可做為在使用 cookie 不需要或可行的情況下儲存 **使用者喜好** 設定的替代方法。

> [!CAUTION]
> **重要** 請勿嘗試以程式設計方式變更在 `-preferences` 沈浸式閱讀程式應用程式中傳送的字串值，因為這可能會導致非預期的行為，而導致客戶的使用者體驗變差。 主機應用程式絕對不應指派自訂值給或操作 `-preferences` 字串。 使用 `-preferences` string 選項時，只使用回呼選項所傳回的確切值 `-onPreferencesChanged` 。

## <a name="how-to-enable-storing-user-preferences"></a>如何啟用儲存使用者喜好設定

沈浸式閱讀程式 SDK [launchAsync](./reference.md#launchasync) `options` 參數包含 `-onPreferencesChanged` 回呼。 每當使用者變更其喜好設定時，就會呼叫此函式。 `value`參數包含字串，表示使用者目前的喜好設定。 然後，主機應用程式會為該使用者儲存這個字串。

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>如何將使用者喜好設定載入沈浸式閱讀程式

使用選項，將使用者的喜好設定傳遞至沈浸式閱讀程式 `-preferences` 。 儲存和載入使用者喜好設定的簡單範例如下：

```typescript
const storedUserPreferences = localStorage.getItem("USER_PREFERENCES");
let userPreferences = storedUserPreferences === null ? null : storedUserPreferences;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
        localStorage.setItem("USER_PREFERENCES", userPreferences);
    }
};
```

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK 參考](./reference.md)