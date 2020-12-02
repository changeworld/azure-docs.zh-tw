---
title: Azure Synapse Analytics 工作區的資料遭到外泄保護
description: 本文將說明 Azure Synapse Analytics 中的資料遭到外泄保護
author: NanditaV
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 7af49b31aed3794d204b653f0ebfd66283c26cd4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501407"
---
# <a name="data-exfiltration-protection-for-azure-synapse-analytics-workspaces"></a>Azure Synapse Analytics 工作區的資料遭到外泄保護
本文將說明 Azure Synapse Analytics 中的資料遭到外泄保護

## <a name="securing-data-egress-from-synapse-workspaces"></a>保護 Synapse 工作區的資料輸出
Azure Synapse Analytics 工作區支援為工作區啟用資料遭到外泄保護。 使用遭到外泄保護時，您可以防止惡意的內部人員存取您的 Azure 資源，並將敏感性資料洩漏到組織範圍以外的位置。 在建立工作區時，您可以選擇使用受控虛擬網路設定工作區，以及針對資料遭到外泄進行額外的保護。 使用 [受控虛擬網路](./synapse-workspace-managed-vnet.md)建立工作區時，資料整合和 Spark 資源會部署在受控虛擬網路中。 工作區的專用 SQL 集區和無伺服器 SQL 集區具有多租使用者功能，因此必須存在於受控虛擬網路之外。 針對具有資料遭到外泄保護的工作區，受管理的虛擬網路內的資源一律會透過 [受控私人端點](./synapse-workspace-managed-private-endpoints.md) 進行通訊，而 Synapse SQL 資源只能連線到已授權的 Azure 資源， (來自工作區) 的已核准受管理私人端點連線。 

>[!Note]
>建立工作區之後，您就無法變更受控虛擬網路和資料遭到外泄保護的工作區設定。

## <a name="managing-synapse-workspace-data-egress-to-approved-targets"></a>管理已核准目標的 Synapse 工作區資料輸出
建立工作區並啟用資料遭到外泄之後，工作區資源的擁有者就可以管理工作區的已核准 Azure AD 租使用者清單。 在工作區上具有 [適當許可權](./access-control.md) 的使用者可以使用 Synapse Studio，為工作區的已核准 Azure AD 租使用者中的資源建立受控私人端點連接要求。 如果使用者嘗試在未核准的租使用者中建立與資源的私人端點連線，將會封鎖受控私人端點建立。

## <a name="sample-workspace-with-data-exfiltration-protection-enabled"></a>啟用資料遭到外泄保護的範例工作區
讓我們使用範例來說明 Synapse 工作區的資料遭到外泄保護。 Contoso 在租使用者 A 和租使用者 B 中有 Azure 資源，而這些資源需要安全地連接。 已在租使用者 A 中建立 Synapse 工作區，並將租使用者 B 新增為已核准的 Azure AD 租使用者。 此圖表顯示租使用者 A 中的 Azure 儲存體帳戶和已由儲存體帳戶擁有者核准的租使用者 B 的私人端點連線。 此圖表也會顯示已封鎖的私人端點建立。 因為此私人端點的目標是 Fabrikam Azure AD 租使用者中的 Azure 儲存體帳戶（不是 Contoso 工作區的核准 Azure AD 租使用者），所以已封鎖此私人端點的建立。 
:::image type="content" source="media/workspace-data-exfiltration-protection/workspace-data-exfiltration-protection-diagram.png" alt-text="下圖顯示如何針對 Synapse 工作區執行資料遭到外泄保護":::

>[!IMPORTANT]
>在工作區租使用者以外的租使用者中的資源，不能有封鎖的防火牆規則，讓 SQL 集區與其連線。 工作區管理的虛擬網路（例如 Spark 叢集）內的資源，可以透過受管理的私人連結連接到受防火牆保護的資源。
## <a name="next-steps"></a>後續步驟

瞭解如何 [建立已啟用資料遭到外泄保護的工作區](./how-to-create-a-workspace-with-data-exfiltration-protection.md)

深入瞭解 [受控工作區虛擬網路](./synapse-workspace-managed-vnet.md)

深入了解[受控私人端點](./synapse-workspace-managed-private-endpoints.md)

[建立資料來源的受控私人端點](./how-to-create-managed-private-endpoints.md)
