---
title: 在 Azure AD 權利管理中變更存取套件的要求設定-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 權利管理中變更存取套件的要求設定。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 160137ca2d14e0012a524ee0818b7fb269fa7984
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90980270"
---
# <a name="change-request-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中變更存取套件的要求設定

身為存取套件管理員，您可以藉由編輯原則或新增原則，隨時變更可要求存取套件的使用者。 本文說明如何變更現有存取套件的要求設定。

## <a name="choose-between-one-or-multiple-policies"></a>在一或多個原則之間進行選擇

您指定誰可以要求存取套件的方式，就是使用原則。 在建立新原則或編輯存取套件中現有的原則之前，您必須決定存取套件需要多少原則。 

當您建立存取套件時，會指定建立原則的要求設定。 大部分的存取套件會有單一原則，但單一存取套件可以有多個原則。 如果您想要允許不同組的使用者被授與不同的要求和核准設定，則會針對存取套件建立多個原則。 

例如，單一原則不能用來將內部和外部使用者指派給相同的存取套件。 不過，您可以在相同的存取套件中建立兩個原則，一個供內部使用者使用，另一個供外部使用者使用。 如果有多個原則套用至使用者，則會在其要求時提示他們選取想要指派給他們的原則。 下圖顯示具有兩個原則的存取套件。

![存取套件中的多個原則](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

### <a name="how-many-policies-will-i-need"></a>我將需要多少原則？

| 狀況 | 原則數目 |
| --- | --- |
| 我希望目錄中的所有使用者都有存取套件的相同要求和核准設定 | 一個 |
| 我希望特定已連線組織中的所有使用者都能夠要求存取套件 | 一個 |
| 我想要允許目錄中的使用者，以及目錄外的使用者要求存取套件 | 多個 |
| 我想為某些使用者指定不同的核准設定 | 多個 |
| 我希望某些使用者存取套件指派到期，而其他使用者可以擴充其存取權 | 多個 |

如需套用多項原則時所使用之優先順序邏輯的詳細資訊，請參閱 [多個原則](entitlement-management-troubleshoot.md#multiple-policies
)。

## <a name="open-an-existing-access-package-and-add-a-new-policy-of-request-settings"></a>開啟現有的存取套件，並新增要求設定的新原則

如果您有一組應該具有不同要求和核准設定的使用者，您可能需要建立新的原則。 請遵循下列步驟，開始將新的原則新增至現有的存取套件：

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [ **存取套件** ]，然後開啟存取套件。

1. 按一下 [ **原則** ]，然後 **新增原則**。

1. 您將從 [ **基本** ] 索引標籤開始。輸入原則的名稱和描述。

    ![建立具有名稱和描述的原則](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. 按 [下一步]  以開啟 [要求]  索引標籤。

1. 變更 **可要求存取** 設定的使用者。 您可以使用以下各節中的步驟，將設定變更為下列其中一個選項： 
    - [適用于您目錄中的使用者](#for-users-in-your-directory) 
    - [針對不在您目錄中的使用者](#for-users-not-in-your-directory)
    - [無 (系統管理員直接指派) ](#none-administrator-direct-assignments-only)

## <a name="for-users-in-your-directory"></a>適用于您目錄中的使用者

如果您想要允許您目錄中的使用者能夠要求此存取套件，請遵循下列步驟。 定義要求原則時，您可以指定個別使用者或更常用的使用者群組。 例如，您的組織可能已經有一個群組，例如 [ **所有員工**]。  如果在可要求存取權的使用者的原則中新增該群組，則該群組的任何成員都可以要求存取權。

1. 在 [可要求存取的使用者]**** 區段中，按一下 [您目錄中的使用者]****。

    當您選取此選項時，會出現新的選項，以進一步精簡您目錄中的誰可以要求此存取套件。

    ![存取套件-要求-適用于您目錄中的使用者](./media/entitlement-management-access-package-request-policy/for-users-in-your-directory.png)

1. 選取下列其中一個選項：

    |  |  |
    | --- | --- |
    | **特定使用者與群組** | 如果您只想要指定的目錄中的使用者和群組可以要求此存取套件，請選擇此選項。 |
    | ** (不包括來賓的所有成員) ** | 如果您希望目錄中的所有成員使用者都能夠要求此存取套件，請選擇此選項。 此選項不包含您可能已邀請到目錄中的任何來賓使用者。 |
    | **包括來賓在內的所有使用者 () ** | 如果您希望目錄中的所有成員使用者和來賓使用者都能夠要求此存取套件，請選擇此選項。 |

    來賓使用者指的是使用 [AZURE AD B2B](../external-identities/what-is-b2b.md)邀請到您目錄中的外部使用者。 如需成員使用者和來賓使用者之間差異的詳細資訊，請參閱 [Azure Active Directory 中的預設使用者許可權為何？](../fundamentals/users-default-permissions.md)。

1. 如果您選取 [ **特定使用者和群組**]，請按一下 [ **新增使用者和群組**]。

1. 在 [選取使用者和群組] 窗格中，選取您要新增的使用者和群組。

    ![存取套件-要求-選取使用者和群組](./media/entitlement-management-access-package-request-policy/select-users-groups.png)

1. 按一下 [ **選取** ] 以新增使用者和群組。

1. 如果您想要要求核准，請使用 [Azure AD 權利管理] 中的 [存取套件的變更核准設定](entitlement-management-access-package-approval-policy.md) ] 中的步驟來設定核准設定。

1. 移至 [ [啟用要求](#enable-requests) ] 區段。
 
## <a name="for-users-not-in-your-directory"></a>針對不在您目錄中的使用者

 **不在您目錄中的使用者** 是指位於另一個 Azure AD 目錄或網域中的使用者。 這些使用者可能尚未受邀進入您的目錄。 Azure AD 目錄必須設定為允許共同作業 **限制**中的邀請。 如需詳細資訊，請參閱 [啟用 B2B 外部共同作業及管理可邀請來賓的人員](../external-identities/delegate-invitations.md)。

> [!NOTE]
> 系統會為尚未在您的目錄中要求核准或自動核准的使用者建立來賓使用者帳戶。 系統會邀請來賓，但不會收到邀請電子郵件。 相反地，他們會在傳遞存取套件指派時收到電子郵件。 根據預設，稍後當來賓使用者不再具有任何存取套件指派時，因為其最後一個指派已過期或已取消，該來賓使用者帳戶將會遭到封鎖而無法登入，並會在之後刪除該帳戶。 如果您想要讓來賓使用者無限期地保留在您的目錄中，即使他們沒有存取套件指派，您也可以變更您權利管理設定的設定。 如需來賓使用者物件的詳細資訊，請參閱 [AZURE ACTIVE DIRECTORY B2B 共同作業使用者的屬性](../external-identities/user-properties.md)。

如果您想要讓不在您目錄中的使用者要求此存取套件，請遵循下列步驟：

1. 在 [ **可要求存取的使用者** ] 區段中，按一下 [ **不在您目錄中的使用者**]。

    當您選取此選項時，會出現新的選項。

    ![存取套件-要求-適用于不在您目錄中的使用者](./media/entitlement-management-access-package-request-policy/for-users-not-in-your-directory.png)

1. 選取下列其中一個選項：

    |  |  |
    | --- | --- |
    | **特定的已連線組織** | 如果您想要從系統管理員先前新增的組織清單中選取此選項，請選擇此選項。 所選組織中的所有使用者都可以要求此存取套件。 |
    | **所有已連線的組織** | 如果所有已連線組織中的所有使用者都可以要求此存取套件，請選擇此選項。 |
    | **所有已連線組織 (的所有使用者，以及任何新的外部使用者) ** | 如果所有已連線組織中的所有使用者都可以要求此存取套件，且 B2B 允許或拒絕清單設定應優先于任何新的外部使用者，請選擇此選項。 |

    已連線的組織是與您關聯的外部 Azure AD 目錄或網域。

1. 如果您選取了 [ **特定連接的組織**]，請按一下 [ **新增目錄** ]，從系統管理員先前新增的已連線組織清單中選取。

1. 輸入名稱或功能變數名稱，以搜尋先前連接的組織。

    ![存取套件-要求-選取目錄](./media/entitlement-management-access-package-request-policy/select-directories.png)

    如果您想要與其共同作業的組織不在清單中，您可以要求系統管理員將它新增為已連線的組織。 如需詳細資訊，請參閱 [加入已連線的組織](entitlement-management-organization.md)。

1. 選取所有已連線的組織之後，請按一下 [ **選取**]。

    > [!NOTE]
    > 所選已連線組織中的所有使用者都可以要求此存取套件。 這包括來自與組織相關聯之所有子域的 Azure AD 使用者，除非 Azure B2B 允許或拒絕清單封鎖這些網域。 如需詳細資訊，請參閱[允許或封鎖對特定組織的 B2B 使用者的邀請](../external-identities/allow-deny-list.md)。

1. 如果您想要要求核准，請使用 [Azure AD 權利管理] 中的 [存取套件的變更核准設定](entitlement-management-access-package-approval-policy.md) ] 中的步驟來設定核准設定。
 
1. 移至 [ [啟用要求](#enable-requests) ] 區段。

## <a name="none-administrator-direct-assignments-only"></a>無 (系統管理員直接指派) 

如果您想要略過存取要求，並允許系統管理員直接將特定使用者指派給此存取套件，請遵循下列步驟。 使用者不需要要求存取套件。 您仍然可以設定生命週期設定，但沒有任何要求設定。

1. 在 [ **可要求存取的使用者** ] 區段中，按一下 [ **無 (系統管理員直接指派**]。

    ![存取套件-要求-僅限無系統管理員直接指派](./media/entitlement-management-access-package-request-policy/none-admin-direct-assignments-only.png)

    建立存取套件之後，您可以將特定的內部和外部使用者直接指派給存取套件。 如果您指定外部使用者，將會在您的目錄中建立來賓使用者帳戶。 如需直接指派使用者的相關資訊，請參閱 [查看、新增及移除存取套件的指派](entitlement-management-access-package-assignments.md)。

1. 跳至 [ [啟用要求](#enable-requests) ] 區段。


## <a name="open-and-edit-an-existing-policy-of-request-settings"></a>開啟和編輯現有的要求設定原則

若要變更存取套件的要求和核准設定，您需要開啟對應的原則。 遵循下列步驟來開啟和編輯存取套件的要求設定：

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [ **存取套件** ]，然後開啟存取套件。

1. 按一下 [ **原則** ]，然後按一下您要編輯的原則。

    [原則詳細資料] 窗格會在頁面底部開啟。

    ![存取套件-原則詳細資料窗格](./media/entitlement-management-shared/policy-details.png)

1. 按一下 [ **編輯** ] 以編輯原則。

    ![存取套件-編輯原則](./media/entitlement-management-shared/policy-edit.png)

1. 按一下 [ **要求** ] 索引標籤，以開啟要求設定。

1. 使用上一節中的步驟，視需要變更要求設定。

1. 移至 [ [啟用要求](#enable-requests) ] 區段。

## <a name="enable-requests"></a>啟用要求

1. 如果您想要讓存取套件立即可供要求原則中的使用者使用，請將 [啟用] 切換為 **[是]**。

    當您完成建立存取套件之後，隨時都可以在未來啟用它。

    如果您選取 [ **無] (系統管理員直接指派) ** 而且您將 [啟用] 設定為 [ **否**]，則系統管理員無法直接指派此存取套件。

    ![存取套件-原則-啟用原則設定](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. 按 [下一步]  。

1. 如果您想要要求要求者在要求存取套件的存取權時提供其他資訊，請使用中的步驟 []() 來設定要求者資訊 (預覽) 。

1. 設定生命週期設定。

1. 如果您正在編輯原則，請按一下 [ **更新**]。 如果您要新增原則，請按一下 [ **建立**]。

## <a name="next-steps"></a>下一步

- [變更存取套件的核准設定](entitlement-management-access-package-approval-policy.md)
- [變更存取套件的生命週期設定](entitlement-management-access-package-lifecycle-policy.md)
- [查看存取套件的要求](entitlement-management-access-package-requests.md)