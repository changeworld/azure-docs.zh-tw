---
title: Azure AD Connect：設定 AD DS 連接器帳戶權限 | Microsoft Docs
description: 本文件將詳述如何使用新的 ADSyncConfig PowerShell 模組來設定 AD DS 連接器帳戶
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 252c033c1a9d4d45c3d48256e65ae9ad10a93c51
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85360056"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect： 設定 AD DS 連接器帳戶權限 

組建 1.1.880.0 (2018 年 8 月發行) 導入了名為 [ADSyncConfig.psm1](reference-connect-adsyncconfig.md) 的 PowerShell 模組，其中包含一個 Cmdlet 集合，可協助您為 Azure AD Connect 部署設定正確的 Active Directory 權限。 

## <a name="overview"></a>概觀 
下列 PowerShell Cmdlet 可用來針對每個您選擇在 Azure AD Connect 中啟用的功能，設定 AD DS 連接器帳戶的 Active Directory 權限。 若要防止發生任何問題，只要您想要使用自訂網域帳戶安裝 Azure AD Connect 以連線到您的樹系，即應事先備妥 Active Directory 權限。 此 ADSyncConfig 模組也可在 Azure AD Connect 部署之後用來設定權限。

![ad ds 帳戶概觀](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

對於 Azure AD Connect Express 安裝，會有一個自動產生的帳戶 (MSOL_nnnnnnnnnn) 建立在 Active Directory 中，且具備所有必要的權限，因此，除非您已在組織單位或您要同步處理至 Azure AD 的特定 Active Directory 物件上封鎖權限繼承，否則不需要使用此 ADSyncConfig 模組。 
 
### <a name="permissions-summary"></a>權限摘要 
下表提供 AD 物件所需權限的摘要： 

| 功能 | 權限 |
| --- | --- |
| ms-DS-ConsistencyGuid 功能 |[設計概念 - 使用 ms-DS-ConsistencyGuid 作為 sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)中記載了對 ms-DS-ConsistencyGuid 屬性的讀取和寫入權限。 | 
| 密碼雜湊同步處理 |<li>複寫目錄變更</li>  <li>複寫目錄變更 (全部) |
| Exchange 混合式部署 |[Exchange 混合回寫](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback)中記載了使用者、群組和連絡人屬性的讀取和寫入權限。 |
| Exchange 郵件公用資料夾 |公用資料夾屬性 (詳情記載於 [Exchange 郵件公用資料夾](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder)) 的讀取權限。 | 
| 密碼回寫 |[開始使用密碼管理](../authentication/howto-sspr-writeback.md)中記載了使用者屬性的讀取和寫入權限。 |
| 裝置回寫 |[裝置回寫](how-to-connect-device-writeback.md)中記載了裝置物件和容器的讀取和寫入權限。 |
| 群組回寫 |讀取、建立、更新和刪除已同步處理之 **Office 365 群組**的群組物件。|

## <a name="using-the-adsyncconfig-powershell-module"></a>使用 ADSyncConfig PowerShell 模組 
ADSyncConfig 模組需要[適用於 AD DS 的遠端伺服器管理工具 (RSAT)](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools)，因為它依存於 AD DS PowerShell 模組和工具。 若要安裝適用於 AD DS 的 RSAT，請使用 [以系統管理員身分執行] 開啟 Windows PowerShell 視窗並執行： 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![設定](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>您也可以將檔案 **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** 複製到已安裝「適用於 AD DS 的 RSAT」的網域控制站，並存該處使用此 PowerShell 模組。

若要開始使用 ADSyncConfig，您必須在 Windows PowerShell 視窗中載入模組： 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

若要檢查此模組中包含的所有 Cmdlet，您可以輸入：  

``` powershell
Get-Command -Module AdSyncConfig  
```

![勾選](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

每個 Cmdlet 都有會輸入 AD DS 連接器帳戶的相同參數，以及 AdminSDHolder 參數。 若要指定您的 AD DS 連接器帳戶，您可以提供帳戶名稱和網域，或僅提供帳戶辨別名稱 (DN)，

例如：

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

或

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

請記得使用環境的正確值來取代 `<ADAccountName>`、`<ADDomainName>` 和 `<ADAccountDN>`。

如果您不想修改 AdminSDHolder 容器的權限，請使用參數 `-SkipAdminSdHolders`。 

根據預設，所有的設定權限 Cmdlet 會都嘗試在樹系中每個網域的根上設定 AD DS 權限，這表示執行 PowerShell 工作階段的使用者必須具有樹系中每個網域的網域管理員權限。  基於此需求，建議您從樹系根使用企業系統管理員。 如果您的 Azure AD Connect 部署有多個 AD DS 連接器，就必須在具有 AD DS 連接器的每個樹系上執行相同的 Cmdlet。 

您也可以使用參數 `-ADobjectDN`，再加上要設定權限的目標物件所具備的 DN，以設定特定 OU 或 AD DS 物件的權限。 使用目標 ADobjectDN 時，Cmdlet 只會設定此物件的權限，而不會設定網域根或 AdminSDHolder 容器的權限。 當您具有已停用權限繼承的特定 OU 或 AD DS 物件時，此參數可能有其效用 (請參閱「尋找已停用權限繼承的 AD DS 物件」) 

這些常見參數的例外狀況是用來在 AD DS 連接器帳戶本身設定權限的 `Set-ADSyncRestrictedPermissions` Cmdlet，以及 `Set-ADSyncPasswordHashSyncPermissions` Cmdlet，因為密碼雜湊同步所需的權限僅設定於網域根上，因此這個 Cmdlet 未包含 `-ObjectDN` 或 `-SkipAdminSdHolders` 參數。

### <a name="determine-your-ad-ds-connector-account"></a>決定您的 AD DS 連接器帳戶 
如果已安裝 Azure AD Connect，而您想要檢查 Azure AD Connect 目前所使用的 AD DS 連接器帳戶，您可以執行下列 Cmdlet： 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>尋找已停用權限繼承的 AD DS 物件 
如果您想要檢查是否有任何 AD DS 物件已停用權限繼承，您可以執行： 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
根據預設，此 Cmdlet 只會尋找已停用繼承的 OU，但您可以在 `-ObjectClass` 參數中指定其他 AD DS 物件類別，或使用 '*' 尋找所有物件類別，如下所示： 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>檢視物件的 AD DS 權限 
您可使用以下 Cmdlet 來檢視目前在 Active Directory 物件上設定的權限清單，只要提供其 DistinguishedName 即可： 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>設定 AD DS 連接器帳戶權限 
 
### <a name="configure-basic-read-only-permissions"></a>設定基本唯讀權限 
若要在未使用任何 Azure AD Connect 功能時設定 AD DS 連接器帳戶的基本唯讀權限，請執行： 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


或 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


此 Cmdlet 會設定下列權限： 
 

|類型 |名稱 |存取 |套用至| 
|-----|-----|-----|-----|
|Allow |AD DS 連接器帳戶 |讀取所有屬性 |子系裝置物件| 
|Allow |AD DS 連接器帳戶|讀取所有屬性 |子系 InetOrgPerson 物件| 
|Allow |AD DS 連接器帳戶 |讀取所有屬性 |子系電腦物件| 
|Allow |AD DS 連接器帳戶 |讀取所有屬性 |子系 foreignSecurityPrincipal 物件| 
|Allow |AD DS 連接器帳戶 |讀取所有屬性 |子系群組物件| 
|Allow |AD DS 連接器帳戶 |讀取所有屬性 |子系使用者物件| 
|Allow |AD DS 連接器帳戶 |讀取所有屬性 |子系連絡人物件| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>設定 MS-DS-Consistency-Guid 權限 
若要在使用 ms-Ds-Consistency-Guid 屬性作為來源錨點 (也稱為「讓 Azure 代為管理來源錨點」選項) 來設定 AD DS 連接器的權限，請執行： 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

或 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

此 Cmdlet 會設定下列權限： 

|類型 |名稱 |存取 |套用至|
|-----|-----|-----|-----| 
|Allow|AD DS 連接器帳戶|讀取/寫入屬性|子系使用者物件|

### <a name="permissions-for-password-hash-synchronization"></a>密碼雜湊同步處理的權限 
若要在使用密碼雜湊同步處理時設定 AD DS 連接器帳戶的權限，請執行： 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


或 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

此 Cmdlet 會設定下列權限： 

|類型 |名稱 |存取 |套用至|
|-----|-----|-----|-----| 
|Allow |AD DS 連接器帳戶 |複寫目錄變更 |僅限此物件 (網域根)| 
|Allow |AD DS 連接器帳戶 |複寫所有目錄變更 |僅限此物件 (網域根)| 
  
### <a name="permissions-for-password-writeback"></a>密碼回寫的權限 
若要在使用密碼回寫時設定 AD DS 連接器帳戶的權限，請執行： 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


或

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
此 Cmdlet 會設定下列權限： 

|類型 |名稱 |存取 |套用至|
|-----|-----|-----|-----| 
|Allow |AD DS 連接器帳戶 |重設密碼 |子系使用者物件| 
|Allow |AD DS 連接器帳戶 |寫入 lockoutTime 屬性 |子系使用者物件| 
|Allow |AD DS 連接器帳戶 |寫入 pwdLastSet 屬性 |子系使用者物件| 

### <a name="permissions-for-group-writeback"></a>群組回寫的權限 
若要在使用群組回寫時設定 AD DS 連接器帳戶的權限，請執行： 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
或 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
此 Cmdlet 會設定下列權限： 

|類型 |名稱 |存取 |套用至|
|-----|-----|-----|-----| 
|Allow |AD DS 連接器帳戶 |一般讀取/寫入 |物件類型群組和子物件的所有屬性| 
|Allow |AD DS 連接器帳戶 |建立/刪除子物件 |物件類型群組和子物件的所有屬性| 
|Allow |AD DS 連接器帳戶 |刪除/刪除樹狀物件|物件類型群組和子物件的所有屬性|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Exchange 混合部署的權限 
若要在使用 Exchange 混合部署時設定 AD DS 連接器帳戶的權限，請執行： 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


或 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

此 Cmdlet 會設定下列權限：  
 

|類型 |名稱 |存取 |套用至|
|-----|-----|-----|-----| 
|Allow |AD DS 連接器帳戶 |讀取/寫入所有屬性 |子系使用者物件| 
|Allow |AD DS 連接器帳戶 |讀取/寫入所有屬性 |子系 InetOrgPerson 物件| 
|Allow |AD DS 連接器帳戶 |讀取/寫入所有屬性 |子系群組物件| 
|Allow |AD DS 連接器帳戶 |讀取/寫入所有屬性 |子系連絡人物件| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Exchange 郵件公用資料夾的權限 (預覽) 
若要在使用 Exchange 郵件公用資料夾功能時設定 AD DS 連接器帳戶的權限，請執行： 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


或 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
此 Cmdlet 會設定下列權限： 

|類型 |名稱 |存取 |套用至|
|-----|-----|-----|-----| 
|Allow |AD DS 連接器帳戶 |讀取所有屬性 |子系 PublicFolder 物件| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>限制 AD DS 連接器帳戶的權限 
此 PowerShell 指令碼會對以參數表示的 AD 連接器帳戶限定權限。 要限定權限必須執行下列步驟： 

- 停用指定物件上的繼承 
- 對特定物件移除 SELF 特有的 ACE 以外的所有 ACE，因為我們想要讓預設權限在用於 SELF 時保持不變。 
 
  -ADConnectorAccountDN 參數是必須限定權限的 AD 帳戶。 這通常是在 AD DS 連接器中設定的 MSOL_nnnnnnnnnnnn 網域帳戶 (請參閱「決定您的 AD DS 連接器帳戶」)。 若要指定具有必要權限可對目標 AD 物件限制 Active Directory 權限的系統管理員帳戶，必須要使用 -Credential 參數。 這通常是企業或網域系統管理員。  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
例如： 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

此 Cmdlet 會設定下列權限： 

|類型 |名稱 |存取 |套用至|
|-----|-----|-----|-----| 
|Allow |系統 |完全控制 |此物件 
|Allow |企業系統管理員 |完全控制 |此物件 
|Allow |網域管理員 |完全控制 |此物件 
|Allow |系統管理員 |完全控制 |此物件 
|Allow |企業網域控制站 |清單內容 |此物件 
|Allow |企業網域控制站 |讀取所有屬性 |此物件 
|Allow |企業網域控制站 |讀取權限 |此物件 
|Allow |驗證的使用者 |清單內容 |此物件 
|Allow |驗證的使用者 |讀取所有屬性 |此物件 
|Allow |驗證的使用者 |讀取權限 |此物件 

## <a name="next-steps"></a>後續步驟
- [Azure AD Connect：帳戶和權限](reference-connect-accounts-permissions.md)
- [快速安裝](how-to-connect-install-express.md)
- [自訂安裝](how-to-connect-install-custom.md)
- [ADSyncConfig 參考](reference-connect-adsyncconfig.md)

