---
title: 密碼保護作業和報告-Azure Active Directory
description: Azure AD 的密碼保護部署後作業和報告
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2440d373b726b4f97cd5d9ba162daaa0714f79e0
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155038"
---
# <a name="azure-ad-password-protection-operational-procedures"></a>Azure AD 密碼保護作業程序

完成在內部部署環境[安裝 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)後，您必須在 Azure 入口網站中設定一些項目。

## <a name="configure-the-custom-banned-password-list"></a>設定自訂禁用密碼清單

請遵循[設定自訂禁用密碼清單](howto-password-ban-bad-configure.md)一文中的指導方針，以了解為組織自訂禁用密碼清單的步驟。

## <a name="enable-password-protection"></a>啟用密碼保護

1. 登入[Azure 入口網站](https://portal.azure.com)，然後流覽至**Azure Active Directory** > 的**安全性** > **驗證方法** > **密碼保護**。
1. 將 [啟用 Windows Server Active Directory 上的密碼保護] 設定為 [是]
1. 如[部署指南](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy)所述，建議您一開始就將 [模式] 設定為 [稽核]
   * 對功能感到滿意後，您可以將 [模式] 切換為 [已強制]
1. 按一下 [儲存]

![在 Azure 入口網站中啟用 Azure AD 密碼保護元件](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>稽核模式

稽核模式的目的是為了在「What If」模式中執行軟體。 每個 DC 代理程式服務都會根據目前作用中的原則來評估傳入的密碼。 如果目前的原則設定為要在稽核模式中執行，「不良」密碼就會產生事件記錄訊息，但系統會接受該密碼。 這是稽核和強制執行模式的唯一差別；其他所有作業的執行方式則相同。

> [!NOTE]
> Microsoft 建議您一律讓初始部署和測試在稽核模式中開始進行。 然後，請監視事件記錄中的事件，以嘗試預期在啟用強制執行模式後，是否會干擾任何現有的作業程序。

## <a name="enforce-mode"></a>強制執行模式

強制執行模式的目的是為了作為最終設定。 和上述在稽核模式時一樣，每個 DC 代理程式服務都會根據目前作用中的原則來評估傳入的密碼。 但如果啟用強制執行模式，則系統會拒絕根據原則而視為不安全的密碼。

當 Azure AD 密碼保護 DC 代理程式在強制執行模式中拒絕密碼時，終端使用者所見到的影響，會和傳統內部部署密碼複雜度強制執行功能拒絕其密碼時所見到的影響相同。 例如，使用者可能會在 Windows 登入\變更密碼畫面看到下列傳統錯誤訊息：

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

這則訊息只是數個可能結果的其中一個範例。 特定錯誤訊息會隨著嘗試設定不安全密碼的實際軟體或案例而有所不同。

受影響的終端使用者可能需要與其 IT 人員合作，以了解新的需求，並更加能夠選擇安全的密碼。

> [!NOTE]
> 當弱式密碼遭到拒絕時，Azure AD 密碼保護無法控制用戶端電腦所顯示的特定錯誤訊息。

## <a name="enable-mode"></a>啟用模式

此設定應保持為其預設啟用（是）狀態。 將此設定設為停用 (否)，會導致所有已部署的 Azure AD 密碼保護 DC 代理程式進入靜止模式，依現況接受其中的所有密碼，而在任何情況下，都不會執行任何驗證活動 (例如，甚至不會發出稽核事件)。

## <a name="next-steps"></a>後續步驟

[Azure AD 密碼保護的監視功能](howto-password-ban-bad-on-premises-monitor.md)
