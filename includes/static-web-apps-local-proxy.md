---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 879d0c2e8c4cd66ce04c0298d00f7d6a1987acf5
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594397"
---
#### <a name="local-proxy"></a>本機 Proxy

您可以設定 Live Server Visual Studio Code 擴充功能的 Proxy，將 `/api` 的所有要求路由至 `http://127.0.0.1:7071/api` 的執行中 API 端點。

1. 開啟 _.vscode/settings.json_ 檔案。

1. 新增下列設定以指定 Live Server 的 Proxy。

   ```json
   "liveServer.settings.proxy": {
      "enable": true,
      "baseUri": "/api",
      "proxyUri": "http://127.0.0.1:7071/api"
   }
   ```

   此組態最好儲存在專案設定檔中，而不是使用者設定檔中。

   使用專案設定可確保 Proxy 不會套用至 Visual Studio Code 中開啟的所有其他專案。
