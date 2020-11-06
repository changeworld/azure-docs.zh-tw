---
title: Azure AD Connect︰當您已經有 Azure AD 時 | Microsoft Docs
description: 本主題描述當您有現有的 Azure AD 租用戶時，如何使用 Connect。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68251270b6273f5a07391138e5c7210f1c46ba5a
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420524"
---
# <a name="azure-ad-connect-when-you-have-an-existing-tenant"></a>Azure AD Connect：當您有現有的租使用者時
大部分說明如何使用 Azure AD Connect 的主題會假設您開始使用新的 Azure AD 租用戶，而且沒有任何使用者或其他物件。 但如果您開始使用 Azure AD 租用戶，且以使用者和其他物件填入，而現在想要使用 Connect，則這個主題很適用於您。

## <a name="the-basics"></a>基本概念
Azure AD 中的物件可能會在雲端 (Azure AD) 中受控或內部部署。 針對一個單一物件，您無法管理內部部署的某些屬性和 Azure AD 中的一些其他屬性。 每個物件都有表示物件受控位置的旗標。

您可以在內部部署管理部分使用者，並在雲端中管理其他使用者。 混合著會計工作者和銷售工作者的組織是此組態的常見案例。 會計工作者具有內部部署 AD 帳戶，但銷售工作者沒有這種帳戶，他們在 Azure AD 中有帳戶。 您可在內部部署管理部分使用者，並在 Azure AD 管理其他使用者。

如果您開始管理 Azure AD 且也在內部部署 AD 中的使用者，且稍後想要使用 Connect，則您必須考慮一些其他考量。

## <a name="sync-with-existing-users-in-azure-ad"></a>在 Azure AD 中與現有的使用者同步處理
當您安裝 Azure AD Connect 並開始同步處理時，Azure AD) 中的 Azure AD 同步處理服務 (會檢查每個新的物件，並嘗試尋找要比對的現有物件。 有三種屬性用於此處理程序︰ **userPrincipalName** 、 **proxyAddresses** 和 **sourceAnchor**/**immutableID** 。 比對 **userPrincipalName** 和 **proxyAddresses** 稱為 **大致比對** 。 比對 **sourceAnchor** 稱為 **精確比對** 。 針對 **proxyAddresses** 屬性，僅具有 **SMTP:** 的值 (也就是主要電子郵件地址) 會用來進行評估。

比對只會評估來自 Connect 的新物件。 如果您變更現有的物件，而其與這些屬性中任何一項相符，則您會看到錯誤。

如果 Azure AD 找到的物件當中屬性值與來自 Connect 的物件相同，且已經存在於 Azure AD，則 Azure AD 中的物件會接替 Connect。 先前的雲端管理的物件標示為內部部署管理。 Azure AD 中具有內部部署 AD 值的所有屬性會以內部部署值覆寫。

> [!WARNING]
> 因為 Azure AD 中的所有屬性都將會以內部部署值覆寫，請確定您有良好的資料在內部部署。 例如，如果您在 Microsoft 365 中只有受管理的電子郵件地址，而且未在內部部署 AD DS 中將其更新，則會遺失 Azure AD/Microsoft 365 中不存在 AD DS 中的任何值。

> [!IMPORTANT]
> 如果您使用一律會使用快速設定的密碼同步，則會使用內部部署 AD 中的密碼覆寫 Azure AD 中的密碼。 如果您的使用者用來管理不同的密碼，則您需要通知它們在您已安裝 Connect 時，應該使用內部部署密碼。

必須在您的規劃中考量上一節和警告。 如果您在 Azure AD 中進行的許多變更不會在內部部署 AD DS 中反映，則您需要規劃如何在使用 Azure AD Connect 同步您的物件之前填入 AD DS 與更新的值。

如果以大致比對來比對軟體，則 **sourceAnchor** 會新增至 Azure AD 中的物件，以便稍後可以使用精確比對。

>[!IMPORTANT]
> Microsoft 強烈建議不要同步內部部署帳戶與 Azure Active Directory 中原有的管理帳戶。

### <a name="hard-match-vs-soft-match"></a>精確比對與大致比對
對於 Connect 的全新安裝，精確比對與大致比對之間並無實際差別。 不同之處在於災害復原狀況。 如果遺失您的伺服器與 Azure AD Connect，您可以重新安裝新的執行個體而不會遺失任何資料。 在初始安裝期間，具有 sourceAnchor 的物件會傳送到 Connect。 接著，用戶端 (Azure AD Connect) 可以評估比對，這會比在 Azure AD 中執行相同動作更快速。 精確比對會由 Connect 和 Azure AD 評估。 大致比對只會由 Azure AD 評估。

### <a name="other-objects-than-users"></a>使用者以外的其他物件
對於擁有郵件功能的群組和連絡人，您可以根據 proxyAddresses 來大致比對。 精確比對不適用，因為您只能更新使用者的 sourceAnchor/immutableID (使用 PowerShell)。 對於未擁有郵件功能的群組，目前不支援大致比對或精確比對。

### <a name="admin-role-considerations"></a>系統管理員角色考慮
為了防止不受信任的內部部署使用者與具有任何系統管理員角色的雲端使用者進行比對，Azure AD Connect 將不會比對內部部署使用者物件與具有系統管理員角色的物件。 這是預設的結果。 若要解決這個行為，您可以執行下列動作：

1.  從僅限雲端的使用者物件中移除目錄角色。
2.  如果嘗試失敗的使用者同步處理，請將雲端中的隔離物件實刪除。
3.  觸發同步處理。
4.  一旦發生相符的情況，即可選擇性地將目錄角色新增回雲端中的使用者物件。



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>從 Azure AD 中的資料建立新的內部部署 Active Directory
有些客戶開始使用僅限雲端的解決方案與 Azure AD，且他們沒有內部部署 AD。 稍後，他們要使用內部部署資源，而且想要根據 Azure AD 資料建置內部部署 AD。 Azure AD Connect 無法針對此案例協助您。 它不會在內部部署建立使用者，且它無法在內部部署將密碼設定為與 Azure AD 中的相同。

如果您計劃要新增內部部署 AD 的唯一理由是要支援 LOB (特定業務應用程式)，則或許您應該考慮改為使用 [Azure AD 網域服務](../../active-directory-domain-services/index.yml)。

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
