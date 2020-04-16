---
title: 使用「我的員工」委派使用者管理(預覽) - Azure AD |微軟文件
description: 使用「我的員工」和「管理單位」委派使用者管理
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 3f7c12612dbe37de6b08cb05a64af460296ade93
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394211"
---
# <a name="manage-your-users-with-my-staff-preview"></a>使用「我的員工」管理您的使用者(預覽版)

"我的工作人員"使您能夠委派許可權,例如商店經理或團隊主管,這些許可權可確保其員工能夠訪問其 Azure AD 帳戶。 組織可以將常見任務(如重置密碼或更改電話號碼)委派給團隊經理,而不是依賴中央説明台。 使用「我的員工」,無法訪問其帳戶的使用者只需單擊幾下即可重新獲得訪問許可權,無需説明台或 IT 人員。

在為您的組織配置「我的員工」之前,我們建議您查看本文檔以及[用戶文檔](../user-help/my-staff-team-manager.md),以確保您瞭解此功能的功能和對使用者的影響。 您可以利用使用者文檔來培訓和準備使用者的新體驗,並幫助確保成功推出。

## <a name="how-my-staff-works"></a>我的員工如何工作

我的工作人員基於行政單位(AU),它們是一個資源容器,可用於限制角色分配管理控制的範圍。 在"我的員工"中,AUS用於定義組織使用者(如商店或部門)的子集。 例如,團隊經理可以分配給作用域為一個或多個 A 的的角色。 在下面的示例中,使用者已被授予身份驗證管理角色,並且這三個 AA 是角色的範圍。 有關管理單位的詳細資訊,請參閱[Azure 活動目錄中的管理單位管理](directory-administrative-units.md)。

## <a name="how-to-enable-my-staff"></a>如何啟用我的員工

配置了 A,即可將此作用域應用於訪問「我的員工」的使用者。 只有分配了管理角色的使用者才能訪問"我的工作人員」。 要啟用「我的員工」,完成以下步驟:

1. 以使用者管理員身份登錄到 Azure 門戶。
2. 瀏覽到**Azure 的目錄** > **使用者設定** > **使用者功能預覽** > **管理使用者功能預覽設定**。
3. 在 **「管理員可以存取我的工作人員」** 下,您可以選擇為所有使用者、所選使用者或沒有使用者存取許可權啟用。

> [!Note]
> 只有已分配了管理員角色的使用者才能訪問"我的員工」。 如果您為未分配管理員角色的用戶啟用"我的員工",他們將無法訪問"我的員工"。

## <a name="using-my-staff"></a>使用我的員工

當用戶轉到"我的工作人員"時,將顯示具有管理許可權[的管理單位](directory-administrative-units.md)的名稱。 在["我的員工"用戶文檔中](../user-help/my-staff-team-manager.md),我們使用術語"位置"來指指行政單位。 如果管理員的許可權沒有 AU 作用域,則許可權將應用於整個組織。 啟用「我的員工」後,已啟用並分配了管理角色的使用者可以通過訪問[https://mystaff.microsoft.com](https://mystaff.microsoft.com)。 他們可以選擇一個 AU 來查看該 AU 中的使用者,並選擇使用者來打開其設定檔。

## <a name="licenses"></a>授權

"我的員工"中啟用的每個使用者都必須獲得許可,即使他們不使用"我的員工"門戶。 每個啟用的使用者必須具有以下 Azure AD 或 Microsoft 365 許可證之一:

- Azure AD Premium P1 或 P2
- 微軟 365 F1 或 F3

## <a name="reset-a-users-password"></a>重設使用者的密碼

以下角色具有重置使用者密碼的許可權:

- [驗證管理員](directory-assign-admin-roles.md#authentication-administrator)
- [特權識別管理員](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [全域管理員](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [說明台管理員](directory-assign-admin-roles.md#helpdesk-administrator)
- [使用者管理員](directory-assign-admin-roles.md#user-administrator)
- [密碼管理員](directory-assign-admin-roles.md#password-administrator)

從我的**工作人員**,打開使用者的個人資料。 選取 [重設密碼]****。

- 如果使用者僅雲,則可以看到可以授予使用者的臨時密碼。
- 如果使用者從本地活動目錄同步,則可以輸入符合本地 AD 策略的密碼。 然後,您可以將該密碼授予使用者。

    ![密碼重設進度指示器和成功通知](media/my-staff-configure/reset-password.png)

使用者下次登錄時必須更改其密碼。

## <a name="manage-a-phone-number"></a>管理電話號碼

從我的**工作人員**,打開使用者的個人資料。

- 選擇 **'新增電話號碼**'部分可為使用者新增電話號碼
- 選擇 **「編輯電話號碼**」 以變更電話號碼
- 選擇 **「刪除電話號碼**」 以刪除使用者的電話號碼

然後,根據您的設置,使用者可以使用您設置的電話號碼使用 SMS 登錄、執行多重身份驗證以及執行自助服務密碼重置。

要管理使用者的電話號碼,必須為您分配以下角色之一:

- [驗證管理員](directory-assign-admin-roles.md#authentication-administrator)
- [特權識別管理員](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [全域管理員](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>搜尋

您可以使用「我的員工」中的搜索欄搜尋組織中的 A 和使用者。 您可以在組織中的所有 AU 和使用者進行搜索,但只能對已被授予管理員許可權的 AU 中的使用者進行更改。

您還可以在 AU 中搜尋使用者。 為此,請使用使用者清單頂部的搜索欄。

## <a name="audit-logs"></a>稽核記錄

您可以查看審核日誌,瞭解在 Azure 活動目錄門戶中「我的員工」中執行的操作。 如果審核日誌是由「我的員工」中執行的操作生成的,您將在審核事件中的「附加詳細資訊」下看到此記錄。

## <a name="next-steps"></a>後續步驟

[我的使用者文件](../user-help/my-staff-team-manager.md)
[管理單位文件](directory-administrative-units.md)
