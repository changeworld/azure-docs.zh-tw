---
title: 在 Azure AD 網域服務建立與使用密碼策略 |微軟文件
description: 瞭解如何以及為什麼使用細粒度密碼策略來保護和控制 Azure AD DS 託管域中的帳戶密碼。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: b14fed07c9bd9b5fcb6a5489719481902351fc0d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654874"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>受控網域上的密碼和帳戶鎖定原則

要管理 Azure 活動目錄域服務 (Azure AD DS) 中的使用者安全性,可以定義控制帳戶鎖定設置或最小密碼長度和複雜性的細粒度密碼策略。 將創建預設細粒度密碼策略,並將其應用於 Azure AD DS 託管域中的所有使用者。 為了提供精細控制並滿足特定業務或合規性需求,可以創建其他策略並將其應用於特定的使用者組。

本文介紹如何使用活動目錄管理中心在 Azure AD DS 中創建和配置細粒度密碼策略。

> [!NOTE]
> 密碼策略僅適用於使用資源管理器部署模型創建的 Azure AD DS 託管域。 對傳統建立的舊的託管域,[從傳統虛擬網路模型移到資源管理員][migrate-from-classic]。

## <a name="before-you-begin"></a>開始之前

要完成本文,您需要以下資源和特權:

* 有效的 Azure 訂用帳戶。
  * 如果沒有 Azure 訂閱,[請建立帳號](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
  * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
  * 如果需要,請完成創建[和配置 Azure 活動目錄域服務實例][create-azure-ad-ds-instance]的教程。
  * Azure AD DS 實例必須使用資源管理員部署模型創建。 如果需要,[從傳統虛擬網路模型移到資源管理員][migrate-from-classic]。
* 加入到 Azure AD DS 託管域的 Windows 伺服器管理 VM。
  * 如果需要,請完成教學以建立[管理 VM][tutorial-create-management-vm]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員** 群組成員的使用者帳戶。

## <a name="default-password-policy-settings"></a>預設密碼原則設定

細粒度密碼策略 (FGP) 允許您對網域中的不同使用者應用密碼和帳戶鎖定策略的特定限制。 例如,要保護特權帳戶,您可以應用比常規非特權帳戶更嚴格的帳戶鎖定設置。 您可以在 Azure AD DS 託管域中創建多個 FGP,並指定優先順序順序以將其應用於使用者。

有關密碼策略和使用活動目錄管理中心的詳細資訊,請參閱以下文章:

* [瞭解細粒度密碼原則](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [使用 AD 管理中心設定小細階性密碼原則](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

策略通過 Azure AD DS 託管域中的組關聯分發,所做的任何更改都應用於下一個用戶登錄。 更改策略不會解鎖已鎖定的使用者帳戶。

密碼策略的行為略有不同,具體取決於它們應用於的使用者帳戶的創建方式。 在 Azure AD DS 中建立使用者帳戶有兩種方式:

* 可以從 Azure AD 同步使用者帳戶。 這包括直接在 Azure 中建立的僅雲端使用者帳戶,以及使用 Azure AD Connect 從本地 AD DS 環境同步的混合使用者帳戶。
    * Azure AD DS 中的大多數使用者帳戶都是通過 Azure AD 的同步過程創建的。
* 使用者帳戶可以在 Azure AD DS 託管域中手動創建,並且在 Azure AD 中不存在。

所有使用者,無論其創建方式如何,都應用了 Azure AD DS 中的預設密碼策略應用的以下帳戶鎖定策略:

* **帳號鎖定持續時間:** 30
* **允許的失敗登入次數:** 5
* **重置失敗登入嘗試計數後:** 30 分鐘
* **最大密碼期限(存留期):90**天

使用這些預設設置,如果在 2 分鐘內使用 5 個無效密碼,使用者帳戶將鎖定 30 分鐘。 30 分鐘後，帳戶會自動解除鎖定。

帳戶鎖定僅在託管域中發生。 使用者帳戶僅在 Azure AD DS 中鎖定,並且僅由於針對託管域的登錄嘗試失敗。 從 Azure AD 或本地同步的使用者帳戶不會鎖定在其源目錄中,僅在 Azure AD DS 中。

如果 Azure AD 密碼策略指定最大密碼期限大於 90 天,則該密碼期限將應用於 Azure AD DS 中的預設策略。 可以配置自定義密碼策略,以在 Azure AD DS 中定義不同的最大密碼期限。 如果 Azure AD DS 密碼策略中配置的最大密碼期限比 Azure AD 或本地 AD DS 環境中的密碼期限短,請小心。 在這種情況下,使用者的密碼可能會在 Azure AD DS 中過期,然後再提示他們在 Azure AD 或本地 AD DS 環境中進行更改。

對於在 Azure AD DS 託管域中手動創建的使用者帳戶,也會從預設策略中應用以下附加密碼設置。 這些設置不適用於從 Azure AD 同步的使用者帳戶,因為使用者無法直接在 Azure AD DS 中更新其密碼。

* **最小密碼長度(字元):** 7
* **密碼必須符合複雜性需求**

無法修改預設密碼策略中的帳戶鎖定或密碼設定。 相反 *,AAD DC 管理員*組的成員可以創建自定義密碼策略,並將其配置為覆蓋(優先於)預設的內置策略,如下一節所示。

## <a name="create-a-custom-password-policy"></a>建立自訂密碼原則

在 Azure 中生成和運行應用程式時,可能需要配置自定義密碼策略。 例如,您可以創建策略來設置不同的帳戶鎖定策略設置。

自定義密碼策略應用於 Azure AD DS 託管域中的組。 此配置有效地覆蓋預設策略。

要建立自定義密碼策略,請使用加入網域的 VM 中的 Active Directory 管理工具。 活動目錄管理中心允許您在 Azure AD DS 託管域(包括 OEM)中查看、編輯和創建資源。

> [!NOTE]
> 要在 Azure AD DS 託管域中創建自定義密碼策略,必須登入*屬於 AAD DC 管理員*組成員的使用者帳戶。

1. 在"開始"螢幕中,選擇 **"管理工具**"。 在本教學中顯示用於[創建管理 VM][tutorial-create-management-vm]的可用管理工具的清單。
1. 要建立與管理 O,請從管理工具清單中選擇**活動目錄管理中心**。
1. 在左邊窗格中,選擇 Azure AD DS 託管域,如*aaddscontoso.com*。
1. 開啟**系統**容器,然後開啟**密碼設定容器**。

    將顯示 Azure AD DS 託管域的內建密碼策略。 無法修改此內置策略。 而是創建自定義密碼策略以覆蓋預設策略。

    ![在活動目錄管理中心建立密碼原則](./media/password-policy/create-password-policy-adac.png)

1. 在右側的 **「任務」** 面板中,選擇 **「新>密碼設置**」。
1. 在 **「建立密碼設定」** 對話框中,輸入策略的名稱,如*MyCustomFGPP*。
1. 如果存在多個密碼策略,則具有最高優先順序或優先順序的策略將應用於使用者。 編號愈低，優先順序愈高。 預設密碼原則的優先權為*200*。

    將自訂密碼策略的優先權設定為覆蓋預設值,例如*1*。

1. 根據需要編輯其他密碼策略設置。 記住以下要點:

    * 密碼複雜性、期限或過期時間等設置僅對在 Azure AD DS 託管域中手動創建的用戶進行設置。
    * 帳戶鎖定設置適用於所有使用者,但僅在託管域中生效,而不是在 Azure AD 本身中生效。

    ![建立自訂細粒度密碼原則](./media/password-policy/custom-fgpp.png)

1. 取消選取**的取消 。** 如果選擇了此選項,則無法保存 FGPP。
1. 在「**直接應用於」** 部分中,選擇「**添加**」 按鈕。 在 **「選擇使用者或群組**」對話框中,選擇「**位置**」 按鈕。

    ![選擇要將密碼原則套用於](./media/password-policy/fgpp-applies-to.png)

1. 密碼策略只能應用於組。 在 **「位置」** 對話框中,展開網域名稱(如*aaddscontoso.com*,然後選擇 OU,如**AADDC 使用者**。 如果您有一個自定義 OU,其中包含要應用的一組使用者,請選擇該 OU。

    ![選擇群組的權的 OU](./media/password-policy/fgpp-container.png)

1. 鍵入要應用策略的組的名稱,然後選擇 **「檢查名稱」** 以驗證該組是否存在。

    ![搜尋並選擇要套用 FGPP 的群組](./media/password-policy/fgpp-apply-group.png)

1. 使用您現在選擇的組的名稱顯示在 **「直接應用於」** 部分中,選擇 **「確定」** 以儲存自訂密碼策略。

## <a name="next-steps"></a>後續步驟

有關密碼策略和使用活動目錄管理中心的詳細資訊,請參閱以下文章:

* [瞭解細粒度密碼原則](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [使用 AD 管理中心設定小細階性密碼原則](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
