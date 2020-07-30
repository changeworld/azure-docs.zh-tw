---
title: 使用 HTML 內容啟動沈浸式閱讀程式
titleSuffix: Azure Cognitive Services
description: 本文將說明如何以 HTML 內容啟動沉浸式讀取器。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.custom: devx-track-javascript
ms.openlocfilehash: fbe54b3bd6e0fc8c42b4dfc7401be74ae65ce6cd
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406923"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>如何啟動具有 HTML 內容的沉浸式讀取器

本文示範如何以 HTML 內容啟動沉浸式讀取器。

## <a name="prepare-the-html-content"></a>準備 HTML 內容

將您想要呈現的內容放在容器元素內的沉浸式讀取器中。 請確定容器元素具有唯一的 `id` 。 沉浸式讀取器提供基本 HTML 專案的支援，如需詳細資訊，請參閱[參考](./reference.md#html-support)。

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>以 JavaScript 取得 HTML 內容

使用 `id` 容器元素的來取得 JavaScript 程式碼中的 HTML 內容。

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>使用您的 HTML 內容啟動沉浸式讀取器

呼叫時 `ImmersiveReader.launchAsync` ，將區塊的 `mimeType` 屬性設定為， `text/html` 以啟用轉譯 HTML。

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK 參考](./reference.md)