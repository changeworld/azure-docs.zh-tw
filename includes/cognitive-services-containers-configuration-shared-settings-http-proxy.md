---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 84cd8ed79281b005407b5a857398b5669635c072
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "68320514"
---
如果您需要設定 HTTP Proxy 以進行輸出要求，請使用以下兩個引數：

| Name | 資料類型 | 描述 |
|--|--|--|
|HTTP_PROXY|字串|要使用的 Proxy，例如 `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|字串|對 Proxy 進行驗證時所需的任何認證，例如 username:password。|
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
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
