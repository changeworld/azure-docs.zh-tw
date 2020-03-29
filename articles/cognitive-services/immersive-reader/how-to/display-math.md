---
title: 在沉浸式閱讀器中顯示數學
titleSuffix: Azure Cognitive Services
description: 本文將向您展示如何在沉浸式閱讀器中顯示數學。
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: e01cc9e8cedb6c38da0b56e04419c706d5d0566e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946284"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>如何在沉浸式閱讀器中顯示數學

沉浸式讀取器可以顯示數學，當提供數學標記語言 （[MathML](https://developer.mozilla.org/docs/Web/MathML)）.
MIME 類型可以通過沉浸式讀取器[塊](../reference.md#chunk)進行設置。 有關詳細資訊[，請參閱支援的 MIME 類型](../reference.md#supported-mime-types)。

## <a name="send-math-to-the-immersive-reader"></a>將數學發送到沉浸式閱讀器
為了將數學發送到沉浸式讀取器，請提供包含 MathML 的塊，並將 MIME```application/mathml+xml```類型設置為 ;

例如，如果您的內容如下：

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

然後，您可以使用以下 JavaScript 顯示內容。

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

當您啟動沉浸式閱讀器時，您應該看到：

![沉浸式閱讀器中的數學](../media/how-tos/1-math.png)

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](../reference.md)