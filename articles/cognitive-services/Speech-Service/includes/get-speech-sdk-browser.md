---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-javascript
ms.openlocfilehash: ab5a106d077713c1332bfb1ba4608d9410086c9f
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186346"
---
:::row:::
    :::column span="3":::
        適用于 JavaScript 的語音 SDK 是以 npm 套件的形式提供，請參閱<a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft cognitiveservices-語音 <span class="docon docon-navigate-external x-hidden-focus"></span> sdk</a>及其隨附的 GitHub 存放庫<a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">認知服務-speech-sdk- <span class="docon docon-navigate-external x-hidden-focus"></span> js </a>。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> 雖然適用于 JavaScript 的語音 SDK 是以 npm 套件的形式提供，因此用戶端網頁瀏覽器和 Node.js 都可以使用它，請考慮每個環境的各種架構含意。 例如，檔<a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">物件模型 (DOM <span class="docon docon-navigate-external x-hidden-focus"></span>) </a>無法供伺服器端應用程式使用，因為用戶端應用程式無法使用<a href="https://nodejs.org/api/fs.html" target="_blank">檔案系統 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 。

### <a name="nodejs-package-manager-npm"></a>Node.js 套件管理員 (NPM) 

若要安裝適用于 JavaScript 的語音 SDK，請執行下列 `npm install` 命令。

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML 腳本標記

或者，您可以直接在 `<script>` HTMLs 元素中包含一個標記 `<head>` ，以依賴<a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM 聯合 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/browser/microsoft.cognitiveservices.speech.sdk.bundle-min.js">
</script>
```

如需詳細資訊，請參閱<a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">網頁瀏覽器語音 <span class="docon docon-navigate-external x-hidden-focus"></span> SDK 快速入門</a>。
