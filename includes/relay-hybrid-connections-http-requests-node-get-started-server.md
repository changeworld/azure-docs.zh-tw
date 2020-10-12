---
title: 包含檔案
description: 包含檔案
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: fdd4e26839661ab9765b7d496e7f60c3686ba637
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174216"
---
### <a name="create-a-nodejs-application"></a>建立 Node.js 應用程式

新增名為 `listener.js` 的 JavaScript 檔案。

### <a name="add-the-relay-npm-package"></a>新增轉送 NPM 套件

在您的專案資料夾中從 Node 命令提示字元執行 `npm install hyco-https`。

### <a name="write-some-code-to-handle-requests"></a>撰寫某個程式碼來處理要求

1. 在 `listener.js` 檔案的開頭處，新增下列常數。

    ```js
    const https = require('hyco-https');
    ```
2. 將下列常數新增至 `listener.js` 檔案，以取得混合式連線詳細資料。 將方括號中的預留位置取代為您在建立混合式連線時所取得的值。

   1. `const ns` - 轉送命名空間。 務必使用完整命名空間名稱；例如，`{namespace}.servicebus.windows.net`。
   2. `const path` - 混合式連線的名稱。
   3. `const keyrule` - SAS 金鑰的名稱。
   4. `const key` - SAS 金鑰值。

3. 將下列程式碼新增至 `listener.js` 檔案。 :

    您會注意到，程式碼與在 Node.js 初級教學課程中找到的任何簡單 HTTP 伺服器範例沒有多大的差異，差異在於使用 `createRelayedServer`，而非一般 `createServer` 函式。

    ```js
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```
    listener.js 檔案看起來應該會像下面這樣：
   
    ```js
    const https = require('hyco-https');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```

