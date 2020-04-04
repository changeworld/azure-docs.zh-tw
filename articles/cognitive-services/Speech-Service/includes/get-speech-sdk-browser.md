---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 1f1512fbd766faed3922e4bdf8a47dba0de69864
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656568"
---
:::row:::
    :::column span="3":::
        JavaScript 語音 SDK 作為 npm 套件提供,請參閱<a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">微軟認知服務-<span class="docon docon-navigate-external x-hidden-focus"></span>語音-sdk,</a>它是配套的 GitHub 儲存庫<a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">認知<span class="docon docon-navigate-external x-hidden-focus"></span>服務-語音-sdk-js </a>。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> 儘管 JavaScript 語音 SDK 作為 npm 套件提供,因此用戶端 Web 瀏覽器和 Node.js 都可以使用它 - 考慮每個環境的各種體系結構含義。 例如,<a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">文件物件<span class="docon docon-navigate-external x-hidden-focus"></span>模型 (DOM)</a>不適用於伺服器端應用程式,就像<a href="https://nodejs.org/api/fs.html" target="_blank">檔案系統<span class="docon docon-navigate-external x-hidden-focus"></span></a>對用戶端應用程式不可用一樣。

### <a name="nodejs-package-manager-npm"></a>Node.js 套件管理員 (NPM)

要安裝 JavaScript 語音 SDK,請`npm install`執行以下命令。

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML 文稿標記

或者,您可以直接在`<script>``<head>`HTML 元素中包含一個標記,依賴於<a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank">**JSDelivr** NPM 辛蒂加<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

有關詳細資訊,請參閱 Web<a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">瀏覽器語音 SDK<span class="docon docon-navigate-external x-hidden-focus"></span>快速 入門</a>。
