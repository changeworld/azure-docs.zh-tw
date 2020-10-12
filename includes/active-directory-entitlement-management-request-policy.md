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
ms.openlocfilehash: 12c9b1226e3ba928a4062049c7839d4e46ef727d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025273"
---
## <a name="for-users-in-your-directory"></a>適用于您目錄中的使用者

如果您想要允許您目錄中的使用者能夠要求此存取套件，請遵循下列步驟。 定義要求原則時，您可以指定個別使用者或更常用的使用者群組。 例如，您的組織可能已經有一個群組，例如 [ **所有員工**]。  如果在可要求存取權的使用者的原則中新增該群組，則該群組的任何成員都可以要求存取權。

1. 在 [可要求存取的使用者]**** 區段中，按一下 [您目錄中的使用者]****。

    當您選取此選項時，會出現新的選項，以進一步精簡您目錄中的誰可以要求此存取套件。

    ![存取套件-要求-適用于您目錄中的使用者](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. 選取下列其中一個選項：

    |  |  |
    | --- | --- |
    | **特定使用者與群組** | 如果您只想要指定的目錄中的使用者和群組可以要求此存取套件，請選擇此選項。 |
    | ** (不包括來賓的所有成員) ** | 如果您希望目錄中的所有成員使用者都能夠要求此存取套件，請選擇此選項。 此選項不包含您可能已邀請到目錄中的任何來賓使用者。 |
    | **包括來賓在內的所有使用者 () ** | 如果您希望目錄中的所有成員使用者和來賓使用者都能夠要求此存取套件，請選擇此選項。 |

    來賓使用者指的是使用 [AZURE AD B2B](../articles/active-directory/b2b/what-is-b2b.md)邀請到您目錄中的外部使用者。 如需成員使用者和來賓使用者之間差異的詳細資訊，請參閱 [Azure Active Directory 中的預設使用者許可權為何？](../articles/active-directory/fundamentals/users-default-permissions.md)。

1. 如果您選取 [ **特定使用者和群組**]，請按一下 [ **新增使用者和群組**]。

1. 在 [選取使用者和群組] 窗格中，選取您要新增的使用者和群組。

    ![存取套件-要求-選取使用者和群組](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. 按一下 [ **選取** ] 以新增使用者和群組。

1. 跳到 [ [核准](#approval) ] 區段。

## <a name="for-users-not-in-your-directory"></a>針對不在您目錄中的使用者

 **不在您目錄中的使用者** 是指位於另一個 Azure AD 目錄或網域中的使用者。 這些使用者可能尚未受邀進入您的目錄。 Azure AD 目錄必須設定為允許共同作業 **限制**中的邀請。 如需詳細資訊，請參閱 [啟用 B2B 外部共同作業及管理可邀請來賓的人員](../articles/active-directory/b2b/delegate-invitations.md)。

> [!NOTE]
> 系統會為尚未在您的目錄中要求核准或自動核准的使用者建立來賓使用者帳戶。 系統會邀請來賓，但不會收到邀請電子郵件。 相反地，他們會在傳遞存取套件指派時收到電子郵件。 根據預設，稍後當來賓使用者不再具有任何存取套件指派時，因為其最後一個指派已過期或已取消，該來賓使用者帳戶將會遭到封鎖而無法登入，並會在之後刪除該帳戶。 如果您想要讓來賓使用者無限期地保留在您的目錄中，即使他們沒有存取套件指派，您也可以變更您權利管理設定的設定。 如需來賓使用者物件的詳細資訊，請參閱 [AZURE ACTIVE DIRECTORY B2B 共同作業使用者的屬性](../articles/active-directory/b2b/user-properties.md)。

如果您想要讓不在您目錄中的使用者要求此存取套件，請遵循下列步驟：

1. 在 [ **可要求存取的使用者** ] 區段中，按一下 [ **不在您目錄中的使用者**]。

    當您選取此選項時，會出現新的選項。

    ![存取套件-要求-適用于不在您目錄中的使用者](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. 選取下列其中一個選項：

    |  |  |
    | --- | --- |
    | **特定的已連線組織** | 如果您想要從系統管理員先前新增的組織清單中選取此選項，請選擇此選項。 所選組織中的所有使用者都可以要求此存取套件。 |
    | **所有已連線的組織** | 如果所有已連線組織中的所有使用者都可以要求此存取套件，請選擇此選項。 |
    | **所有已連線組織 (的所有使用者，以及任何新的外部使用者) ** | 如果所有已連線組織中的所有使用者都可以要求此存取套件，且 B2B 允許或拒絕清單設定應優先于任何新的外部使用者，請選擇此選項。 |

    已連線的組織是與您關聯的外部 Azure AD 目錄或網域。

1. 如果您選取了 [ **特定連接的組織**]，請按一下 [ **新增目錄** ]，從系統管理員先前新增的已連線組織清單中選取。

1. 輸入名稱或功能變數名稱，以搜尋先前連接的組織。

    ![存取套件-要求-選取目錄](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    如果您想要與其共同作業的組織不在清單中，您可以要求系統管理員將它新增為已連線的組織。 如需詳細資訊，請參閱 [加入已連線的組織](../articles/active-directory/governance/entitlement-management-organization.md)。

1. 選取所有已連線的組織之後，請按一下 [ **選取**]。

    > [!NOTE]
    > 所選已連線組織中的所有使用者都可以要求此存取套件。 這包括來自與組織相關聯之所有子域的 Azure AD 使用者，除非 Azure B2B 允許或拒絕清單封鎖這些網域。 如需詳細資訊，請參閱[允許或封鎖對特定組織的 B2B 使用者的邀請](../articles/active-directory/b2b/allow-deny-list.md)。

1. 跳到 [ [核准](#approval) ] 區段。

## <a name="none-administrator-direct-assignments-only"></a>無 (系統管理員直接指派) 

如果您想要略過存取要求，並允許系統管理員直接將特定使用者指派給此存取套件，請遵循下列步驟。 使用者不需要要求存取套件。 您仍然可以設定生命週期設定，但沒有任何要求設定。

1. 在 [ **可要求存取的使用者** ] 區段中，按一下 [ **無 (系統管理員直接指派**]。

    ![存取套件-要求-僅限無系統管理員直接指派](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    建立存取套件之後，您可以將特定的內部和外部使用者直接指派給存取套件。 如果您指定外部使用者，將會在您的目錄中建立來賓使用者帳戶。 如需直接指派使用者的相關資訊，請參閱 [查看、新增及移除存取套件的指派](../articles/active-directory/governance/entitlement-management-access-package-assignments.md)。

1. 跳至 [ [啟用要求](#enable-requests) ] 區段。

## <a name="approval"></a>核准

在 [核准] 區段中，您會指定使用者要求此存取套件時是否需要核准。 核准設定的運作方式如下：

- 只有其中一個選取的核准者或回溯核准者需要核准單一階段核准的要求。 
- 每個階段中只有一個選取的核准者必須核准要求進行2階段核准。
- 核准者可以是經理、內部贊助者或外部贊助者，視原則管理存取權的人員而定。
- 單一或2階段的核准不需要每個所選核准者的核准。
- 核准決策是根據任何核准者先審核要求的核准者。

如需如何將核准者新增至要求原則的示範，請觀看下列影片：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

如需如何將多階段核准新增至要求原則的示範，請觀看下列影片：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

遵循下列步驟來指定存取套件要求的核准設定：

1. 若要要求核准所選使用者的要求，請將 [ **需要核准** ] 切換設定為 **[是]**。 或者，若要自動核准要求，請將切換設定為 [ **否**]。

1. 若要要求使用者提供理由來要求存取套件，請將 [ **要求要求者理由** ] 切換設定為 **[是]**。
    
1. 現在判斷要求是否需要進行單一或2階段的核准。 設定要讓單一階段核准切換為**1**的**階段**數，或將切換設定為**2**階段核准。

    ![存取套件-要求-核准設定](./media/active-directory-entitlement-management-request-policy/approval.png)

選取您需要的階段數之後，請使用下列步驟來新增核准者： 

### <a name="single-stage-approval"></a>單一階段核准

1. 新增 **第一個核准者**：
    
    如果將原則設定為在您的目錄中管理使用者的存取權，您可以選取 [ **管理員為核准者**]。 或者，在從下拉式功能表中選取 [選擇特定核准者] 之後，按一下 [ **加入核准者** ] 來新增特定的使用者。
    
    ![存取套件-要求-適用于目錄優先核准者的使用者](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    如果將此原則設定為不在您目錄中的使用者管理存取權，您可以選取 [ **外部贊助** 者] 或 [ **內部贊助**者]。 或者，按一下 [選擇特定核准者] 下的 [ **新增核准者** 或群組]，以新增特定的使用者。
    
    ![存取套件-要求-使用者超出目錄優先核准者](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. 如果您選取 [ **管理員** ] 作為第一個核准者，請按一下 [ **新增** 回復]，以選取您目錄中的一或多個使用者或群組作為回溯核准者。 如果權利管理找不到要求存取權之使用者的管理員，則回復核准者會收到要求。

    管理員可透過使用 **manager** 屬性的權利管理來找到。 屬性位於 Azure AD 中的使用者設定檔中。 如需詳細資訊，請參閱 [使用 Azure Active Directory 新增或更新使用者的設定檔資訊](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md)。

1. 如果您選取 **[選擇特定核准者**]，請按一下 [ **新增核准** 者]，選取您目錄中要成為核准者的一或多個使用者或群組。

1. 在 [ **決策所需的天數**] 方塊中，指定核准者必須檢查此存取套件要求的天數。

    如果要求未在這段時間內核准，則會自動拒絕。 使用者必須提交存取套件的另一個要求。

1. 若要要求核准者為其決策提供理由，請將 [需要核准者理由] 設定為 **[是]**。

    其他核准者和要求者可看到理由。

### <a name="2-stage-approval"></a>2-階段核准

如果您選取了2階段核准，您將需要新增第二個核准者。

1. 新增 **第二個核准者**： 
    
    如果使用者在您的目錄中，請按一下 [選擇特定核准者] 下的 [ **新增核准** 者]，將特定使用者新增為第二個核准者。

    ![存取套件-要求-針對目錄中的使用者-第二個核准者](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    如果使用者不在目錄中，請選取 [ **內部贊助** 者] 或 [ **外部贊助** 者] 作為第二個核准者。 選取核准者之後，請新增回溯核准者。

    ![存取套件-要求-使用者超出目錄-第二個核准者](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. 指定第二個核准者核准要求的天數，此為 [必須在多少 **天數內進行決策**]。 

1. 將 [需要核准者理由] 切換設定為 **[是]** 或 [ **否**]。

### <a name="alternate-approvers"></a>替代核准者

您可以指定替代核准者，類似于指定可核准要求的第一個和第二個核准者。 擁有替代核准者將有助於確保在 (timeout) 到期之前核准或拒絕要求。 您可以向第一個核准者和第二個核准者列出替代核准者，以進行2階段核准。 

藉由指定替代核准者，如果第一或第二個核准者無法核准或拒絕要求，則會根據您在原則設定期間所指定的轉送排程，將擱置的要求轉寄給替代的核准者。 他們會收到一封電子郵件，以核准或拒絕擱置中的要求。

將要求轉送到替代核准者之後，第一或第二個核准者仍然可以核准或拒絕要求。 替代核准者會使用相同的我的存取權網站來核准或拒絕擱置中的要求。

我們可以列出人員或人員群組，做為核准者和替代核准者。 請確定您列出不同的人員集合，使其成為第一個、第二個和替代的核准者。
例如，如果您將 Alice 和 Bob 列為第一個核准者 (s) ，請將 Carol 和 Dave 列為替代核准者。 使用下列步驟將替代核准者新增至存取套件：

1. 在第一個核准者、第二個核准者或兩者底下，按一下 [ **顯示 advanced request settings**]。

    ![存取套件-原則-顯示 advanced request 設定](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. 設定 **是否未採取任何動作，轉寄給替代核准者？** 切換為 **[是]**。

1. 按一下 [ **新增其他核准者** ]，然後從清單中選取替代核准者 (s) 。

    ![存取套件-原則-新增替代核准者](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. 在 [ **轉寄給替代核准者 (s]) [天數** ] 方塊之後，放入核准者核准或拒絕要求的天數。 如果沒有核准者在要求期間之前核准或拒絕要求，則要求會過期 (超時) ，而且使用者必須提交其他存取套件的要求。 

    要求在要求持續時間達到半生命週期後，一天只能轉送給替代核准者，且 () 的主要核准者決定必須在至少4天后到期。 如果要求超時時間小於或等於3，則沒有足夠的時間將要求轉寄給替代核准者 (s) 。 在此範例中，要求的持續時間為14天。 因此，要求持續時間會在第7天到達半生命週期。 因此，無法在第8天之前轉送要求。 此外，您無法在要求持續時間的最後一天轉寄要求。 因此，在此範例中，可轉送的最新要求是第13天。

## <a name="enable-requests"></a>啟用要求

1. 如果您想要讓存取套件立即可供要求原則中的使用者使用，請將 [啟用] 切換為 **[是]**。

    當您完成建立存取套件之後，隨時都可以在未來啟用它。

    如果您選取 [ **無] (系統管理員直接指派) ** 而且您將 [啟用] 設定為 [ **否**]，則系統管理員無法直接指派此存取套件。

    ![存取套件-原則-啟用原則設定](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. 按一下 [下一步]。

## <a name="add-requestor-information-preview-to-an-access-package"></a>將要求者資訊 (預覽) 新增至存取套件

1. 移至 [要求者 **資訊** ] 索引標籤，然後按一下 [ **問題** ] 子索引標籤。
 
1. 針對 **問題** 方塊中的問題，輸入您想要詢問要求者的內容，也稱為顯示字串。

    ![存取套件-原則-啟用要求者資訊設定](./media/active-directory-entitlement-management-request-policy/add-requestor-info-question.png)

1. 如果您想要新增自己的當地語系化選項，請按一下 [ **新增當地語系化**]。
    1. 在 [ **新增當地語系化的問題** ] 窗格中，選取您要當地語系化問題之語言的 **語言代碼** 。
    1. 在您設定的語言中，于 [當地語系化的] **文字方塊** 中輸入問題。
    1. 新增所有所需的當地語系化之後，請按一下 [ **儲存**]。

    ![存取套件-原則-設定當地語系化的文字](./media/active-directory-entitlement-management-request-policy/add-localization-question.png)

1. 選取您希望要求者回答的 **回應格式** 。 答案格式包括： *簡短文字*、 *多重選取*和 *長文字*。
 
    ![存取套件-原則-選取 view 和 edit multiple choice 回應格式](./media/active-directory-entitlement-management-request-policy/answer-format-view-edit.png)
 
1. 如果選取多個選項，請按一下 [ **視圖] 和 [編輯** ] 按鈕，以設定答案選項。
    1. 選取 [視圖] 和 [編輯] 之後，就會開啟 [ **視圖/編輯問題** ] 窗格。
    1. 當您在 [ **回答值** ] 方塊中回答問題時，請輸入您想要提供給要求者的回應選項。
    1. 輸入您需要的回應數目，然後按一下 [ **儲存**]。
    
    ![存取套件-原則-輸入多個選擇選項](./media/active-directory-entitlement-management-request-policy/answer-multiple-choice.png)
  
1. 若要要求要求者在要求存取套件的存取權時回答這個問題，請按一下 [ **必要**] 下的核取方塊。

1. 按一下 [下一步]
