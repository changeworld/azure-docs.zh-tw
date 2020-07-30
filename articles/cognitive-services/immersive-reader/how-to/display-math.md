---
title: 在沉浸式閱讀程式中顯示數學
titleSuffix: Azure Cognitive Services
description: 本文將說明如何在沉浸式閱讀程式中顯示數學。
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: pasta
ms.custom: devx-track-javascript
ms.openlocfilehash: 6bde75fe0fb856633d7849173faf21db76edc950
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406906"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>如何在沉浸式閱讀程式中顯示數學

當以數學標記語言（[MathML](https://developer.mozilla.org/docs/Web/MathML)）的形式提供時，沉浸式讀取器可以顯示數學。
MIME 類型可透過沉浸式讀取器[區塊](../reference.md#chunk)來設定。 如需詳細資訊，請參閱[支援的 MIME 類型](../reference.md#supported-mime-types)。

## <a name="send-math-to-the-immersive-reader"></a>將數學傳送給沉浸式讀者
若要將數學傳送給沉浸式讀取器，請提供包含 MathML 的區塊，並將 MIME 類型設定為 ```application/mathml+xml``` 。

例如，如果您的內容如下所示：

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

然後，您可以使用下列 JavaScript 來顯示內容。

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

當您啟動沉浸式讀取器時，您應該會看到：

![沉浸式讀取器中的數學運算](../media/how-tos/1-math.png)

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](../reference.md)