---
title: 包含檔案
description: 包含檔案
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 9d8de8826fc3dfcc2360eb3b6c82b3ff8d65d845
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "79461226"
---
### <a name="access-the-media-services-api"></a>存取媒體服務 API

若要連線到 Azure Media Services API，您可使用 Azure AD 服務主體驗證。 下列命令會建立 Azure AD 應用程式，並將服務主體連結到帳戶。 您應使用傳回值來設定您的應用程式。

執行指令碼之前，您應以建立資源時所選擇的名稱取代 `amsaccount` 和 `amsResourceGroup`。 `amsaccount` 是連結服務主體的 Azure 媒體服務名稱。

如果您有多個訂用帳戶的存取權，請先將使用中的訂用帳戶設定為其中已建立媒體服務帳戶的訂用帳戶。

```azurecli
az account set --subscription subscriptionId
```

此命令會傳回 `json` 輸出︰

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

此命令會產生如下的回應：

```json
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "00000000-0000-0000-0000-000000000000",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": "amsaccount",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "West US 2",
  "ResourceGroup": "amsResourceGroup",
  "SubscriptionId": "00000000-0000-0000-0000-000000000000"
}
```

如果您想要在回應中取得 `xml`，請使用下列命令：

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
