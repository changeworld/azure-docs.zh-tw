---
title: 使用專用連結連接到 Azure 同步工作區
description: 本文將教您如何使用專用連結連接到 Azure Synapse 工作區
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432186"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>使用專用連結連接到 Azure 同步工作區(預覽)

本文將介紹如何創建專用終結點到 Azure Synapse 工作區。 有關詳細資訊,請參閱[私人連結和專用終結點](https://docs.microsoft.com/azure/private-link/)。

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>第 1 步:在 Azure 門戶中打開 Azure 突觸工作區

選擇 **"安全"** 下的**專用終結點連接**,然後選擇 **「專用終結點**」。
![在 Azure 門戶中開啟 Azure 同步工作區](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>第二步:選擇訂閱與區域詳細資訊

在 **「創建專用終結點**」視窗中的 **「基本」** 選項卡下,選擇 **「訂閱****和資源群組**」 。。 新增的專用的終結點**指定名稱**。 選擇要建立專用的區域 **。**

專用終結點在子網中創建。 所選訂閱、資源組和地區篩選專用終結點子網。 選擇 **「下一步」:資源>** 完成後。
![選擇訂閱及地區詳細資訊](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>第三步:選擇 Azure 同步工作區詳細資訊

在 **「資源」** 選項卡**中選擇「連接到目錄中的 Azure 資源**。選擇包含 Azure 同步工作區的**訂閱**」。 用於將專用終結點創建到 Azure Synapse 工作區**的資源類型**是*Microsoft。*

選擇 Azure 同步工作區作為**資源**。 每個 Azure Synapse 工作區都有三個目標**子資源**,您可以創建專用終結點:Sql、SqlOnDemand 和 Dev。

選擇 **「下一步」:配置>** 前進到設置的下一部分。
![選擇訂閱及地區詳細資訊](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

在 **「設定」** 選項卡中,選擇應在其中建立專用的螢幕的**虛擬網路和****子網路**。 您還需要建立映射到專用終結點的 DNS 記錄。

選擇 **「是**」以**與專用 DNS 區域整合**,以便將專用終結點與專用 DNS 區域整合。 如果沒有與 VNet 關聯的專用 DNS 區域,則會創建新的專用 DNS 區域。 選擇 **「審閱+ 創建**」,完成後。

![選擇訂閱及地區詳細資訊](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

部署完成後,在 Azure 門戶開啟 Azure 突觸工作區,然後選擇**專用終結點連接**。 將顯示與專用終結點關聯的新專用終結點和專用終結點連接名稱。

![選擇訂閱及地區詳細資訊](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>後續步驟

瞭解有關[託管工作區 VNet 的更多](./synapse-workspace-managed-vnet.md)

瞭解有關[託管專用終結點](./synapse-workspace-managed-private-endpoints.md)的更多

[建立資料來源的託管專用終結點](./how-to-create-managed-private-endpoints.md)
