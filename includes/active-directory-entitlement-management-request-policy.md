---
title: 包含檔案
description: 包含檔案
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 03/30/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 7fd716be397d9ef6b9d6132cd4470f653f3cea0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80655962"
---
## <a name="for-users-in-your-directory"></a>適用于您目錄中的使用者

如果您想要允許目錄中的使用者能夠要求此存取套件，請遵循下列步驟。 定義要求原則時，您可以指定個別使用者，或更常用的使用者群組。 例如，您的組織可能已經有一個群組，例如 [**所有員工**]。  如果為可要求存取權的使用者在原則中新增該群組，則該群組的任何成員都可以要求存取權。

1. 在 [可要求存取的使用者]**** 區段中，按一下 [您目錄中的使用者]****。

    當您選取此選項時，會出現新的選項，以進一步精簡您的目錄中可以要求此存取封裝的人員。

    ![存取套件-要求-適用于您目錄中的使用者](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. 選取下列其中一個選項：

    |  |  |
    | --- | --- |
    | **特定使用者與群組** | 如果您只想要您指定的目錄中的使用者和群組能夠要求此存取封裝，請選擇此選項。 |
    | **所有成員（不包括來賓）** | 如果您要讓目錄中的所有成員使用者都能夠要求此存取封裝，請選擇此選項。 此選項不包含任何您可能已受邀進入目錄的來賓使用者。 |
    | **所有使用者（包括來賓）** | 如果您要讓目錄中的所有成員使用者和來賓使用者都能夠要求此存取封裝，請選擇此選項。 |

    來賓使用者會參考已受邀使用[AZURE AD B2B](../articles/active-directory/b2b/what-is-b2b.md)的目錄中的外部使用者。 如需成員使用者與來賓使用者之間差異的詳細資訊，請參閱[Azure Active Directory 中的預設使用者許可權為何？](../articles/active-directory/fundamentals/users-default-permissions.md)。

1. 如果您選取 [**特定使用者和群組**]，請按一下 [**新增使用者和群組**]。

1. 在 [選取使用者和群組] 窗格中，選取您想要新增的使用者和群組。

    ![存取套件-要求-選取使用者和群組](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. 按一下 [**選取**] 以新增使用者和群組。

1. 向下跳至 [[核准](#approval)] 區段。

## <a name="for-users-not-in-your-directory"></a>針對不在您目錄中的使用者

 **不在您目錄中的使用者**是指位於另一個 Azure AD 目錄或網域中的使用者。 這些使用者可能尚未被邀請到您的目錄。 Azure AD 目錄必須設定為允許共同作業**限制**中的邀請。 如需詳細資訊，請參閱[啟用 B2B 外部共同作業和管理可以邀請來賓的人員](../articles/active-directory/b2b/delegate-invitations.md)。

> [!NOTE]
> 系統會針對尚未在您的目錄中核准或自動核准要求的使用者，建立來賓使用者帳戶。 系統會邀請來賓，但不會收到邀請電子郵件。 相反地，他們會在其存取套件指派傳遞時收到電子郵件。 根據預設，當該來賓使用者不再具有任何存取套件指派時，由於其上次指派已過期或已取消，所以該來賓使用者帳戶將會遭到封鎖而無法登入，且後續將會刪除。 如果您想要讓來賓使用者無限期地保留在您的目錄中，即使他們沒有任何存取套件指派，您也可以變更您的權利管理設定。 如需有關來賓使用者物件的詳細資訊，請參閱[AZURE ACTIVE DIRECTORY B2B 共同作業使用者的屬性](../articles/active-directory/b2b/user-properties.md)。

如果您想要讓不在您目錄中的使用者要求此存取套件，請遵循下列步驟：

1. 在 [**可以要求存取權的使用者**] 區段中，按一下 [**針對不在您的目錄中的使用者**]。

    當您選取此選項時，會出現新的選項。

    ![存取套件-要求-適用于不在您目錄中的使用者](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. 選取下列其中一個選項：

    |  |  |
    | --- | --- |
    | **特定的已連線組織** | 如果您想要從系統管理員先前新增的組織清單中選取，請選擇此選項。 來自所選組織的所有使用者都可以要求此存取封裝。 |
    | **所有已連線的組織** | 如果所有已連線組織中的所有使用者都可以要求此存取套件，請選擇此選項。 |
    | **所有使用者（所有已連線的組織 + 任何新的外部使用者）** | 如果所有已連線組織中的所有使用者都可以要求此存取套件，而且 B2B 允許或拒絕清單設定應優先于任何新的外部使用者，請選擇此選項。 |

    已連線的組織是與您有關聯性的外部 Azure AD 目錄或網域。

1. 如果您選取了 [**特定的連線組織**]，請按一下 [**新增目錄**]，從您的系統管理員先前新增的已連線組織清單中選取。

1. 輸入 [名稱] 或 [功能變數名稱]，以搜尋先前連線的組織。

    ![存取套件-要求-選取目錄](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    如果您想要與其共同作業的組織不在清單中，您可以要求系統管理員將其新增為已連線的組織。 如需詳細資訊，請參閱[新增已連線的組織](../articles/active-directory/governance/entitlement-management-organization.md)。

1. 選取所有已連線的組織之後，請按一下 [**選取**]。

    > [!NOTE]
    > 來自所選已連線組織的所有使用者都將能夠要求此存取封裝。 這包括從與組織相關聯的所有子域 Azure AD 的使用者，除非 Azure B2B 允許或拒絕清單已封鎖這些網域。 如需詳細資訊，請參閱[允許或封鎖對特定組織的 B2B 使用者的邀請](../articles/active-directory/b2b/allow-deny-list.md)。

1. 向下跳至 [[核准](#approval)] 區段。

## <a name="none-administrator-direct-assignments-only"></a>無（僅限系統管理員直接指派）

如果您想要略過存取要求，並允許系統管理員直接將特定使用者指派給此存取套件，請遵循下列步驟。 使用者不需要要求存取封裝。 您仍然可以設定生命週期設定，但不會有任何要求設定。

1. 在 [**可要求存取的使用者**] 區段中，按一下 [**無] （僅限系統管理員直接指派**。

    ![存取套件-要求-無系統管理員直接指派](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    建立存取套件之後，您可以直接將特定的內部和外部使用者指派給存取封裝。 如果您指定外部使用者，將會在您的目錄中建立來賓使用者帳戶。 如需直接指派使用者的相關資訊，請參閱[View、add 和 remove access package 的指派](../articles/active-directory/governance/entitlement-management-access-package-assignments.md)。

1. 向下跳至 [[啟用要求](#enable-requests)] 區段。

## <a name="approval"></a>核准

在 [核准] 區段中，您可以指定使用者要求此存取套件時是否需要核准。 核准設定的工作方式如下：

- 只有一個選取的核准者或回溯核准者需要核准單一階段核准的要求。 
- 每個階段中只有一個選取的核准者需要核准2階段核准的要求。
- 核准者可以是經理、內部贊助商或外部贊助商，視原則負責存取的物件而定。
- 對每個選取的核准者進行核准，並不需要進行單一或2階段核准。
- 核准決策是以第一次審核要求的核准者為基礎。

如需如何將核准者新增至要求原則的示範，請觀看下列影片：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

如需如何將多階段核准新增至要求原則的示範，請觀看下列影片：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

請遵循下列步驟來指定存取封裝要求的核准設定：

1. 若要要求核准所選使用者的要求，請將 [**需要核准**] 切換設定為 **[是]**。 或者，若要自動核准要求，請將切換設定為 [**否**]。

1. 若要要求使用者提供理由來要求存取封裝，請將 [**需要要求者理由**] 切換為 **[是]**。
    
1. 現在判斷要求是否需要單一或2階段核准。 設定**多個階段**切換為**1**以進行單一階段核准，或將2階段核准的切換設定為**2** 。

    ![存取套件-要求-核准設定](./media/active-directory-entitlement-management-request-policy/approval.png)

選取您需要多少階段之後，請使用下列步驟來新增核准者： 

### <a name="single-stage-approval"></a>單一階段核准

1. 新增**第一個核准者**：
    
    如果原則設定為管理目錄中使用者的存取權，您可以選取 [**管理員] 作為 [核准者**]。 或者，在選取下拉式功能表中的 [選擇特定核准者] 後，按一下 [**新增核准**者] 來新增特定的使用者。
    
    ![存取套件-要求-針對目錄優先核准者中的使用者](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    如果此原則設定為管理不在您目錄中的使用者存取權，您可以選取 [**外部贊助商**] 或 [**內部贊助**者]。 或者，按一下 [選擇特定核准者] 底下的 [**新增核准者**或群組]，以新增特定的使用者。
    
    ![存取套件-要求-使用者超出目錄優先核准者](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. 如果您已選取 [**管理員**] 做為第一個核准者，請按一下 [**新增**回溯]，在您的目錄中選取一或多個使用者或群組做為回溯核准者。 如果權利管理找不到要求存取權之使用者的管理員，則回溯核准者會收到要求。

    管理員是由使用**manager**屬性的權利管理找到。 屬性位於使用者設定檔的 Azure AD 中。 如需詳細資訊，請參閱[使用 Azure Active Directory 新增或更新使用者的設定檔資訊](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md)。

1. 如果您選取 **[選擇特定核准者**]，請按一下 [**新增核准**者]，在您的目錄中選取核准者的一或多個使用者或群組。

1. 在 [必須在**幾天內進行決策**] 底下的方塊中，指定核准者審查此存取套件要求所需的天數。

    如果要求未在這段時間內核准，則會自動拒絕。 使用者必須提交存取套件的另一個要求。

1. 若要要求核准者提供其決策的理由，請將 [需要核准者理由] 設定為 **[是]**。

    其他核准者和要求者可以看到此理由。

### <a name="2-stage-approval"></a>2階段核准

如果您選取了2階段核准，則必須新增第二個核准者。

1. 新增**第二個核准者**： 
    
    如果使用者在您的目錄中，請按一下 [選擇特定核准者] 底下的 [**新增核准**者]，將特定使用者新增為第二個核准者。

    ![存取套件-要求-針對目錄-第二核准者的使用者](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    如果使用者不在您的目錄中，請選取 [**內部贊助商**] 或 [**外部贊助商**] 作為第二個核准者 選取核准者之後，請新增 fallback 核准者。

    ![存取套件-要求-使用者超出目錄-第二核准者](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. 指定第二核准者核准要求的天數，在 [**決定必須在多少天內完成？**] 方塊中。 

1. 將 [需要核准者理由] 切換為 **[是]** 或 [**否**]。

### <a name="alternate-approvers"></a>替代核准者

您可以指定替代的核准者，類似于指定可核准要求的第一個和第二個核准者。 擁有替代的核准者將有助於確保要求會在到期之前核准或拒絕（timeout）。 您可以列出第一個核准者和第二個核准者進行2階段核准的替代核准者。 

藉由指定替代核准者，當第一或第二個核准者無法核准或拒絕要求時，就會根據您在原則設定期間指定的轉送排程，將擱置的要求轉寄給替代的核准者。 他們會收到電子郵件，以核准或拒絕擱置中的要求。

將要求轉送到替代核准者之後，第一個或第二個核准者仍然可以核准或拒絕要求。 替代核准者會使用相同的我的存取權網站來核准或拒絕擱置中的要求。

我們可以向核准者和替代核准者列出人員或群組。 請確定您列出不同的人員集合，使其成為第一個、第二個和替代的核准者。
例如，如果您將 Alice 和 Bob 列為第一個核准者，請將 Carol 和 Dave 列出為替代的核准者。 使用下列步驟，將替代核准者新增至存取套件：

1. 在第一個核准者、第二個核准者或兩者底下，按一下 [**顯示先進的要求設定**]。

    ![存取套件-原則-顯示 advanced 要求設定](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. 設定**是否未採取任何動作，轉寄給替代核准者？** 切換為 **[是]**。

1. 按一下 [**新增替代核准者**]，並從清單中選取替代核准者。

    ![存取套件-原則-新增替代的核准者](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. 在 [在**幾天後向前前往替代核准者**] 方塊中，放入核准者核准或拒絕要求的天數。 如果沒有核准者在要求期間之前核准或拒絕要求，則要求會過期（超時），而且使用者必須提交存取封裝的另一個要求。 

    在要求持續時間達到半生命後，才可將要求轉寄給替代核准者一天。 在此範例中，要求的持續時間為14天。 因此，要求持續時間到達第7天的半生命週期。 因此，要求無法轉送早于第8天。 此外，要求不能在要求持續時間的最後一天轉送。 因此，在此範例中，最新的要求可以轉送為第13天。

## <a name="enable-requests"></a>啟用要求

1. 如果您想要讓存取套件立即提供給要求原則中的使用者要求，請將 [啟用] 切換為 **[是]**。

    在您完成建立存取套件之後，隨時都可以在未來啟用它。

    如果您選取 **[無] （僅限系統管理員直接指派）** ，並將 [啟用] 設定為 [**否**]，則系統管理員無法直接指派此存取套件。

    ![存取套件-原則-啟用原則設定](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. 按 [下一步] 。
