---
title: 啟用本地 Azure AD 密碼保護
description: 瞭解如何為本地活動目錄域服務環境啟用 Azure AD 密碼保護
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d00a8b84477226b68913f95c5121bbbdfc2eb09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263812"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>啟用本地 Azure 活動目錄密碼保護

使用者通常創建使用常見本地單詞（如學校、運動隊或名人）的密碼。 這些密碼很容易猜測，並且對基於字典的攻擊很弱。 要在組織中強制實施強式密碼，Azure 活動目錄 （Azure AD） 密碼保護提供了全域和自訂禁止的密碼清單。 如果這些禁用密碼清單中有匹配項，則密碼變更要求將失敗。

為了保護本地活動目錄域服務 （AD DS） 環境，可以安裝和配置 Azure AD 密碼保護以使用上置目錄 DC。 本文介紹如何為本地環境啟用 Azure AD 密碼保護。

有關 Azure AD 密碼保護在本地環境中的工作方式的詳細資訊，請參閱[如何為 Windows 伺服器活動目錄強制實施 Azure AD 密碼保護](concept-password-ban-bad-on-premises.md)。

## <a name="before-you-begin"></a>開始之前

本文介紹如何為本地環境啟用 Azure AD 密碼保護。 在完成本文之前，請在本地 AD DS 環境中[安裝和註冊 Azure AD 密碼保護代理服務和 DC 代理](howto-password-ban-bad-on-premises-deploy.md)。

## <a name="enable-on-premises-password-protection"></a>啟用本地密碼保護

1. 登錄到 Azure[門戶](https://portal.azure.com)並流覽到**Azure 活動目錄** > **安全** > **身份驗證方法** > **密碼保護**。
1. 將 Windows**伺服器活動目錄中啟用密碼保護**的選項設置為 *"是*"。

    當此設置設置為 *"否*"時，所有已部署的 Azure AD 密碼保護 DC 代理進入靜止模式，所有密碼均接受原樣。 不執行任何驗證活動，也不會生成審核事件。

1. 建議最初將**模式**設置為*審核*。 熟悉該功能及其對組織中使用者的影響後，可以將**模式**切換到*強制*。 有關詳細資訊，請參閱以下有關[操作模式的部分](#modes-of-operation)。
1. 在準備就緒時，選取 [儲存]****。

    [![](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png "Enable on-premises password protection under Authentication Methods in the Azure portal")](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>作業模式

啟用本地 Azure AD 密碼保護時，可以使用*審核*模式或*強制*模式。 我們建議初始部署和測試始終以稽核模式開始。 然後，應監視事件日誌中的條目，以預測啟用*強制*模式後，是否存在任何現有操作進程是否會受到干擾。

### <a name="audit-mode"></a>稽核模式

*審核*模式旨在作為在"如果"模式下運行軟體的一種方式。 每個 Azure AD 密碼保護 DC 代理服務都根據當前活動策略評估傳入密碼。

如果當前策略配置為處於稽核模式，"壞"密碼會導致事件日誌消息，但會處理和更新。 此行為是審核和強制模式之間的唯一區別。 所有其他操作都運行相同。

### <a name="enforced-mode"></a>強制模式

*強制*模式旨在作為最終配置。 與稽核模式一樣，每個 Azure AD 密碼保護 DC 代理服務根據當前活動策略評估傳入密碼。 但是，啟用強制模式時，根據策略被視為不安全的密碼將被拒絕。

當 Azure AD 密碼保護 DC 代理在強制模式下拒絕密碼時，最終使用者將看到類似的錯誤，就像他們看到其密碼是否被傳統的本地密碼複雜性強制拒絕一樣。 例如，使用者可能在 Windows 登錄或更改密碼螢幕看到以下傳統錯誤訊息：

*"無法更新密碼。為新密碼提供的值不符合域的長度、複雜性或歷史記錄要求。*

這則訊息只是數個可能結果的其中一個範例。 特定錯誤訊息可能因嘗試設置不安全密碼的實際軟體或方案而異。

受影響的最終使用者可能需要與其 IT 人員合作，以瞭解新要求並選擇安全密碼。

> [!NOTE]
> Azure AD 密碼保護無法控制用戶端電腦在拒絕弱密碼時顯示的特定錯誤訊息。

## <a name="next-steps"></a>後續步驟

要自訂群組織的禁止密碼清單，請參閱[配置 Azure AD 密碼保護自訂禁止密碼清單](tutorial-configure-custom-password-protection.md)。

要監視上前事件，請參閱[監視前 Azure AD 密碼保護](howto-password-ban-bad-on-premises-monitor.md)。
