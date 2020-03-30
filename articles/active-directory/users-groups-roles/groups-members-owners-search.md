---
title: 搜索和篩選組成員和擁有者（預覽） - Azure 活動目錄 |微軟文檔
description: 搜索和篩選 Azure 門戶中的成員和擁有者。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/28/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a815446b79b3e5ec0a75e5d179953956643b16c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206107"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Azure 活動目錄中的搜索組和成員（預覽）

本文介紹如何搜索組的成員和擁有者，以及如何在 Azure 活動目錄 （Azure AD） 門戶中使用搜索篩選器作為組改進預覽的一部分。 在組體驗方面有很多改進，可説明您快速輕鬆地管理組（包括成員和擁有者）。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

此預覽中的更改包括：

- 新的組搜索功能，如組名稱中的子字串搜索
- 成員和擁有者清單上的新篩選和排序選項
- 成員和擁有者清單的新搜索功能
- 大組更準確的組計數

## <a name="enabling-and-managing-the-preview"></a>啟用和管理預覽

我們可以輕鬆加入預覽：

  1. 登錄到 Azure [AD 門戶](https://portal.azure.com)，然後選擇**組**。
  2. 從"組 + 所有組"頁面中，選擇頁面頂部的橫幅以加入預覽。

您還可以通過在"**所有組**"頁面上選擇 **"預覽資訊**"來查看最新的功能和改進。 加入預覽後，您可以在具有改進且屬於預覽的所有組頁面上看到預覽標記。 不是每個組頁面都已作為此預覽的一部分進行更新。

如果遇到任何問題，可以通過選擇 **"所有組"** 頁面頂部的橫幅來切換舊體驗。 我們感謝您的回饋，以便我們可以改善我們的體驗。

## <a name="group-search-and-sorting"></a>分組搜索和排序

組清單搜索已增強，以便在可以輸入搜索字串時，搜索會自動在組名稱清單中執行`startswith`和子字串搜索。 子字串搜索僅對整個單詞執行，不包括特殊字元。 子字串搜索區分大小寫。

!["所有組"頁上的新子字串搜索](./media/groups-members-owners-search/groups-search-preview.png)

例如，搜索"策略"現在將返回"MDM 策略 + 西部"和"策略組"。 不會返回名為"New_policy"的組。

- 您還可以對組成員身份清單執行相同的搜索。
- 現在，您可以使用名稱列標題右側的箭頭按名稱按名稱對組清單進行排序，以便按昇冪或降冪對清單進行排序。

## <a name="group-member-search-and-filtering"></a>組成員搜索和篩選

### <a name="search-group-member-and-owner-lists"></a>搜索組成員和擁有者清單

現在，您可以按名稱搜索特定組的成員，也可以對組的擁有者清單執行相同的搜索。 在新體驗中，如果在搜索框中輸入字串，將自動執行"開始搜索"。 例如，搜索"斯科特"將返回斯科特·威爾金森。

![對組成員和擁有者清單的新子字串搜索](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>篩選器成員和擁有者清單

除了搜索之外，現在還可以按使用者類型篩選成員和擁有者清單。 這是在清單的使用者類型列中找到的資訊。 因此，您可以按成員和來賓篩選清單，以確定組中是否有任何來賓。

### <a name="view-and-manage-membership"></a>查看和管理成員資格

除了查看特定組的直接成員外，您現在還可以查看"成員"頁中該組所有成員的清單。 成員清單包括組的所有唯一成員，包括任何傳遞成員。

您還可以單獨搜索和篩選直接成員清單和所有成員清單。 篩選所有成員清單不會影回應用於直接成員清單的篩選器。

## <a name="improved-group-member-counts"></a>改進的組成員計數

我們改進了組**概述**頁面，以為所有大小的組提供組成員計數。 即使對於成員超過 1，000 個的組，也可以查看成員計數。 現在，您可以在 **"概述"** 頁上查看組的直接成員總數和成員總數（包括傳遞成員在內的組的所有唯一成員）。

![組成員數的準確性更高](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>後續步驟

這些文章提供了有關在 Azure AD 中處理組的其他資訊。

- [檢視群組和成員](../fundamentals/active-directory-groups-view-azure-portal.md)
- [管理群組成員資格](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [管理群組中使用者的動態規則](groups-create-rule.md)
- [編輯群組設定](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [使用群組管理對資源的存取](../fundamentals/active-directory-manage-groups.md)
- [使用群組管理對 SaaS 應用程式的存取](groups-saasapps.md)
- [使用 PowerShell 命令管理群組](groups-settings-v2-cmdlets.md)
- [將 Azure 訂閱添加到 Azure 活動目錄](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
