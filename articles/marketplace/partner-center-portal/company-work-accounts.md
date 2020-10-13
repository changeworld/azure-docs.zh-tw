---
title: 公司工作帳戶和合作夥伴中心
description: 如何檢查您的公司是否有與 Microsoft 一起設定的工作帳戶、建立新的工作帳戶，或設定多個工作帳戶以搭配合作夥伴中心使用。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: parthpandyaMSFT
ms.author: parthp
ms.date: 05/30/2019
ms.openlocfilehash: 246fbcd9bc72683e41489daf105f174f9380c029
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400259"
---
# <a name="company-work-accounts-and-partner-center"></a>公司工作帳戶和合作夥伴中心

合作夥伴中心使用公司工作帳戶（也稱為 Azure Active Directory (AD) 租使用者）來管理多個使用者的帳戶存取權、控制許可權、主機群組和應用程式，以及維護設定檔資料。 藉由將貴公司的公司電子郵件帳戶網域連結至您的合作夥伴中心帳戶，公司員工可以使用自己的工作帳戶使用者名稱和密碼登入合作夥伴中心，以管理 marketplace 供應專案。

## <a name="check-whether-your-company-already-has-a-work-account"></a>檢查您的公司是否已有工作帳戶

如果您的公司已訂閱 Microsoft 雲端服務（例如 Azure、Microsoft Intune 或 Microsoft 365），則您已經有工作電子郵件帳戶網域 (也稱為可與) 搭配使用的 Azure Active Directory 租使用者合作夥伴中心。

請遵循下列步驟來檢查：
1. 登入 Azure 系統管理員入口網站 https://portal.azure.com 。
2. 從左側導覽功能表中選取 **Azure Active Directory** ，然後選取 [ **自訂功能變數名稱**]。
3. 如果您已經有工作帳戶，將會列出您的網域名稱。

如果您的公司還沒有工作帳戶，在合作夥伴中心註冊程式期間，系統會為您建立一個帳戶。

## <a name="set-up-multiple-work-accounts"></a>設定多個工作帳戶

在決定使用現有的工作帳戶之前，請考慮工作帳戶中有多少使用者需要存取合作夥伴中心。 如果您的使用者不需要存取合作夥伴中心的工作帳戶，您可能會想要考慮建立多個工作帳戶，如此一來，只有需要存取合作夥伴中心的使用者才會在特定帳戶上表示。

## <a name="create-a-new-work-account"></a>建立新的工作帳戶

若要為您的公司建立新的工作帳戶，請遵循下列步驟。 您可能需要向任何有公司 Microsoft Azure 帳戶系統管理許可權的人員要求協助。

1. 登入 [Microsoft Azure 入口網站](https://portal.azure.com)。
2. 從左側導覽功能表中，選取**Azure Active Directory**  ->  **使用者**。
3. 選取 [ **新增使用者** ]，然後輸入名稱和電子郵件地址來建立新的 Azure 工作帳戶。 確定 **目錄角色** 設定為 [ **使用者** ]，並選取底部的 [ **顯示密碼** ] 核取方塊，並記下自動產生的密碼。
4. 選取 [ **建立** ] 以儲存新的使用者。

使用者帳戶的電子郵件地址必須是您目錄中已驗證的功能變數名稱。 您可以在**Azure Active Directory**  ->  左側導覽功能表中選取 Azure Active Directory 的**自訂功能變數名稱**，以列出目錄中所有已驗證的網域。

若要深入瞭解如何在 Azure Active Directory 中新增自訂網域，請參閱 [在 Azure AD 中新增或關聯網域](../../active-directory/active-directory-add-domain.md)。

## <a name="troubleshoot-work-email-sign-in"></a>針對工作電子郵件登入進行疑難排解

如果您在登入公司帳戶時遇到問題 (也稱為 Azure AD 租使用者) ，請在下圖中找出最符合您的情況的案例，並遵循建議的步驟。

![針對工作帳戶登入進行疑難排解的圖表](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>後續步驟

- [在合作夥伴中心管理您的商業市集帳戶](./manage-account.md) 
