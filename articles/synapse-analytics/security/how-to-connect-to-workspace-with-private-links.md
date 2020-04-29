---
title: 使用私人連結連接到 Azure Synapse 工作區
description: 本文將告訴您如何使用私人連結連線到您的 Azure Synapse 工作區
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81432186"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>使用私人連結（預覽）連接到您的 Azure Synapse 工作區

本文將告訴您如何為 Azure Synapse 工作區建立私人端點。 若要深入瞭解，請參閱[私用連結和私人端點](https://docs.microsoft.com/azure/private-link/)。

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>步驟1：在 Azure 入口網站中開啟您的 Azure Synapse 工作區

選取 [**安全性**] 底下的 [**私人端點**連線]，然後選取 [ **+ 私用端點**]。
![在 Azure 入口網站中開啟 Azure Synapse 工作區](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>步驟2：選取您的訂用帳戶和區域詳細資料

在 [**建立私人端點**] 視窗的 [**基本**] 索引標籤下，選擇您的**訂**用帳戶和**資源群組**。 為您想要建立的私用端點指定**名稱**。 選取您要建立私人端點的**區域**。

私人端點會建立在子網中。 選取的訂用帳戶、資源群組和區域會篩選私人端點子網。 選取 **[下一步]：** 完成後的資源 >。
![選取訂用帳戶和區域詳細資料](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>步驟3：選取您的 Azure Synapse 工作區詳細資料

在 [**資源**] 索引標籤中選取 [連線**到我的目錄中的 Azure 資源**]。選取包含您 Azure Synapse 工作區的**訂**用帳戶。 建立 Azure Synapse 工作區私人端點的**資源類型**為*Synapse/工作區*。

選取您的 Azure Synapse 工作區作為**資源**。 每個 Azure Synapse 工作區都有三個**目標子資源**，您可以建立私用端點來： Sql、SqlOnDemand 和 Dev。

選取 **[下一步]： [設定>** ] 繼續進行安裝的下一個部分。
![選取訂用帳戶和區域詳細資料](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

**在 [設定] 索引**標籤中，選取要在其中建立私人端點的**虛擬網路**和**子網**。 您也需要建立對應至私人端點的 DNS 記錄。

針對 [**與私人 dns 區域整合** **] 選取 [是]** ，將您的私用端點與私人 dns 區域整合。 如果您沒有與 VNet 相關聯的私人 DNS 區域，則會建立新的私人 DNS 區域。 完成時選取 [**審查 + 建立**]。

![選取訂用帳戶和區域詳細資料](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

部署完成時，請在 Azure 入口網站中開啟您的 Azure Synapse 工作區，然後選取 [**私人端點連接**]。 會顯示與私人端點相關聯的新私用端點和私人端點連接名稱。

![選取訂用帳戶和區域詳細資料](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>後續步驟

深入瞭解[受控工作區 VNet](./synapse-workspace-managed-vnet.md)

深入了解[受控私人端點](./synapse-workspace-managed-private-endpoints.md)

[建立資料來源的受控私人端點](./how-to-create-managed-private-endpoints.md)
