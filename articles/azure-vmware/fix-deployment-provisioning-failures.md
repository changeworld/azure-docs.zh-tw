---
title: 取得 Azure VMware 解決方案部署或布建失敗的協助
description: 如何取得您的 Azure VMware 解決方案私人雲端所需的資訊，以針對 Azure VMware 解決方案部署或布建失敗提出服務要求。
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 1f46dde895db417fd2b488a6203d5482e73d3c5e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779487"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>取得 Azure VMware 解決方案部署或布建失敗的協助

本文可協助您使用 Azure VMware 解決方案部署和布建失敗。 當您的私人雲端發生失敗時，您需要在 Azure 入口網站中 (SR) 開啟 [支援要求](https://rc.portal.azure.com/#create/Microsoft.Support) 。 

不過，您必須先在 Azure 入口網站中收集一些重要資訊：

- 相互關連識別碼
- ExpressRoute 線路識別碼

## <a name="collect-the-correlation-id"></a>收集相互關聯識別碼
 
當您在 Azure 中建立私人雲端或任何資源時，會產生相互關聯識別碼。 每個 Azure Resource Manager 部署也會產生相互關聯識別碼。 此識別碼可讓您更快速地建立和解決。 
 
以下是失敗私用雲端部署的輸出範例，其中已醒目提示相互關聯識別碼。

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="具有相互關聯識別碼的私用雲端部署失敗。":::

複製並儲存此相互關聯識別碼，以包含在服務要求中。 如需詳細資訊，請參閱本文結尾的 [建立您的支援要求](#create-your-support-request) 。

如果在預先驗證階段中發生失敗，則不會產生相互關聯識別碼。 在此情況下，您可以提供建立 Azure VMware 解決方案私人雲端時所使用的資訊，包括：

- 位置
- 資源群組
- 資源名稱
 
### <a name="collect-a-summary-of-errors"></a>收集錯誤摘要

任何錯誤的詳細資料也可以協助解決您的問題。 從上一個畫面中，選取警告訊息以查看錯誤摘要。
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="具有相互關聯識別碼的私用雲端部署失敗。":::

同樣地，複製並儲存此摘要以包含在 SR 中。
 
### <a name="retrieve-past-deployments"></a>取出過去的部署

您可以藉由選取 [通知] 圖示來搜尋所存取的部署活動記錄，以抓取過去的部署，包括失敗的部署。

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="具有相互關聯識別碼的私用雲端部署失敗。":::

在 [通知] 中，選取 **活動記錄中的多個事件** 。

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="具有相互關聯識別碼的私用雲端部署失敗。":::

然後搜尋資源的名稱或其他用來建立資源的資訊，以找出失敗的部署和其相互關聯識別碼。 下列範例顯示私人雲端資源 (pc03) 的搜尋結果。
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="具有相互關聯識別碼的私用雲端部署失敗。":::
 
選取失敗部署的作業名稱，會開啟具有詳細資料的視窗。 選取 [JSON] 索引標籤，並尋找 correlationId。 在 SR 中複製並納入。 
 
## <a name="collect-the-expressroute-id-uri"></a> (URI 收集 ExpressRoute 識別碼) 
 
可能是您嘗試使用私用雲端 ExpressRoute 線路來調整或對等現有的私人雲端進行調整，且失敗。 在此情況下，您將需要 ExpressRoute 識別碼。 

在 [Azure 入口網站中，選取 **> expressroute** 的連線，並將 **expressroute 識別碼** 複製到您的剪貼簿。
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="具有相互關聯識別碼的私用雲端部署失敗。"::: 
 
> [!NOTE]
> 有時，在部署之前，預先驗證檢查可能會失敗，而且唯一可用的資訊將會是錯誤和失敗訊息。 這些可能有助於許多失敗，例如，與配額相關的問題，而且在支援要求中包含這些訊息很重要。 若要收集這些內容，請參閱稍早的章節， [收集錯誤摘要](#collect-a-summary-of-errors)。

## <a name="create-your-support-request"></a>建立您的支援要求

如需建立支援要求的一般指引，請參閱 [如何建立 Azure 支援要求](../azure-portal/supportability/how-to-create-azure-support-request.md)。 

以下是針對 Azure VMware 解決方案部署或布建失敗建立 SR 的特定指引。

1. 選取 [ **說明] 圖示，然後** 選取 [ **+ 新增支援要求** ]。

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="具有相互關聯識別碼的私用雲端部署失敗。":::

2. 填寫所有必要欄位，然後在 [ **基本** ] 索引標籤上：

    - 針對 **問題類型** ，請選取 **設定和安裝問題** 。

    - 針對 **問題子類型** ，選取 [布建 **私人雲端** ]。

3. 在 [ **詳細資料** ] 索引標籤上：

    - 填寫所有必要欄位。

    - 將您的相互關聯識別碼或 ExpressRoute 識別碼貼入提供的特定欄位中。 如果您沒有看到特定欄位，可以將它們貼到 [ **提供問題的詳細資料** ] 底下的文字方塊中。

    - 將任何錯誤詳細資料（包括您複製的錯誤摘要）貼到 [ **提供問題的詳細資料** ] 底下的文字方塊中。

4. 複習並選取 [ **建立** ] 以建立您的 SR。
