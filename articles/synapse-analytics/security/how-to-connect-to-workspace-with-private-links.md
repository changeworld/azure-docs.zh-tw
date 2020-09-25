---
title: 使用私人連結連接到 Synapse 工作區
description: 此文章將告訴您如何使用私人連結連線到您的 Azure Synapse 工作區
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ea791e4fc1031c0a5c041119c409f8623ce7aee9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91260371"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>使用私人連結連線到您的 Azure Synapse 工作區 (預覽)

此文章將告訴您如何為 Azure Synapse 工作區建立私人端點。 深入了解[私人連結和私人端點](https://docs.microsoft.com/azure/private-link/)。

## <a name="step-1-register-network-resource-provider"></a>步驟 1:註冊網路資源提供者

如果您尚未這麼做，請註冊網路資源提供者。 註冊資源提供者可將您的訂用帳戶設定為可搭配資源提供者使用。 當您[註冊](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types) \(部分機器翻譯\) 時，請從資源提供者清單中選擇 [Microsoft.Network]。 如果網路資源提供者已註冊，請繼續進行步驟 2。


## <a name="step-2-open-your-azure-synapse-workspace-in-azure-portal"></a>步驟 2:在 Azure 入口網站中開啟您的 Azure Synapse 工作區

選取 [安全性] 下的 [私人端點連線]，然後選取 [+ 私人端點]。
![在 Azure 入口網站中開啟 Azure Synapse 工作區](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-3-select-your-subscription-and-region-details"></a>步驟 3：選取訂用帳戶和區域詳細資料

在 [建立私人端點] 視窗的 [基本] 索引標籤下，選擇您的 [訂用帳戶] 和 [資源群組]。 為您要建立的私人端點提供**名稱**。 選取您要在其中建立私人端點的**區域**。

私人端點會建立在子網路中。 選取的訂用帳戶、資源群組和區域會篩選私人端點子網路。 完成時，選取 [下一步:資源 >]。
![選取訂用帳戶和區域詳細資料1](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-4-select-your-azure-synapse-workspace-details"></a>步驟 4：選取您的 Azure Synapse 工作區詳細資料

選取 [資源] 索引標籤中的 [連線到我目錄中的 Azure 資源]。選取包含 Azure Synapse 工作區的**訂用帳戶**。 建立 Azure Synapse 工作區私人端點的 [資源類型] 是 *Microsoft.Synapse/workspaces*。

選取您的 Azure Synapse 工作區作為**資源**。 每個 Azure Synapse 工作區都有三個您可以建立私人端點的**目標子資源**：Sql、SqlOnDemand 和 Dev。

完成時，選取 [下一步:設定>] 繼續進行設定的下一個部分。
![選取訂用帳戶和區域詳細資料2](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

在 [設定] 索引標籤中，選取要在其中建立私人端點的 [虛擬網路] 和 [子網路]。 您也需要建立對應至私人端點的 DNS 記錄。

若要將您的私人端點與私人 DNS 區域整合，請針對 [與私人 DNS 區域整合] 選取 [是]。 如果您的 Microsoft Azure 虛擬網路沒有相關聯的私人 DNS 區域，則會建立新的私人 DNS 區域。 完成時，請選取 [檢閱+建立]。

![選取訂用帳戶和區域詳細資料3](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

部署完成時，請在 Azure 入口網站中開啟您的 Azure Synapse 工作區，並選取 [私人端點連線]。 這會顯示與私人端點相關聯的新私人端點和私人端點連線名稱。

![選取訂用帳戶和區域詳細資料4](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>後續步驟

深入瞭解 [受控工作區虛擬網路](./synapse-workspace-managed-vnet.md)

深入了解[受控私人端點](./synapse-workspace-managed-private-endpoints.md)

[建立資料來源的受控私人端點](./how-to-create-managed-private-endpoints.md)
