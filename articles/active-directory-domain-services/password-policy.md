---
title: 在 Azure AD Domain Services 中建立及使用密碼原則 |Microsoft Docs
description: 瞭解如何及為何使用更細緻的密碼原則來保護及控制 Azure AD DS 受控網域中的帳戶密碼。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: df132af1675b3f373fe1eab5685c5d2f07813445
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619227"
---
# <a name="password-and-account-lockout-policies-on-azure-active-directory-domain-services-managed-domains"></a>Azure Active Directory Domain Services 受控網域上的密碼和帳戶鎖定原則

若要在 Azure Active Directory Domain Services (Azure AD DS) 中管理使用者安全性，您可以定義更細緻的密碼原則，以控制帳戶鎖定設定或密碼長度下限和複雜度。 系統會建立預設的更細緻的密碼原則，並將其套用至 Azure AD DS 受控網域中的所有使用者。 若要提供更細微的控制，並符合特定商務或合規性需求，您可以建立額外的原則，並將其套用至特定的使用者群組。

本文說明如何使用 Active Directory 管理中心在 Azure AD DS 中建立及設定更細緻的密碼原則。

> [!NOTE]
> 密碼原則僅適用于使用 Resource Manager 部署模型建立的受控網域。 針對使用傳統建立的較舊受控網域，請 [從傳統虛擬網路模型遷移至 Resource Manager][migrate-from-classic]。

## <a name="before-you-begin"></a>開始之前

若要完成本文章，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
  * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
  * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
  * 如有需要，請完成教學課程，以 [建立並設定 Azure Active Directory Domain Services 受控網域][create-azure-ad-ds-instance]。
  * 受控網域必須使用 Resource Manager 部署模型來建立。 如有需要，請 [從傳統虛擬網路模型遷移至 Resource Manager][migrate-from-classic]。
* 已加入受控網域的 Windows Server 管理 VM。
  * 如有需要，請完成教學課程以 [建立管理 VM][tutorial-create-management-vm]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員群組成員的使用者帳戶。

## <a name="default-password-policy-settings"></a>預設密碼原則設定

更細緻的密碼原則 (Fgpp) 可讓您將特定的密碼和帳戶鎖定原則限制套用到網域中的不同使用者。 例如，為了保護特殊許可權帳戶，您可以套用更嚴格的帳戶鎖定設定，而不是一般的非特殊許可權帳戶。 您可以在受控網域內建立多個 Fgpp，並指定要套用至使用者的優先順序順序。

如需有關密碼原則和使用 Active Directory 系統管理中心的詳細資訊，請參閱下列文章：

* [深入瞭解更細緻的密碼原則](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [使用 AD 系統管理中心設定更細緻的密碼原則](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

原則是透過受控網域中的群組關聯來散發，而您所做的任何變更都會在下次使用者登入時套用。 變更原則無法解除鎖定已鎖定的使用者帳戶。

密碼原則的行為會有很大的不同，這取決於建立使用者帳戶的方式。 有兩種方式可以在 Azure AD DS 中建立使用者帳戶：

* 您可以從 Azure AD 同步處理使用者帳戶。 這包括直接在 Azure 中建立的僅限雲端使用者帳戶，以及使用 Azure AD Connect 從內部部署 AD DS 環境同步處理的混合式使用者帳戶。
    * Azure AD DS 中的大部分使用者帳戶都是透過 Azure AD 的同步處理常式所建立。
* 您可以在受控網域中手動建立使用者帳戶，而且不存在於 Azure AD 中。

所有使用者無論其建立方式為何，都有下列 Azure AD DS 中的預設密碼原則所套用的帳戶鎖定原則：

* **帳戶鎖定持續時間：** 30
* **允許的失敗登入嘗試次數：** 5
* **重設失敗的登入嘗試次數：** 30 分鐘
* **密碼最長使用期限 (存留期) ：** 90 天

使用這些預設設定時，如果在2分鐘內使用5個不正確密碼，使用者帳戶就會被鎖定30分鐘。 30 分鐘後，帳戶會自動解除鎖定。

帳戶鎖定只會在受控網域中進行。 使用者帳戶只會在 Azure AD DS 中鎖定，而且只會因為嘗試對受控網域進行的登入嘗試失敗。 從 Azure AD 或內部部署同步處理的使用者帳戶，不會在其來原始目錄中被鎖定，只會在 Azure AD DS 中。

如果您的 Azure AD 密碼原則指定的密碼最長使用期限超過90天，則會將該密碼存留期套用至 Azure AD DS 中的預設原則。 您可以設定自訂密碼原則，以在 Azure AD DS 中定義不同的密碼最長使用期限。 請注意，如果您在 Azure AD DS 密碼原則中設定的密碼最長使用期限，與 Azure AD 或內部部署 AD DS 環境中的時間長度較短。 在該案例中，使用者的密碼可能會在 Azure AD DS 過期，然後才會在 Azure AD 或內部部署 AD DS 環境中提示變更。

針對在受控網域中手動建立的使用者帳戶，也會從預設原則套用下列其他密碼設定。 這些設定不適用於從 Azure AD 同步處理的使用者帳戶，因為使用者無法直接在 Azure AD DS 中更新其密碼。

* **密碼長度下限 (字元) ：** 7
* **密碼必須符合複雜性需求**

您無法在預設密碼原則中修改帳戶鎖定或密碼設定。 相反地， *AAD DC 系統管理員* 群組的成員可以建立自訂密碼原則，並將其設定為覆寫 (優先于) 預設的內建原則，如下一節所示。

## <a name="create-a-custom-password-policy"></a>建立自訂密碼原則

當您在 Azure 中建立和執行應用程式時，您可能會想要設定自訂密碼原則。 例如，您可以建立原則來設定不同的帳戶鎖定原則設定。

自訂密碼原則適用于受控網域中的群組。 這種設定會有效地覆寫預設原則。

若要建立自訂密碼原則，您可以從已加入網域的 VM 使用 Active Directory 系統管理工具。 此 Active Directory 管理中心可讓您在受控網域中查看、編輯和建立資源，包括 Ou。

> [!NOTE]
> 若要在受控網域中建立自訂密碼原則，您必須登入屬於 *AAD DC 系統管理員* 群組成員的使用者帳戶。

1. 從開始畫面選取 [系統 **管理工具**]。 您可以在教學課程中所安裝的可用管理工具清單中， [建立管理 VM][tutorial-create-management-vm]。
1. 若要建立和管理 Ou，請從 [系統管理工具] 清單中選取 [ **Active Directory 管理中心** ]。
1. 在左窗格中，選擇您的受控網域，例如 *aaddscontoso.com*。
1. 開啟 [ **系統** ] 容器，然後 **密碼設定容器**]。

    受控網域的內建密碼原則會顯示。 您無法修改此內建原則。 相反地，請建立自訂密碼原則來覆寫預設原則。

    ![在 Active Directory 管理中心中建立密碼原則](./media/password-policy/create-password-policy-adac.png)

1. 在右側 **的 [工作** ] 面板中，選取 [ **新增 > 密碼設定**]。
1. 在 [ **建立密碼設定** ] 對話方塊中，輸入原則的名稱，例如 *MyCustomFGPP*。
1. 如果有多個密碼原則，則會將具有最高優先順序或優先順序的原則套用至使用者。 編號愈低，優先順序愈高。 預設密碼原則的優先順序為 *200*。

    設定自訂密碼原則的優先順序以覆寫預設值，例如 *1*。

1. 視需要編輯其他密碼原則設定。 請記住下列要點：

    * 只有手動在受控網域中建立的使用者，才會有密碼複雜性、年齡或到期時間等設定。
    * 帳戶鎖定設定適用于所有使用者，但只會在受控網域中生效，而不會在 Azure AD 本身生效。

    ![建立自訂更細緻的密碼原則](./media/password-policy/custom-fgpp.png)

1. 取消核取 [ **防止意外刪除**]。 如果選取此選項，您就無法儲存 FGPP。
1. 在 [ **直接套用至** ] 區段中，選取 [ **加入** ] 按鈕。 在 [ **選取使用者或群組** ] 對話方塊中，選取 [ **位置** ] 按鈕。

    ![選取要套用密碼原則的使用者和群組](./media/password-policy/fgpp-applies-to.png)

1. 密碼原則只能套用至群組。 在 [ **位置** ] 對話方塊中，展開功能變數名稱（例如 *aaddscontoso.com*），然後選取 [ **AADDC 使用者**] 之類的 OU。 如果您的自訂 OU 包含您想要套用的使用者群組，請選取該 OU。

    ![選取群組所屬的 OU](./media/password-policy/fgpp-container.png)

1. 輸入您想要套用原則的組名，然後選取 [ **檢查名稱** ] 以驗證該群組是否存在。

    ![搜尋並選取要套用的群組 FGPP](./media/password-policy/fgpp-apply-group.png)

1. 使用您所選取的組名立即顯示在 [ **直接套用至** ] 區段中，選取 **[確定]** 以儲存您的自訂密碼原則。

## <a name="next-steps"></a>後續步驟

如需有關密碼原則和使用 Active Directory 系統管理中心的詳細資訊，請參閱下列文章：

* [深入瞭解更細緻的密碼原則](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [使用 AD 系統管理中心設定更細緻的密碼原則](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
