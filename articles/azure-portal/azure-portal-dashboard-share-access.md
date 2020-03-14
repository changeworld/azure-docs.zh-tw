---
title: 使用以角色為基礎的存取控制來共用 Azure 入口網站儀表板
description: 本文說明如何在 Azure 入口網站中使用角色型存取控制來共用儀表板。
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/29/2020
ms.author: mblythe
ms.openlocfilehash: e8d251cef9e67cb8fc0c11df8ce546383f75a679
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248459"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>使用角色型存取控制來共用 Azure 儀表板

設定儀表板之後，您可以將它發佈，並與組織中的其他使用者共用。 您可以使用 Azure[角色型存取控制](../role-based-access-control/role-assignments-portal.md)（RBAC）讓其他人查看您的儀表板。 將使用者或使用者群組指派給角色。 該角色會定義這些使用者是否可以查看或修改已發佈的儀表板。

所有已發佈的儀表板都會實作為 Azure 資源。 它們會在您的訂用帳戶內以可管理的專案形式存在，並包含在資源群組中。 從存取控制的觀點來看，儀表板與其他資源 (例如虛擬機器或儲存體帳戶) 並無不同。

> [!TIP]
> 儀表板上的個別圖格會根據其顯示的資源，強制執行自己的存取控制需求。 您可以廣泛共用儀表板，同時保護個別磚上的資料。
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>了解儀表板的存取控制

若使用角色型存取控制 (RBAC)，您可以將使用者指派給三個不同範圍層級的角色︰

* 訂用帳戶
* 資源群組
* resource

您指派的許可權會從訂用帳戶向下繼承到資源。 發佈的儀表板是一種資源。 您可能已將使用者指派給適用于已發佈儀表板之訂用帳戶的角色。

假設您有 Azure 訂用帳戶，而且已為小組各種成員指派訂用帳戶的*擁有者*、*參與者*或*讀取者*角色。 身為擁有者或參與者的使用者可以列出、查看、建立、修改或刪除訂用帳戶內的儀表板。 身為讀者的使用者可以列出和查看儀表板，但無法修改或刪除它們。 具有讀取者存取權的使用者可以對已發佈的儀表板進行本機編輯（例如在疑難排解問題時），但無法將這些變更發佈回伺服器。 他們可以為自己製作儀表板的私用複本。

您也可以將許可權指派給包含數個儀表板或個別儀表板的資源群組。 例如，您可能會決定，一群使用者應具有有限的訂用帳戶權限，但對特定儀表板則具有更大範圍的存取權。 將這些使用者指派給該儀表板的角色。

## <a name="publish-dashboard"></a>發佈儀表板

假設您設定了要與訂用帳戶中的一組使用者共用的儀表板。 下列步驟示範如何將儀表板共用至名為「儲存管理員」的群組。 您可以將群組命名為任何您喜歡的名稱。 如需詳細資訊，請參閱[管理 Azure Active Directory 中的群組](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

在指派存取權之前，您必須先發佈儀表板。

1. 在儀表板中選取 [共用]。

    ![選取儀表板的共用](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. 在 [**共用 + 存取控制**] 中，選取 [**發佈**]。

    ![發佈您的儀表板](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     根據預設，共用會將您的儀表板發佈到名為**儀表板**的資源群組。

儀表板現已發佈。 如果繼承自訂用帳戶的許可權合適，您就不需要執行任何動作。 您組織中的其他使用者可以根據其訂用帳戶層級角色來存取和修改儀表板。

## <a name="assign-access-to-a-dashboard"></a>指派儀表板存取權

您可以將使用者群組指派給該儀表板的角色。

1. 發佈儀表板之後，請在 [**共用 + 存取控制**] 中選取 [**管理使用者**]。

    ![管理儀表板的使用者](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

    若要從儀表板存取**共用 + 存取控制**，請選取 [**共用** **] 或 [取消共用]** 選項。

1. 選取 [**角色指派**]，以查看已為此儀表板指派角色的現有使用者。

1. 若要新增使用者或群組，請選取 [新增]。

    ![新增使用者以存取儀表板](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. 選取代表要授與許可權的角色。 在此範例中，請選取 [參與者]。

1. 選取要指派給角色的使用者或群組。 如果您在清單中看不到您要尋找的使用者或群組，請使用 [搜尋] 方塊。 您的可用群組清單取決於您在 Active Directory 中建立的群組。

1. 使用者或群組新增完成時，請選取 [確定]。

    新的指派就會加入至使用者清單。 其**存取權**會列為 [**已指派**] 而非 [已**繼承**]。

    ![指派的角色](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>後續步驟

* 如需角色清單，請參閱[Azure 資源的內建角色](../role-based-access-control/built-in-roles.md)。
* 若要瞭解如何管理資源，請參閱[使用 Azure 入口網站來管理 Azure 資源](resource-group-portal.md)。

