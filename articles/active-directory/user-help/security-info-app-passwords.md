---
title: 從 [安全性資訊（預覽）] 頁面建立應用程式密碼-Azure AD
description: 建立自動產生的密碼（應用程式密碼），以用於任何非瀏覽器應用程式，或在您的組織中不支援雙因素驗證的任何應用程式。 此應用程式密碼與一般密碼不同，可以在安全性資訊頁面上設定。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: curtand
ms.openlocfilehash: 787fa67ee77997fd1f9967db3abdbfc83d4ffad2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77064065"
---
# <a name="create-app-passwords-from-the-security-info-preview-page"></a>從 [安全性資訊（預覽）] 頁面建立應用程式密碼

某些應用程式 (例如 Outlook 2010) 不支援雙步驟驗證。 缺乏支援的意思就是，如果您在組織中使用雙步驟驗證，應用程式會無法運作。 若要解決此問題，您可以建立自動產生的密碼來與每個非瀏覽器應用程式搭配使用，此密碼與您的一般密碼不同。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>您的系統管理員可能不允許您使用應用程式密碼。 如果沒有看到 [應用程式密碼] **** 的選項，即是無法在貴組織使用此功能。

使用應用程式密碼時，請務必記住：

- 應用程式密碼會自動產生，且應針對每個應用程式建立並輸入一次。

- 每位使用者的密碼以 40 組為限。 如果您在到達該限制之後嘗試建立一個密碼，系統就會提示您先刪除現有的密碼，然後才能建立新密碼。

    >[!Note]
    >Office 2013 用戶端 (包括 Outlook) 支援新式驗證通訊協定，而且可搭配雙步驟驗證。 此支援表示在開啟雙步驟驗證之後，您將不再需要適用於 Office 2013 用戶端的應用程式密碼。 如需詳細資訊，請參閱 [Office 2013 和 Office 2016 用戶端應用程式的新式驗證運作方式](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)一文。

## <a name="create-new-app-passwords"></a>建立新的應用程式密碼

如果您搭配公司或學校帳戶使用雙步驟驗證，而且您的系統管理員已開啟安全性資訊體驗，您就可以使用 [安全性資訊] **** 頁面來建立和刪除應用程式密碼。

>[!Note]
>如果您的系統管理員尚未開啟安全性資訊體驗，您就必須遵循[管理應用程式密碼以進行雙步驟驗證](multi-factor-authentication-end-user-app-passwords.md)一節中的指示和資訊。

### <a name="to-create-a-new-app-password"></a>建立新的應用程式密碼

1. 登入您的公司或學校帳戶，然後前往 https://myprofile.microsoft.com/ 頁面。

    ![[我的設定檔] 頁面，顯示醒目提示的 [安全性資訊] 連結](media/security-info/securityinfo-myprofile-page.png)

2. 以左側瀏覽窗格或是 [安全性資訊]**** 區塊中的連結選取 [安全性資訊]****，然後在 [安全性資訊]**** 頁面選取 [新增方法]****。

    ![醒目提示 [新增方法] 選項的 [安全性資訊] 頁面](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. 在 [新增方法]**** 頁面上，從下拉式清單中選取 [應用程式密碼]****，然後選取 [新增]****。

    ![已選取 [應用程式密碼] 的 [新增方法] 方塊](media/security-info/securityinfo-myprofile-addpassword.png)

4. 輸入需要應用程式密碼的應用程式名稱，然後選取 [下一步]****。

    ![上有應用程式名稱的 [應用程式密碼] 頁面](media/security-info/securityinfo-myprofile-password-appname.png)

5. 複製 [密碼]**** 欄位的文字，在應用程式的密碼區域貼上密碼 (本範例為 Outlook 2010)，然後選取 [完成]****。

    ![上有應用程式名稱的 [應用程式密碼] 頁面](media/security-info/securityinfo-myprofile-password-copytext.png)

    密碼已新增，您之後可以成功登入應用程式。

## <a name="delete-your-app-passwords"></a>刪除您的應用程式密碼

如果不需再使用需要應用程式密碼的應用程式，可將相關聯的應用程式密碼刪除。 刪除應用程式密碼後，即會釋出其中一個可用的應用程式密碼空位，以供未來使用。

>[!Important]
>若不小心刪除了應用程式密碼，就無法再復原。 如此則需建立新的應用程式密碼，並重新輸入至應用程式，請按照本文[建立新的應用程式密碼](#create-new-app-passwords)一節的步驟進行。

### <a name="to-delete-an-app-password"></a>刪除應用程式密碼

1. 請在 [安全性資訊]**** 頁面上，選取指定應用程式的 [應用程式密碼]**** 選項旁的 [刪除]**** 連結。

    ![刪除安全性資訊中應用程式密碼方法的連結](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. 在確認方塊中選取 [是]****，即會刪除 [應用程式密碼]****。 刪除應用程式密碼之後，該應用程式密碼就會從您的安全性資訊中移除，並且從 [安全性資訊]**** 頁面上消失。

## <a name="for-more-information"></a>取得詳細資訊

- 如需進一步瞭解 [安全性資訊]**** 頁面以及其設定方式，請參閱[安全性資訊概觀](user-help-security-info-overview.md)
