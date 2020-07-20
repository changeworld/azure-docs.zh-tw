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
ms.openlocfilehash: da27f818bf368108568287f1ed1bbdae4c3902d4
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72560266"
---
## <a name="create-a-media-services-account"></a>建立媒體服務帳戶

您必須先建立媒體服務帳戶。 本節說明使用 Azure CLI 建立帳戶的必要條件。

### <a name="create-a-resource-group"></a>建立資源群組

使用下列命令建立資源群組。 Azure 資源群組是一個邏輯容器，在其中可部署及管理 Azure 媒體服務和相關聯的儲存體帳戶等資源。

您可以用您的值取代 `amsResourceGroup`。

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>建立儲存體帳戶

建立媒體服務帳戶時，您需要提供 Azure 儲存體帳戶資源的名稱。 指定的儲存體帳戶會附加到您的媒體服務帳戶。 如需如何在媒體服務中使用儲存體帳戶的詳細資訊，請參閱[儲存體帳戶](../articles/media-services/latest/storage-account-concept.md)。

您只能有一個**主要**儲存體帳戶，而與您的媒體服務帳戶相關聯的**次要**儲存體帳戶可以有任意數量。 媒體服務支援**一般用途 v2** (GPv2) 或**一般用途 v1** (GPv1) 帳戶。 僅有 Blob 的帳戶不允許作為**主要**帳戶。 如果您要深入了解儲存體帳戶，請參閱 [Azure 儲存體帳戶選項](../articles/storage/common/storage-account-options.md)。 

在此範例中，我們將建立一般用途 v2 的標準 LRS 帳戶。 如果您想要以儲存體帳戶進行試驗，請使用 `--sku Standard_LRS`。 不過，在選擇用於生產環境的 SKU 時應考慮使用 `--sku Standard_RAGRS`，以提供地理複寫功能而確保商務持續性。 如需詳細資訊，請參閱[儲存體帳戶](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest)。
 
以下命令會建立即將與媒體服務帳戶相關聯的儲存體帳戶。 在下列指令碼中，您可將 `storageaccountforams` 替換為您的值。 `amsResourceGroup` 必須符合您在上一個步驟中為資源群組提供的值。 儲存體帳戶名稱的長度必須小於24。

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>建立媒體服務帳戶

以下 Azure CLI 命令會建立新的媒體服務帳戶。 您可以取代下列值：`amsaccount`  `storageaccountforams` (必須符合您為儲存體帳戶提供的值)，以及 `amsResourceGroup` (必須符合您為資源群組提供的值)。

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```
