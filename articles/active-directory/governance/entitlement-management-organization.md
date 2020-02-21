---
title: 在 Azure AD 權利管理中新增已連線的組織-Azure Active Directory
description: 瞭解如何允許組織外部人員要求存取套件，讓您可以在專案上共同作業。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/22/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ecf3a8819518c674a3d8bd7af55d1a3c6393c42
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77483851"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中新增已連線的組織

Azure AD 權利管理可讓您與組織外部的人員共同作業。 如果您經常與外部 Azure AD 目錄或網域中的使用者共同作業，您可以將他們新增為已連線的組織。 本文說明如何新增已連線的組織，讓您的組織外部使用者可以要求您的目錄中的資源。

## <a name="what-is-a-connected-organization"></a>什麼是已連線的組織？

已連線的組織是與您有關聯性的外部 Azure AD 目錄或網域。

例如，假設您在 Woodgrove Bank 工作，而且想要與兩個外部組織共同作業。 這兩個組織有不同的設定：

- 圖形設計局使用 Azure AD，而其使用者的使用者主體名稱結尾為 `graphicdesigninstitute.com`
- Contoso 尚未使用 Azure AD。 Contoso 使用者的使用者主體名稱結尾為 `contoso.com`。

在此情況下，您可以設定兩個已連線的組織。 您會為圖形設計局建立一個已連線的組織，一個用於 Contoso。 如果您接著將這兩個已連線的組織新增至原則，則每個組織中具有符合原則之使用者主體名稱的使用者，都可以要求存取封裝。 使用者主體名稱具有 graphicdesigninstitute.com 網域的使用者，會符合圖形設計研究所已連線的組織並允許提交要求，而具有 contoso.com 網域之使用者主體名稱的使用者則會符合Contoso 已連線的組織也會允許要求封裝。 此外，因為圖形設計研究所使用 Azure AD，所以主體名稱符合[已驗證網域](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name)（例如 graphicdesigninstitute）的任何使用者，都可以使用相同的原則來要求存取封裝。

![已連線的組織範例](./media/entitlement-management-organization/connected-organization-example.png)

Azure AD 目錄或網域中的使用者將如何驗證，取決於驗證類型。 已連線組織的驗證類型如下：

- Azure AD
- [直接同盟](../b2b/direct-federation.md)
- [一次性密碼](../b2b/one-time-passcode.md)（網域）

如需如何新增已連線組織的示範，請觀看下列影片：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>新增已連線的組織

請遵循下列步驟，將外部 Azure AD 目錄或網域新增為已連線的組織。

**先決條件角色：** 全域管理員、使用者系統管理員或來賓邀請者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**已連接的組織**]，然後按一下 [**新增已連線的組織**]。

    ![身分識別治理-已連線的組織-新增已連線的組織](./media/entitlement-management-organization/connected-organization.png)

1. 在 [**基本**] 索引標籤上，輸入組織的 [顯示名稱] 和 [描述]。

    ![[新增已連線的組織-基本] 索引標籤](./media/entitlement-management-organization/organization-basics.png)

1. 在 [**目錄 + 網域**] 索引標籤上，按一下 [**新增目錄 + 網域**] 以開啟 [選取目錄 + 網域] 窗格。

1. 輸入功能變數名稱，以搜尋 Azure AD 目錄或網域。 您必須輸入完整的功能變數名稱。

1. 依照提供的名稱和驗證類型，確認它是正確的組織。 使用者登入的方式將取決於驗證類型。

    ![新增已連線的組織-選取目錄 + 網域](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. 按一下 [**新增**] 以新增 Azure AD 目錄或網域。 目前，您只能為每個連線的組織新增一個 Azure AD 目錄或網域。

    > [!NOTE]
    > Azure AD 目錄或網域中的所有使用者都可以要求此存取封裝。 這包括從與目錄相關聯的所有子域 Azure AD 的使用者，除非 Azure B2B 允許或拒絕清單封鎖這些網域。 如需詳細資訊，請參閱[允許或封鎖對特定組織的 B2B 使用者的邀請](../b2b/allow-deny-list.md)。

1. 新增 Azure AD 目錄或網域之後，請按一下 [**選取**]。

    組織會出現在清單中。

    ![[新增已連線的組織-目錄] 索引標籤](./media/entitlement-management-organization/organization-directory-domain.png)

1. 在 [**贊助**者] 索引標籤上，為此連線組織新增選用的贊助者。

    贊助者是已在您目錄中的內部或外部使用者，這是與此連線組織的關係點。 內部贊助者是您目錄中的成員使用者。 外部贊助者是來自已連線組織的來賓使用者，先前已受邀並已在您的目錄中。 當此連線組織中的使用者要求存取此存取套件時，可將贊助者當做核准者使用。 如需如何將來賓使用者邀請至您目錄的詳細資訊，請參閱[在 Azure 入口網站中新增 AZURE ACTIVE DIRECTORY B2B](../b2b/add-users-administrator.md)共同作業使用者。

    當您按一下 [**新增/移除**] 時，就會出現一個窗格，供您選取內部或外部贊助者。 此窗格會在您的目錄中顯示未篩選的使用者和群組清單。

    ![存取套件-原則-新增已連線的組織-贊助商索引標籤](./media/entitlement-management-organization/organization-sponsors.png)

1. 在 [**審查 + 建立**] 索引標籤上，檢查您的組織設定，然後按一下 [**建立**]。

    ![存取套件-原則-新增已連線的組織-審核 + 建立索引標籤](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>更新已連線的組織 

如果已連線的組織變更為不同的網域，如果您有該組織的新名稱，或想要變更贊助者，則可以更新已連線的組織。

**先決條件角色：** 全域管理員、使用者系統管理員或來賓邀請者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**已連接的組織**]，然後按一下以開啟 [已連線的組織]。

1. 在 [總覽] 頁面上，按一下 [**編輯**] 以變更組織名稱或描述。  

1. 在 [目錄 + 網域] 頁面上，按一下 [**更新目錄 + 網域**]，以變更為不同的目錄或網域。

1. 在 [贊助者] 頁面上，按一下 [**新增內部贊助**者] 或 [**新增外部贊助**者]，將使用者新增為贊助者。  若要移除贊助者，請按一下 [贊助者]，然後在右邊的功能表上，按一下 [**刪除**]。


## <a name="delete-a-connected-organization"></a>刪除已連線的組織

如果您不再具有外部 Azure AD 目錄或網域的關聯性，您可以刪除已連線的組織。

**先決條件角色：** 全域管理員、使用者系統管理員或來賓邀請者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**已連接的組織**]，然後按一下以開啟 [已連線的組織]。

1. 在 [總覽] 頁面上，按一下 [**刪除**] 以刪除已連線的組織。

    目前，只有在沒有已連線的使用者時，您才能刪除已連線的組織。

    ![身分識別治理-已連線的組織-刪除已連線的組織](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>後續步驟

- [管理外部使用者的存取權](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [針對不在您目錄中的使用者](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
