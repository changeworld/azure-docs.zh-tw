---
title: 在 Azure AD 權利管理中新增已連線的組織-Azure Active Directory
description: 瞭解如何允許組織外部人員要求存取套件，讓您可以在專案上共同作業。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8928e7293f184e8eb366df6a29e50cbea6a7c93
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85078193"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中新增已連線的組織

有了 Azure Active Directory （Azure AD）權利管理，您就可以與組織外部的人員共同作業。 如果您經常與外部 Azure AD 目錄或網域中的使用者共同作業，您可以將他們新增為已連線的組織。 本文說明如何新增已連線的組織，讓您的組織外部使用者可以要求您的目錄中的資源。

## <a name="what-is-a-connected-organization"></a>什麼是已連線的組織？

已連線的組織是與您有關聯性的外部 Azure AD 目錄或網域。

例如，假設您在 Woodgrove Bank 工作，而且想要與兩個外部組織共同作業。 這兩個組織有不同的設定：

- 圖形設計研究所使用 Azure AD，而且其使用者的使用者主體名稱結尾為*graphicdesigninstitute.com*。
- Contoso 尚未使用 Azure AD。 Contoso 使用者擁有以*contoso.com*結尾的使用者主體名稱。

在此情況下，您可以設定兩個已連線的組織。 您為圖形設計局建立一個已連線的組織，一個用於 Contoso。 如果您接著將兩個已連線的組織新增至原則，則每個組織中具有符合原則之使用者主體名稱的使用者，都可以要求存取封裝。 使用者主體名稱具有*graphicdesigninstitute.com*網域的使用者，會符合圖形設計協會連接的組織，並允許提交要求。 使用者主體名稱具有*contoso.com*網域的使用者，會符合 contoso 連線的組織，而且也會允許要求封裝。 此外，由於圖形設計研究所使用 Azure AD，因此任何主體名稱符合已加入其租使用者之[已驗證網域](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name)（例如*graphicdesigninstitute*）的使用者，也可以使用相同的原則來要求存取封裝。

![已連線的組織範例](./media/entitlement-management-organization/connected-organization-example.png)

Azure AD 目錄或網域驗證使用者的方式，取決於驗證類型。 已連線組織的驗證類型如下：

- Azure AD
- [直接同盟](../b2b/direct-federation.md)
- [一次性密碼](../b2b/one-time-passcode.md)（網域）

如需如何新增已連線組織的示範，請觀看下列影片：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>新增已連線的組織

若要將外部 Azure AD 目錄或網域新增為已連線的組織，請遵循本節中的指示。

必要條件**角色**：*全域管理員*、*使用者系統管理員*或*來賓邀請者*

1. 在 [Azure 入口網站中，選取 [ **Azure Active Directory**]，然後選取 [身分**識別管理**]。

1. 在左窗格中選取 [**已連線的組織**]，然後選取 [**新增已連線的組織**]。

    ![[新增已連線的組織] 按鈕](./media/entitlement-management-organization/connected-organization.png)

1. 選取 [**基本**] 索引標籤，然後輸入組織的 [顯示名稱] 和 [描述]。

    ![[新增已連線的組織] 基本窗格](./media/entitlement-management-organization/organization-basics.png)

1. 選取 [**目錄 + 網域**] 索引標籤，然後選取 [**新增目錄 + 網域**]。

    [**選取目錄 + 網域**] 窗格隨即開啟。

1. 在 [搜尋] 方塊中，輸入要搜尋 Azure AD 目錄或網域的功能變數名稱。 請務必輸入整個功能變數名稱。

1. 請確認 [組織名稱] 和 [驗證類型] 是正確的。 使用者登入的方式取決於驗證類型。

    ![[選取目錄 + 網域] 窗格](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. 選取 [**新增**] 以新增 Azure AD 目錄或網域。 目前，您只能為每個連線的組織新增一個 Azure AD 目錄或網域。

    > [!NOTE]
    > Azure AD 目錄或網域中的所有使用者都可以要求此存取封裝。 這包括從與目錄相關聯的所有子域 Azure AD 的使用者，除非這些網域被 Azure AD 企業對企業（B2B）允許或拒絕清單封鎖。 如需詳細資訊，請參閱[允許或封鎖對特定組織的 B2B 使用者的邀請](../b2b/allow-deny-list.md)。

1. 新增 Azure AD 目錄或網域之後，請選取 [**選取**]。

    組織會出現在清單中。

    ![[目錄 + 網域] 窗格](./media/entitlement-management-organization/organization-directory-domain.png)

1. 選取 [**贊助**商] 索引標籤，然後為這個已連線的組織新增選擇性贊助者。

    贊助者是已在您目錄中的內部或外部使用者，這是與此連線組織的關係點。 內部贊助者是您目錄中的成員使用者。 外部贊助者是來自已連線組織的來賓使用者，先前已受邀並已在您的目錄中。 當此連線組織中的使用者要求存取此存取套件時，可將贊助者當做核准者使用。 如需如何將來賓使用者邀請至您目錄的詳細資訊，請參閱[在 Azure 入口網站中新增 AZURE ACTIVE DIRECTORY B2B](../b2b/add-users-administrator.md)共同作業使用者。

    當您選取 [**新增/移除**] 時，就會開啟一個窗格，您可以在其中選擇內部或外部贊助者。 此窗格會在您的目錄中顯示未篩選的使用者和群組清單。

    ![[贊助者] 窗格](./media/entitlement-management-organization/organization-sponsors.png)

1. 選取 [**審查 + 建立**] 索引標籤，檢查您的組織設定，然後選取 [**建立**]。

    ![[審查 + 建立] 窗格](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>更新已連線的組織 

如果已連線的組織變更為不同的網域、組織的名稱變更，或您想要變更贊助者，您可以遵循本節中的指示來更新已連線的組織。

必要條件**角色**：*全域管理員*、*使用者系統管理員*或*來賓邀請者*

1. 在 [Azure 入口網站中，選取 [ **Azure Active Directory**]，然後選取 [身分**識別管理**]。

1. 在左窗格中選取 [**已連線的組織**]，然後選取 [已連線的組織] 以開啟它。

1. 在 [已連線的組織總覽] 窗格中，選取 [**編輯**] 以變更組織名稱或描述。  

1. 在 [**目錄 + 網域**] 窗格中，選取 [**更新目錄 + 網域**] 以變更至不同的目錄或網域。

1. 在 [**贊助**者] 窗格中，選取 [**新增內部贊助**者] 或 [**新增外部贊助**者]，將使用者新增為贊助者。 若要移除贊助者，請選取 [贊助商]，然後在右窗格中選取 [**刪除**]。


## <a name="delete-a-connected-organization"></a>刪除已連線的組織

如果您不再具有外部 Azure AD 目錄或網域的關聯性，您可以刪除已連線的組織。

必要條件**角色**：*全域管理員*、*使用者系統管理員*或*來賓邀請者*

1. 在 [Azure 入口網站中，選取 [ **Azure Active Directory**]，然後選取 [身分**識別管理**]。

1. 在左窗格中選取 [**已連線的組織**]，然後選取 [已連線的組織] 以開啟它。

1. 在已連線組織的 [總覽] 窗格中，選取 [**刪除**] 將其刪除。

    目前，只有在沒有已連線的使用者時，您才可以刪除已連線的組織。

    ![[已連線的組織刪除] 按鈕](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>後續步驟

- [管控外部使用者的存取](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [管理不在您目錄中的使用者存取權](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
