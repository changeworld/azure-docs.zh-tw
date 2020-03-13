---
title: 使用 Azure AD Connect 同步來實作密碼雜湊同步處理 | Microsoft Docs
description: 提供有關密碼雜湊同步處理如何運作及如何設定的資訊。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6451507eb5a25f432c73468d0da0db1838c8c9a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261381"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>使用 Azure AD Connect 同步來實作密碼雜湊同步處理
本文提供您所需資訊，以讓您將使用者密碼從內部部署 Active Directory 執行個體同步處理至雲端式 Azure Active Directory (Azure AD) 執行個體。

## <a name="how-password-hash-synchronization-works"></a>密碼雜湊同步處理如何運作
Active Directory 網域服務是以使用者實際密碼的雜湊值表示法格式來儲存密碼。 雜湊值是單向數學函式 (「雜湊演算法」) 的計算結果。 沒有任何方法可將單向函式的結果還原為純文字版本的密碼。 

為了同步密碼，Azure AD Connect 同步處理會從內部部署的 Active Directory 執行個體擷取您的密碼雜湊。 在將密碼雜湊與 Azure Active Directory 驗證服務同步之前，會進行額外的安全性處理。 密碼會以每個使用者為基礎，依照時間先後順序來進行同步處理。

密碼雜湊同步處理程序的實際資料流程，與使用者資料的同步處理類似。 不過，相較於其他屬性的標準目錄同步作業週期，密碼會更頻繁地進行同步。 密碼雜湊同步處理程序每 2 分鐘會執行一次。 您無法修改此程序的執行頻率。 當您同步處理密碼時，它便會覆寫現有的雲端密碼。

第一次啟用密碼雜湊同步處理功能時，它會對範圍內的所有使用者執行初次密碼同步處理。 您無法明確定義一小組要同步處理的使用者密碼。 不過，如果有多個連接器，則可以使用[ADSyncAADPasswordSyncConfiguration](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant)指令程式來停用某些連接器的密碼雜湊同步處理，而不是其他連接器。

當您變更內部部署密碼時，更新後的密碼將會進行同步處理，而這個動作大多會在幾分鐘內完成。
密碼雜湊同步處理功能會自動重試失敗的同步處理嘗試。 若嘗試同步密碼期間發生錯誤，該錯誤會記錄在事件檢視器中。

密碼同步處理不會影響目前已登入的使用者。
在您已經登入雲端服務的情況下，目前的雲端服務工作階段不會立即受到同步處理的密碼變更影響。 不過，當雲端服務要求您重新驗證時，就需要提供新的密碼。

使用者必須輸入其公司認證第二次對 Azure AD 進行驗證，不論他們是否登入其公司網路。 不過，如果使用者在登入時選取 [讓我保持登入 (KMSI)] 核取方塊，則此模式可最小化。 選取此方塊會設定在 180 天內略過驗證的工作階段 Cookie。 KMSI 行為可以由 Azure AD 系統管理員啟用或停用。 此外，您可以藉由開啟[無縫 SSO](how-to-connect-sso.md) 來減少密碼提示，此功能會在使用者是位於連線到您公司網路的公司裝置上時，自動將他們登入。

> [!NOTE]
> 只有 Active Directory 的物件類型使用者才支援密碼同步。 不支援 iNetOrgPerson 物件類型。

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>密碼雜湊同步處理運作方式的詳細描述

下列小節深入說明 Active Directory 與 Azure AD 之間的密碼雜湊同步處理運作方式。

![詳細的密碼流程](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. AD Connect 伺服器上的密碼雜湊同步處理代理程式每隔兩分鐘就會向 DC 要求儲存的密碼雜湊 (unicodePwd 屬性)。  此要求是使用標準 [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) \(英文\)複寫通訊協定，用來在 DC 之間同步資料。 服務帳戶必須具有複寫目錄變更和複寫目錄變更所有 AD 權限 (預設在安裝時授與)，以取得密碼雜湊。
2. 在傳送之前，DC 會使用金鑰 (它是 RPC 工作階段金鑰和 salt 的 [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) 雜湊)，來加密 MD4 密碼雜湊。 然後它會透過 RPC 將結果傳送給密碼雜湊同步處理代理程式。 DC 也會使用 DC 複寫通訊協定將 salt 傳送至同步處理代理程式，讓代理程式可以解密信封。
3. 在密碼雜湊同步處理代理程式將信封加密後，它會使用 [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) 和 salt 來產生金鑰，以將接收的資料解密回其原始 MD4 格式。 密碼雜湊同步處理代理程式永遠不會存取純文字密碼。 密碼雜湊同步處理代理程式使用 MD5 純粹是為了與 DC 的複寫通訊協定相容，並且只會在內部部署環境中的 DC 和密碼雜湊同步處理代理程式之間使用。
4. 密碼雜湊同步處理代理程式會將 16 位元組二進位密碼雜湊擴展成 64 位元組，方法是先將該雜湊轉換成 32 位元組十六進位字串，再將此字串轉換回具有 UTF-16 編碼的二進位檔。
5. 密碼雜湊同步處理代理程式會將每位使用者 salt 處理 (由 10 位元組長度 salt 所組成) 新增至 64 位元組二進位檔，以進一步保護原始雜湊。
6. 接著，密碼雜湊同步處理代理程式會結合 MD4 雜湊加上每位使用者 salt 處理，然後將其輸入至 [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) 函式。 系統會使用 [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) 索引雜湊演算法的 1000 次反覆運算。 
7. 密碼雜湊同步處理代理程式會使用產生的 32 位元組雜湊，將每位使用者 salt 處理和 SHA256 反覆運算數與其串連 (以供 Azure AD 使用)，然後透過 SSL 將字串從 Azure AD Connect 傳輸至 Azure AD。</br> 
8. 當使用者嘗試登入 Azure AD 並且輸入其密碼時，密碼會執行相同的 MD4+salt+PBKDF2+HMAC-SHA256 程序。 如果產生的雜湊符合 Azure AD 中儲存的雜湊，使用者輸入的密碼正確並且通過驗證。

> [!NOTE]
> 系統不會將原始的 MD4 雜湊傳輸至 Azure AD。 而是會傳輸原始 MD4 雜湊的 SHA256 雜湊。 如此一來，如果取得儲存在 Azure AD 的雜湊，它不能在內部部署傳遞雜湊攻擊中使用。

### <a name="security-considerations"></a>安全性考量

同步密碼時，不會向密碼雜湊同步處理功能、Azure AD 或任何相關的服務公開純文字版本的密碼。

使用者驗證是針對 Azure AD 進行，而不是針對組織自己的 Active Directory 執行個體進行。 儲存在 Azure AD 中的 SHA256 密碼資料 (原始 MD4 雜湊的雜湊)，比儲存在 Active Directory 中的資料更安全。 此外，因為此 SHA256 雜湊無法解密，所以無法帶回組織的 Active Directory 環境，並且在傳遞雜湊攻擊中顯示為有效的使用者密碼。

### <a name="password-policy-considerations"></a>密碼原則考量

啟用密碼雜湊同步處理會影響兩種類型的密碼原則：

* 密碼複雜性原則
* 密碼到期原則

#### <a name="password-complexity-policy"></a>密碼複雜性原則

啟用密碼雜湊同步處理時，內部部署 Active Directory 執行個體中的密碼複雜性原則，會覆寫所同步處理使用者在雲端中的複雜性原則。 您可以使用內部部署 Active Directory 執行個體的所有有效密碼，來存取 Azure AD 服務。

> [!NOTE]
> 直接在雲端建立的使用者的密碼仍受制於在雲端定義的密碼原則。

#### <a name="password-expiration-policy"></a>密碼到期原則

如果使用者在密碼雜湊同步處理的範圍內，則雲端帳戶密碼預設會設定為*永不過期*。

您可以使用內部部署環境中已過期的同步處理密碼，繼續登入雲端服務。 您的雲端密碼會於下一次您在內部部署環境中變更密碼時更新。

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>*EnforceCloudPasswordPolicyForPasswordSyncedUsers*功能的公開預覽

如果有同步處理的使用者只與 Azure AD 整合式服務互動，而且也必須符合密碼到期原則，您可以藉由啟用*EnforceCloudPasswordPolicyForPasswordSyncedUsers*功能，強制他們符合您 Azure AD 的密碼到期原則。

停用*EnforceCloudPasswordPolicyForPasswordSyncedUsers*時（這是預設設定），Azure AD Connect 將同步處理使用者的 PasswordPolicies 屬性設定為 "DisablePasswordExpiration"。 這會在每次同步處理使用者的密碼時完成，並指示 Azure AD 忽略該使用者的雲端密碼到期原則。 您可以使用 Azure AD PowerShell 模組搭配下列命令來檢查屬性的值：

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


若要啟用 EnforceCloudPasswordPolicyForPasswordSyncedUsers 功能，請使用 MSOnline PowerShell 模組執行下列命令，如下所示。 您必須為 Enable 參數輸入 yes，如下所示：

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

啟用之後，Azure AD 不會移至每個已同步處理的使用者，從 PasswordPolicies 屬性中移除 `DisablePasswordExpiration` 值。 相反地，當每位使用者下次在內部部署 AD 中變更其密碼時，此值會設定為 `None` 在下一個密碼同步處理期間。  

建議您先啟用 EnforceCloudPasswordPolicyForPasswordSyncedUsers，再啟用密碼雜湊同步處理，讓密碼雜湊的初始同步處理不會將 `DisablePasswordExpiration` 值新增至使用者的 PasswordPolicies 屬性。

預設 Azure AD 密碼原則會要求使用者每90天變更密碼一次。 如果您在 AD 中的原則也是90天，則這兩個原則應相符。 不過，如果 AD 原則不是90天，您可以使用 Set-msolpasswordpolicy PowerShell 命令，將 Azure AD 密碼原則更新為相符。

Azure AD 針對每個已註冊的網域支援不同的密碼到期原則。

警告：如果已同步處理的帳戶在 Azure AD 中需要有未過期的密碼，您必須明確地將 `DisablePasswordExpiration` 值新增至 Azure AD 中 user 物件的 PasswordPolicies 屬性。  您可以藉由執行下列命令來完成此動作。

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> 這項功能目前處於公開預覽狀態。
> Set-msolpasswordpolicy PowerShell 命令將無法在同盟網域上作用。 

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>同步處理暫時密碼和「下次登入時強制密碼變更」的公開預覽

通常會強制使用者在第一次登入時變更其密碼，特別是在發生系統管理員密碼重設之後。  這通常稱為設定「暫時」密碼，並透過在 Active Directory （AD）中的使用者物件上核取 [使用者必須在下次登入時變更密碼] 旗標來完成。
  
暫時密碼功能有助於確保認證的擁有權轉移會在第一次使用時完成，以將多個個人知道該認證的持續時間減到最短。

若要支援同步處理使用者 Azure AD 中的暫時密碼，您可以在 Azure AD Connect 伺服器上執行下列命令，以啟用*ForcePasswordChangeOnLogOn*功能：

`Set-ADSyncAADCompanyFeature  -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> 強制使用者在下次登入時變更其密碼時，需要同時變更密碼。  Azure AD Connect 不會自行收取強制密碼變更旗標;這是密碼雜湊同步處理期間所發生之偵測到的密碼變更補充。

> [!CAUTION]
> 只有在租使用者上啟用 SSPR 和密碼回寫時，您才應該使用這項功能。  如此一來，如果使用者透過 SSPR 變更其密碼，則會同步處理到 Active Directory。

> [!NOTE]
> 這項功能目前處於公開預覽狀態。

#### <a name="account-expiration"></a>帳戶到期

如果您的組織使用 accountExpires 屬性作為使用者帳戶管理的一部分，這個屬性不會同步處理至 Azure AD。 如此一來，在針對密碼雜湊同步處理設定之環境中已到期的 Active Directory 帳戶在 Azure AD 將仍然為作用中。 我們的建議是，如果帳戶到期，工作流程動作應觸發 PowerShell 指令碼，以停用使用者的 Azure AD 帳戶 (使用 [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) \(英文\) Cmdlet)。 相反地，當帳戶開啟時，Azure AD 執行個體也應開啟。

### <a name="overwrite-synchronized-passwords"></a>覆寫已同步的密碼

系統管理員可以使用 Windows PowerShell 手動重設您的密碼。

在此情況下，新的密碼會覆寫您已同步處理的密碼，且雲端中定義的所有密碼原則都會套用到新的密碼。

如果您再次變更內部部署密碼，則新密碼會同步到雲端並覆寫手動更新的密碼。

密碼同步處理不會影響已登入的 Azure 使用者。 目前的雲端服務工作階段不會立即受到同步處理的密碼變更之影響，會在您登入雲端服務時才會受到影響。 KMSI 會延伸此差異的持續時間。 當雲端服務要求您重新驗證時，就需要提供新的密碼。

### <a name="additional-advantages"></a>其他優點

- 一般而言，密碼雜湊同步處理比同盟服務更容易實作。 它不需要任何額外的伺服器，並且會排除高可用性同盟服務的相依性以驗證使用者。
- 您也可以除了同盟之外，再啟用密碼雜湊同步處理。 您可以將它作為同盟服務發生中斷時的後援服務。

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Azure AD Domain Services 的密碼雜湊同步處理常式

如果您使用 Azure AD Domain Services 為需要使用 Kerberos、LDAP 或 NTLM 的應用程式和服務提供舊版驗證，某些額外的進程就是密碼雜湊同步處理流程的一部分。 Azure AD Connect 使用下列額外的程式，將密碼雜湊同步處理至 Azure AD 以用於 Azure AD Domain Services：

> [!IMPORTANT]
> Azure AD Connect 應該只會為了與內部部署 AD DS 環境同步處理而安裝和設定。 不支援在 Azure AD DS 受控網域中安裝 Azure AD Connect，以將物件同步處理回 Azure AD。
>
> Azure AD Connect 只有在為 Azure AD 租使用者啟用 Azure AD DS 時，才會同步處理舊版密碼雜湊。 如果您只使用 Azure AD Connect 來同步處理內部部署 AD DS 環境與 Azure AD，則不會使用下列步驟。
>
> 如果您的繼承應用程式未使用 NTLM 驗證或 LDAP 簡單系結，我們建議您停用 Azure AD DS 的 NTLM 密碼雜湊同步處理。 如需詳細資訊，請參閱[停用弱式加密套件和 NTLM 認證雜湊同步](../../active-directory-domain-services/secure-your-domain.md)處理。

1. Azure AD Connect 會抓取租使用者之 Azure AD Domain Services 實例的公開金鑰。
1. 當使用者變更其密碼時，內部部署網域控制站會將密碼變更（雜湊）的結果儲存在兩個屬性中：
    * NTLM 密碼雜湊的*unicodePwd* 。
    * Kerberos 密碼雜湊的*supplementalCredentials* 。
1. Azure AD Connect 透過目錄複寫通道偵測密碼變更（需要複寫到其他網域控制站的屬性變更）。
1. 針對每個密碼已變更的使用者，Azure AD Connect 執行下列步驟：
    * 產生隨機 AES 256 位對稱金鑰。
    * 產生第一輪加密所需的隨機初始化向量。
    * 從*supplementalCredentials*屬性解壓縮 Kerberos 密碼雜湊。
    * 檢查 [Azure AD Domain Services 安全性設定*SyncNtlmPasswords* ] 設定。
        * 如果停用此設定，則會產生隨機的高熵 NTLM 雜湊（與使用者的密碼不同）。 然後，此雜湊會與*supplementalCrendetials*屬性中的 exacted Kerberos 密碼雜湊結合成一個資料結構。
        * 啟用時，會將*unicodePwd*屬性的值與從*supplementalCredentials*屬性解壓縮的 Kerberos 密碼雜湊結合成一個資料結構。
    * 使用 AES 對稱金鑰來加密單一資料結構。
    * 使用租使用者的 Azure AD Domain Services 公開金鑰來加密 AES 對稱金鑰。
1. Azure AD Connect 會傳輸加密的 AES 對稱金鑰、包含密碼雜湊的加密資料結構，以及要 Azure AD 的初始化向量。
1. Azure AD 會儲存加密的 AES 對稱金鑰、加密的資料結構，以及使用者的初始化向量。
1. Azure AD 使用內部同步處理機制，透過加密的 HTTP 會話，將加密的 AES 對稱金鑰、加密的資料結構和初始化向量推送至 Azure AD Domain Services。
1. Azure AD Domain Services 從 Azure Key vault 抓取租使用者實例的私密金鑰。
1. 針對每一組加密的資料（代表單一使用者的密碼變更），Azure AD Domain Services 接著執行下列步驟：
    * 會使用其私密金鑰來解密 AES 對稱金鑰。
    * 使用 AES 對稱金鑰搭配初始化向量來解密包含密碼雜湊的加密資料結構。
    * 將接收到的 Kerberos 密碼雜湊寫入 Azure AD Domain Services 網域控制站。 雜湊會儲存到使用者物件的*supplementalCredentials*屬性中，並加密為 Azure AD Domain Services 網域控制站的公開金鑰。
    * Azure AD Domain Services 會將收到的 NTLM 密碼雜湊寫入 Azure AD Domain Services 網域控制站。 雜湊會儲存到使用者物件的*unicodePwd*屬性中，並加密為 Azure AD Domain Services 網域控制站的公開金鑰。

## <a name="enable-password-hash-synchronization"></a>啟用密碼雜湊同步處理

>[!IMPORTANT]
>如果您要從 AD FS (或其他同盟技術) 遷移至密碼雜湊同步處理，強烈建議您遵循我們在[此處](https://aka.ms/adfstophsdpdownload) \(英文\) 發佈的詳細部署指南。

當您使用 [快速設定] 選項來安裝 Azure AD Connect 時，系統會自動啟用密碼雜湊同步處理。 如需詳細資訊，請參閱[利用快速設定開始使用 Azure AD Connect](how-to-connect-install-express.md)。

如果您在安裝 Azure AD Connect 時使用自訂設定，在使用者登入頁面上便會提供密碼雜湊同步處理。 如需詳細資訊，請參閱[自訂 Azure AD Connect 安裝](how-to-connect-install-custom.md)。

![啟用密碼雜湊同步處理](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>密碼雜湊同步處理和 FIPS
如果我們已經根據美國聯邦資訊處理標準 (FIPS) 鎖定您的伺服器，則 MD5 會停用。

**若要針對密碼雜湊同步處理啟用 MD5，請執行下列步驟︰**

1. 移至 %programfiles%\Azure AD Sync\Bin。
2. 開啟 miiserver.exe.config。
3. 移至檔案結尾處的 configuration/runtime 節點。
4. 新增下列節點︰ `<enforceFIPSPolicy enabled="false"/>`
5. 儲存您的變更。

如需參考，此程式碼片段就是其大致樣貌︰

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

如需安全性和 FIPS 的詳細資訊，請參閱 [Azure AD 密碼雜湊同步、加密和 FIPS 合規性](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)。

## <a name="troubleshoot-password-hash-synchronization"></a>針對密碼雜湊同步處理進行疑難排解
如果您在進行密碼雜湊同步處理時發生問題，請參閱[針對密碼雜湊同步處理進行疑難排解](tshoot-connect-password-hash-synchronization.md)。

## <a name="next-steps"></a>後續步驟
* [Azure AD Connect 同步處理：自訂同步處理選項](how-to-connect-sync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)
* [取得從 ADFS 遷移至密碼雜湊同步處理的逐步部署方案](https://aka.ms/authenticationDeploymentPlan)
