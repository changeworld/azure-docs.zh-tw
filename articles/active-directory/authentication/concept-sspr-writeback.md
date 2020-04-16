---
title: 使用自助服務密碼重置的本地密碼寫回 - Azure 活動目錄
description: 瞭解如何將 Azure 活動目錄中的密碼變更或重置事件寫回本地目錄環境
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 787c15c11c995c7eb30662131302658175c7f877
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393013"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>自助服務密碼重置回寫在 Azure 活動目錄中如何工作?

Azure 活動目錄 (Azure AD) 自助服務密碼重置 (SSPR) 允許使用者在雲端中重置其密碼,但大多數公司也有其使用者所在的本地活動目錄域服務 (AD DS) 環境。 密碼回寫是透過 [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) 所實現的功能，可將雲端中的密碼變更即時回寫至現有內部部署目錄。 在這裡設定中,當使用者在雲端中使用 SSPR 變更或重置其密碼時,更新的密碼也會寫回本地 AD DS 環境

使用以下混合識別模型的環境中支援密碼寫回:

* [密碼雜湊同步處理](../hybrid/how-to-connect-password-hash-synchronization.md)
* [傳遞驗證](../hybrid/how-to-connect-pta.md)
* [活動目錄聯合服務](../hybrid/how-to-connect-fed-management.md)

密碼回寫提供下列功能：

* **實施本地活動目錄域服務 (AD DS) 密碼原則**:當使用者重置其密碼時,將檢查它以確保在將其提交到該目錄之前符合本地 AD DS 策略。 此審核包括檢查歷史記錄、複雜性、年齡、密碼篩選器以及您在 ADDS 中定義的任何其他密碼限制。
* **零延遲的意見反應**：密碼回寫是一項同步作業。 如果使用者的密碼不符合策略,或者由於任何原因無法重置或更改,則會立即通知使用者。
* **支援從存取面板和Office 365更改密碼**:當聯合或密碼哈希同步使用者來更改其過期或未過期的密碼時,這些密碼將寫回AD DS。
* **支援管理員從 Azure 門戶重置密碼時返回密碼**:當管理員在[Azure 門戶](https://portal.azure.com)中重置使用者的密碼時,如果該使用者是聯合的或密碼哈希同步的,則密碼將寫回本地。 Office 管理入口網站目前不支援此功能。
* **不需要任何入站防火牆規則**:密碼寫回使用 Azure 服務總線中繼作為基礎通信通道。 所有通訊都會透過連接埠 443 來輸出。

> [!NOTE]
> 內部部署 AD 中的受保護群組所包含的系統管理員帳戶無法用於密碼回寫。 管理員可以在雲中更改其密碼,但不能使用密碼重置來重置忘記的密碼。 如需受保護群組的詳細資訊，請參閱 [Active Directory 中的受保護帳戶和群組](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory)。

## <a name="how-password-writeback-works"></a>密碼回寫的運作方式

當已同盟的或已同步處理密碼雜湊的使用者嘗試在雲端重設或變更其密碼時，會發生下列動作：

1. 系統會執行檢查，以了解使用者擁有哪一種密碼。 如果密碼是在內部部署環境中進行管理的：
   * 系統會執行檢查以了解回寫服務是否已啟動並在執行中。 如果是，使用者可以繼續進行。
   * 如果回寫服務關閉，系統會告知使用者其無法立即重設其密碼。
1. 接著，使用者會通過適當的驗證閘道，然後到達 [重設密碼]**** 畫面。
1. 使用者選取新的密碼，並加以確認。
1. 當使用者選取 [送出]**** 時，系統會以在回寫設定程序期間所建立的對稱金鑰加密純文字密碼。
1. 經過加密的密碼會放入承載中，透過 HTTPS 通道傳送到租用戶特定的服務匯流排轉送 (會在回寫設定程序期間為您設定此轉送)。 此轉送受到只有您的內部部署安裝才會知道的隨機產生密碼所保護。
1. 在訊息抵達服務匯流排之後，密碼重設端點會自動甦醒，並看到有擱置中的重設要求。
1. 服務會接著使用雲端錨點屬性來尋找使用者。 要成功此尋找,必須滿足以下條件:

   * 使用者物件必須存在於 Active Directory 連接器空間中。
   * 使用者物件必須連結至對應的 Metaverse (MV) 物件。
   * 使用者物件必須連結至對應的 Azure Active Directory 連接器物件。
   * 從 Active Directory 連接器物件到 MV 的連結上必須有同步處理規則 `Microsoft.InfromADUserAccountEnabled.xxx`。

   從雲端傳來呼叫時，同步處理引擎會使用 **cloudAnchor** 屬性來查閱 Azure Active Directory 連接器空間物件。 接著，它會依循連結回到 MV 物件，然後再依循連結回到 Active Directory 物件。 因為相同使用者可能會有多個 Active Directory 物件 (多樹系)，所以同步處理引擎需倚賴 `Microsoft.InfromADUserAccountEnabled.xxx` 連結來選出正確的物件。

1. 找到使用者帳戶之後，系統會嘗試在適當的 Active Directory 樹系中直接重設密碼。
1. 如果密碼設定作業成功，系統會告訴使用者其密碼已變更。

   > [!NOTE]
   > 如果使用者的密碼哈希通過使用密碼哈希同步同步到 Azure AD,則本地密碼策略可能比雲端密碼策略弱。 在此情況下，系統會強制執行內部部署原則。 此原則可確保不論您是使用密碼雜湊同步處理還是同盟來提供單一登入，都會在雲端強制執行內部部署原則。

1. 如果密碼設定作業失敗，系統會顯示錯誤以提示使用者再試一次。 操作可能會失敗,原因如下:
    * 服務已停止運作。
    * 他們選擇的密碼不符合組織的策略。
    * 無法在本機 Active Directory 中找到使用者。

   錯誤訊息會對使用者提供指引，讓他們可以嘗試自行解決而不需要系統管理員介入。

## <a name="password-writeback-security"></a>密碼回寫安全性

密碼回寫是一項極為安全的服務。 為確保資訊受到保護,啟用了四層安全模型,如下所示:

* **租用戶特定的服務匯流排轉送**
   * 當您設定服務時，便會設定一個以隨機產生強式密碼保護的租用戶特定服務匯流排轉送，且 Microsoft 永遠無法得知該密碼。
* **受到鎖定的密碼編譯強式密碼加密金鑰**
   * 創建服務總線中繼後,將創建一個強對稱密鑰,用於在密碼通過導線時加密密碼。 此金鑰只會存在於您公司的雲端密碼存放區中，並受到嚴密鎖定和稽核，就像目錄中的任何其他密碼一樣。
* **業界標準傳輸層安全性 (TLS)**
   1. 當雲端上發生密碼重設或變更作業時，系統便會以公開金鑰將純文字密碼加密。
   1. 加密的密碼被放入通過加密通道發送到服務總線中繼的 HTTPS 消息中。
   1. 在訊息抵達服務匯流排之後，您的內部部署代理程式會喚醒服務匯流排，並使用先前產生的強式密碼向其進行驗證。
   1. 內部部署代理程式會拾取加密訊息，然後使用私密金鑰進行解密。
   1. 內部部署代理程式會嘗試透過 AD DS SetPassword API 來設定密碼。 這個步驟可讓您在雲端強制執行 Active Directory 內部部署密碼原則 (例如複雜度、有效期、歷程記錄、篩選等)。
* **訊息到期原則**
   * 如果訊息因內部部署服務已停止運作而停留在服務匯流排中，在數分鐘之後，它就會逾時而被移除。 讓訊息逾時並予以移除可更進一步提升安全性。

### <a name="password-writeback-encryption-details"></a>密碼回寫的加密詳細資料

在使用者提交密碼重設之後，重設要求會先經過數個加密步驟，然後才抵達您的內部部署環境。 這些加密步驟可確保提供最高的服務可靠性和安全性。 這些步驟的說明如下：

1. **密碼加密與2048位RSA密鑰**:使用者提交密碼以寫回本地後,提交的密碼本身使用2048位RSA密鑰加密。
1. **使用 AES-GCM 進行包級加密:使用 AES-GCM**加密整個包、密碼以及所需的中繼資料。 這個加密可防止任何可直接存取基礎 ServiceBus 通道的人員檢視或竄改內容。
1. **所有通訊都透過 TLS/SSL 進行**:與 ServiceBus 的所有通訊都發生在 SSL/TLS 通道中。 這個加密可保護內容，免於遭到未經授權的第三方存取。
1. **每 6 個月自動變換金鑰**：所有金鑰會每 6 個月變換一次，或在每一次於 Azure AD Connect 上停用再重新啟用密碼回寫時進行變換，以確保最高的服務安全性。

### <a name="password-writeback-bandwidth-usage"></a>密碼回寫頻寬使用量

密碼回寫是一種低頻寬服務，只有在下列情況下，才會將要求傳回給內部部署代理程式︰

* 透過 Azure AD Connect 啟用或停用此功能時會傳送兩則訊息。
* 每 5 分鐘傳送一則訊息作為服務活動訊號 (只要服務正在執行)。
* 每次提交新密碼時會傳送兩則訊息：
   * 第一則訊息是一個執行作業的要求。
   * 第二則訊息包含該作業的結果，並且會在下列情況下傳送︰
      * 每次在使用者自助式密碼重設期間提交新密碼時。
      * 每次在使用者密碼變更作業期間提交新密碼時。
      * 每次在系統管理員所起始的使用者密碼重設 (僅限從 Azure 系統管理員入口網站) 期間提交新密碼時。

#### <a name="message-size-and-bandwidth-considerations"></a>訊息大小和頻寬考量

上述每則訊息的大小通常會小於 1 KB。 即使在負載極大的情況下，密碼回寫服務本身每秒也只會耗用幾千位元的頻寬。 因為每則訊息都以即時方式傳送 (僅限密碼更新作業要求時)，且因為訊息大小是如此的小，所以回寫功能的頻寬使用量會因太小，以致沒有可測得的影響。

## <a name="supported-writeback-operations"></a>支援的回寫作業

下列所有情況都會回寫密碼︰

* **支援的使用者作業**
   * 任何最終使用者自助服務自願更改密碼操作。
   * 任何最終使用者自助服務強制更改密碼操作,例如密碼過期。
   * 任何來自[密碼重置門戶](https://passwordreset.microsoftonline.com)的最終使用者自助服務密碼重置。

* **支援的系統管理員作業**
   * 任何管理員自助服務自願更改密碼操作。
   * 任何管理員自助服務強制更改密碼操作,例如密碼過期。
   * 任何源自[密碼重置門戶](https://passwordreset.microsoftonline.com)的管理員自助服務密碼重置。
   * 任何管理員啟動的最終使用者密碼重置從 Azure[門戶](https://portal.azure.com)。

## <a name="unsupported-writeback-operations"></a>不支援的回寫作業

密碼不會在以下任何情況下寫回:

* **不支援的使用者作業**
   * 任何最終使用者使用 PowerShell 版本 1、版本 2 或 Microsoft 圖形 API 重置自己的密碼。
* **不支援的管理員操作**
   * 任何管理員啟動的最終使用者密碼重置從 PowerShell 版本 1、版本 2 或 Microsoft 圖形 API。
   * 任何管理員啟動的最終使用者密碼重置從[微軟365管理中心](https://admin.microsoft.com)。

> [!WARNING]
> 在本地 AD DS 管理工具(如活動目錄使用者和電腦)或活動目錄管理中心作為 Azure AD 連接的預覽功能支援使用複選框「使用者必須在下次登錄時更改密碼」。 如需詳細資訊，請參閱[使用 Azure AD Connect 同步實作密碼雜湊同步處理](../hybrid/how-to-connect-password-hash-synchronization.md)。

## <a name="next-steps"></a>後續步驟

要開始使用 SSPR 回寫,請完成以下教學:

> [!div class="nextstepaction"]
> [教學:啟用自助服務密碼重置 (SSPR) 寫回](tutorial-enable-writeback.md)
