---
title: 使用 HTML 內容啟動沈浸式閱讀程式
titleSuffix: Azure Cognitive Services
description: 本文將說明如何使用 HTML 內容啟動沈浸式閱讀程式。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.custom: devx-track-js
ms.openlocfilehash: f7f55d1d349fc7d02c2ac53f7a50f9d649688cbd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326757"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>如何使用 HTML 內容啟動沈浸式閱讀程式

本文示範如何使用 HTML 內容啟動沈浸式閱讀程式。

## <a name="prepare-the-html-content"></a>準備 HTML 內容

將您想要轉譯的內容放在容器元素內的沈浸式閱讀程式。 請確定容器元素具有唯一的 `id` 。 沈浸式閱讀程式提供基本 HTML 元素的支援，請參閱 [參考](./reference.md#html-support) 以取得詳細資訊。

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

## <a name="get-the-html-content-in-javascript"></a>取得 JavaScript 中的 HTML 內容

使用 `id` 容器元素的來取得 JavaScript 程式碼中的 HTML 內容。

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>使用您的 HTML 內容啟動沈浸式閱讀程式

當您呼叫時 `ImmersiveReader.launchAsync` ，請將區塊的 `mimeType` 屬性設定為， `text/html` 以啟用呈現 HTML。

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