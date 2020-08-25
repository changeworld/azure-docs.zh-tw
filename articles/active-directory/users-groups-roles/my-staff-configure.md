---
title: 使用我的員工來委派使用者管理 (預覽版) -Azure AD |Microsoft Docs
description: 使用「我的員工」和管理單位來委派使用者管理
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 05/08/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: da358ecb126dda21d18120bbe698fec234c85e4f
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798953"
---
# <a name="manage-your-users-with-my-staff-preview"></a>使用「我的員工」 (預覽版來管理您的使用者) 

「我的員工」可讓您委派給授權單位（例如商店經理或小組負責人）的許可權，以確保員工成員能夠存取其 Azure AD 帳戶。 組織可以委派一般工作，例如重設密碼或將電話號碼變更為 team manager，而不是依賴中央技術服務人員。 使用「我的員工」時，無法存取其帳戶的使用者只要按幾下滑鼠就能重新取得存取權，而不需要技術服務人員或 IT 人員。

在您為組織設定我的員工之前，建議您先參閱這份檔以及 [使用者檔](../user-help/my-staff-team-manager.md) ，以確保您瞭解這項功能對使用者的功能和影響。 您可以利用使用者檔來訓練和準備您的使用者，以獲得新的體驗，並協助確保成功推出。

適用於使用者的 SMS 型驗證是 Azure Active Directory 的一項公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="how-my-staff-works"></a>員工的運作方式

我的員工是根據管理單位 (au) ，這是資源的容器，可用來限制角色指派的系統管理控制範圍。 在「我的員工」中，au 用來定義組織使用者的子集，例如商店或部門。 例如，您可以將 team manager 指派給其範圍為一或多個 au 的角色。 在下列範例中，使用者已被授與「驗證」系統管理角色，而這三個 au 是角色的範圍。 如需管理單位的詳細資訊，請參閱 [Azure Active Directory 中的管理單位管理](directory-administrative-units.md)。

## <a name="before-you-begin"></a>開始之前

若要完成本文章，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。

  * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶。

  * 如果需要，請[建立 Azure Active Directory 租用戶](../fundamentals/sign-up-organization.md)或[將 Azure 訂用帳戶與您的帳戶建立關聯](../fundamentals/active-directory-how-subscriptions-associated-directory.md)。
* 您需要 Azure AD 租使用者中的 *全域系統管理員* 許可權，才能啟用以 SMS 為基礎的驗證。
* 在文字簡訊驗證方法原則中啟用的每個使用者都必須獲得授權，即使使用者未使用該驗證。 每個啟用的使用者都必須具有下列其中一個 Azure AD 或 Microsoft 365 授權：

  * [Azure AD Premium P1 或 P2](https://azure.microsoft.com/pricing/details/active-directory/)
  * [Microsoft 365 (M365) F1 或 F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [Enterprise Mobility + Security (EMS) E3 或 E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) 或 [Microsoft 365 (M365) E3 或 E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>如何啟用我的員工

設定 au 之後，您可以將此範圍套用至存取我的員工的使用者。 只有獲派系統管理角色的使用者可以存取我的員工。 若要啟用我的員工，請完成下列步驟：

1. 以使用者系統管理員身分登入 Azure 入口網站。
2. 流覽至**Azure Active Directory**  >  **使用者設定**  >  **使用者功能**預覽  >  **管理使用者功能預覽設定**。
3. 在 [系統 **管理員**] 下，您可以選擇啟用 [所有使用者]、[選取的使用者] 或 [無使用者存取]。

> [!Note]
> 只有獲派系統管理員角色的使用者可以存取我的員工。 如果您針對未獲指派系統管理員角色的使用者啟用「我的員工」，他們將無法存取「我的員工」。

## <a name="conditional-access"></a>條件式存取

您可以使用 Azure AD 條件式存取原則來保護「我的員工」入口網站。 將它用於需要多重要素驗證的工作，才能存取我的員工。

強烈建議您使用 [Azure AD 條件式存取原則](../conditional-access/index.yml)來保護我的員工。 若要將條件式存取原則套用至「我的員工」，您必須使用 PowerShell 手動建立「我的員工」服務主體。

### <a name="apply-a-conditional-access-policy-to-my-staff"></a>將條件式存取原則套用至我的員工

1. 安裝 [Microsoft Graph Beta PowerShell Cmdlet](https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/samples/0-InstallModule.ps1)。
1. 執行下列命令：

   ```powershell
   Connect-Graph -Scopes "Directory.AccessAsUser.All"
   New-MgServicePrincipal -DisplayName "My Staff" -AppId "ba9ff945-a723-4ab5-a977-bd8c9044fe61"
   ```
1. 建立適用于「我的員工」雲端應用程式的條件式存取原則。

    ![為「我的員工」應用程式建立條件式存取原則](media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>使用我的員工

當使用者移至「我的員工」時，會顯示其具有系統管理許可權的系統 [管理單位](directory-administrative-units.md) 的名稱。 在「 [我的員工」的使用者檔](../user-help/my-staff-team-manager.md)中，我們使用「位置」一詞來指稱管理單位。 如果系統管理員的許可權沒有 AU 範圍，就會將許可權套用至整個組織。 啟用「我的員工」之後，已被指派系統管理角色的使用者就可以透過進行存取 [https://mystaff.microsoft.com](https://mystaff.microsoft.com) 。 他們可以選取 AU 來查看該 AU 中的使用者，並選取要開啟其設定檔的使用者。

## <a name="reset-a-users-password"></a>重設使用者的密碼

下列角色具有重設使用者密碼的許可權：

- [驗證系統管理員](directory-assign-admin-roles.md#authentication-administrator)
- [特殊權限驗證管理員](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [全域管理員](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [服務台管理員](directory-assign-admin-roles.md#helpdesk-administrator)
- [使用者管理員](directory-assign-admin-roles.md#user-administrator)
- [密碼管理員](directory-assign-admin-roles.md#password-administrator)

從 [ **我的員工**] 開啟使用者的設定檔。 選取 [重設密碼]。

- 如果使用者是僅限雲端的使用者，您可以看到可提供給使用者的暫時密碼。
- 如果使用者是從內部部署 Active Directory 同步，您可以輸入符合內部部署 AD 原則的密碼。 然後，您可以將該密碼提供給使用者。

    ![密碼重設進度指標和成功通知](media/my-staff-configure/reset-password.png)

使用者必須在下次登入時變更其密碼。

## <a name="manage-a-phone-number"></a>管理電話號碼

從 [ **我的員工**] 開啟使用者的設定檔。

- 選取 [ **新增電話號碼** ] 區段以新增使用者的電話號碼
- 選取 [ **編輯電話號碼** ] 來變更電話號碼
- 選取 [ **移除電話號碼** ] 以移除使用者的電話號碼

視您的設定而定，使用者接著可以使用您設定的電話號碼，以使用 SMS 登入、執行多重要素驗證，以及執行自助式密碼重設。

若要管理使用者的電話號碼，您必須獲指派下列其中一個角色：

- [驗證系統管理員](directory-assign-admin-roles.md#authentication-administrator)
- [特殊權限驗證管理員](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [全域管理員](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>搜尋

您可以使用「我的員工」中的搜尋列來搜尋您組織中的 au 和使用者。 您可以在組織中的所有 au 和使用者之間搜尋，但只能對您已獲得系統管理員許可權的 AU 中的使用者進行變更。

您也可以在 AU 內搜尋使用者。 若要這樣做，請使用使用者清單頂端的 [搜尋] 列。

## <a name="audit-logs"></a>稽核記錄

您可以在 Azure Active Directory 入口網站中，針對在我的員工中所採取的動作，查看審核記錄。 如果審核記錄是由在「我的員工」中採取的動作所產生，您將會在 audit 事件的 [其他詳細資料] 下看到這種情況。

## <a name="next-steps"></a>後續步驟

[我的員工使用者檔](../user-help/my-staff-team-manager.md) 
[管理單位檔](directory-administrative-units.md)