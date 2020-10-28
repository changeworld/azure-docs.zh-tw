---
title: 設定大聲朗讀
titleSuffix: Azure Cognitive Services
description: 本文將說明如何設定各種選項來大聲讀出。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 648227521e5e4e8feecd864d3e572a4758e551ca
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92633258"
---
# <a name="how-to-configure-read-aloud"></a>如何設定大聲讀出

本文示範如何在沈浸式閱讀程式中設定各種大聲大聲的選項。

## <a name="automatically-start-read-aloud"></a>自動開始朗讀

`options`參數包含可用來大聲進行大聲設定的所有旗標。 將設定 `autoplay` 為 `true` ，可讓您在啟動沈浸式閱讀程式之後，自動開始朗讀。

```typescript
const options = {
    readAloudOptions: {
        autoplay: true
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

> [!NOTE]
> 由於瀏覽器的限制，Safari 不支援自動播放。

## <a name="configure-the-voice"></a>設定語音

設定 `voice` 為 `male` 或 `female` 。 並非所有語言都支援這兩種語音。 如需詳細資訊，請參閱 [語言支援](./language-support.md) 頁面。

```typescript
const options = {
    readAloudOptions: {
        voice: 'female'
    }
};
```

## <a name="configure-playback-speed"></a>設定播放速度

設定 `speed` 為介於 `0.5` (50% ) 和 `2.5` (250% ) （含）之間的數位。 此範圍外的值會壓制到0.5 或2.5。

```typescript
const options = {
    readAloudOptions: {
        speed: 1.5
    }
};
```

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK 參考](./reference.md)