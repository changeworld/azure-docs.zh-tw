---
title: 啟用 Azure Active Directory 密碼回寫
description: 在本教學課程中，您將了解如何使用 Azure AD Connect 啟用 Azure AD 自助式密碼重設回寫，以將變更同步回內部部署 Active Directory Domain Services 環境。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 04/24/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: a25fe090c88d2540bdf63cd6479d25b879090a38
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86202542"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>教學課程：啟用目的地為內部部署環境的 Azure Active Directory 自助式密碼重設回寫

透過 Azure Active Directory (Azure AD) 自助式密碼重設 (SSPR)，使用者可以使用網頁瀏覽器來更新其密碼或解除鎖定其帳戶。 在 Azure AD 連線到內部部署 Active Directory Domain Services (AD DS) 環境的混合式環境中，這種情況可能會導致兩個目錄之間的密碼有所不同。

您可以使用密碼回寫，將 Azure AD 中的密碼變更同步回內部部署 AD DS 環境。 Azure AD Connect 有提供安全機制，供您將這些密碼變更從 Azure AD 傳送回現有的內部部署目錄。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定要進行密碼回寫所需的權限
> * 在 Azure AD Connect 中啟用密碼回寫選項
> * 在 Azure AD SSPR 中啟用密碼回寫

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列資源和權限：

* 至少已啟用 Azure AD Premium P1 或 P2 試用版授權的有效 Azure AD 租用戶。
    * 如有需要，[請建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
    * 如需詳細資訊，請參閱 [Azure AD SSPR 的授權需求](concept-sspr-licensing.md)。
* 具有「全域系統管理員」權限的帳戶。
* 已設定好用於進行自助式密碼重設的 Azure AD。
    * 如有需要，[請完成上一個教學課程以啟用 Azure AD SSPR](tutorial-enable-sspr.md)。
* 已使用目前版本的 Azure AD Connect 設定好現有內部部署 AD DS 環境。
    * 如有需要，請使用[快速](../hybrid/how-to-connect-install-express.md)或[自訂](../hybrid/how-to-connect-install-custom.md)設定來設定 Azure AD Connect。
    * 若要使用密碼回寫，您的網域控制站必須是 Windows Server 2012 或更新版本。

## <a name="configure-account-permissions-for-azure-ad-connect"></a>設定 Azure AD Connect 的帳戶權限

Azure AD Connect 可讓您同步內部部署 AD DS 環境與 Azure AD 之間的使用者、群組和認證。 您通常會在加入到內部部署 AD DS 網域的 Windows Server 2012 或更新版本電腦上安裝 Azure AD Connect。

若要正確使用 SSPR 回寫，Azure AD Connect 中所指定的帳戶必須設定適當的權限和選項。 如果您不確定目前正在使用哪一個帳戶，請開啟 Azure AD Connect，然後選取 [檢視目前的設定] 選項。 您需要新增權限的帳戶會列在 [同步處理的目錄] 底下。 您必須在帳戶上設定下列權限和選項：

* **重設密碼**
* `lockoutTime` 的**寫入權限**
* `pwdLastSet` 的**寫入權限**
* 針對以下任何一項的「未到期密碼」**延伸權限**：
   * 該樹系中「每個網域」的根物件
   * 要在 SSPR 範圍中的使用者組織單位 (OU)

如果未指派這些權限，回寫雖看似已正確設定，但使用者會在從雲端管理其內部部署密碼時遇到錯誤。 必須將權限套用至**此物件和所有子系物件**，才會出現「未到期密碼」。  

> [!TIP]
>
> 如果某些使用者帳戶的密碼不會寫回內部部署目錄，請確定內部部署 AD DS 環境中的帳戶未停用繼承。 密碼的寫入權限必須套用至子系物件，功能才能正常運作。

若要設定適當權限以進行密碼回寫，請完成下列步驟：

1. 在內部部署 AD DS 環境中，使用有適當*網域系統管理員*權限的帳戶，開啟 [Active Directory 使用者和電腦]。
1. 從 [檢視] 功能表中，確定已開啟 [進階功能]。
1. 在左面板中，以滑鼠右鍵選取代表網域根目錄的物件，然後選取 [屬性] > [安全性] > [進階]。
1. 從 [權限] 索引標籤中，選取 [新增]。
1. 在 [主體] 中，選取要套用權限的帳戶 (Azure AD Connect 所使用的帳戶)。
1. 在 [套用至] 下拉式清單中，選取 [下階使用者物件]。
1. 在 [權限] 底下，選取下列選項的方塊：
    * **重設密碼**
1. 在 [屬性] 底下，選取下列選項的方塊。 您需要捲動清單來尋找這些可能已依預設完成設定的選項：
    * **寫入 lockoutTime**
    * **寫入 pwdLastSet**

    [![](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png "Set the appropriate permissions in Active Users and Computers for the account that is used by Azure AD Connect")](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. 準備好時，選取 [套用]/[確定] 以套用變更並結束任何開啟的對話方塊。

當您更新權限時，最多可能需要一小時或更久，這些權限才會複寫至您目錄中的所有物件。

內部部署 AD DS 環境中的密碼原則可能會導致密碼重設無法正確進行處理。 若要讓密碼回寫最有效地運作，[密碼最短存留期] 的群組原則必須設定為 0。 此設定可在 `gpedit.msc` 內的 > [電腦設定] > [原則] > [Windows 設定] > [安全性設定] > [帳戶原則] 底下找到。

如果您更新群組原則，請等候更新原則進行複寫，或使用 `gpupdate /force` 命令。

> [!Note]
> 若要讓密碼立即變更，密碼回寫必須設定為 0。 不過，如果使用者遵循內部部署原則，且 [密碼最短存留期] 設定為大於零的值，則在評估內部部署原則後，密碼回寫仍可正常運作。 

## <a name="enable-password-writeback-in-azure-ad-connect"></a>在 Azure AD Connect 中啟用密碼回寫

Azure AD Connect 中的其中一個設定選項會用於密碼回寫。 啟用此選項時，密碼變更事件會導致 Azure AD Connect 將已更新的認證同步回內部部署 AD DS 環境。

若要啟用自助式密碼重設回寫，請先在 Azure AD Connect 中啟用回寫選項。 從 Azure AD Connect 伺服器完成下列步驟：

1. 登入 Azure AD Connect 伺服器，然後啟動 **Azure AD Connect** 設定精靈。
1. 在 [歡迎] 頁面上，選取 [設定]。
1. 在 [其他工作] 頁面上，選取 [自訂同步處理選項]，然後選取 [下一步]。
1. 在 [連線到 Azure AD] 頁面上，輸入 Azure 租用戶的全域管理員認證，然後選取 [下一步]。
1. 在 [連線目錄] 和 [網域/OU] 篩選頁面上，選取 [下一步]。
1. 在 [選用功能] 頁面上，選取 [密碼回寫] 旁邊的方塊，然後選取 [下一步]。

    ![設定要用於密碼回寫的 Azure AD Connect](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. 在 [準備好設定] 頁面上，選取 [設定]，然後等待程序完成。
1. 看到設定完成時，選取 [結束]。

## <a name="enable-password-writeback-for-sspr"></a>啟用 SSPR 的密碼回寫

由於 Azure AD Connect 已啟用密碼回寫，現在請設定要用於回寫的 Azure AD SSPR。 當您啟用 SSPR 以使用密碼回寫時，變更或重設其密碼的使用者也會將已更新的密碼同步回內部部署 AD DS 環境。

若要在 SSPR 中啟用密碼回寫，請完成下列步驟：

1. 使用全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 [Azure Active Directory]、選取 [密碼重設]，然後選擇 [內部部署整合]。
1. 將 [將密碼寫回至內部部署目錄?] 的選項設定為 [是]。
1. 將 [允許使用者在不重設密碼的情況下解除鎖定帳戶?] 的選項設定為 [是]。

    ![針對密碼回寫啟用 Azure AD 自助式密碼重設](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. 在準備就緒時，選取 [儲存]。

## <a name="clean-up-resources"></a>清除資源

如果您不想再使用您在本教學課程中所設定的 SSPR 回寫功能，請完成下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 [Azure Active Directory]、選取 [密碼重設]，然後選擇 [內部部署整合]。
1. 將 [將密碼寫回至內部部署目錄?] 的選項設定為 [否]。
1. 將 [允許使用者在不重設密碼的情況下解除鎖定帳戶?] 的選項設定為 [否]。

如果您不想再使用任何密碼功能，請從 Azure AD Connect 伺服器完成下列步驟：

1. 登入 Azure AD Connect 伺服器，然後啟動 **Azure AD Connect** 設定精靈。
1. 在 [歡迎] 頁面上，選取 [設定]。
1. 在 [其他工作] 頁面上，選取 [自訂同步處理選項]，然後選取 [下一步]。
1. 在 [連線到 Azure AD] 頁面上，輸入 Azure 租用戶的全域管理員認證，然後選取 [下一步]。
1. 在 [連線目錄] 和 [網域/OU] 篩選頁面上，選取 [下一步]。
1. 在 [選用功能] 頁面上，取消選取 [密碼回寫] 旁邊的方塊，然後選取 [下一步]。
1. 在 [準備好設定] 頁面上，選取 [設定]，然後等待程序完成。
1. 看到設定完成時，選取 [結束]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已啟用目的地為內部部署 AD DS 環境的 Azure AD SSPR 回寫。 您已了解如何︰

> [!div class="checklist"]
> * 設定要進行密碼回寫所需的權限
> * 在 Azure AD Connect 中啟用密碼回寫選項
> * 在 Azure AD SSPR 中啟用密碼回寫

> [!div class="nextstepaction"]
> [在登入時評估風險](tutorial-risk-based-sspr-mfa.md)
