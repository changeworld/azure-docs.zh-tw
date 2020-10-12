---
title: 設定翻譯
titleSuffix: Azure Cognitive Services
description: 本文將示範如何設定各種不同的轉譯選項。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: ea066fab713100309103a040d309bac5b984fb99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85486960"
---
# <a name="how-to-configure-translation"></a>如何設定翻譯

本文示範如何在沈浸式閱讀程式中設定各種不同的轉譯選項。

## <a name="configure-translation-language"></a>設定翻譯語言

`options`參數包含可用於設定轉譯的所有旗標。 將 `language` 參數設定為您想要翻譯成的語言。 如需支援語言的完整清單，請參閱 [語言支援](./language-support.md) 。

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>在載入時自動轉譯檔

將設定 `autoEnableDocumentTranslation` 為 `true` ，可在沈浸式閱讀程式載入時，自動翻譯整份檔。

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>自動啟用文字轉譯

設定 `autoEnableWordTranslation` 為 `true` 以啟用單一文字轉譯。

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK 參考](./reference.md)