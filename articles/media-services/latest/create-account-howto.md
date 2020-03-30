---
title: 建立 Azure 媒體服務帳戶
description: 本教程將指導您完成創建 Azure 媒體服務帳戶的步驟。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 2f1694825319ed8b8682c044e7e2282ed4c43dcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478795"
---
# <a name="create-a-media-services-account"></a>建立媒體服務帳戶

若要在 Azure 中開始加密、編碼、分析、管理和串流處理媒體內容，您需要建立 Media Services 帳戶。 媒體服務帳戶必須與一或多個儲存體帳戶產生關聯。

> [!NOTE]
> 媒體服務帳戶和所有相關聯的儲存體帳戶必須位於相同的 Azure 訂用帳戶中。 強烈建議使用與媒體服務帳戶位於相同位置的儲存體帳戶，以避免產生額外的延遲和資料輸出費用。

本文介紹創建新 Azure 媒體服務帳戶的步驟。 從以下選項卡中進行選擇。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Azure 門戶提供了一種快速建立 Azure 媒體服務帳戶的方法。 您可以使用自己的帳戶，來存取讓您在 Azure 中儲存、加密、編碼、管理和串流播放媒體內容的媒體服務。

目前，可以使用 Azure[門戶](https://portal.azure.com/)：

* 管理媒體服務 v3[即時事件](live-events-outputs-concept.md)， 
* 視圖（未管理） v3[資產](assets-concept.md)， 
* [獲取有關訪問 API 的資訊](access-api-portal.md)。 

對於所有其他管理工作（例如，[轉換、作業](transforms-jobs-concept.md)和[內容保護](content-protection-overview.md)），請使用[REST API](https://aka.ms/ams-v3-rest-ref) [、CLI](https://aka.ms/ams-v3-cli-ref)或受支援的[SDK](media-services-apis-overview.md#sdks)之一。

本文說明如何使用 Azure 入口網站建立媒體服務帳戶。

### <a name="create-a-media-services-account"></a>建立媒體服務帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 按一下 **"創建資源** > **媒體** > **媒體服務**"。
1. 在"**創建媒體服務帳戶"** 部分中輸入所需值。
    
    | 名稱 | 描述 |
    | ---|---|
    |**帳戶名稱**|輸入新的媒體服務帳戶的名稱。 媒體服務帳戶名稱為全部小寫且不含空格的字母或數字，且長度是 3 到 24 個字元。|
    |**訂閱**|如果您有多個訂閱，請從有權訪問的 Azure 訂閱清單中選擇一個。|
    |**資源組**|選擇新資源或現有資源。 資源群組是共用生命週期、權限及原則的資源集合。 [在此處](../../azure-resource-manager/management/overview.md#resource-groups)瞭解更多資訊。|
    |**位置**|選擇將用於存儲媒體服務帳戶的媒體和元資料記錄的地理區域。 此區域將用於處理和串流媒體。 只有可用的媒體服務區域才會出現在下拉式清單方塊中。 |
    |**存儲帳戶**|選擇存儲帳戶以提供媒體服務帳戶中的媒體內容的 blob 存儲。 您可以選取與媒體服務帳戶相同地理區域中的現有儲存體帳戶，也可以建立新的儲存體帳戶。 新的儲存體帳戶會建立於相同的區域中。 儲存體帳戶名稱的規則會與媒體服務帳戶相同。<br/><br/>您必須有一個**主**存儲帳戶，並且可以具有與媒體服務帳戶關聯的任意數量的**輔助**存儲帳戶。 可以使用 Azure 門戶添加輔助存儲帳戶。 有關詳細資訊，請參閱[Azure 媒體服務帳戶的 Azure 存儲帳戶](storage-account-concept.md)。<br/><br/>媒體服務帳戶和所有相關聯的儲存體帳戶必須位於相同的 Azure 訂用帳戶中。 強烈建議使用與媒體服務帳戶位於相同位置的儲存體帳戶，以避免產生額外的延遲和資料輸出費用。|
    
1. 選取 **[釘選到儀表板]** 以查看帳戶部署的進度。
1. 按一下表單底部的 [建立] **** 。

    創建媒體服務帳戶時，**預設**流式處理終結點將添加到處于 **"已停止"** 狀態的帳戶。 要開始資料流內容並利用[動態打包](dynamic-packaging-overview.md)和[動態加密](content-protection-overview.md)，要從中資料流內容的流式處理終結點必須處於 **"正在運行"** 狀態。 

## <a name="use-the-azure-cli"></a>使用 Azure CLI

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="set-the-azure-subscription"></a>設定 Azure 訂用帳戶

在下列命令中，提供您要用於媒體服務帳戶的 Azure 訂用帳戶識別碼。 您可以瀏覽至[訂閱](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，以查看可以存取的訂用帳戶。

```azurecli
az account set --subscription mySubscriptionId
```

### <a name="create-a-resource-group"></a>建立資源群組

使用下列命令建立資源群組。 Azure 資源群組是一個邏輯容器，在其中可部署及管理 Azure 媒體服務和相關聯的儲存體帳戶等資源。

您可以用你的`amsResourceGroup`價值來代替你的價值。

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>建立儲存體帳戶

建立媒體服務帳戶時，您需要提供 Azure 儲存體帳戶資源的名稱。 指定的儲存體帳戶會附加到您的媒體服務帳戶。 如需如何在媒體服務中使用儲存體帳戶的詳細資訊，請參閱[儲存體帳戶](storage-account-concept.md)。

您只能有一個**主要**儲存體帳戶，而與您的媒體服務帳戶相關聯的**次要**儲存體帳戶可以有任意數量。 媒體服務支援**一般用途 v2** (GPv2) 或**一般用途 v1** (GPv1) 帳戶。 僅有 Blob 的帳戶不允許作為**主要**帳戶。 如果您要深入了解儲存體帳戶，請參閱 [Azure 儲存體帳戶選項](../../storage/common/storage-account-options.md)。 

在此範例中，我們將建立一般用途 v2 的標準 LRS 帳戶。 如果您想要以儲存體帳戶進行試驗，請使用 `--sku Standard_LRS`。 不過，在選擇用於生產環境的 SKU 時應考慮使用 `--sku Standard_RAGRS`，以提供地理複寫功能而確保商務持續性。 如需詳細資訊，請參閱[儲存體帳戶](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest)。
 
以下命令會建立即將與媒體服務帳戶相關聯的儲存體帳戶。 在下列指令碼中，您可將 `storageaccountforams` 替換為您的值。 `amsResourceGroup`必須匹配在上一步驟中為資源組提供的值。 存儲帳戶名稱的長度必須小於 24。

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>建立媒體服務帳戶

以下 Azure CLI 命令會建立新的媒體服務帳戶。 您可以替換以下值：（`amsaccount`  `storageaccountforams`必須匹配您為存儲帳戶提供的值）和`amsResourceGroup`（必須匹配為資源組提供的值）。

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```

### <a name="see-also"></a>另請參閱

* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [將輔助存儲附加到媒體服務帳戶](https://docs.microsoft.com/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>後續步驟

[串流處理檔案](stream-files-dotnet-quickstart.md)
