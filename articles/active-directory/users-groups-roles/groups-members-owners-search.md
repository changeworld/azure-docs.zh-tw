---
title: 搜尋和篩選群組成員和擁有者（預覽）-Azure Active Directory |Microsoft Docs
description: 在 Azure 入口網站中搜尋和篩選群組成員和擁有者。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 02/28/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92674ed9d39ea9e84d477d19aadbaeda6da6f32c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84728260"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>搜尋 Azure Active Directory 中的群組和成員（預覽）

本文會告訴您如何搜尋群組的成員和擁有者，以及如何在 Azure Active Directory （Azure AD）入口網站中，使用搜尋篩選作為 [群組改進預覽] 的一部分。 群組體驗中有許多增強功能，可協助您快速且輕鬆地管理您的群組，包括成員和擁有者。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

此預覽中的變更包括：

- 新群組搜尋功能，例如組名中的子字串搜尋
- 成員和擁有者清單上的新篩選和排序選項
- 成員和擁有者清單的新搜尋功能
- 更精確的大型群組群組計數

## <a name="enabling-and-managing-the-preview"></a>啟用和管理預覽

我們已讓您輕鬆加入預覽版：

  1. 登入[Azure AD 入口網站](https://portal.azure.com)，然後選取 [**群組**]。
  2. 從 [群組-所有群組] 頁面上，選取頁面頂端的橫幅以加入預覽。

您也可以選取 [**所有群組**] 頁面上的 [**預覽資訊**]，查看最新的功能和改善。 加入預覽之後，您可以在具有改良功能的 [所有群組] 頁面上看到 [預覽] 標記，並且屬於預覽的一部分。 並非每個群組頁面都已更新為此預覽的一部分。

如果您遇到任何問題，可以選取 [**所有群組**] 頁面頂端的橫幅，切換回舊版體驗。 我們非常感謝您的意見反應，讓我們可以改善我們的體驗。

## <a name="group-search-and-sorting"></a>群組搜尋和排序

群組清單搜尋已經過增強，因此當您輸入搜尋字串時，搜尋會自動 `startswith` 在組名清單上執行和 substring 搜尋。 子字串搜尋只會針對整個單字執行，而且不會包含特殊字元。 子字串搜尋會區分大小寫。

![[所有群組] 頁面上的新子字串搜尋](./media/groups-members-owners-search/groups-search-preview.png)

例如，搜尋「原則」現在會同時傳回「MDM 原則-西部」和「原則群組」。 將不會傳回名為 "New_policy" 的群組。

- 您也可以對群組成員資格清單執行相同的搜尋。
- 您現在可以使用 [名稱] 資料行標題右邊的箭號，依名稱排序群組清單，以遞增或遞減順序排序清單。

## <a name="group-member-search-and-filtering"></a>群組成員搜尋和篩選

### <a name="search-group-member-and-owner-lists"></a>搜尋群組成員和擁有者清單

您現在可以依名稱搜尋特定群組的成員，以及在群組擁有者的清單上執行相同的搜尋。 在新的體驗中，如果您在 [搜尋] 方塊中輸入字串，則會自動執行 startswith 搜尋。 例如，搜尋「Scott」將會傳回 Scott Wilkinson。

![群組成員和擁有者清單上的新子字串搜尋](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>篩選成員和擁有者清單

除了搜尋以外，您現在可以依使用者類型篩選成員和擁有者清單。 這是在清單的 [使用者類型] 資料行中找到的資訊。 因此，您可以依成員和來賓篩選清單，以判斷群組中是否有任何來賓。

### <a name="view-and-manage-membership"></a>查看和管理成員資格

除了查看特定群組的直接成員之外，您現在還可以在 [成員] 頁面中，查看該群組的所有成員清單。 [成員] 清單包含群組的所有唯一成員，包括任何可轉移的成員。

您也可以個別搜尋和篩選 [直接成員] 清單和 [所有成員] 清單。 篩選 [所有成員] 清單並不會影響直接成員清單上所套用的篩選。

## <a name="improved-group-member-counts"></a>已改善群組成員計數

我們已改善 [群組**總覽**] 頁面，為各種大小的群組提供群組成員計數。 您可以看到成員計數，即使是具有超過1000個成員的群組。 您現在可以在 [**總覽**] 頁面上，看到群組的直接成員總數和成員資格總計數（群組的所有唯一成員，包括可轉移的成員）。

![群組成員資格計數的精確度越高](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>後續步驟

這些文章提供有關在 Azure AD 中使用群組的其他資訊。

- [檢視群組和成員](../fundamentals/active-directory-groups-view-azure-portal.md)
- [管理群組成員資格](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [管理群組中使用者的動態規則](groups-create-rule.md)
- [編輯群組設定](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [使用群組管理對資源的存取](../fundamentals/active-directory-manage-groups.md)
- [使用群組管理對 SaaS 應用程式的存取](groups-saasapps.md)
- [使用 PowerShell 命令管理群組](groups-settings-v2-cmdlets.md)
- [將 Azure 訂用帳戶新增至 Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
