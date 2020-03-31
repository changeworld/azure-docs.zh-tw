---
title: Azure 傳統部署模型
description: 經典部署模型現在被資源管理器模型取代，它為 VM 和虛擬機器規模集強制實施全域 vCPU 配額限制。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: a3d5106cafc1d3bfe77f3e42e85cedb668fc4fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76835544"
---
# <a name="classic-deployment-model"></a>傳統部署模型

經典部署模型是較老一代 Azure 部署模型。 它對虛擬機器和虛擬機器規模集強制實施全域 vCPU 配額限制。 不再推薦經典部署模型，現在被資源管理器模型取代。

要瞭解有關這兩種部署模型和使用資源管理器的優勢的更多資訊，請參閱[資源管理器和經典部署](../../azure-resource-manager/management/deployment-models.md)。

創建新訂閱時，將為其分配預設的 vCPU 配額。 每當使用經典部署模型部署新虛擬機器時，所有區域的新 vCPU 使用方式的總和不得超過為經典部署模型批准的 vCPU 配額。

要瞭解有關配額的更多，請參閱[Azure 訂閱和服務限制、配額和約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

您可以請求提高經典部署模型的 vCPU 配額限制。 在 Azure 門戶中使用**説明 + 支援**或使用方式 +**配額**。

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>使用説明和支援，在訂閱級別請求每個 VM 系列 vCPU 配額增加

按照以下說明，在 Azure 門戶中使用説明 **+ 支援**來創建支援請求。

1. 在[Azure 門戶](https://portal.azure.com)功能表中，選擇 **"説明 + 支援**"。

   ![在 Azure 門戶中選擇説明和支援](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 選取 [新增支援要求]****。

   ![在 Azure 門戶中創建新的支援請求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 在 **"問題"類型**中，選擇**服務和訂閱限制（配額）。**

   ![選擇配額作為問題類型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 選擇要增加其配額的訂閱。

   ![選擇要增加配額的訂閱](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 對於**配額類型**，選擇**計算 VM（核心-vCPU）訂閱限制增加**。

   ![選擇要增加的配額類型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 選擇 **"下一步"：** 打開**問題詳細資訊的解決方案**。 選擇 **"提供詳細資訊**以提供其他資訊"。

   ![提供詳細資訊，説明您獲得請求](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 在**配額詳細資訊**中，選擇 **"經典"** 並選擇 **"位置**"。

   ![添加詳細資訊，包括部署模型和位置](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. 對於**SKU 系列**，選擇要增加的一個或多個 SKU 族。

   ![指定要增加的 SKU 系列](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. 輸入您想要對訂用帳戶採取的新限制。 要刪除行，請從**SKU 系列**中取消選擇 SKU 或選擇丟棄的"X"圖示。 為每個 SKU 系列輸入配額後，在 **"配額詳細資訊**中**保存並繼續"** 以繼續支援請求。

   ![請求新限制](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>使用使用方式和配額，在訂閱級別請求每個 VM 系列 vCPU 配額增加

按照以下說明在 Azure 門戶中使用 **"使用方式 + 配額**"創建支援請求。

1. 在[Azure 門戶](https://portal.azure.com)中，搜索並選擇 **"訂閱**"。

   ![轉到 Azure 門戶中的訂閱](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 選擇要增加其配額的訂閱。

   ![選擇要修改的訂閱](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 選擇 **"用法+配額**"。

   ![選擇訂閱的使用方式和配額](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 在右上角，選擇 **"請求增加**"。

   ![選擇增加配額](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 選擇**計算 VM（核心-vCPU）訂閱限制隨著****配額類型**增加。

   ![選擇配額類型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 選擇 **"下一步"：** 打開**問題詳細資訊的解決方案**。 選擇 **"提供詳細資訊**以提供其他資訊"。

   ![提供您的請求的詳細資訊](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 在**配額詳細資訊**中，選擇 **"經典"** 和"**位置**"。

   ![選擇配額詳細資訊，包括部署模型和位置](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. 選擇一個或多個 SKU 族進行增加。

   ![選擇 SKU 系列以進行增加](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. 輸入您想要對訂用帳戶採取的新限制。 要刪除行，請從**SKU 系列**中取消選擇 SKU 或選擇丟棄的"X"圖示。 為每個 SKU 系列輸入配額後，在 **"配額詳細資訊**中**保存並繼續"** 以繼續支援請求。

   ![輸入新配額](./media/resource-manager-core-quotas-request/new-limits-classic.png)

