---
title: 公司工作帳戶和合作夥伴中心
description: 如何檢查您的公司是否已設定 Microsoft 的工作帳戶、建立新的工作帳戶，或設定要與合作夥伴中心搭配使用的多個工作帳戶。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: parthpandyaMSFT
ms.author: parthp
ms.date: 05/30/2019
ms.openlocfilehash: 6fca3429ab96a63b01eaa7e9d1f0ae83dc46e9c7
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119747"
---
# <a name="company-work-accounts-and-partner-center"></a>公司工作帳戶和合作夥伴中心

合作夥伴中心使用公司工作帳戶（也稱為 Azure Active Directory （AD）租使用者）來管理多個使用者的帳戶存取權、控制許可權、主機群組和應用程式，以及維護設定檔資料。 藉由將公司的工作電子郵件帳戶網域連結到合作夥伴中心帳戶，您公司的員工可以使用自己的工作帳戶使用者名稱和密碼來登入合作夥伴中心，以管理 marketplace 供應專案。

## <a name="check-whether-your-company-already-has-a-work-account"></a>檢查您的公司是否已有工作帳戶

如果您的公司訂閱了 Microsoft 雲端服務（例如 Azure、Microsoft Intune 或 Office 365），則您已經擁有可與合作夥伴中心搭配使用的工作電子郵件帳戶網域（也稱為 Azure Active Directory 租使用者）。

請遵循下列步驟來檢查：
1. 登入 Azure 系統管理員入口網站，網址為 https://portal.azure.com 。
2. 從左側導覽功能表中選取 [ **Azure Active Directory** ]，然後選取 [**自訂功能變數名稱**]。
3. 如果您已經有工作帳戶，將會列出您的網域名稱。

如果您的公司還沒有工作帳戶，則會在合作夥伴中心註冊程式期間為您建立一個。

## <a name="set-up-multiple-work-accounts"></a>設定多個工作帳戶

在決定使用現有的工作帳戶之前，請考慮工作帳戶中有多少使用者需要存取合作夥伴中心。 如果您的使用者在公司帳戶中，而不需要存取合作夥伴中心，您可以考慮建立多個工作帳戶，這樣一來，只有需要存取合作夥伴中心的使用者才會在特定帳戶上呈現。

## <a name="create-a-new-work-account"></a>建立新的工作帳戶

若要為您的公司建立新的工作帳戶，請遵循下列步驟。 您可能需要向擁有貴公司 Microsoft Azure 帳戶之系統管理許可權的人員要求協助。

1. 登入 [Microsoft Azure 入口網站](https://portal.azure.com)。
2. 從左側導覽功能表中，選取 [ **Azure Active Directory**  ->  **使用者**]。
3. 選取 [**新增使用者**]，然後輸入名稱和電子郵件地址來建立新的 Azure 工作帳戶。 確定 [**目錄角色**] 已設定為 [**使用者**]，然後選取底部的 [**顯示密碼**] 核取方塊以查看並記下自動產生的密碼。
4. 選取 [**建立**] 以儲存新的使用者。

使用者帳戶的電子郵件地址必須是您目錄中已驗證的功能變數名稱。 您可以選取左側導覽功能表中的 [ **Azure Active Directory**  ->  **自訂功能變數名稱**]，列出目錄中所有已驗證的網域。

若要深入瞭解如何在 Azure Active Directory 中新增自訂網域，請參閱[在 Azure AD 中新增或關聯網域](../../active-directory/active-directory-add-domain.md)。

## <a name="troubleshoot-work-email-sign-in"></a>針對工作電子郵件登入進行疑難排解

如果您無法登入您的工作帳戶（也稱為 Azure AD 租使用者），請在下圖中尋找最符合您情況的案例，並遵循建議的步驟。

![疑難排解工作帳戶登入的圖表](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>後續步驟

- [在合作夥伴中心管理您的商業市集帳戶](./manage-account.md) 
