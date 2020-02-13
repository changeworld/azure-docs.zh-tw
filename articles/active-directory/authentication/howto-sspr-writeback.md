---
title: 設定 SSPR 的密碼回寫-Azure Active Directory
description: 使用 Azure AD 和 Azure AD Connect 將密碼回寫到內部部署目錄
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1fa447312ad6a1f92eaed1164020cb6ee95606e
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161591"
---
# <a name="how-to-configure-password-writeback"></a>如何：設定密碼回寫

下列步驟假設您已使用[快速](../hybrid/how-to-connect-install-express.md)或[自訂](../hybrid/how-to-connect-install-custom.md)設定在您的環境中設定 Azure AD Connect。

1. 若要設定和啟用密碼回寫，請登入 Azure AD Connect 伺服器，然後啟動 **Azure AD Connect** 設定精靈。
2. 在 [歡迎] 頁面上，選取 [設定]。
3. 在 [其他工作] 頁面上，選取 [自訂同步處理選項]，然後選取 [下一步]。
4. 在 [連線到 Azure AD] 頁面上，輸入全域管理員認證，然後選取 [下一步]。
5. 在 [連線目錄] 和 [網域/OU] 篩選頁面上，選取 [下一步]。
6. 在 [選用功能] 頁面上，選取 [密碼回寫] 旁邊的方塊，然後選取 [下一步]。
   ![在 Azure AD Connect 中啟用密碼回寫][Writeback]
7. 在 [準備好設定] 頁面上，選取 [設定]，然後等待程序完成。
8. 看到設定完成時，選取 [結束]。

如需了解有關密碼回寫的常見疑難排解工作，請參閱我們疑難排解文章中的[針對密碼回寫問題進行疑難排解](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback)一節。

> [!WARNING]
> 在 [Azure 存取控制服務 (ACS) 於 2018 年 11 月 7 日淘汰](../azuread-dev/active-directory-acs-migration.md)後，使用 Azure AD Connect 1.0.8641.0 版和更舊版本的使用者將無法進行密碼回寫。 屆時，Azure AD Connect 1.0.8641.0 版和更舊版本將不再允許進行密碼回寫，因為它們倚賴 ACS 來進行該功能。
>
> 若要避免服務中斷，請從舊版 Azure AD Connect 升級至新版，請參閱 [Azure AD Connect：從舊版升級到最新版本](../hybrid/how-to-upgrade-previous-version.md)
>

## <a name="licensing-requirements-for-password-writeback"></a>密碼回寫的授權需求

**自助式密碼重設/變更/使用內部部署回寫來解除鎖定是 Azure AD 的進階功能**。 如需授權的詳細資訊，請參閱 [Azure Active Directory 價格網站](https://azure.microsoft.com/pricing/details/active-directory/)。

若要使用密碼回寫，您的租用戶中必須已指派下列其中一項授權：

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 或 A3
* Enterprise Mobility + Security E5 或 A5
* Microsoft 365 E3 或 A3
* Microsoft 365 E5 或 A5
* Microsoft 365 F1
* Microsoft 365 商務版

> [!WARNING]
> 獨立的 Office 365 授權方案不支援「自助式密碼重設/變更/使用內部部署回寫來解鎖」，而且需要您具備上述其中一個方案，這項功能才能運作。
>

## <a name="active-directory-permissions-and-on-premises-password-complexity-policies"></a>Active Directory 許可權和內部部署密碼複雜性原則 

如果您想要在 SSPR 的範圍中，Azure AD Connect 公用程式中指定的帳戶必須設定好下列項目：

* **重設密碼** 
* **變更密碼** 
* **的**寫入權限`lockoutTime`
* **的**寫入權限`pwdLastSet`
* 下列其中一項的**延伸權限**：
   * 該樹系中「每個網域」的根物件
   * 要在 SSPR 範圍中的使用者組織單位 (OU)

如果您不確定上述所指的是哪個帳戶，請開啟 Azure Active Directory Connect 組態 UI，然後選取 [檢視目前的設定] 選項。 您需要新增權限的帳戶會列在 [同步處理的目錄] 底下。

如果您設定這些權限，每個樹系的 MA 服務帳戶便可代表該樹系內的使用者帳戶管理密碼。 

> [!IMPORTANT]
> 如果您忘了指派這些權限，則即使回寫看似設定正確，但當使用者嘗試從雲端管理其內部部署密碼時，還是會遇到錯誤。
>

> [!NOTE]
> 最多可能需要一小時或更久，這些權限才會複寫至您目錄中的所有物件。
>

若要設定適當權限以進行密碼回寫，請完成下列步驟：

1. 以具有適當網域管理權限的帳戶開啟 [Active Directory 使用者及電腦]。
2. 從 [檢視] 功能表中，確定已開啟 [進階功能]。
3. 在左面板中，於代表網域根目錄的物件上按一下滑鼠右鍵，然後選取 [屬性] > [安全性] > [進階]。
4. 從 [權限] 索引標籤中，選取 [新增]。
5. 挑選要套用權限的帳戶 (從 Azure AD Connect 安裝程式)。
6. 在 [套用至] 下拉式清單中，選取 [下階使用者物件]。
7. 在 [權限] 底下，選取下列選項的方塊：
    * **變更密碼**
    * **重設密碼**
8. 在 [屬性] 底下，選取下列選項的方塊：
    * **寫入 lockoutTime**
    * **寫入 pwdLastSet**
9. 選取 [套用]/[確定] 以套用變更並結束任何開啟的對話方塊。

由於授權來源是在內部部署，因此密碼複雜性原則會套用至相同的已連線資料來源。 請確定您已變更「密碼最短使用期限」的現有群組原則。 群組原則不應設定為1，這表示密碼必須至少為一天，才能進行更新。 您需要確定它是設定為0。 這些設定可在 電腦設定 > 原則 下的 `gpmc.msc` 中找到 **> Windows 設定 > 安全性設定 > 帳戶原則**。 執行 `gpupdate /force`，以確保變更會生效。 

## <a name="next-steps"></a>後續步驟

[什麼是密碼回寫？](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "在 Azure AD Connect 中啟用密碼回寫"
