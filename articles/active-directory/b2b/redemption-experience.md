---
title: B2B 協作中的邀請兌換 - Azure AD
description: 描述使用者的 Azure AD B2B 共同作業邀請兌換經驗，包括隱私權條款的協議。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 043e0f3a0ff2c1c642c63a387c571b575f77cf7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050826"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B 共同作業邀請兌換

本文介紹了來賓使用者訪問您的資源的方式以及他們遇到的同意過程。 如果您向來賓發送邀請電子郵件，邀請包括該來賓可以兌換的連結，以便訪問您的應用或閘戶。 邀請電子郵件只是來賓存取資源的方式之一。 作為替代方法，您可以將來賓添加到目錄，並為他們提供指向要共用的門戶或應用的直接連結。 無論他們使用哪種方法，客人都會通過首次同意程式。 此過程可確保您的客人同意隱私條款，並接受您設置的任何[使用條款](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou)。

當您將來賓使用者添加到目錄時，來賓使用者帳戶具有同意狀態（在 PowerShell 中查看），最初設置為 **"掛起接受"。** 此設置將保留，直到客人接受您的邀請並同意您的隱私政策和使用條款。 之後，同意狀態更改為 **"已接受**"，並且同意頁不再呈現給來賓。

   > [!IMPORTANT]
   > **從 2021 年 3 月 31 日起**，Microsoft 將不再支援為 B2B 協作方案創建非託管 Azure AD 帳戶和租戶來兌換邀請。 在準備中，我們鼓勵客戶選擇[電子郵件一次性密碼身份驗證](one-time-passcode.md)。 我們歡迎您對此公共預覽功能的回饋，並興奮地創建更多協作方式。

## <a name="redemption-through-the-invitation-email"></a>透過邀請電子郵件兌換

使用[Azure 門戶](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)將來賓使用者添加到目錄時，將在此過程中向來賓發送邀請電子郵件。 當您[使用 PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)將來賓使用者添加到目錄中時，您還可以選擇發送邀請電子郵件。 以下是客人在電子郵件中兌換連結時的體驗描述。

1. 來賓收到從**微軟邀請**發送[的邀請電子郵件](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements)。
2. 來賓選擇在電子郵件中**開始**。
3. 如果來賓在聯合組織中沒有 Azure AD 帳戶、Microsoft 帳戶 （MSA） 或電子郵件帳戶，系統將提示他們創建 MSA（除非啟用了[一次性密碼](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)功能，不需要 MSA）。
4. 客人將體驗以下的[同意體驗](#consent-experience-for-the-guest)。

## <a name="redemption-through-a-direct-link"></a>透過直接連結兌換

作為邀請電子郵件的替代方法，您可以向來賓提供指向應用或閘戶的直接連結。 首先需要通過[Azure 門戶](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)或[PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)將來賓使用者添加到目錄中。 然後，您可以使用任何[可自訂的方式將應用程式部署到使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)，包括直接登錄連結。 當來賓使用直接連結而不是邀請電子郵件時，他們仍將獲得首次同意體驗的指導。

> [!IMPORTANT]
> 直接連結必須特定于租戶。 換句話說，它必須包括租戶 ID 或已驗證的域，以便可以在共用應用所在的租戶中對來賓進行身份驗證。 類似https://myapps.microsoft.com這樣的通用 URL 對來賓不起作用，因為它將重定向到其主租戶進行身份驗證。 下面是一些與租戶上下文的直接連結的示例：
 > - 應用訪問面板： https://myapps.microsoft.com/?tenantid=&lt;租戶 ID&gt; 
 > - 已驗證域的應用訪問面板： https://myapps.microsoft.com/&lt;已驗證域&gt;
 > - Azure 門戶https://portal.azure.com/&lt： ;租戶 ID&gt;
 > - 單個應用：瞭解如何使用[直接登錄連結](../manage-apps/end-user-experiences.md#direct-sign-on-links)

在某些情況下，建議通過直接連結發送邀請電子郵件。 如果這些特殊情況對您的組織很重要，我們建議您使用仍發送邀請電子郵件的方法邀請使用者：
 - 使用者在聯合組織中沒有 Azure AD 帳戶、MSA 或電子郵件帳戶。 除非您使用的是一次性密碼功能，否則來賓需要兌換邀請電子郵件，以便指導您完成創建 MSA 的步驟。
 - 受邀的使用者物件有時可能因為與連絡人物件 (例如 Outlook 連絡人物件) 衝突，而沒有電子郵件地址。 在此情況下，使用者必須按一下邀請電子郵件中的兌換 URL。
 - 使用者可以使用受邀的電子郵件地址別名登入。 （別名是與電子郵件帳戶關聯的其他電子郵件地址。在這種情況下，使用者必須按一下邀請電子郵件中的兌換 URL。

## <a name="consent-experience-for-the-guest"></a>客人的同意體驗

當來賓首次登錄以訪問合作夥伴組織中的資源時，他們會通過以下頁面進行引導。 

1. 來賓查看描述邀請組織隱私權聲明的 **"審閱許可權**"頁。 使用者**必須接受根據**邀請組織的隱私政策繼續使用其資訊。

   ![顯示 [檢閱權限] 頁面的螢幕擷取畫面](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > 有關身為租用戶管理員的您如何連結至貴組織隱私權聲明的詳細資訊，請參閱[做法：在 Azure Active Directory 中新增貴組織的隱私權資訊](https://aka.ms/adprivacystatement)。

2. 如果配置了使用條款，來賓將打開並查看使用條款，然後選擇"**接受**"。 

   ![顯示新增使用規定的螢幕擷取畫面](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > 您可以在 **"管理** > **組織關係** > 使用**條款**"中配置[使用條款](../governance/active-directory-tou.md)。

3. 除非另有說明，否則來賓將重定向到"應用訪問"面板，該面板列出了來賓可以訪問的應用程式。

   ![顯示應用訪問面板的螢幕截圖](media/redemption-experience/myapps.png) 

在目錄中，來賓接受**的邀請值**更改為 **"是**"。 如果創建了 MSA，則來賓的**源**將顯示**Microsoft 帳戶**。 有關來賓使用者帳戶屬性的詳細資訊，請參閱 Azure [AD B2B 協作使用者的屬性](user-properties.md)。 

## <a name="next-steps"></a>後續步驟

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
- [在 Azure 入口網站中新增 Azure Active Directory B2B 共同作業使用者](add-users-administrator.md)
- [資訊工作者如何將 B2B 共同作業使用者新增到 Azure Active Directory？](add-users-information-worker.md)
- [使用 PowerShell 新增 Azure Active Directory B2B 共同作業使用者](customize-invitation-api.md#powershell)
- [以來賓使用者的身分從組織離職](leave-the-organization.md)
