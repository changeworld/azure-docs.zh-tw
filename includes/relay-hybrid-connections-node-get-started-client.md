---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 9d4f7faa18ee7fae158afb42b8c42287e61dd103
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67174566"
---
### <a name="create-a-nodejs-application"></a>建立 Node.js 應用程式

新增名為 `sender.js` 的 JavaScript 檔案。

### <a name="add-the-relay-npm-package"></a>新增轉送 NPM 套件

在您的專案資料夾中從 Node 命令提示字元執行 `npm install hyco-ws`。

### <a name="write-some-code-to-send-messages"></a>撰寫一些程式碼來傳送訊息

1. 將下列 `constants` 新增至 `sender.js` 檔案開頭處。
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
    ```
2. 將下列常數新增至 `sender.js` 檔案，以取得混合式連線詳細資料。 將方括號中的預留位置取代為您在建立混合式連線時所取得的值。
   
   1. `const ns` - 轉送命名空間。 務必使用完整命名空間名稱；例如，`{namespace}.servicebus.windows.net`。
   2. `const path` - 混合式連線的名稱。
   3. `const keyrule` - SAS 金鑰的名稱。
   4. `const key` - SAS 金鑰值。

3. 將下列程式碼新增至 `sender.js` 檔案：
   
    ```js
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```
    sender.js 檔案看起來應該會像下面這樣：
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```

