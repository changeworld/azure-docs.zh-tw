---
title: 檢視和管理服務提供者
description: 客戶可以使用 Azure 入口網站中的 [服務提供者] 頁面，檢視服務提供者、服務提供者供應項目與委派的資源的相關資訊。
ms.date: 10/12/2020
ms.topic: how-to
ms.openlocfilehash: e94a9fd6562a9fa8bc0f2d84cce477e7907a1e48
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371098"
---
# <a name="view-and-manage-service-providers"></a>檢視和管理服務提供者

客戶可以使用[Azure 入口網站](https://portal.azure.com)中的 [**服務提供者**] 頁面，以查看有關服務提供者和服務提供者供應專案的詳細資料、將特定資源委派給[Azure Lighthouse](../overview.md)，以及購買新的服務提供者供應專案。

> [!TIP]
> 雖然我們會在這裡參考服務提供者和客戶，但 [管理多個](../concepts/enterprise.md) 租使用者的企業可以使用相同的程式來合併其管理體驗。

若要存取 Azure 入口網站中的 [服務提供者]**** 頁面，客戶可選取 [所有服務]****，然後搜尋**服務提供者**並選取它。 他們也可以在靠近 Azure 入口網站頂端的搜尋方塊中輸入「服務提供者」或「Azure Lighthouse」來找到它。

> [!NOTE]
> 若要查看 [ **服務提供者** ] 頁面，客戶租使用者中的使用者必須具有 [讀取者] [內建角色](../../role-based-access-control/built-in-roles.md#reader) (或其他內建角色，其中包括讀取器存取) 。
>
> 若要新增或更新供應專案、委派資源及移除供應專案，使用者必須擁有訂用帳戶的 [擁有者內建角色](../../role-based-access-control/built-in-roles.md#owner) 。

請記住，[ **服務提供者** ] 頁面只會顯示透過 Azure Lighthouse 可存取客戶訂用帳戶或資源群組之服務提供者的相關資訊。 如果客戶與不使用 Azure Lighthouse 來存取客戶資源的其他服務提供者合作，此處不會顯示這些服務提供者的相關資訊。

> [!TIP]
> 服務提供者可以藉由在 Azure 入口網站中流覽至 **我的客戶** 來查看其客戶的相關資訊。 如需詳細資訊，請參閱[檢視和管理客戶和委派的資源](view-manage-customers.md)。

## <a name="view-service-provider-details"></a>檢視服務提供者詳細資料

若要查看服務提供者的詳細資料，客戶可以在 [**服務提供者**] 頁面的左側選取 [**服務提供者供應**專案]。

針對每個服務提供者供應項目，客戶都會看到服務提供者的名稱與相關聯的供應項目，以及客戶在上線程序中輸入的名稱。

在 [委派]**** 欄中，客戶會看到有多少訂用帳戶和/或資源群組，已經針對該供應項目委派至服務提供者。 服務提供者將能根據供應項目中指定的存取層級，存取及管理這些訂用帳戶和/或資源群組。

## <a name="add-or-remove-service-provider-offers"></a>新增或移除服務提供者供應項目

客戶可以選取 [新增**供應**專案]，從 [**服務提供者供應**專案] 頁面新增服務提供者供應專案。 服務提供者必須已經為此客戶發佈供應項目。 客戶接著可以從 [私人供應項目]**** 畫面中選取該供應項目，然後選取 [建立]****。

如果客戶想要移除服務提供者供應項目，他們可以選取該供應項目列中的垃圾桶圖示。 確認刪除之後，該服務提供者將無法再存取先前委派給該供應項目的客戶資源。

## <a name="delegate-resources"></a>委派資源

必須先委派資源，服務提供者才能存取及管理客戶的資源。 如果客戶已接受供應專案，但尚未委派任何資源，則他們會在 [ **服務提供者供應** 專案] 區段的頂端看到附注。 這會讓客戶知道他們必須先採取動作，服務提供者才能存取客戶的任何資源。

委派訂用帳戶或資源群組：

1. 選取包含服務提供者、供應項目與名稱的核取方塊。 然後選取畫面頂端的 [委派資源]****。
1. 在 [委派資源]**** 頁面的 [產品/服務詳細資料]**** 區段中，檢閱服務提供者與供應項目的詳細資料。 若要檢閱供應項目的角色指派，請選取 [按一下此處即可查看所選供應項目的詳細資料]****。
1. 在 [委派]**** 區段中，選取 [委派訂用帳戶]**** 或 [委派資源群組]****。
1. 選擇您想要委派給此供應項目的訂用帳戶和/或資源群組，然後選取 [新增]****。
1. 選取頁面底部的核取方塊，以確認您要授與此服務提供者您所選取資源的存取權，然後選取 [委派]****。

## <a name="update-service-provider-offers"></a>更新服務提供者供應項目

在客戶新增供應項目之後，服務提供者可能會將相同供應項目的更新版本發佈到 Azure Marketplace。 例如，他們可能會想要新增新的角色定義。 如果發行了新版本的供應專案， **服務提供者** 的 [提供] 頁面將會在該供應專案的資料列中顯示「更新」圖示。 客戶可以選取此圖示，以查看供應項目目前版本與新版本之間的差異。

 ![更新供應專案圖示](../media/update-offer.jpg)

在檢閱變更之後，客戶可以選擇更新至新版本。 一旦他們這麼做，在新版本中指定的授權和其他設定，將會套用至已針對該供應項目委派的任何訂用帳戶和/或資源群組。

## <a name="view-delegations"></a>檢視委派

委派代表授與服務提供者客戶所委派資源存取權的角色指派。 若要檢視此資訊，請選取位於 [服務提供者]**** 頁面左側的 [委派]****。

頁面頂端的篩選可讓您排序和分組委派資訊。 您也可以依特定客戶、供應專案或關鍵字進行篩選。

> [!NOTE]
> 當您 [在 Azure 入口網站中或透過 api 來查看委派範圍的角色指派](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) 時，客戶不會看到這些角色指派，或已授與這些角色之服務提供者租使用者中的任何使用者。

## <a name="audit-delegations-in-your-environment"></a>您環境中的 Audit 委派

客戶可能想要深入瞭解已委派給 Azure Lighthouse 的訂用帳戶和/或資源群組。 這特別適用于具有大量訂用帳戶的客戶，或有許多使用者執行管理工作的使用者。

我們提供 [Azure 原則內建原則定義](../../governance/policy/samples/built-in-policies.md#lighthouse) ，以將 [範圍委派審核至管理租](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json)使用者。 您可以將此原則指派給管理群組，其中包含您想要審核的所有訂用帳戶。 當您檢查是否符合此原則時，在指派原則的管理群組內 (任何委派的訂用帳戶和/或資源群組) 將會顯示為不符合規範的狀態。 然後，您可以檢查結果，並確認沒有任何未預期的委派。

另一個 [內建原則定義](../../governance/policy/samples/built-in-policies.md#lighthouse) 可讓您將 [委派限制于特定的管理](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json)租使用者。 此原則同樣可以套用至管理群組，其中包含您想要限制委派的任何訂用帳戶。 部署原則之後，任何嘗試將訂用帳戶委派給您指定的租使用者的嘗試將會被拒絕。

如需如何指派原則和查看合規性狀態結果的詳細資訊，請參閱 [快速入門：建立原則指派](../../governance/policy/assign-policy-portal.md)。

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure Lighthouse](../overview.md)。
- 瞭解服務提供者如何在 Azure 入口網站中的 [**我的客戶**] 頁面上[，查看和管理客戶](view-manage-customers.md)。
