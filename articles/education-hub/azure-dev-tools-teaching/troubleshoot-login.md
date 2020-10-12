---
title: 解決 Azure Dev Tools for Teaching 的登入錯誤
description: 這描述當學生在登入 Azure Dev Tools for Teaching 時收到錯誤訊息時，所應採取的動作。
author: rymend
ms.author: rymend
ms.topic: troubleshooting
ms.service: azure-education
ms.subservice: education-hub
ms.date: 06/30/2020
ms.openlocfilehash: 276ab2cc1d79235c00ce1bb50ff9b648bb1de6ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096134"
---
# <a name="troubleshooting-student-login-issues"></a>針對學生登入問題進行疑難排解
存取 Azure Dev Tools for Teaching 需要使用者擁有 Microsoft 帳戶 (MSA) 。 如果學生的帳戶還不是 MSA 或連結到 MSA，則會自動將學生導向建立 MSA。 如果您的網域與 Active Directory 相關聯，則該網域下的所有帳戶都已經被視為 MSA。

如果學生嘗試登入並收到下列錯誤訊息，請使用下面所述的其中一個解決方案。

:::image type="content" source="media/troubleshoot-login/error-login-message.png" alt-text="登入錯誤訊息。" border="false":::

有兩種解決方案：建立新的 Microsoft 帳戶或使用現有的 Microsoft 帳戶。

## <a name="create-a-new-microsoft-account"></a>建立新的 Microsoft 帳戶
### <a name="use-a-university-email-address"></a>使用大學電子郵件地址
如果您是以大學電子郵件地址登入 (例如 `John.Smith@university.edu`) ，您必須使用該電子郵件地址建立 Microsoft 帳戶。 建立帳戶是免費的，而且只需要幾分鐘的時間。 擁有 Microsoft 帳戶可讓您使用單一使用者名稱和密碼，自動登入您所有的 Microsoft 產品。

### <a name="use-a-personal-email-address"></a>使用個人電子郵件地址
如果您是使用個人電子郵件地址登入 (例如， `John.Smith@email.com`) 但也有大學電子郵件地址，請嘗試使用大學電子郵件地址。 如果您先前已使用個人電子郵件地址登入機構的 webstore，或沒有大學電子郵件地址，您必須使用您的個人電子郵件地址建立或連結 Microsoft 帳戶。

## <a name="use-an-existing-microsoft-account"></a>使用現有的 Microsoft 帳戶
如果學生有現成的 Microsoft 帳戶 (例如 Xbox) ，他們可以將該帳戶連接至 Azure Dev Tools 帳戶。

1. 前往 https://account.microsoft.com。
1. 使用您的 Microsoft 帳戶認證登入。
1. 從頂端功能區功能表中選取 **您的資訊** 。

1. 按一下 [ **管理您登入 Microsoft 的方式**]。 系統會要求您驗證您的身分識別。 您將會透過電子郵件傳送安全驗證碼。

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in.png" alt-text="登入錯誤訊息。" border="false":::

1. 輸入以電子郵件傳送的安全驗證碼。

    :::image type="content" source="media/troubleshoot-accounts/sign-in-enter-code.png" alt-text="登入錯誤訊息。" border="false":::！

1. 按一下 [ **將電子郵件新增** 到您的帳戶]，然後輸入您的大學電子郵件地址。
下一次登入時，您可以使用您的大學電子郵件地址來存取您的 Azure Dev Tools for Teaching。

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in-add-email.png" alt-text="登入錯誤訊息。" border="false":::

## <a name="next-steps"></a>接下來的步驟
- [常見問題集](program-faq.md)

- [支援選項](program-support.md)
