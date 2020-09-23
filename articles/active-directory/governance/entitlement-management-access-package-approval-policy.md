---
title: 在 Azure AD 權利管理中變更存取套件的核准設定-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 權利管理中變更存取套件的核准和要求者資訊設定。
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
ms.openlocfilehash: 2e36b2d4576b43544bec89efd326363344b35be9
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994601"
---
# <a name="change-approval-and-requestor-information-preview-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>變更核准和要求者資訊 (預覽 Azure AD 權利管理中的存取套件) 設定

您可以藉由編輯現有原則或新增原則，隨時變更存取套件的核准和要求者資訊設定，作為存取套件管理員。

本文說明如何變更現有存取套件的核准和要求者資訊設定。

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


## <a name="change-approval-settings-of-an-existing-access-package"></a>變更現有存取套件的核准設定

遵循下列步驟來指定存取套件要求的核准設定：

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [ **存取套件** ]，然後開啟存取套件。

1. 選取要編輯的原則，或將新的原則新增至存取套件
    1. 如果您想要建立新的原則，請按一下 [ **原則** ]，然後 **新增原則** 。
    1. 按一下您要編輯的原則，然後按一下 [ **編輯**]。

1. 移至 [ **要求** ] 索引標籤。

1. 若要要求核准所選使用者的要求，請將 [ **需要核准** ] 切換設定為 **[是]**。 或者，若要自動核准要求，請將切換設定為 [ **否**]。

1. 若要要求使用者提供理由來要求存取套件，請將 [ **要求要求者理由** ] 切換設定為 **[是]**。
    
1. 現在判斷要求是否需要進行單一或2階段的核准。 設定要讓單一階段核准切換為**1**的**階段**數，或將切換設定為**2**階段核准。

    ![存取套件-要求-核准設定](./media/entitlement-management-access-package-approval-policy/approval.png)


選取您需要的階段數之後，請使用下列步驟來新增核准者： 

### <a name="single-stage-approval"></a>單一階段核准

1. 新增 **第一個核准者**：
    
    如果將原則設定為在您的目錄中管理使用者的存取權，您可以選取 [ **管理員為核准者**]。 或者，在從下拉式功能表中選取 [選擇特定核准者] 之後，按一下 [ **加入核准者** ] 來新增特定的使用者。
    
    ![存取套件-要求-適用于目錄優先核准者的使用者](./media/entitlement-management-access-package-approval-policy/approval-single-stage-first-approver-manager.png)

    如果將此原則設定為不在您目錄中的使用者管理存取權，您可以選取 [ **外部贊助** 者] 或 [ **內部贊助**者]。 或者，按一下 [選擇特定核准者] 下的 [ **新增核准者** 或群組]，以新增特定的使用者。
    
    ![存取套件-要求-使用者超出目錄優先核准者](./media/entitlement-management-access-package-approval-policy/out-directory-first-approver.png)
    
1. 如果您選取 [ **管理員** ] 作為第一個核准者，請按一下 [ **新增** 回復]，以選取您目錄中的一或多個使用者或群組作為回溯核准者。 如果權利管理找不到要求存取權之使用者的管理員，則回復核准者會收到要求。

    管理員可透過使用 **manager** 屬性的權利管理來找到。 屬性位於 Azure AD 中的使用者設定檔中。 如需詳細資訊，請參閱 [使用 Azure Active Directory 新增或更新使用者的設定檔資訊](../fundamentals/active-directory-users-profile-azure-portal.md)。

1. 如果您選取 **[選擇特定核准者**]，請按一下 [ **新增核准** 者]，選取您目錄中要成為核准者的一或多個使用者或群組。

1. 在 [ **決策所需的天數**] 方塊中，指定核准者必須檢查此存取套件要求的天數。

    如果要求未在這段時間內核准，則會自動拒絕。 使用者必須提交存取套件的另一個要求。

1. 若要要求核准者為其決策提供理由，請將 [需要核准者理由] 設定為 **[是]**。

    其他核准者和要求者可看到理由。

### <a name="2-stage-approval"></a>2-階段核准

如果您選取了2階段核准，您將需要新增第二個核准者。

1. 新增 **第二個核准者**： 
    
    如果使用者在您的目錄中，請按一下 [選擇特定核准者] 下的 [ **新增核准** 者]，將特定使用者新增為第二個核准者。

    ![存取套件-要求-針對目錄中的使用者-第二個核准者](./media/entitlement-management-access-package-approval-policy/in-directory-second-approver.png)

    如果使用者不在目錄中，請選取 [ **內部贊助** 者] 或 [ **外部贊助** 者] 作為第二個核准者。 選取核准者之後，請新增回溯核准者。

    ![存取套件-要求-使用者超出目錄-第二個核准者](./media/entitlement-management-access-package-approval-policy/out-directory-second-approver.png) 

1. 指定第二個核准者核准要求的天數，此為 [必須在多少 **天數內進行決策**]。 

1. 將 [需要核准者理由] 切換設定為 **[是]** 或 [ **否**]。

### <a name="alternate-approvers"></a>替代核准者

您可以指定替代核准者，類似于指定可核准要求的第一個和第二個核准者。 擁有替代核准者將有助於確保在 (timeout) 到期之前核准或拒絕要求。 您可以向第一個核准者和第二個核准者列出替代核准者，以進行2階段核准。 

藉由指定替代核准者，如果第一或第二個核准者無法核准或拒絕要求，則會根據您在原則設定期間所指定的轉送排程，將擱置的要求轉寄給替代的核准者。 他們會收到一封電子郵件，以核准或拒絕擱置中的要求。

將要求轉送到替代核准者之後，第一或第二個核准者仍然可以核准或拒絕要求。 替代核准者會使用相同的我的存取權網站來核准或拒絕擱置中的要求。

我們可以列出人員或人員群組，做為核准者和替代核准者。 請確定您列出不同的人員集合，使其成為第一個、第二個和替代的核准者。
例如，如果您將 Alice 和 Bob 列為第一個核准者 (s) ，請將 Carol 和 Dave 列為替代核准者。 使用下列步驟將替代核准者新增至存取套件：

1. 在第一個核准者、第二個核准者或兩者底下，按一下 [ **顯示 advanced request settings**]。

    ![存取套件-原則-顯示 advanced request 設定](./media/entitlement-management-access-package-approval-policy/alternate-approvers-click-advanced-request.png)

1. 設定 **是否未採取任何動作，轉寄給替代核准者？** 切換為 **[是]**。

1. 按一下 [ **新增其他核准者** ]，然後從清單中選取替代核准者 (s) 。

    ![存取套件-原則-新增替代核准者](./media/entitlement-management-access-package-approval-policy/alternate-approvers-add.png)

1. 在 [ **轉寄給替代核准者 (s]) [天數** ] 方塊之後，放入核准者核准或拒絕要求的天數。 如果沒有核准者在要求期間之前核准或拒絕要求，則要求會過期 (超時) ，而且使用者必須提交其他存取套件的要求。 

    要求在要求持續時間達到半生命週期後，一天只能轉送給替代核准者，且 () 的主要核准者決定必須在至少4天后到期。 如果要求超時時間小於或等於3，則沒有足夠的時間將要求轉寄給替代核准者 (s) 。 在此範例中，要求的持續時間為14天。 因此，要求持續時間會在第7天到達半生命週期。 因此，無法在第8天之前轉送要求。 此外，您無法在要求持續時間的最後一天轉寄要求。 因此，在此範例中，可轉送的最新要求是第13天。

## <a name="enable-requests"></a>啟用要求

1. 如果您想要讓存取套件立即可供要求原則中的使用者使用，請將 [啟用] 切換為 **[是]**。

    當您完成建立存取套件之後，隨時都可以在未來啟用它。

    如果您選取 [ **無] (系統管理員直接指派) ** 而且您將 [啟用] 設定為 [ **否**]，則系統管理員無法直接指派此存取套件。

    ![存取套件-原則-啟用原則設定](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. 按 [下一步]  。

## <a name="collect-additional-requestor-information-for-approval-preview"></a> (預覽版收集額外的要求者資訊以進行核准) 

為了確保使用者能夠存取正確的存取套件，您可以要求要求者在要求時回答自訂文字欄位或多個選擇問題。 每個原則的限制為20個問題，而針對多個選擇問題，有25個答案的限制。 然後會向核准者顯示問題，以協助他們做出決策。

1. 移至 [要求者 **資訊** ] 索引標籤，然後按一下 [ **問題** ] 子索引標籤。
 
1. 針對 **問題** 方塊中的問題，輸入您想要詢問要求者的內容，也稱為顯示字串。

    ![存取套件-原則-啟用要求者資訊設定](./media/entitlement-management-access-package-approval-policy/add-requestor-info-question.png)

1. 如果需要存取存取套件的使用者群體並非全都有常用的慣用語言，則您可以改善使用者在 myaccess.microsoft.com 上要求存取權的體驗。 若要改善體驗，您可以提供不同語言的替代顯示字串。 例如，如果使用者的網頁瀏覽器設定為西班牙文，而且您已設定西班牙文顯示字串，則這些字串將會顯示給提出要求的使用者。 若要設定要求的當地語系化，請按一下 [ **新增當地語系化**]。
    1. 在 [ **新增當地語系化的問題** ] 窗格中，選取您要當地語系化問題之語言的 **語言代碼** 。
    1. 在您設定的語言中，于 [當地語系化的] **文字方塊** 中輸入問題。
    1. 新增所有所需的當地語系化之後，請按一下 [ **儲存**]。

    ![存取套件-原則-設定當地語系化的文字](./media/entitlement-management-access-package-approval-policy/add-localization-question.png)

1. 選取您希望要求者回答的 **回應格式** 。 答案格式包括： *簡短文字*、 *多重選取*和 *長文字*。
 
    ![存取套件-原則-選取 view 和 edit multiple choice 回應格式](./media/entitlement-management-access-package-approval-policy/answer-format-view-edit.png)
 
1. 如果選取多個選項，請按一下 [ **視圖] 和 [編輯** ] 按鈕，以設定答案選項。
    1. 選取 [視圖] 和 [編輯] 之後，就會開啟 [ **視圖/編輯問題** ] 窗格。
    1. 當您在 [ **回答值** ] 方塊中回答問題時，請輸入您想要提供給要求者的回應選項。
    1. 輸入您需要的回應數目，然後按一下 [ **儲存**]。
    
    ![存取套件-原則-輸入多個選擇選項](./media/entitlement-management-access-package-approval-policy/answer-multiple-choice.png)
  
1. 若要要求要求者在要求存取套件的存取權時回答這個問題，請按一下 [ **必要**] 下的核取方塊。

1. 根據您的需求，填寫其餘索引標籤 (例如生命週期) 。

在存取套件原則中設定要求者資訊之後，可以查看要求者對問題的回應。 如需有關查看要求者資訊的指引，請參閱 View 要求者 [對問題的解答 (Preview) ](entitlement-management-request-approve.md#view-requestors-answers-to-questions-preview)。

## <a name="next-steps"></a>下一步
- [變更存取套件的生命週期設定](entitlement-management-access-package-lifecycle-policy.md)
- [查看存取套件的要求](entitlement-management-access-package-requests.md)
