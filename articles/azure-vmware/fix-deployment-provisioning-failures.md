---
title: 取得 Azure VMware 解決方案部署或布建失敗的協助
description: 如何取得您的 Azure VMware 解決方案（AVS）私人雲端所需的資訊，以提出適用于 AVS 部署或布建失敗的服務要求。
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 05c9dee088b37c37cdcdee7e745cdcd2222b63e0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86514572"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>取得 Azure VMware 解決方案部署或布建失敗的協助

在本文中，您將瞭解如何在 Azure 入口網站中開啟服務要求（SR），以取得私人雲端上的 Azure VMware 解決方案（AVS）部署或布建失敗的協助。 不過，您必須先在 Azure 入口網站中收集一些重要資訊。 在大部分的情況下，您需要：

- 相互關聯識別碼（失敗的部署）
- ExpressRoute 線路識別碼（嘗試使用私用雲端 ExpressRoute 線路調整或對等現有的私人雲端時，它會失敗）

## <a name="collect-the-correlation-id"></a>收集相互關聯識別碼
 
讓我們先看一下相互關聯識別碼。 當您建立私人雲端（或 Azure 中的任何資源）時，會產生相關聯的相互關聯識別碼。 每個 Azure Resource Manager 部署也會產生唯一的相互關聯識別碼。 此識別碼可加速建立和解決 SR。 
 
以下是來自失敗的私用雲端部署的輸出範例，其中的相互關聯識別碼已反白顯示。

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="具有相互關聯識別碼的私用雲端部署失敗。":::

複製並儲存此相互關聯識別碼，以包含在服務要求中。 如需詳細資訊，請參閱本文結尾處的[建立支援要求](#create-your-support-request)。

如果失敗發生在預先驗證階段，在部署私人雲端之前，不會產生相互關聯識別碼。 在此情況下，您可以只提供建立 AVS 私用雲端時所使用的資訊，包括：

- Location
- 資源群組
- 資源名稱
 
### <a name="collect-a-summary-of-errors"></a>收集錯誤摘要

任何錯誤的詳細資料也有助於解決您的問題。 從上一個畫面中，選取 [**按一下這裡以取得詳細資料**（反白顯示）]，並開啟錯誤摘要，如下列螢幕擷取畫面所示。
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="錯誤摘要。":::

同樣地，複製並儲存此摘要以包含在 SR 中。
 
### <a name="retrieve-past-deployments"></a>取出過去的部署

您可以藉由選取 [通知] 圖示，在存取的 [部署活動記錄] 中搜尋，以取出過去的部署，包括失敗的部署。

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="開啟 [通知]。":::

在 [通知] 中，選取 **[活動記錄] 中的 [更多事件**]。

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="連結：活動記錄中的更多事件。":::

然後搜尋資源的名稱，或在您用來建立資源的另一個唯一的資訊片段中，尋找失敗的部署和其相互關聯識別碼。 下列範例會顯示私人雲端資源（pc03）上的搜尋結果。
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="尋找過去失敗的 AVS 部署。":::
 
選取失敗部署的作業名稱會開啟包含詳細資料的視窗。 選取 [JSON] 索引標籤，然後尋找 [correlationId]。 複製並包含在 SR 中。 
 
## <a name="collect-the-expressroute-id-uri"></a>收集 ExpressRoute 識別碼（URI）
 
也許您已經有私用雲端，當您嘗試以私人雲端 ExpressRoute 線路調整 it 或對等時，會遇到失敗。 在此情況下，您可以在建立 SR 時使用私人雲端的 ExpressRoute 識別碼來識別它。

在入口網站中觀看私人雲端時，請選取 [連線 **> expressroute** ]，並將 [ **ExpressRoute 識別碼**] 複製到剪貼簿。
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="收集 ExpressRoute 識別碼。"::: 
 
將 ExpressRoute 識別碼貼入新支援要求中的適當欄位。 如需詳細資訊，請參閱下一節[建立您的支援要求](#create-your-support-request)。
 
> [!NOTE]
> 有時候，在部署之前，預先驗證檢查可能會失敗，而且唯一可用的資訊將會是錯誤和（或）失敗訊息。 這些可能會對一些失敗有説明，例如，與配額相關的問題，而且在支援要求中包含這些訊息是很重要的。 若要收集這些內容，請參閱先前的[收集錯誤摘要](#collect-a-summary-of-errors)一節。

## <a name="create-your-support-request"></a>建立您的支援要求

如需建立支援要求的一般指引，請參閱[如何建立 Azure 支援要求](../azure-portal/supportability/how-to-create-azure-support-request.md)。 

以下是針對 AVS 部署或布建失敗建立 SR 的其他指引。

1. 選取 [**說明] 圖示，然後**選取 [ **+ 新增支援要求**]。

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="收集 ExpressRoute 識別碼。":::

2. 填寫所有必要的欄位，然後在 [**基本**] 索引標籤上：

    - 針對 [**問題類型**]，選取 [**設定和安裝問題**]。

    - 針對**問題子類型**，請選取 [布建**私人雲端**]。

3. 在 [**詳細資料**] 索引標籤上：

    - 填寫所有必要欄位。

    - 將您的相互關聯識別碼或 ExpressRoute 識別碼貼入提供的特定欄位。 如果您沒有看到這些專案的特定欄位，您可以將其貼到 [**提供問題的詳細資料**] 底下的文字方塊中。

    - 在 [**提供問題的詳細資料**] 底下的文字方塊中，貼上任何錯誤詳細資料，包括您複製的錯誤摘要。

4. 檢查並選取 [**建立**] 以建立您的 SR。
