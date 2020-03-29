---
title: 連接到 Azure 媒體服務 v3 API - Node.js
description: 本文演示如何使用 Node.js 連接到媒體服務 v3 API。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 0381a2e2b8fd2a8b60e7cb702e0336a5678df057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896105"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>連接到媒體服務 v3 API - Node.js

本文介紹如何使用服務主體登錄方法連接到 Azure 媒體服務 v3 node.js SDK。

## <a name="prerequisites"></a>Prerequisites

- 安裝[Node.js](https://nodejs.org/en/download/).
- [創建媒體服務帳戶](create-account-cli-how-to.md)。 請務必記住資源組名稱和媒體服務帳戶名稱。

> [!IMPORTANT]
> 查看[命名約定](media-services-apis-overview.md#naming-conventions)。

## <a name="create-packagejson"></a>創建包.json

1. 使用您最喜愛的編輯器創建一個包.json 檔。
1. 打開檔並粘貼以下代碼：

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

應指定以下包：

|Package|描述|
|---|---|
|`azure-arm-mediaservices`|Azure 媒體服務 SDK。 <br/>要確保使用最新的 Azure 媒體服務包，請檢查[NPM 安裝 Azure 武裝媒體服務](https://www.npmjs.com/package/azure-arm-mediaservices/)。|
|`azure-storage`|存儲 SDK。 將檔上載到資產時使用。|
|`ms-rest-azure`| 用於登錄。|

您可以運行以下命令以確保使用最新的包：

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>連接到 Node.js 用戶端

1. 使用您最喜愛的編輯器創建 .js 檔。
1. 開啟該檔案，並貼上下列程式碼。
1. 將"端點配置"部分中的值設置為從[訪問 API](access-api-cli-how-to.md)獲得的值。

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>執行您的應用程式

開啟命令提示字元。 流覽到示例的目錄，並執行以下命令：

```
npm install 
node index.js
```

## <a name="see-also"></a>另請參閱

- [媒體服務概念](concepts-overview.md)
- [NPM 安裝 azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>後續步驟

探索媒體服務 [Node.js 參考](/javascript/api/overview/azure/mediaservices/management)文件，並查看[範例](https://github.com/Azure-Samples/media-services-v3-node-tutorials)示範如何搭配使用媒體服務 API 與 Node.js。

