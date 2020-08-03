---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-javascript
ms.openlocfilehash: 0c48116c285ee5b9f0de0d9333a49d5f8ac183af
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87425004"
---
:::row:::
    :::column span="3":::
        適用于 JavaScript 的語音 SDK 是以 npm 套件的形式提供，請參閱<a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft cognitiveservices-語音 <span class="docon docon-navigate-external x-hidden-focus"></span> sdk</a>和其隨附的 GitHub 存放庫<a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">認知服務-speech-SDK- <span class="docon docon-navigate-external x-hidden-focus"></span> js </a>。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> 雖然適用于 JavaScript 的語音 SDK 是以 npm 套件的形式提供，因此 Node.js 和用戶端網頁瀏覽器都可以使用它，請考慮每個環境的各種架構含意。 例如，檔<a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">物件模型（DOM <span class="docon docon-navigate-external x-hidden-focus"></span> ）</a>無法供伺服器端應用程式使用，因為用戶端應用程式無法使用<a href="https://nodejs.org/api/fs.html" target="_blank">檔案系統 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 。

### <a name="nodejs-package-manager-npm"></a>Node.js 套件管理員（NPM）

若要安裝適用于 JavaScript 的語音 SDK，請執行下列 `npm install` 命令。

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

如需詳細資訊，請參閱<a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">Node.js 語音 SDK <span class="docon docon-navigate-external x-hidden-focus"></span> 快速入門</a>。
