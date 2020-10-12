---
title: 取得 Azure VMware 解決方案部署或布建失敗的協助
description: 如何取得您的 Azure VMware 解決方案私人雲端所需的資訊，以針對 Azure VMware 解決方案部署或布建失敗提出服務要求。
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 63d5440a9e2b15463e465e1d32762889508feca1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88752229"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>取得 Azure VMware 解決方案部署或布建失敗的協助

在本文中，您將瞭解如何在 Azure 入口網站中開啟服務要求 (SR) ，以取得 Azure VMware 解決方案部署的協助，或在私人雲端上布建失敗。 不過，您必須先在 Azure 入口網站中收集一些重要資訊。 在大多數情況下，您需要：

- 失敗部署的相互關聯識別碼 () 
- 當您嘗試以私用雲端 ExpressRoute 線路調整或對等現有的私人雲端進行調整或對等互連時，ExpressRoute 線路識別碼 (失敗) 

## <a name="collect-the-correlation-id"></a>收集相互關聯識別碼
 
讓我們先看看相互關聯識別碼。 當您在 Azure) 中建立私人雲端 (或任何資源時，會產生相關聯的相互關聯識別碼。 每個 Azure Resource Manager 部署也會產生唯一的相互關聯識別碼。 此識別碼可讓您更快速地建立和解決。 
 
以下是失敗私用雲端部署的輸出範例，其中已醒目提示相互關聯識別碼。

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="具有相互關聯識別碼的私用雲端部署失敗。":::

複製並儲存此相互關聯識別碼，以包含在服務要求中。 如需詳細資訊，請參閱本文結尾的 [建立您的支援要求](#create-your-support-request) 。

如果在預先驗證階段中發生失敗，則在部署私用雲端之前，不會產生相互關聯識別碼。 在此情況下，您可以直接提供您在建立 Azure VMware 解決方案私人雲端時所使用的資訊，包括：

- Location
- 資源群組
- 資源名稱
 
### <a name="collect-a-summary-of-errors"></a>收集錯誤摘要

任何錯誤的詳細資料也有助於解決您的問題。 從上一個畫面中，選取 [ **按一下此處以取得詳細資料** (醒目提示) ，並開啟 [錯誤摘要]，如下列螢幕擷取畫面所示。
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="具有相互關聯識別碼的私用雲端部署失敗。":::

同樣地，複製並儲存此摘要以包含在 SR 中。
 
### <a name="retrieve-past-deployments"></a>取出過去的部署

您可以藉由選取 [通知] 圖示來搜尋所存取的部署活動記錄，以抓取過去的部署，包括失敗的部署。

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="具有相互關聯識別碼的私用雲端部署失敗。":::

在 [通知] 中，選取 **活動記錄中的多個事件**。

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="具有相互關聯識別碼的私用雲端部署失敗。":::

然後搜尋資源的名稱，或在您用來建立資源的另一項唯一資訊上搜尋，以找出失敗的部署和其相互關聯識別碼。 下列範例顯示私人雲端資源 (pc03) 的搜尋結果。
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="具有相互關聯識別碼的私用雲端部署失敗。":::
 
選取失敗部署的作業名稱，會開啟具有詳細資料的視窗。 選取 [JSON] 索引標籤，並尋找 correlationId。 在 SR 中複製並納入。 
 
## <a name="collect-the-expressroute-id-uri"></a> (URI 收集 ExpressRoute 識別碼) 
 
或許您已經有私用雲端，當您嘗試使用私用雲端 ExpressRoute 線路來調整它或對等時，就會遇到失敗。 在此情況下，私用雲端的 ExpressRoute 識別碼可用來在您建立 SR 時加以識別。

在入口網站中查看私人雲端時，請選取 **> ExpressRoute** 的連線能力，並將 **expressroute 識別碼** 複製到剪貼簿。
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="具有相互關聯識別碼的私用雲端部署失敗。"::: 
 
在新的支援要求中，將 ExpressRoute 識別碼貼到適當的欄位。 如需詳細資訊，請參閱下一節， [建立您的支援要求](#create-your-support-request)。
 
> [!NOTE]
> 有時，在部署之前，預先驗證檢查可能會失敗，而唯一可用的資訊將會是錯誤和/或失敗訊息。 這些可能有助於許多失敗，例如與配額相關的問題，而且在支援要求中包含這些訊息很重要。 若要收集這些內容，請參閱稍早的章節， [收集錯誤摘要](#collect-a-summary-of-errors)。

## <a name="create-your-support-request"></a>建立您的支援要求

如需建立支援要求的一般指引，請參閱 [如何建立 Azure 支援要求](../azure-portal/supportability/how-to-create-azure-support-request.md)。 

以下是建立適用于 Azure VMware 解決方案部署或布建失敗的 SR 的其他指引。

1. 選取 [ **說明] 圖示，然後** 選取 [ **+ 新增支援要求**]。

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="具有相互關聯識別碼的私用雲端部署失敗。":::

2. 填寫所有必要欄位，然後在 [ **基本** ] 索引標籤上：

    - 針對 **問題類型**，請選取 **設定和安裝問題**。

    - 針對 **問題子類型**，選取 [布建 **私人雲端**]。

3. 在 [ **詳細資料** ] 索引標籤上：

    - 填寫所有必要欄位。

    - 將您的相互關聯識別碼或 ExpressRoute 識別碼貼入提供的特定欄位中。 如果您沒有看到這些專案的特定欄位，可以將它們貼到 [**提供問題的詳細資料**] 底下的文字方塊中。

    - 將任何錯誤詳細資料（包括您複製的錯誤摘要）貼到 [**提供問題的詳細資料**] 底下的文字方塊中。

4. 複習並選取 [ **建立** ] 以建立您的 SR。
