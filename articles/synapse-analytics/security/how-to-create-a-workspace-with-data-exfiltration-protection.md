---
title: 建立已啟用資料遭到外泄保護的工作區
description: 本文將說明如何在 Azure Synapse Analytics 中建立具有資料遭到外泄保護的工作區。
author: NanditaV
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: f8ebbdf70836f3f2613183268f03dc43da1f0671
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590555"
---
# <a name="create-a-workspace-with-data-exfiltration-protection-enabled"></a>建立已啟用資料遭到外泄保護的工作區
本文說明如何建立已啟用資料遭到外泄保護的工作區，以及如何管理此工作區已核准的 Azure AD 租使用者。

>[!Note]
>建立工作區之後，您就無法變更受控虛擬網路和資料遭到外泄保護的工作區設定。

## <a name="prerequisites"></a>必要條件
- 在 Azure 中建立工作區資源的許可權。
- Synapse 工作區許可權，以建立受控私人端點。
- 註冊網路資源提供者的訂閱。 [深入了解。](../../azure-resource-manager/management/resource-providers-and-types.md)

遵循 [快速入門：建立 Synapse 工作區](../quickstart-create-workspace.md) 中所列的步驟，開始建立您的工作區。 在建立您的工作區之前，請使用下列資訊將資料遭到外泄保護新增至您的工作區。

## <a name="add-data-exfiltration-protection-when-creating-your-workspace"></a>在建立工作區時新增資料遭到外泄保護
1. 在 [網路功能] 索引標籤上，選取 [啟用受控虛擬網路] 核取方塊。
1. 針對 [只允許通過核准目標的輸出資料流量] 選項選取 [是]。
1. 選擇此工作區已核准的 Azure AD 租使用者。
1. 檢查設定，並建立工作區。
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-creation-data-exfiltration-protection.png" alt-text="顯示 [建立 Synapse] 工作區的螢幕擷取畫面，其中已選取 [啟用管理虛擬網路]。":::

## <a name="manage-approved-azure-active-directory-tenants-for-the-workspace"></a>管理工作區已核准的 Azure Active Directory 租使用者
1. 從工作區的 Azure 入口網站中，流覽至 [核准的 Azure AD 租使用者]。 工作區已核准的 Azure AD 租使用者清單會列在這裡。 預設會包含工作區的租使用者，且不會列出。
1. 使用 [+ 新增] 將新的租使用者包含在核准的清單中。
1. 若要從核准的清單中移除 Azure AD 的租使用者，請選取租使用者，然後選取 [刪除]，然後選取 [儲存]。
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-manage-aad-tenant-list.png" alt-text="使用資料遭到外泄保護建立工作區":::


## <a name="connecting-to-azure-resources-in-approved-azure-ad-tenants"></a>在已核准的 Azure AD 租使用者中連接至 Azure 資源

您可以建立受控私人端點，以連線至位於 Azure AD 租使用者（已核准工作區）的 Azure 資源。 遵循指南中所列的步驟來 [建立受控私人端點](./how-to-create-managed-private-endpoints.md)。

>[!IMPORTANT]
>在工作區租使用者以外的租使用者中的資源，不能有封鎖的防火牆規則，讓 SQL 集區與其連線。 工作區管理的虛擬網路（例如 Spark 叢集）內的資源，可以透過受管理的私人連結連接到受防火牆保護的資源。

## <a name="next-steps"></a>後續步驟

深入瞭解 [Synapse 工作區中的資料遭到外泄保護](./workspace-data-exfiltration-protection.md)

深入瞭解 [受控工作區虛擬網路](./synapse-workspace-managed-vnet.md)

深入了解[受控私人端點](./synapse-workspace-managed-private-endpoints.md)

[建立資料來源的受控私人端點](./how-to-create-managed-private-endpoints.md)
