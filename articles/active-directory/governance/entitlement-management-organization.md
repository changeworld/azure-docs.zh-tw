---
title: 在 Azure AD 權利管理中新增已連線的組織-Azure Active Directory
description: 瞭解如何允許組織外部的人員要求存取套件，以便您可以在專案上共同作業。
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
ms.date: 09/28/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96106cc1d9f9040f98c7d9201f05b4cff87af7e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449908"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中新增已連線的組織

有了 Azure Active Directory (Azure AD) 權利管理，您就可以與組織外的人員共同作業。 如果您經常與外部 Azure AD 目錄或網域中的使用者共同作業，您可以將他們新增為已連線的組織。 本文說明如何新增已連線的組織，讓您可以讓組織外部的使用者要求您的目錄中的資源。

## <a name="what-is-a-connected-organization"></a>什麼是已連線的組織？

已連線的組織是與您關聯的外部 Azure AD 目錄或網域。

例如，假設您任職于 Woodgrove Bank，而您想要與兩個外部組織共同作業。 這兩個組織有不同的設定：

- 圖形設計局使用 Azure AD，其使用者的使用者主要名稱以 *graphicdesigninstitute.com*結尾。
- Contoso 還不會使用 Azure AD。 Contoso 使用者的使用者主體名稱以 *contoso.com*結尾。

在此情況下，您可以設定兩個已連線的組織。 您可以針對圖形設計協會和 Contoso 建立一個已連線的組織。 如果您接著將兩個已連線的組織新增至原則，則具有符合原則之使用者主體名稱的每個組織使用者都可以要求存取套件。 具有 *graphicdesigninstitute.com* 網域之使用者主體名稱的使用者，會符合圖形設計協會連接的組織，並允許提交要求。 具有 *contoso.com* 網域之使用者主體名稱的使用者，會符合 contoso 連接的組織，也允許要求套件。 此外，由於圖形設計協會會使用 Azure AD，具有主體名稱的任何使用者（例如 graphicdesigninstitute）都符合 [已驗證的網域](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) （例如， * *），也可以使用相同的原則來要求存取套件。

![已連線的組織範例](./media/entitlement-management-organization/connected-organization-example.png)

Azure AD 目錄或網域驗證的使用者取決於驗證類型。 已連線組織的驗證類型如下：

- Azure AD
- [直接同盟](../external-identities/direct-federation.md)
- 單次[密碼](../external-identities/one-time-passcode.md) (網域) 

如需如何新增已連線組織的示範，請觀看下列影片：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>新增已連線的組織

若要將外部 Azure AD 目錄或網域新增為已連線的組織，請遵循本節中的指示。

**先決條件角色**： *全域管理員* 或 *使用者管理員*

1. 在 [Azure 入口網站中，選取 [ **Azure Active Directory**]，然後選取 [身分 **識別管理**]。

1. 在左窗格中，選取 [ **已連線的組織**]，然後選取 [ **新增已連線的組織**]。

    ![[新增已連線的組織] 按鈕](./media/entitlement-management-organization/connected-organization.png)

1. 選取 [ **基本** ] 索引標籤，然後輸入組織的顯示名稱和描述。

    ![[新增已連線的組織] 基本窗格](./media/entitlement-management-organization/organization-basics.png)

1. 當您建立新的已連線組織時，狀態會自動設定為 [ **已** 設定]。 如需狀態屬性的詳細資訊，請參閱 [已連線組織的狀態屬性](#state-properties-of-connected-organizations)

1. 選取 [ **目錄 + 網域** ] 索引標籤，然後選取 [ **新增目錄 + 網域**]。

    [ **選取目錄 + 網域** ] 窗格隨即開啟。

1. 在搜尋方塊中，輸入功能變數名稱以搜尋 Azure AD 目錄或網域。 請務必輸入完整的功能變數名稱。

1. 確認組織名稱和驗證類型是否正確。 使用者登入的方式取決於驗證類型。

    ![[選取目錄 + 網域] 窗格](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. 選取 [ **新增** ] 以新增 Azure AD 目錄或網域。 目前，您只能為每個已連線的組織新增一個 Azure AD 目錄或網域。

    > [!NOTE]
    > Azure AD 目錄或網域中的所有使用者都可以要求此存取套件。 這包括從與目錄相關聯的所有子域 Azure AD 的使用者，除非這些網域被 Azure AD 企業對企業 (B2B) 允許或拒絕清單。 如需詳細資訊，請參閱[允許或封鎖對特定組織的 B2B 使用者的邀請](../external-identities/allow-deny-list.md)。

1. 新增 Azure AD 目錄或網域之後，請選取 [ **選取**]。

    組織會出現在清單中。

    ![[目錄 + 網域] 窗格](./media/entitlement-management-organization/organization-directory-domain.png)

1. 選取 [ **贊助** 者] 索引標籤，然後為這個已連線的組織新增選用的贊助者。

    贊助者是您目錄中已存在的內部或外部使用者，也就是與這個已連線組織關聯性的聯繫點。 內部贊助者是您目錄中的成員使用者。 外部贊助者是來自先前受邀且已在您目錄中的已連線組織的來賓使用者。 當此已連線組織中的使用者要求存取此存取套件時，可使用贊助者作為核准者。 如需如何將來賓使用者邀請到您目錄的詳細資訊，請參閱 [Azure 入口網站中的新增 AZURE ACTIVE DIRECTORY B2B](../external-identities/add-users-administrator.md)共同作業使用者。

    當您選取 [ **新增/移除**] 時，會開啟一個窗格，您可以在其中選擇內部或外部的贊助者。 此窗格會在您的目錄中顯示未篩選的使用者和群組清單。

    ![[贊助者] 窗格](./media/entitlement-management-organization/organization-sponsors.png)

1. 選取 [ **審核 + 建立** ] 索引標籤，檢查您的組織設定，然後選取 [ **建立**]。

    ![[審核 + 建立] 窗格](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>更新已連線的組織 

如果連線的組織變更為不同的網域、組織的名稱變更，或您想要變更贊助者，則可以遵循本節中的指示來更新已連線的組織。

**先決條件角色**： *全域管理員* 或 *使用者管理員*

1. 在 [Azure 入口網站中，選取 [ **Azure Active Directory**]，然後選取 [身分 **識別管理**]。

1. 在左窗格中，選取 [ **已連線的組織**]，然後選取已連線的組織以開啟它。

1. 在 [已連線組織的總覽] 窗格中，選取 [ **編輯** ] 來變更組織名稱、描述或狀態。  

1. 在 [ **目錄 + 網域** ] 窗格中，選取 [ **更新目錄 + 網域** ] 以變更至不同的目錄或網域。

1. 在 [ **贊助** 者] 窗格中，選取 [ **新增內部贊助** 者] 或 [ **新增外部贊助** 者]，將使用者新增為贊助者。 若要移除贊助者，請選取贊助者，然後在右窗格中選取 [ **刪除**]。


## <a name="delete-a-connected-organization"></a>刪除已連線的組織

如果您不再有與外部 Azure AD 目錄或網域的關聯性，您可以刪除已連線的組織。

**先決條件角色**： *全域管理員* 或 *使用者管理員*

1. 在 [Azure 入口網站中，選取 [ **Azure Active Directory**]，然後選取 [身分 **識別管理**]。

1. 在左窗格中，選取 [ **已連線的組織**]，然後選取已連線的組織以開啟它。

1. 在已連線的組織的 [總覽] 窗格中，選取 [ **刪除** ] 以刪除它。

    目前，只有在沒有任何已連線的使用者時，您才能刪除已連線的組織。

    ![[已連線的組織刪除] 按鈕](./media/entitlement-management-organization/organization-delete.png)

## <a name="managing-a-connected-organization-programmatically"></a>以程式設計方式管理已連線的組織

您也可以使用 Microsoft Graph 來建立、列出、更新及刪除已連線的組織。 具有具有委派許可權之應用程式的適當角色使用者 `EntitlementManagement.ReadWrite.All` 可以呼叫 API 來管理 [connectedOrganization](/graph/api/resources/connectedorganization?view=graph-rest-beta) 物件，並為其設定贊助者。

## <a name="state-properties-of-connected-organizations"></a>已連線組織的狀態屬性

目前已設定和建議的 Azure AD 權利管理中，已連線組織有兩種不同的狀態屬性類型： 

- 設定的已連線組織是功能完整的連線組織，可讓該組織內的使用者存取套件。 當系統管理員在 Azure 入口網站中建立新的已連線組織時，預設會處於 **設定** 狀態，因為系統管理員建立並想要使用此連線的組織。 此外，當透過 API 以程式設計方式建立已連線的組織時，除非明確設定為另一個狀態，否則 **應設定預設** 狀態。 

    已設定的已連線組織會顯示在已連線組織的選擇器中，而且將會在以「所有」已連線組織為目標的任何原則範圍內。

- 建議的已連線組織是已自動建立，但尚未讓系統管理員建立或核准組織的已連線組織。 當使用者在已設定的已連線組織之外註冊存取套件時，任何自動建立的已連線組織都會處於「 **建議** 」狀態，因為租使用者中的系統管理員不會設定該合作關係。 
    
    建議的已連線組織不會顯示在已設定連線組織的選擇器中，而且不在任何原則的 [所有已設定的已連線組織] 設定範圍內。 

只有已設定的已連線組織使用者可以要求可供所有已設定組織的使用者使用的存取套件。 來自建議連線組織的使用者有經驗，就像該網域沒有任何已連線的組織一樣，而且在系統管理員變更狀態之前無法存取存取套件。

> [!NOTE]
> 在推出這項新功能的過程中，在09/09/20 之前建立的所有已連線組織都被視為 **已設定**。 如果您的存取套件允許來自任何組織的使用者註冊，您應該檢查在該日期之前建立的已連線組織清單，以確保未依 **設定**miscategorized。  系統管理員可以適當地更新 **狀態** 屬性。 如需指引，請參閱 [更新已連線的組織](#update-a-connected-organization)。

## <a name="next-steps"></a>後續步驟

- [管控外部使用者的存取](./entitlement-management-external-users.md)
- [針對不在您目錄中的使用者管理存取權](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
