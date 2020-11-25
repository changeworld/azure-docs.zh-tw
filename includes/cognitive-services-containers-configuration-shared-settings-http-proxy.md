---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: ce4cc68826b39b5707549afc799d2d214e8876c6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001141"
---
如果您需要設定 HTTP Proxy 以進行輸出要求，請使用以下兩個引數：

| 名稱 | 資料類型 | 描述 |
|--|--|--|
|HTTPS_PROXY|字串|要使用的 Proxy，例如 `https://proxy:8888`<br>`<proxy-url>`|
|HTTPS_PROXY_CREDS|字串|對 Proxy 進行驗證時所需的任何認證，例如 username:password。|
|`<proxy-user>`|字串|Proxy 的使用者。|
|`<proxy-password>`|字串|對於 Proxy 與 `<proxy-user>` 相關聯的密碼。|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTPS_PROXY=<proxy-url> \
HTTPS_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
