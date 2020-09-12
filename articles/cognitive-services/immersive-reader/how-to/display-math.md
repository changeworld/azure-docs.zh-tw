---
title: 在沈浸式閱讀程式中顯示數學
titleSuffix: Azure Cognitive Services
description: 本文將示範如何在沈浸式閱讀程式中顯示數學。
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: nitinme
ms.custom: devx-track-javascript
ms.openlocfilehash: 2bc178dcd473154f65d0b74d20e9f608c4061f9f
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505313"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>如何在沈浸式閱讀程式中顯示數學

當以數學標記語言的形式提供時，沈浸式閱讀程式可以顯示數學標記語言 ([MathML](https://developer.mozilla.org/docs/Web/MathML)) 。
您可以透過沈浸式閱讀程式 [區塊](../reference.md#chunk)來設定 MIME 類型。 如需詳細資訊，請參閱 [支援的 MIME 類型](../reference.md#supported-mime-types) 。

## <a name="send-math-to-the-immersive-reader"></a>將數學傳送給沈浸式閱讀程式
為了傳送數學給沈浸式閱讀程式，請提供包含 MathML 的區塊，並將 MIME 類型設定為 ```application/mathml+xml``` ;

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

當您啟動沈浸式閱讀程式時，您應該會看到：

![沈浸式閱讀程式中的數學](../media/how-tos/1-math.png)

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](../reference.md)