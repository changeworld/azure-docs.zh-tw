---
title: 啟用內部部署 Azure AD 密碼保護
description: 瞭解如何為內部部署 Active Directory Domain Services 環境啟用 Azure AD 密碼保護
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ee0f3d89d48b23db48e3bf4b78203b09fbcbdbd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80652626"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>啟用內部部署 Azure Active Directory 密碼保護

使用者通常會建立使用像是學校、體育小組或知名人等常見當地用語的密碼。 這些密碼很容易猜測，而且不會受到字典型攻擊的危害。 為了在您的組織中強制使用強式密碼，Azure Active Directory （Azure AD）密碼保護提供了全域和自訂的禁用密碼清單。 如果這些禁用密碼清單中有相符的結果，密碼變更要求就會失敗。

若要保護您的內部部署 Active Directory Domain Services （AD DS）環境，您可以安裝和設定 Azure AD 密碼保護，以與您的內部內部部署 DC 搭配使用。 本文說明如何為您的內部部署環境啟用 Azure AD 密碼保護。

如需有關 Azure AD 密碼保護在內部部署環境中如何運作的詳細資訊，請參閱[如何強制執行 Windows Server 的 Azure AD 密碼保護 Active Directory](concept-password-ban-bad-on-premises.md)。

## <a name="before-you-begin"></a>開始之前

本文說明如何為您的內部部署環境啟用 Azure AD 密碼保護。 在您完成本文之前，請在內部部署 AD DS 環境中[安裝並註冊 Azure AD 的密碼保護 proxy 服務和 DC 代理](howto-password-ban-bad-on-premises-deploy.md)程式。

## <a name="enable-on-premises-password-protection"></a>啟用內部部署密碼保護

1. 登入[Azure 入口網站](https://portal.azure.com)，然後流覽至**Azure Active Directory**  >  **安全性**  >  **驗證方法**  >  **密碼保護**。
1. 將 [**啟用 Windows Server 上的密碼保護 Active Directory** ] 的選項設定為 *[是]*。

    當此設定設為 [*否*] 時，所有已部署的 Azure AD 密碼保護 DC 代理程式都會進入靜止模式，其中所有的密碼都已接受。 不會執行任何驗證活動，也不會產生 audit 事件。

1. 建議您一開始先將**模式**設定為*Audit*。 在您熟悉此功能以及對組織中的使用者所造成的影響之後，您可以將**模式**切換為 [*強制*]。 如需詳細資訊，請參閱下一節的[操作模式](#modes-of-operation)。
1. 在準備就緒時，選取 [儲存]。

    [![](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png "Enable on-premises password protection under Authentication Methods in the Azure portal")](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>作業模式

當您啟用內部部署 Azure AD 密碼保護時，您可以使用 [ *audit* ] 模式或 [*強制執行*] 模式。 我們建議您一律以 audit 模式開始進行初始部署和測試。 接著，您應該監視事件記錄檔中的專案，以預期在啟用*強制*模式後是否會干擾任何現有的操作程式。

### <a name="audit-mode"></a>稽核模式

「 *Audit*模式」是用來在「假設」模式下執行軟體的方式。 每個 Azure AD 密碼保護 DC 代理程式服務都會根據目前作用中的原則來評估傳入的密碼。

如果目前的原則設定為處於 audit 模式，則「錯誤」密碼會導致事件記錄檔訊息，但會進行處理和更新。 此行為是「audit」和「強制執行」模式之間的唯一差異。 所有其他作業執行相同的工作。

### <a name="enforced-mode"></a>強制模式

*強制執行*模式的目的是做為最終的設定。 如同在 audit 模式時，每個 Azure AD 密碼保護 DC 代理程式服務都會根據目前作用中的原則來評估傳入的密碼。 不過，啟用強制模式時，會拒絕根據原則視為不安全的密碼。

當 Azure AD 密碼保護 DC 代理程式在強制執行模式中拒絕密碼時，終端使用者會看到類似的錯誤，像是在傳統內部部署密碼複雜度強制執行時，他們會看到其密碼是否遭到拒絕。 例如，使用者可能會在 Windows 登入或變更密碼畫面看到下列傳統錯誤訊息：

*「無法更新密碼。為新密碼提供的值不符合網域的長度、複雜度或歷程記錄需求。」*

這則訊息只是數個可能結果的其中一個範例。 特定的錯誤訊息會根據嘗試設定不安全密碼的實際軟體或案例而有所不同。

受影響的使用者可能需要與 IT 人員合作，以瞭解新的需求，並選擇安全的密碼。

> [!NOTE]
> 當弱式密碼遭到拒絕時，Azure AD 密碼保護無法控制用戶端電腦所顯示的特定錯誤訊息。

## <a name="next-steps"></a>後續步驟

若要為您的組織自訂禁用密碼清單，請參閱[設定 Azure AD 密碼保護自訂禁用密碼清單](tutorial-configure-custom-password-protection.md)。

若要監視內部部署上的事件，請參閱[監視內部內部部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-monitor.md)。
