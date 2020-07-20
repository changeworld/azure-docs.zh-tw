---
title: 設定大聲讀出
titleSuffix: Azure Cognitive Services
description: 本文將說明如何設定大聲讀出的各種選項。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: 0705977c04afe742718a96aebbac31e78dc5815f
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486961"
---
# <a name="how-to-configure-read-aloud"></a>如何設定大聲讀出

本文示範如何在沉浸式讀取器中，設定大聲讀出的各種選項。

## <a name="automatically-start-read-aloud"></a>自動開始大聲讀出

`options`參數包含所有可用於設定大聲讀出的旗標。 將設定 `autoplay` 為 `true` ，可讓您在啟動沉浸式讀取器之後自動開始大聲朗讀。

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

將設定 `voice` 為 `male` 或 `female` 。 並非所有語言都支援這兩種語音。 如需詳細資訊，請參閱[語言支援](./language-support.md)頁面。

```typescript
const options = {
    readAloudOptions: {
        voice: 'female'
    }
};
```

## <a name="configure-playback-speed"></a>設定播放速度

設定 `speed` 為介於 `0.5` （50%）之間的數位和 `2.5` （250%）整數. 超出此範圍的值將會壓制為0.5 或2.5。

```typescript
const options = {
    readAloudOptions: {
        speed: 1.5
    }
};
```

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK 參考](./reference.md)