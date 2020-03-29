---
title: 使用 HTML 內容啟動沈浸式閱讀程式
titleSuffix: Azure Cognitive Services
description: 本文將向您展示如何啟動包含 HTML 內容的沉浸式閱讀器。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: bc7ab46113e1b819fc71a9f6e8a18400f8acfbef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946306"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>如何啟動包含 HTML 內容的沉浸式閱讀器

本文演示如何啟動包含 HTML 內容的沉浸式閱讀器。

## <a name="prepare-the-html-content"></a>準備 HTML 內容

將要呈現的內容放在容器元素的沉浸式讀取器中。 確保容器元素具有唯`id`一的 。 沉浸式閱讀器支援基本 HTML 元素，有關詳細資訊，請參閱[參考](./reference.md#html-support)。

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

## <a name="get-the-html-content-in-javascript"></a>獲取 JavaScript 中的 HTML 內容

`id`使用 容器元素獲取 JavaScript 代碼中的 HTML 內容。

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>使用 HTML 內容啟動沉浸式閱讀器

調用`ImmersiveReader.launchAsync`時，將塊的屬性`mimeType``text/html`設置為啟用呈現 HTML。

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