---
title: Azure Active Directory 中的風險型使用者登入保護
description: 在本教學課程中，您將了解如何啟用 Azure Identity Protection，以在偵測到使用者帳戶上的登入行為有風險時保護使用者。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d0fcd57a71baec54fbed2dd41a936895ad9a462
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966571"
---
# <a name="tutorial-use-risk-detections-for-user-sign-ins-to-trigger-azure-multi-factor-authentication-or-password-changes"></a>教學課程：利用使用者登入的風險偵測來觸發 Azure Multi-Factor Authentication 或密碼變更

若要保護您的使用者，您可以在 Azure Active Directory (Azure AD) 中設定會自動因應風險行為的風險型原則。 Azure AD Identity Protection 原則可以自動封鎖登入嘗試或要求額外的動作，例如，要求變更密碼或提示進行 Azure Multi-Factor Authentication。 這些原則可以與現有的 Azure AD 條件式存取原則搭配使用，為組織提供多一層的保護。 使用者雖然不太可能觸發這類原則中的風險行為，但若有人試圖危害您的安全性，組織將會受到保護。

> [!IMPORTANT]
> 此教學課程將說明系統管理員如何啟用風險型 Azure Multi-Factor Authentication。
>
> 如果您的 IT 小組尚未啟用使用 Azure Multi-Factor Authentication 的功能，或您在進行單一登入時遇到問題，請與您的技術服務人員聯繫以取得其他協助。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 了解 Azure AD Identity Protection 的可用原則
> * 啟用 Azure Multi-Factor Authentication 註冊
> * 啟用風險型密碼變更
> * 啟用風險型 Multi-Factor Authentication
> * 測試使用者登入嘗試的風險型原則

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要下列資源和權限：

* 至少已啟用 Azure AD Premium P2 或試用版授權的有效 Azure AD 租用戶。
    * 如有需要，[請建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 具有「全域系統管理員」權限的帳戶。
* 已設定自助式密碼重設和 Azure Multi-Factor Authentication 的 Azure AD
    * 如有需要，請[完成教學課程以啟用 Azure AD SSPR](tutorial-enable-sspr.md)。
    * 如有需要，請[完成教學課程以啟用 Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md)。

## <a name="overview-of-azure-ad-identity-protection"></a>Azure AD Identity Protection 概觀

Microsoft 每天都會從使用者登入嘗試中收集到數兆個匿名訊號，並加以分析。 這些訊號有助於建置良好使用者登入行為的模式，以及識別可能有風險的登入嘗試。 Azure AD Identity Protection 可以檢閱使用者登入嘗試，並在發現可疑行為時採取進一步的動作：

下列某些動作可能會觸發 Azure AD Identity Protection 進行風險偵測：

* 認證外洩的使用者。
* 從匿名 IP 位址登入。
* 以不可能的方式移動到非慣用位置。
* 從受感染的裝置登入。
* 從具有可疑活動的 IP 位址登入。
* 從不熟悉的位置登入。

Azure AD Identity Protection 提供下列三項原則，用以保護使用者及因應可疑的活動。 您可以選擇開啟或關閉原則的強制執行、選取要套用原則的使用者或群組，以及決定是要在登入時封鎖存取，還是提示使用執行額外的動作。

* 使用者風險原則
    * 識別認證可能已遭入侵的使用者帳戶，並加以因應。 可提示使用者建立新的密碼。
* 登入風險原則
    * 識別可疑的登入嘗試，並加以因應。 可提示使用者使用 Azure Multi-Factor Authentication 提供其他形式的驗證。
* MFA 註冊原則
    * 確定使用者已註冊 Azure Multi-Factor Authentication。 如果登入風險原則提示須進行 MFA，使用者必須已註冊 Azure Multi-Factor Authentication。

當您啟用原則使用者或登入風險原則時，您也可以選擇風險層級的閾值 - *低 (含) 以上*、*中 (含) 以上*或*高*。 此彈性可讓您決定對可疑的登入事件強制執行任何控制措施的積極程度。

如需 Azure AD Identity Protection 的詳細資訊，請參閱[什麼是 Azure AD Identity Protection？](../identity-protection/overview-identity-protection.md)

## <a name="enable-mfa-registration-policy"></a>啟用 MFA 註冊原則

Azure AD Identity Protection 包含預設原則，可協助使用者進行 Azure Multi-Factor Authentication 註冊。 如果您使用其他原則來保護登入事件，您的使用者必須已註冊 MFA。 此原則啟用時並不會要求使用者在每次登入時執行 MFA。 此原則只會檢查使用者的註冊狀態，並要求他們在必要時預先註冊。

建議您為要啟用其他 Azure AD Identity Protection 原則的使用者啟用 MFA 註冊原則。 若要啟用此原則，請完成下列步驟：

1. 使用全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 **Azure Active Directory**，選取 [安全性]，然後在 [保護] 功能表標題下，選擇 [Identity Protection]。
1. 從左側功能表中選取 [MFA 註冊原則]。
1. 根據預設，原則會套用至 *所有使用者*。 如有需要，請選取 [指派]，然後選擇要套用原則的使用者或群組。
1. 在 [控制項] 底下，選取 [存取]。 請確定已核取 [需要 Azure MFA 註冊] 選項，然後選擇 [選取]。
1. 將 [強制執行原則] 設定為 [開啟]，然後選取 [儲存]。

    ![如何要求使用者在 Azure 入口網站中註冊 MFA 的螢幕擷取畫面](./media/tutorial-risk-based-sspr-mfa/enable-mfa-registration.png)

## <a name="enable-user-risk-policy-for-password-change"></a>啟用密碼變更的使用者風險原則

Microsoft 與研究人員、執法機關，Microsoft 的各個安全性小組和其他受信任的來源合作，以找出使用者名稱和密碼的配對。 當其中一個配對符合您環境中的帳戶時，就可以要求進行風險型密碼變更。 此原則和動作會要求使用者必須先更新其密碼才能登入，以確保任何先前公開的認證都不再有效。

若要啟用此原則，請完成下列步驟：

1. 從左側功能表中選取 [使用者風險原則]。
1. 根據預設，原則會套用至 *所有使用者*。 如有需要，請選取 [指派]，然後選擇要套用原則的使用者或群組。
1. 在 [條件] 底下選擇 [選取條件] > [選取風險層級]，然後選擇 [中 (含) 以上]。
1. 選擇 [選取]，然後選擇 [完成]。
1. 在 [存取] 底下，選取 [存取]。 請確定已核取 [允許存取] 和 [需要變更密碼] 的選項，然後選擇 [選取]。
1. 將 [強制執行原則] 設定為 [開啟]，然後選取 [儲存]。

    ![如何在 Azure 入口網站中啟用使用者風險原則的螢幕擷取畫面](./media/tutorial-risk-based-sspr-mfa/enable-user-risk-policy.png)

## <a name="enable-sign-in-risk-policy-for-mfa"></a>針對 MFA 啟用登入風險原則

大部分的使用者都會有可追蹤的正常行為。 當其行為與以往不同時，允許他們順利登入可能會有風險。 此時，您可以封鎖該使用者，或要求他們執行多重要素驗證。 如果使用者成功完成 MFA 挑戰，您即可將其視為有效的登入嘗試，並為其授與應用程式或服務的存取權。

若要啟用此原則，請完成下列步驟：

1. 從左側功能表中選取 [登入風險原則]。
1. 根據預設，原則會套用至 *所有使用者*。 如有需要，請選取 [指派]，然後選擇要套用原則的使用者或群組。
1. 在 [條件] 底下選擇 [選取條件] > [選取風險層級]，然後選擇 [中 (含) 以上]。
1. 選擇 [選取]，然後選擇 [完成]。
1. 在 [存取] 底下，選擇 [選取控制項]。 請確定已核取 [允許存取] 和 [需要多重要素驗證] 的選項，然後選擇 [選取]。
1. 將 [強制執行原則] 設定為 [開啟]，然後選取 [儲存]。

    ![如何在 Azure 入口網站中啟用登入風險原則的螢幕擷取畫面](./media/tutorial-risk-based-sspr-mfa/enable-sign-in-risk-policy.png)

## <a name="test-risky-sign-events"></a>測試有風險的登入事件

大部分的使用者登入事件並不會觸發先前的步驟中所設定的風險型原則。 使用者可能永遠不會看到額外執行 MFA 或重設密碼的提示。 如果其認證保有安全性，且其行為一致，其登入事件就會成功。

若要測試在先前的步驟中建立的 Azure AD Identity Protection 原則，您必須能夠模擬有風險的行為或潛在的攻擊。 執行這些測試的步驟，會根據您要驗證的 Azure AD Identity Protection 原則而有所不同。 如需案例和步驟的詳細資訊，請參閱[模擬 Azure AD Identity Protection 中的風險偵測](../identity-protection/howto-identity-protection-simulate-risk.md)。

## <a name="clean-up-resources"></a>清除資源

如果您已完成測試，且不再需要啟用風險型原則，請回到您要停用的每個原則，並將 [強制執行原則] 設定為 [關閉]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已針對 Azure AD Identity Protection 啟用風險型使用者原則。 您已了解如何︰

> [!div class="checklist"]
> * 了解 Azure AD Identity Protection 的可用原則
> * 啟用 Azure Multi-Factor Authentication 註冊
> * 啟用風險型密碼變更
> * 啟用風險型 Multi-Factor Authentication
> * 測試使用者登入嘗試的風險型原則

> [!div class="nextstepaction"]
> [深入了解 Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)
