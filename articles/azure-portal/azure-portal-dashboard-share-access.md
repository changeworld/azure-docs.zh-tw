---
title: 使用 Azure 角色型存取控制來共用 Azure 入口網站儀表板
description: 本文說明如何使用 Azure 角色型存取控制來共用 Azure 入口網站中的儀表板。
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: b74cae439ce351a4d720f215679cc0802eebe898
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92092200"
---
# <a name="share-azure-dashboards-by-using-azure-role-based-access-control"></a>使用 Azure 角色型存取控制來共用 Azure 儀表板

設定儀表板之後，您可以將它發佈，並與組織中的其他使用者共用。 您可以使用 [azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/role-assignments-portal.md)，讓其他人可以查看您的儀表板。 將使用者或使用者群組指派給角色。 該角色定義這些使用者是否可以查看或修改已發佈的儀表板。

所有已發佈的儀表板都會實作為 Azure 資源。 它們在您的訂用帳戶內會以可管理的專案形式存在，而且會包含在資源群組中。 從存取控制的觀點來看，儀表板與其他資源 (例如虛擬機器或儲存體帳戶) 並無不同。

> [!TIP]
> 儀表板上的個別圖格會根據其顯示的資源，強制執行自己的存取控制需求。 您可以廣泛共用儀表板，同時保護個別磚上的資料。
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>了解儀表板的存取控制

使用 Azure 角色型存取控制 (Azure RBAC) ，您可以將使用者指派給三個不同範圍層級的角色：

* 訂用帳戶
* 資源群組
* resource

您指派的許可權繼承自訂用帳戶到資源。 發佈的儀表板是一種資源。 您可能已經將使用者指派給適用于已發行儀表板的訂用帳戶角色。

假設您有 Azure 訂用帳戶，而且已為小組各種成員指派訂用帳戶的*擁有者*、*參與者*或*讀取者*角色。 身為擁有者或參與者的使用者可以列出、查看、建立、修改或刪除訂用帳戶內的儀表板。 讀者可以列出和查看儀表板，但無法修改或刪除儀表板。 具有讀者存取權的使用者可以對已發佈的儀表板進行本機編輯，例如針對問題進行疑難排解時，但無法將這些變更發佈回伺服器。 他們可以自行建立儀表板的私用複本。

您也可以將許可權指派給包含數個儀表板的資源群組，或指派給個別儀表板。 例如，您可能會決定，一群使用者應具有有限的訂用帳戶權限，但對特定儀表板則具有更大範圍的存取權。 將這些使用者指派給該儀表板的角色。

## <a name="publish-dashboard"></a>發佈儀表板

假設您設定了一個儀表板，您想要與訂用帳戶中的一組使用者共用該儀表板。 下列步驟說明如何將儀表板共用至稱為存儲裝置管理員的群組。 您可以將群組命名為任何您喜歡的名稱。 如需詳細資訊，請參閱 [Azure Active Directory 中的管理群組](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

在指派存取權之前，您必須先發佈儀表板。

1. 在儀表板中選取 [共用] ****。

    ![選取儀表板的共用](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. 在 [ **共用 + 存取控制**] 中，選取 [ **發佈**]。

    ![發佈您的儀表板](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     依預設，共用會將您的儀表板發佈至名為 [ **儀表板**] 的資源群組。 若要選取不同的資源群組，請清除該核取方塊。

儀表板現已發佈。 如果適合從訂用帳戶繼承的許可權，您就不需要再做任何動作。 您組織中的其他使用者可以根據其訂用帳戶層級角色來存取和修改儀表板。

## <a name="assign-access-to-a-dashboard"></a>指派儀表板存取權

您可以將使用者群組指派給該儀表板的角色。

1. 發佈儀表板之後，選取 [ **共用** ] **或 [取消共用]** 選項以存取 **共用 + 存取控制**。

1. 在 [ **共用 + 存取控制**] 中，選取 [ **管理使用者**]。

    ![管理儀表板的使用者](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

1. 選取 [ **角色指派** ]，以查看已為此儀表板指派角色的現有使用者。

1. 若要加入新的使用者或群組， **請選取 [新增]** ，然後新增 **角色指派**。

    ![新增使用者以存取儀表板](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. 選取代表要授與之許可權的角色。 在此範例中，請選取 [參與者] ****。

1. 選取要指派給角色的使用者或群組。 如果您在清單中看不到您要尋找的使用者或群組，請使用 [搜尋] 方塊。 可用群組的清單取決於您在 Active Directory 中建立的群組。

1. 當您完成新增使用者或群組時，請選取 [ **儲存**]。

## <a name="next-steps"></a>後續步驟

* 如需角色清單，請參閱 [Azure 內建角色](../role-based-access-control/built-in-roles.md)。
* 若要瞭解如何管理資源，請參閱 [使用 Azure 入口網站管理 Azure 資源](../azure-resource-manager/management/manage-resources-portal.md)。