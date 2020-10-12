---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 195caa6f8d7c00c741741fbf80a77bd7fe5579b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84464955"
---
下列 CLI 命令會拒絕所有傳送至儲存體帳戶公用端點的流量。 請注意，此命令會將 `-bypass` 參數設定為 `AzureServices`。 這會允許受信任的第一方服務 (例如 Azure 檔案同步) 透過公用端點存取儲存體帳戶。

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```