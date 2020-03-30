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
ms.openlocfilehash: 3c18efa7eb520b765c9bb3c2aff00104f971f5a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174217"
---
### <a name="create-a-nodejs-application"></a>建立 Node.js 應用程式

如果您已在建立轉送時停用 [Requires Client Authorization] \(需要用戶端授權\) 選項，則可以使用任何瀏覽器將要求傳送至混合式連線 URL。 若要存取受保護的端點，您需要建立權杖，並在 `ServiceBusAuthorization` 標頭中傳遞權杖，如下所示。

若要開始，請建立稱為 `sender.js` 的新 JavaScript 檔案。

### <a name="add-the-relay-npm-package"></a>新增轉送 NPM 套件

在您的專案資料夾中從 Node 命令提示字元執行 `npm install hyco-https`。 此套件也會匯入一般 `https` 套件。 用戶端的主要差異在於套件提供函式來建構轉送 URI 和權杖。

### <a name="write-some-code-to-send-messages"></a>撰寫一些程式碼來傳送訊息

1. 將下列 `constants` 新增至 `sender.js` 檔案開頭處。
   
    ```js
    const https = require('hyco-https');
    ```

2. 將下列常數新增至 `sender.js` 檔案，以取得混合式連線詳細資料。 將方括號中的預留位置取代為您在建立混合式連線時所取得的值。
   
   1. `const ns` - 轉送命名空間。 務必使用完整命名空間名稱；例如，`{namespace}.servicebus.windows.net`。
   2. `const path` - 混合式連線的名稱。
   3. `const keyrule` - SAS 金鑰的名稱。
   4. `const key` - SAS 金鑰值。

3. 將下列程式碼新增至 `sender.js` 檔案。 您會注意到，程式碼與 Node.js HTTPS 用戶端的一般使用沒有明顯的差異；它只是新增了授權標頭。
   
    ```js
   https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```
    sender.js 檔案看起來應該會像下面這樣：
   
    ```js
    const https = require('hyco-https');
       
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```

