---
title: 使用私用連結連接到 Synapse Studio
description: 本文將指導您如何使用 private links 連接到您的 Azure Synapse Studio
author: NanditaV
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: d49868199d8f9f2da97f08dd06f29afd8f553bd9
ms.sourcegitcommit: 17e9cb8d05edaac9addcd6e0f2c230f71573422c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2020
ms.locfileid: "97586970"
---
# <a name="connect-to-azure-synapse-studio-using-azure-private-link-hubs"></a>使用 Azure Private Link Hub 連接到 Azure Synapse Studio 

本文說明如何使用 Azure Synapse Analytics Private Link 中樞私用連結來安全地連線至 Synapse Studio。 

## <a name="azure-private-link-hubs"></a>Azure Private Link 中樞 
您可以使用私人連結，從 Azure 虛擬網路安全地連線至 Azure Synapse Studio。 Azure Synapse Analytics private link 中樞是 Azure 資源，可作為您的安全網路與 Synapse Studio 網路體驗之間的連接器。 

使用私人連結連接到 Synapse Studio 有兩個步驟。 首先，您必須建立 private link 中樞資源。 其次，您必須從 Azure 虛擬網路建立私人端點到此私人連結中樞。 然後，您可以使用私人端點來與 Synapse Studio 安全地通訊。 您必須將私人端點與您的 DNS 解決方案（您的內部部署解決方案或 Azure 私人 DNS）整合。 

## <a name="azure-private-links-hubs-and-azure-synapse-studio"></a>Azure Private Links 中樞和 Azure Synapse Studio
您可以使用單一 Azure Synapse private link hub 資源，以私人連線至使用 Azure Synapse Studio 的所有 Azure Synapse Analytics 工作區。 工作區不一定要位於與 Azure Synapse Private link hub 相同的區域中。 Azure Synapse Private link 中樞資源也可用於連線至不同訂用帳戶或 Azure AD 租使用者中的 Synapse 工作區。

您可以在 Azure 入口網站中搜尋 *Synapse private link* 中樞，然後選取 [服務] **Azure Synapse Analytics (private link 中樞)** ，建立私人連結中樞。 遵循指南中的步驟，以瞭解如何 [從受限制的網路連線至工作區資源](./how-to-connect-to-workspace-from-restricted-network.md) 以取得詳細資料。

>[!NOTE]
>Private link 中樞是為了安全地透過私人連結載入靜態 content Synapse Studio。 您必須針對您想要在工作區中連接的資源（例如布建/專用 SQL 集區或 Spark 集區），建立 **個別的私人端點** 。 

## <a name="azure-private-links-hubs-and-azure-virtual-network"></a>Azure Private Links 中樞和 Azure 虛擬網路
您必須將 Azure 虛擬網路連線到 Synapse private link hub 資源，以保護 Synapse Studio 的端對端連線。 若要這樣做，您必須從您的虛擬網路建立私人端點至您所建立的私人連結中樞。 您可以使用私人連結中樞的 Azure 入口網站，並移至私人端點區段。 選取 [+ 私人端點] 以建立新的私人端點，以連接到您的私人連結中樞。

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-private-endpoint.png" alt-text="顯示 [私人端點連接] 頁面的螢幕擷取畫面。":::

請務必選擇 [資源] 索引標籤上的 "Synapse/privateLinkHubs" 資源類型。顯示反:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-resource-type.png" alt-text="白顯示 [資源類型] 的 [建立私人端點] 頁面的螢幕擷取畫面":::。

在 [設定] 索引標籤上，針對私人 DNS 區域選取 [privatelink.azuresynapse.net]，以便與您的虛擬網路和私人 DNS 區域整合。

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-dns-zones.png" alt-text="建立私人端點至私人連結中樞":::

## <a name="next-steps"></a>後續步驟

[從受限制的網路連接到工作區資源](./how-to-connect-to-workspace-from-restricted-network.md)

深入瞭解 [受控工作區虛擬網路](./synapse-workspace-managed-vnet.md)

深入了解[受控私人端點](./synapse-workspace-managed-private-endpoints.md)

[建立資料來源的受控私人端點](./how-to-create-managed-private-endpoints.md)

[使用私人端點連接至 Synapse 工作區](./how-to-connect-to-workspace-with-private-links.md)

