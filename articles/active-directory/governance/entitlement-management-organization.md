---
title: 在 Azure AD 授權管理中添加已連接的組織 - Azure 活動目錄
description: 瞭解如何允許組織外部人員請求訪問包，以便可以協作處理專案。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee370bc9c381eb11ae7cae53b31d0c987c52733c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128600"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>在 Azure AD 授權管理中添加已連接的組織

使用 Azure 活動目錄 （Azure AD） 授權管理，可以與組織外部的人員協作。 如果經常與外部 Azure AD 目錄或域中的使用者協作，則可以將它們添加為已連接的組織。 本文介紹如何添加已連接的組織，以便允許組織外部的使用者請求目錄中的資源。

## <a name="what-is-a-connected-organization"></a>什麼是互聯組織？

已連接的組織是您與其有關系的外部 Azure AD 目錄或域。

例如，假設您在 Woodgrove Bank 工作，並且希望與兩個外部組織協作。 這兩個組織有不同的配置：

- 圖形設計研究所使用 Azure AD，其使用者具有以*graphicdesigninstitute.com*結尾的使用者主體名稱。
- Contoso 尚未使用 Azure AD。 Contoso 使用者具有以*contoso.com*結尾的使用者主體名稱。

在這種情況下，您可以配置兩個已連接的組織。 為圖形設計機構創建一個連接的組織，為 Contoso 創建一個連接的組織。 如果隨後將兩個已連接的組織添加到策略中，則具有與策略匹配的使用者主體名稱的每個組織的使用者可以請求訪問包。 具有*graphicdesigninstitute.com*域的使用者主體名稱的使用者將與圖形設計研究所關聯的組織匹配，並允許提交請求。 具有*contoso.com*域的使用者主體名稱的使用者將與 Contoso 連接的組織匹配，並且還允許請求包。 而且，由於圖形設計機構使用 Azure AD，因此具有與添加到其租戶的[已驗證域](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name)匹配的主名稱（如*圖形設計機構.example）* 的任何使用者也能夠使用相同的策略請求訪問包。

![已連接的組織示例](./media/entitlement-management-organization/connected-organization-example.png)

Azure AD 目錄或域中的使用者身份驗證方式取決於身份驗證類型。 已連接組織的身份驗證類型包括：

- Azure AD
- [直接同盟](../b2b/direct-federation.md)
- [一次性密碼](../b2b/one-time-passcode.md)（域）

有關如何添加連接組織的演示，請觀看以下視頻：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>新增已連線的組織

要將外部 Azure AD 目錄或域添加為已連接的組織，請按照本節中的說明操作。

**先決條件角色**：*全域管理員*、*使用者管理員*或*來賓邀請者*

1. 在 Azure 門戶中，選擇**Azure 活動目錄**，然後選擇**標識治理**。

1. 在左側窗格中，選擇 **"已連接組織**"，然後選擇"**添加已連接的組織**"。

    !["添加已連接的組織"按鈕](./media/entitlement-management-organization/connected-organization.png)

1. 選擇 **"基本"** 選項卡，然後輸入組織的顯示名稱和說明。

    !["添加已連接的組織"基礎知識窗格](./media/entitlement-management-organization/organization-basics.png)

1. 選擇 **"目錄 + 域**"選項卡，然後選擇 **"添加目錄 + 域**"。

    將打開"**選擇目錄 + 域**"窗格。

1. 在搜索框中，輸入要搜索 Azure AD 目錄或域的功能變數名稱。 請務必輸入整個功能變數名稱。

1. 驗證組織名稱和身份驗證類型是否正確。 使用者登錄方式取決於身份驗證類型。

    !["選擇目錄 + 域"窗格](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. 選擇 **"添加"** 以添加 Azure AD 目錄或域。 目前，每個連接的組織只能添加一個 Azure AD 目錄或域。

    > [!NOTE]
    > Azure AD 目錄或域中的所有使用者將能夠請求此訪問包。 這包括 Azure AD 中來自與目錄關聯的所有子域的使用者，除非 Azure AD 業務阻止到業務 （B2B） 允許或拒絕清單。 如需詳細資訊，請參閱[允許或封鎖對特定組織的 B2B 使用者的邀請](../b2b/allow-deny-list.md)。

1. 添加 Azure AD 目錄或域後，選擇 **"**

    組織將顯示在清單中。

    !["目錄 + 域"窗格](./media/entitlement-management-organization/organization-directory-domain.png)

1. 選擇"**贊助商**"選項卡，然後為此連接的組織添加可選贊助商。

    贊助者是目錄中已有的內部或外部使用者，是與此連接組織的關係的連絡人。 內部贊助商是目錄中的成員使用者。 外部贊助商是以前受邀且已在目錄中的已連接組織的來賓使用者。 當此連接組織中的使用者請求訪問此訪問包時，可以將贊助商用作核准者。 有關如何邀請來賓使用者加入目錄的資訊，請參閱[在 Azure 門戶中添加 Azure 活動目錄 B2B 協作使用者](../b2b/add-users-administrator.md)。

    當您選擇 **"添加/刪除**"時，將打開一個窗格，您可以在其中選擇內部或外部贊助商。 該窗格顯示目錄中使用者和組未篩選的清單。

    !["贊助商"窗格](./media/entitlement-management-organization/organization-sponsors.png)

1. 選擇"**審閱 + 創建**"選項卡，查看組織設置，然後選擇"**創建**"。

    !["查看 + 創建"窗格](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>更新已連接的組織 

如果連接的組織更改為其他域、組織的名稱更改，或者您希望更改贊助者，則可以按照本節中的說明更新已連接的組織。

**先決條件角色**：*全域管理員*、*使用者管理員*或*來賓邀請者*

1. 在 Azure 門戶中，選擇**Azure 活動目錄**，然後選擇**標識治理**。

1. 在左側窗格中，選擇 **"已連接的組織**"，然後選擇已連接的組織以將其打開。

1. 在連接組織的概述窗格中，選擇 **"編輯"** 以更改組織名稱或說明。  

1. 在 **"目錄 + 域**"窗格中，選擇 **"更新目錄 + 域**"以更改為其他目錄或域。

1. 在 **"贊助商"** 窗格中，選擇 **"添加內部贊助商**"或 **"添加外部贊助商**"以將使用者添加為贊助商。 要刪除贊助商，請選擇贊助商，並在右側窗格中選擇 **"刪除**"。


## <a name="delete-a-connected-organization"></a>刪除已連接的組織

如果不再與外部 Azure AD 目錄或域有關系，則可以刪除已連接的組織。

**先決條件角色**：*全域管理員*、*使用者管理員*或*來賓邀請者*

1. 在 Azure 門戶中，選擇**Azure 活動目錄**，然後選擇**標識治理**。

1. 在左側窗格中，選擇 **"已連接的組織**"，然後選擇已連接的組織以將其打開。

1. 在連接組織的概述窗格中，選擇 **"刪除**"以將其刪除。

    目前，只有在沒有連接的使用者時，才能刪除已連接的組織。

    ![已連接的組織 刪除按鈕](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>後續步驟

- [管控外部使用者的存取](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [管理不在目錄中的使用者的訪問](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
