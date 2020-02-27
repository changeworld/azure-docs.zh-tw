---
title: 在 Azure AD Domain Services 中建立及使用密碼原則 |Microsoft Docs
description: 瞭解如何以及為何要使用更細緻的密碼原則來保護及控制 Azure AD DS 受控網域中的帳戶密碼。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: c4402c1ce2f051c8d1911e7c0332d4cac787ce1d
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77613206"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>受控網域上的密碼和帳戶鎖定原則

若要管理 Azure Active Directory Domain Services （Azure AD DS）中的使用者安全性，您可以定義更細緻的密碼原則，以控制帳戶鎖定設定或最小密碼長度和複雜度。 系統會建立預設的更細緻密碼原則，並將其套用至 Azure AD DS 受控網域中的所有使用者。 若要提供更細微的控制，並符合特定的商務或合規性需求，可以建立額外的原則，並將其套用至特定的使用者群組。

本文說明如何使用 Active Directory 管理中心在 Azure AD DS 中建立及設定更細緻的密碼原則。

> [!NOTE]
> 密碼原則僅適用于使用 Resource Manager 部署模型建立的 Azure AD DS 受控網域。 針對使用傳統建立的較舊受控網域，請[從傳統虛擬網路模型遷移至 Resource Manager][migrate-from-classic]。

## <a name="before-you-begin"></a>開始之前

若要完成本文，您需要下列資源和許可權：

* 有效的 Azure 訂用帳戶。
  * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
  * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
  * 如有需要，請完成教學課程，以[建立及設定 Azure Active Directory Domain Services 實例][create-azure-ad-ds-instance]。
  * Azure AD DS 實例必須已使用 Resource Manager 部署模型建立。 如有需要，請[從傳統虛擬網路模型遷移至 Resource Manager][migrate-from-classic]。
* 已加入 Azure AD DS 受控網域的 Windows Server 管理 VM。
  * 如有需要，請完成教學課程以[建立管理 VM][tutorial-create-management-vm]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員群組成員的使用者帳戶。

## <a name="default-password-policy-settings"></a>預設密碼原則設定

更細緻的密碼原則（Fgpp）可讓您將密碼和帳戶鎖定原則的特定限制套用到網域中的不同使用者。 例如，若要保護特殊許可權帳戶，您可以套用比一般非特殊許可權帳戶更嚴格的帳戶鎖定設定。 您可以在 Azure AD DS 受控網域內建立多個 Fgpp，並指定優先順序來將它們套用至使用者。

原則是透過 Azure AD DS 受控網域中的群組關聯來散發，而您所做的任何變更都會在下次使用者登入時套用。 變更原則並不會解除鎖定已鎖定的使用者帳戶。

密碼原則的行為稍有不同，取決於建立使用者帳戶的方式。 有兩種方式可以在 Azure AD DS 中建立使用者帳戶：

* 使用者帳戶可以從 Azure AD 同步處理。 這包括直接在 Azure 中建立的僅限雲端使用者帳戶，以及使用 Azure AD Connect 從內部部署 AD DS 環境同步處理的混合式使用者帳戶。
    * Azure AD DS 中的大部分使用者帳戶都是透過 Azure AD 的同步處理常式來建立。
* 使用者帳戶可以在 Azure AD DS 受控網域中手動建立，而且不存在於 Azure AD 中。

所有使用者無論建立的方式為何，都具有下列 Azure AD DS 中的預設密碼原則所套用的帳戶鎖定原則：

* **帳戶鎖定持續時間：** 30
* **允許的失敗登入嘗試次數：** 5
* **重設失敗的登入嘗試次數：** 30 分鐘
* **密碼最長使用期限（存留期）：** 90 天

使用這些預設設定，如果在2分鐘內使用5個不正確密碼，使用者帳戶就會被鎖定30分鐘。 30 分鐘後，帳戶會自動解除鎖定。

帳戶鎖定只會發生在受控網域中。 使用者帳戶只會在 Azure AD DS 中被鎖定，而且只會因為對受控網域的登入嘗試失敗而受到限制。 從 Azure AD 或內部部署環境中同步處理的使用者帳戶，不會在其來原始目錄中被鎖定，只會在 Azure AD DS 中遭到封鎖。

如果您的 Azure AD 密碼原則指定超過90天的密碼最長使用期限，則會將密碼使用期限套用至 Azure AD DS 中的預設原則。 您可以設定自訂密碼原則，在 Azure AD DS 中定義不同的密碼最長使用期限。 如果 Azure AD DS 密碼原則中所設定的密碼最長使用期限比 Azure AD 或內部部署 AD DS 環境還短，請小心。 在該案例中，使用者的密碼可能會在 Azure AD DS 中到期，然後系統會提示他們在 Azure AD 或內部部署 AD DS 環境中進行變更。

針對在 Azure AD DS 受控網域中手動建立的使用者帳戶，也會從預設原則套用下列額外的密碼設定。 這些設定不適用於從 Azure AD 同步處理的使用者帳戶，因為使用者無法直接在 Azure AD DS 中更新其密碼。

* **密碼長度下限（字元數）：** 7
* **密碼必須符合複雜性需求**

您無法修改預設密碼原則中的帳戶鎖定或密碼設定。 相反地， *AAD DC 系統管理員*群組的成員可以建立自訂密碼原則，並將它設定為覆寫（優先于）預設的內建原則，如下一節所示。

## <a name="create-a-custom-password-policy"></a>建立自訂密碼原則

當您在 Azure 中建立並執行應用程式時，您可能會想要設定自訂密碼原則。 例如，您可以建立原則來設定不同的帳戶鎖定原則設定。

自訂密碼原則會套用至 Azure AD DS 受控網域中的群組。 此設定會有效覆寫預設原則。

若要建立自訂密碼原則，您可以使用來自已加入網域之 VM 的 Active Directory 系統管理工具。 此 Active Directory 管理中心可讓您在 Azure AD DS 受控網域（包括 Ou）中，查看、編輯和建立資源。

> [!NOTE]
> 若要在 Azure AD DS 受控網域中建立自訂密碼原則，您必須登入屬於*AAD DC 系統管理員*群組成員的使用者帳戶。

1. 從 [開始] 畫面中，選取 [系統**管理工具**]。 已安裝在教學課程中的可用管理工具清單，以[建立管理 VM][tutorial-create-management-vm]。
1. 若要建立和管理 Ou，請從系統管理工具清單中選取 [ **Active Directory 管理中心**]。
1. 在左窗格中，選擇您的 Azure AD DS 受控網域，例如*aaddscontoso.com*。
1. 開啟 [**系統**] 容器，然後按 [**密碼設定容器**]。

    隨即顯示 Azure AD DS 受控網域的內建密碼原則。 您無法修改此內建原則。 相反地，請建立自訂密碼原則來覆寫預設原則。

    ![在 Active Directory 管理中心中建立密碼原則](./media/password-policy/create-password-policy-adac.png)

1. **在右側的 [工作]** 面板中，選取 [新增] > [**密碼設定**]。
1. 在 [**建立密碼設定**] 對話方塊中，輸入原則的名稱，例如*MyCustomFGPP*。
1. 當有多個密碼原則時，具有最高優先順序或優先順序的原則會套用至使用者。 編號愈低，優先順序愈高。 預設密碼原則的優先順序為*200*。

    設定自訂密碼原則的優先順序以覆寫預設值，例如*1*。

1. 視需要編輯其他密碼原則設定。 請記住下列重點：

    * 只有手動在 Azure AD DS 受控網域中建立之使用者的密碼複雜性、存留期或到期時間等設定。
    * 帳戶鎖定設定會套用至所有使用者，但只會在受控網域中生效，而不會在 Azure AD 本身中生效。

    ![建立自訂的更細緻密碼原則](./media/how-to/custom-fgpp.png)

1. 取消核取 [**防止意外刪除**]。 如果選取此選項，您就無法儲存 FGPP。
1. 在 [**直接套用至**] 區段中，選取 [**新增**] 按鈕。 在 [**選取使用者或群組**] 對話方塊中，選取 [**位置**] 按鈕。

    ![選取要套用密碼原則的使用者和群組](./media/how-to/fgpp-applies-to.png)

1. 密碼原則只能套用至群組。 在 [**位置**] 對話方塊中，展開功能變數名稱，例如*aaddscontoso.com*，然後選取 OU，例如**AADDC Users**。 如果您有一個自訂 OU，其中包含您想要套用的使用者群組，請選取該 OU。

    ![選取群組所屬的 OU](./media/how-to/fgpp-container.png)

1. 輸入您想要套用原則的組名，然後選取 [**檢查名稱**] 以驗證該群組是否存在。

    ![搜尋並選取要套用 FGPP 的群組](./media/how-to/fgpp-apply-group.png)

1. 在 [**直接套用至**] 區段中，使用您所選取的組名，選取 **[確定]** 儲存您的自訂密碼原則。

## <a name="next-steps"></a>後續步驟

如需密碼原則和使用 Active Directory 系統管理中心的詳細資訊，請參閱下列文章：

* [深入瞭解更細緻的密碼原則](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [使用 AD 系統管理中心設定更細緻的密碼原則](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
