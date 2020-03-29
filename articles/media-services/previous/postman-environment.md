---
title: 匯入 Azure 媒體服務 REST 呼叫的 Postman 環境
description: 此文章提供適用於 Azure 媒體服務 REST 呼叫之 Postman 集合的定義。
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 8254d121c62a20de0a1593920b7793195f8eb50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926703"
---
# <a name="import-the-postman-environment"></a>匯入 Postman 環境  

本文包含用於 [Postman 集合](postman-collection.md)的 **Postman** 環境變數定義，其中包含呼叫媒體服務 REST API 的分組 HTTP 要求。 此環境與集合檔案適用於[設定 Postman 以進行媒體服務 REST API 呼叫](media-rest-apis-with-postman.md)教學課程。

> [!NOTE]
> `AzureADSTSEndpoint `  = 的值`https://login.microsoftonline.com/{{TenantId}}/oauth2/token`。 要獲取租戶 ID，可以將滑鼠懸停在門戶中的使用者名上（右上角），並且它將位於"目錄：微軟 （[TENANTID]]）中"。

```
{
  "id": "2dbce3ce-74c2-2ceb-0461-c4c2323f5b09",
  "name": "AzureMedia",
  "values": [
    {
      "enabled": true,
      "key": "TenantID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AzureADSTSEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "RESTAPIEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientSecret",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AccessToken",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAssetId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAccessPolicyId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "UploadURL",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "MediaFileName",
      "value": "BigBuckBunny.mp4",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastChannelId",
      "value": "",
      "type": "text"
    }
  ],
  "_postman_variable_scope": "environment",
  "_postman_exported_using": "Postman/5.5.0"
}
```
