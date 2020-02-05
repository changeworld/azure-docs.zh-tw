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
ms.openlocfilehash: 58e9156091702718dccd75eb4a57e5b6d8c1f073
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896316"
---
## <a name="access-the-media-services-api"></a>存取媒體服務 API

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
