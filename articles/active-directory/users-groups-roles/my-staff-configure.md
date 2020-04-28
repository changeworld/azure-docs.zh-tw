---
title: 使用我的員工來委派使用者管理（預覽）-Azure AD |Microsoft Docs
description: 使用我的員工和管理單位來委派使用者管理
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/23/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 282946a023e4e79ee79b05cc2a317efc5a4056e4
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "82165855"
---
# <a name="manage-your-users-with-my-staff-preview"></a>使用我的員工管理您的使用者（預覽）

「我的員工」可讓您委派給授權單位（例如商店經理或小組負責人），確保其員工成員能夠存取其 Azure AD 帳戶的許可權。 組織可以委派一般工作，例如重設密碼或將電話號碼變更為小組經理，而不是依賴中央技術服務人員。 在我的員工中，無法存取其帳戶的使用者只要按幾下滑鼠就能重新取得存取權，而不需要任何技術服務人員或 IT 人員。

在為您的組織設定「我的員工」之前，建議您先參閱這份檔以及[使用者檔](../user-help/my-staff-team-manager.md)，以確保您瞭解這項功能對使用者的功能和影響。 您可以利用使用者檔來訓練並準備您的使用者，以取得新的體驗，並協助確保成功的推出。

## <a name="how-my-staff-works"></a>我的員工如何運作

我的員工是以管理單位（au）為基礎，這是資源的容器，可用來限制角色指派的系統管理控制範圍。 在我的員工中，au 是用來定義組織使用者的子集，例如商店或部門。 例如，小組經理可以指派給範圍為一或多個 au 的角色。 在下列範例中，使用者已被授與驗證系統管理角色，而三個 au 是角色的範圍。 如需管理單位的詳細資訊，請參閱[Azure Active Directory 中的系統管理單位管理](directory-administrative-units.md)。

## <a name="how-to-enable-my-staff"></a>如何啟用我的員工

設定 au 之後，您可以將此範圍套用到存取我的員工的使用者。 只有獲指派系統管理角色的使用者才能存取「我的員工」。 若要啟用 [我的員工]，請完成下列步驟：

1. 以使用者系統管理員身分登入 Azure 入口網站。
2. 流覽至**Azure Active Directory** > **使用者設定** > ] [**使用者功能預覽** > ] [**管理使用者功能預覽設定**]。
3. 在 [系統**管理員可以存取我的員工**] 底下，您可以選擇針對所有使用者、選取的使用者或無使用者存取啟用。

> [!Note]
> 只有已獲指派系統管理員角色的使用者可以存取「我的員工」。 如果您為未獲指派系統管理員角色的使用者啟用「我的員工」，他們將無法存取「我的員工」。

## <a name="conditional-access"></a>條件式存取

您可以使用 Azure AD 條件式存取原則來保護 [我的員工] 入口網站。 在存取「我的員工」之前，請將它用於需要多重要素驗證的工作。

我們強烈建議您使用[Azure AD 條件式存取原則](https://docs.microsoft.com/azure/active-directory/conditional-access/)來保護我的員工。 若要將條件式存取原則套用至「我的員工」，您必須使用 PowerShell 手動建立「我的員工」服務主體。

### <a name="apply-a-----conditional-access-policy-to-my-staff"></a>將條件式存取原則套用至我的員工

1. 安裝 Microsoft Graph 搶鮮[版（Beta） PowerShell Cmdlet](https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/samples/0-InstallModule.ps1)。
1. 執行下列命令：

        Connect-Graph -Scopes "Directory.AccessAsUser.All"
        New-MgServicePrincipal -DisplayName "My Staff" -AppId "ba9ff945-a723-4ab5-a977-bd8c9044fe61"

1. 建立適用于「我的員工」雲端應用程式的條件式存取原則。

    ![為「我的員工」應用程式建立條件式存取原則](media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>使用我的員工

當使用者前往 [我的員工] 時，會顯示其具有系統管理許可權的系統[管理單位](directory-administrative-units.md)名稱。 在 [[我的員工] 使用者檔](../user-help/my-staff-team-manager.md)中，我們使用「位置」一詞來參照管理單位。 如果系統管理員的許可權沒有 AU 範圍，這些許可權會套用到整個組織。 啟用 [我的員工] 之後，已啟用並獲派系統管理角色的使用者可以透過[https://mystaff.microsoft.com](https://mystaff.microsoft.com)來存取。 他們可以選取 AU 來查看該 AU 中的使用者，並選取要開啟其設定檔的使用者。

## <a name="licenses"></a>授權

在「我的員工」中啟用的每個使用者都必須獲得授權，即使他們沒有使用「我的員工」入口網站也一樣。 每個啟用的使用者都必須具有下列其中一項 Azure AD 或 Microsoft 365 授權：

- Azure AD Premium P1 或 P2
- Microsoft 365 F1 或 F3

## <a name="reset-a-users-password"></a>重設使用者的密碼

下列角色具有重設使用者密碼的許可權：

- [驗證管理員](directory-assign-admin-roles.md#authentication-administrator)
- [特殊許可權驗證管理員](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [全域管理員](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [服務台管理員](directory-assign-admin-roles.md#helpdesk-administrator)
- [使用者系統管理員](directory-assign-admin-roles.md#user-administrator)
- [密碼管理員](directory-assign-admin-roles.md#password-administrator)

在 [**我的員工**] 中，開啟使用者的設定檔。 選取 [重設密碼]****。

- 如果使用者是僅限雲端，您會看到可提供給使用者的暫時密碼。
- 如果使用者是從內部部署 Active Directory 同步處理，您可以輸入符合內部部署 AD 原則的密碼。 然後，您可以將該密碼提供給使用者。

    ![密碼重設進度指示器和成功通知](media/my-staff-configure/reset-password.png)

使用者下一次登入時，必須變更其密碼。

## <a name="manage-a-phone-number"></a>管理電話號碼

在 [**我的員工**] 中，開啟使用者的設定檔。

- 選取 [**新增電話號碼**] 區段以新增使用者的電話號碼
- 選取 [**編輯電話號碼**] 以變更電話號碼
- 選取 [**移除電話號碼**] 以移除使用者的電話號碼

根據您的設定，使用者可以使用您設定的電話號碼來登入 SMS、執行多重要素驗證，以及執行自助式密碼重設。

若要管理使用者的電話號碼，您必須獲指派下列其中一個角色：

- [驗證管理員](directory-assign-admin-roles.md#authentication-administrator)
- [特殊許可權驗證管理員](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [全域管理員](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>搜尋

您可以使用 [我的員工] 中的 [搜尋] 列，搜尋組織中的 au 和使用者。 您可以在組織中的所有 AU 和使用者之間搜尋，但只能對您已獲得系統管理員許可權的 AU 中的使用者進行變更。

您也可以在 AU 中搜尋使用者。 若要這麼做，請使用使用者清單頂端的搜尋列。

## <a name="audit-logs"></a>稽核記錄

您可以在 Azure Active Directory 入口網站中，針對在我的員工中採取的動作來查看 audit 記錄。 如果在「我的員工」中採取的動作產生了「審核記錄」，您會在 audit 事件的 [其他詳細資料] 中看到這項指示。

## <a name="next-steps"></a>後續步驟

[我的員工使用者檔](../user-help/my-staff-team-manager.md)
[管理單位檔](directory-administrative-units.md)
