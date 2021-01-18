---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: mikben
ms.openlocfilehash: 90bc9def9c1a78799f4931b58ad0a661602985e3
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013187"
---
## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

開啟您的終端機或命令視窗，為您的應用程式建立新的目錄，並瀏覽至該目錄。

```console
mkdir calling-quickstart && cd calling-quickstart
```

執行 `npm init -y` 以使用預設設定建立 **package.json** 檔案。

```console
npm init -y
```

### <a name="install-the-package"></a>安裝套件

使用 `npm install` 命令來安裝適用於 JavaScript 的 Azure 通訊服務通話用戶端程式庫。

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

本快速入門中建議使用下列版本的 Webpack：

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

`--save` 選項會在您的 **package.json** 檔案中，將程式庫列為相依性。

### <a name="set-up-the-app-framework"></a>設定應用程式架構

本快速入門會使用 webpack 來組合應用程式資產。 執行下列命令來安裝 webpack、webpack-cli 和 webpack-dev-server 套件，並將這些套件列為 **package.json** 中的開發相依性：

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

在專案的根目錄中建立 **index.html** 檔案。 我們將使用此檔案來設定基本配置，讓使用者能夠撥打電話。
