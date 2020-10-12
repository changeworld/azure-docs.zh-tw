---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 313c8ea9046deea953b4143f1a0264f81da38764
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88602549"
---
<!-- ### Create a storage account -->

建立媒體服務帳戶時，您需要提供 Azure 儲存體帳戶資源的名稱。 指定的儲存體帳戶會附加到您的媒體服務帳戶。 如需如何在媒體服務中使用儲存體帳戶的詳細資訊，請參閱[儲存體帳戶](../storage-account-concept.md)。

您只能有一個**主要**儲存體帳戶，而與您的媒體服務帳戶相關聯的**次要**儲存體帳戶可以有任意數量。 媒體服務支援**一般用途 v2** (GPv2) 或**一般用途 v1** (GPv1) 帳戶。 僅有 Blob 的帳戶不允許作為**主要**帳戶。 如果您要深入了解儲存體帳戶，請參閱 [Azure 儲存體帳戶選項](../../../storage/common/storage-account-overview.md)。 

在此範例中，我們將建立一般用途 v2 的標準 LRS 帳戶。 如果您想要以儲存體帳戶進行試驗，請使用 `--sku Standard_LRS`。 不過，在選擇用於生產環境的 SKU 時應考慮使用 `--sku Standard_RAGRS`，以提供地理複寫功能而確保商務持續性。 如需詳細資訊，請參閱[儲存體帳戶](/cli/azure/storage/account?view=azure-cli-latest)。

以下命令會建立即將與媒體服務帳戶相關聯的儲存體帳戶。 在下列指令碼中，您可將 `storageaccountforams` 替換為您的值。 `amsResourceGroup` 必須符合您在上一個步驟中提供給資源群組的值。 儲存體帳戶名稱的長度必須小於24。

```azurecli
az storage account create --name storageaccountforams --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```
