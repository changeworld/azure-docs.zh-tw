---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5545b10a4228448d49849e7cd52728febf14ce2d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400038"
---
:::row:::
    :::column span="3":::
        JavaScript 語音 sdk 是以 npm 套件的形式提供，請參閱<a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft cognitiveservices-語音<span class="docon docon-navigate-external x-hidden-focus"></span> sdk</a>和其隨附的 GitHub 存放庫<a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">認知服務-Speech-SDK-js <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> 雖然 JavaScript 語音 SDK 是以 npm 套件的形式提供，因此用戶端網頁瀏覽器和 node.js 都可以使用它，請考慮每個環境的各種架構含意。 例如，檔<a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">物件模型（DOM <span class="docon docon-navigate-external x-hidden-focus"></span> ）</a>無法供伺服器端應用程式使用，因為用戶端應用程式無法使用<a href="https://nodejs.org/api/fs.html" target="_blank">檔案系統<span class="docon docon-navigate-external x-hidden-focus"></span> </a> 。

### <a name="nodejs-package-manager-npm"></a>Node.js 套件管理員（NPM）

若要安裝 JavaScript 語音 SDK，請執行下列`npm install`命令。

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML 腳本標記

或者，您可以直接在 HTMLs `<script>` `<head>`元素中包含一個標記，以依賴<a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM 聯合<span class="docon docon-navigate-external x-hidden-focus"></span> </a>。

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

如需詳細資訊，請參閱<a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">網頁瀏覽器語音<span class="docon docon-navigate-external x-hidden-focus"> </span>SDK 快速入門</a>。
