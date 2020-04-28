---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 531866ece1c4acacb926c9e9c6598158c1aa077f
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2020
ms.locfileid: "67174569"
---
### <a name="create-a-nodejs-application"></a>建立 Node.js 應用程式

新增名為 `listener.js` 的 JavaScript 檔案。

### <a name="add-the-relay-npm-package"></a>新增轉送 NPM 套件

在您的專案資料夾中從 Node 命令提示字元執行 `npm install hyco-ws`。

### <a name="write-some-code-to-receive-messages"></a>撰寫一些程式碼來接收訊息

1. 在 `listener.js` 檔案的開頭處，新增下列常數。
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. 將下列常數新增至 `listener.js` 檔案，以取得混合式連線詳細資料。 將方括號中的預留位置取代為您在建立混合式連線時所取得的值。
   
   1. `const ns` - 轉送命名空間。 務必使用完整命名空間名稱；例如，`{namespace}.servicebus.windows.net`。
   2. `const path` - 混合式連線的名稱。
   3. `const keyrule` - SAS 金鑰的名稱。
   4. `const key` - SAS 金鑰值。

3. 將下列程式碼新增至 `listener.js` 檔案：
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    listener.js 檔案看起來應該會像下面這樣：
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```

