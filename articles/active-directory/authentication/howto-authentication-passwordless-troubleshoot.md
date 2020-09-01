---
title: 混合式 FIDO2 安全性金鑰的已知問題與疑難排解-Azure Active Directory
description: '瞭解一些已知問題，以及使用 Azure Active Directory (preview 的無密碼混合式 FIDO2 安全性金鑰登入方式進行疑難排解) '
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/19/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecab82e43bff6c0d1d83c9c1cdc38cafd809e277
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236621"
---
# <a name="troubleshooting-for-hybrid-deployments-of-fido2-security-keys-in-azure-ad-preview"></a>針對 Azure AD (preview) 中 FIDO2 安全性金鑰的混合式部署進行疑難排解

本文涵蓋混合式 Azure AD 聯結裝置的常見問題，以及無密碼登入內部內部部署資源的問題。 使用這項無密碼功能，您可以在 Windows 10 裝置上，使用 FIDO2 安全性金鑰來啟用混合式 Azure AD 聯結裝置的 Azure AD 驗證。 使用者可以在其裝置上使用新式認證（例如 FIDO2 金鑰）登入 Windows，並存取傳統 Active Directory Domain Services (AD DS) 的資源，並透過無縫單一登入 (內部部署資源的 SSO) 體驗。

支援混合式環境中的使用者下列案例：

* 使用 FIDO2 安全性金鑰登入已加入混合式 Azure AD 的裝置，並取得內部內部部署資源的 SSO 存取權。
* 使用 FIDO2 安全性金鑰登入 Azure AD 加入的裝置，並取得內部內部部署資源的 SSO 存取權。

若要開始使用 FIDO2 安全性金鑰和內部部署資源的混合式存取，請參閱下列文章：

* [無密碼安全性金鑰](howto-authentication-passwordless-security-key.md)
* [無密碼 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [無密碼內部部署](howto-authentication-passwordless-security-key-on-premises.md)

> [!NOTE]
> FIDO2 安全性金鑰是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="known-issues"></a>已知問題

* [使用者無法使用 FIDO2 安全性金鑰登入，因為 Windows Hello 臉部太快，而且是預設的登入機制](#users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism)
* [使用者建立已加入混合式 Azure AD 的電腦之後，無法立即使用 FIDO2 安全性金鑰](#users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine)
* [使用者在使用 FIDO2 安全性金鑰登入並收到認證提示之後，無法取得我的 NTLM 網路資源的 SSO](#users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt)

### <a name="users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism"></a>使用者無法使用 FIDO2 安全性金鑰登入，因為 Windows Hello 臉部太快，而且是預設的登入機制

Windows Hello 臉部是註冊使用者的裝置所需的最佳體驗。 FIDO2 安全性金鑰適用于共用裝置，或 Windows Hello 企業版註冊是一道關卡。

如果 Windows Hello 臉部防止使用者嘗試 FIDO2 安全性金鑰登入案例，則使用者可以在 [ **設定] > 登入選項**中移除臉部註冊，以關閉 Hello 臉部登入。

### <a name="users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine"></a>使用者建立已加入混合式 Azure AD 的電腦之後，無法立即使用 FIDO2 安全性金鑰

在全新安裝混合式 Azure AD 已加入的電腦上進行網域加入和重新開機程式之後，您必須使用密碼登入，並等候原則進行同步處理，才能使用 FIDO2 安全性金鑰登入。

此行為是已加入網域之裝置的已知限制，不是 FIDO2 安全性金鑰的特定行為。

若要檢查目前的狀態，請使用 `dsregcmd /status` 命令。 確認 *>azureadjoined* 和 *DomainJoined* 都顯示 *[是]*。

### <a name="users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt"></a>使用者在使用 FIDO2 安全性金鑰登入並收到認證提示之後，無法取得我的 NTLM 網路資源的 SSO

請確定已修補足夠的 Dc，以回應資源要求的時間。 若要檢查您是否可以看到執行此功能的伺服器，請檢查輸出 `nltest /dsgetdc:<dc name> /keylist /kdc`

如果您可以看到具有這項功能的 DC，則使用者的密碼可能會在登入後變更，或是發生另一個問題。 收集記錄，如下一節中的詳細說明，讓 Microsoft 支援小組進行 debug。

## <a name="troubleshoot"></a>疑難排解

有兩個領域可以進行疑難排解- [視窗用戶端問題](#windows-client-issues)或 [部署問題](#deployment-issues)。

### <a name="windows-client-issues"></a>Windows 用戶端問題

若要收集可協助您針對登入 Windows 或從 Windows 10 裝置存取內部部署資源的問題進行疑難排解的資料，請完成下列步驟：

1. 開啟 **意見反應中樞** 應用程式。 請確定您的名稱位於應用程式的左下方，然後選取 [ **建立新的意見專案**]。

    針對意見專案類型，選擇 [ *問題*]。

1. 選取 [ *安全性與隱私權* ] 類別，然後選取 [ *FIDO* ] 子類別。
1. 將 [ *傳送附加的*檔案] 和 [診斷] 的核取方塊，切換至 [我的意見反應]。
1. 選取 [ *重新建立我的問題*]，然後啟動 [ *capture*]。
1. 使用 FIDO2 安全性金鑰來鎖定電腦並將其解除鎖定。 如果發生此問題，請嘗試使用其他認證來解除鎖定。
1. 返回 **意見反應中樞**，選取 [ **停止捕捉**]，並提交您的意見反應。
1. 移至 *意見* 反應頁面，然後移至 [ *我的意見* 反應] 索引標籤。選取您最近提交的意見反應。
1. 選取右上角的 [ *共用* ] 按鈕，以取得意見反應的連結。 當您開啟支援案例，或回復指派給現有支援案例的工程師時，請包含此連結。

收集下列事件記錄檔和登錄機碼資訊：

**登錄機碼**

* *HKEY_LOCAL_MACHINE \SOFTWARE\Policies\Microsoft\FIDO [ \* ]*
* *HKEY_LOCAL_MACHINE \SOFTWARE\Policies\Microsoft\PasswordForWork \* [ \* ]*
* *HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Policies\PasswordForWork \* [ \* ]*

**診斷資訊**

* 即時核心轉儲
* 收集 AppX 套件資訊
* 如果內容檔案

**事件記錄檔**

* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-AAD%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-WebAuthN%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-HelloForBusiness%40Operational.evtx*

### <a name="deployment-issues"></a>部署問題

若要針對部署 Azure AD Kerberos 伺服器的問題進行疑難排解，請使用 Azure AD Connect 隨附的新 PowerShell 模組。

#### <a name="viewing-the-logs"></a>查看記錄

Azure AD Kerberos Server PowerShell Cmdlet 會使用與標準 Azure AD Connect Wizard 相同的記錄。 若要從 Cmdlet 查看資訊或錯誤詳細資料，請完成下列步驟：

1. 在 Azure AD Connect 伺服器上，流覽至 `C:\ProgramData\AADConnect\` 。 此資料夾預設為隱藏。
1. 開啟並查看位於目錄中的最新檔案 `trace-*.log` 。

#### <a name="viewing-the-azure-ad-kerberos-server-objects"></a>查看 Azure AD Kerberos 伺服器物件

若要查看 Azure AD Kerberos 伺服器物件，並確認它們的順序正確，請完成下列步驟：

1. 在 Azure AD Connect 伺服器上，開啟 PowerShell 並流覽至 `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. 執行下列 PowerShell 命令，以從 Azure AD 和內部部署 AD DS 查看 Azure AD 的 Kerberos 伺服器。

    將 *corp.contoso.com* 取代為您的內部部署 AD DS 網域的名稱。

    ```powershell
    Import-Module ".\AzureAdKerberos.psd1"

    # Specify the on-premises AD DS domain.
    $domain = "corp.contoso.com"

    # Enter an Azure Active Directory Global Admin username and password.
    $cloudCred = Get-Credential

    # Enter a Domain Admin username and password.
    $domainCred = Get-Credential

    # Get the Azure AD Kerberos Server Object
    Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential
    $domainCred
    ```

此命令會從 Azure AD 和內部部署 AD DS 輸出 Azure AD Kerberos 伺服器的屬性。 請檢查屬性，以確認所有專案都是正確的順序。 使用下表來確認屬性。

第一組屬性是來自內部部署 AD DS 環境中的物件。 第二個部分 (以 * Cloud * * 開頭的屬性是來自 Azure AD 中的 Kerberos 伺服器物件 ) ：

| 屬性           | 說明  |
|--------------------|--------------|
| Id                 | AD DS 網域控制站物件的唯一 *識別碼* 。 |
| DomainDnsName      | AD DS 網域的 DNS 功能變數名稱。 |
| ComputerAccount    | Azure AD Kerberos 伺服器物件的電腦帳戶物件 (DC) 。 |
| UserAccount        | 停用的使用者帳戶物件，該物件保留 Azure AD Kerberos 伺服器 TGT 加密金鑰。 此帳戶的 DN 是 *cn = krbtgt_AzureAD，cn = Users，<網域-DN>* |
| KeyVersion         | Azure AD Kerberos Server TGT 加密金鑰的金鑰版本。 建立金鑰時，會指派此版本。 然後，每次輪替金鑰時，就會遞增版本。 遞增是以複寫中繼資料為基礎，而且可能會大於1。<br /><br /> 例如，初始 *KeyVersion* 可能是 *192272*。 第一次輪替金鑰時，版本可能前進至 *212621*。<br /><br /> 要確認的重要事項是內部部署物件的 *KeyVersion* 和雲端物件的 *CloudKeyVersion* 相同。 |
| KeyUpdatedOn       | 更新或建立 Azure AD Kerberos Server TGT 加密金鑰的日期和時間。 |
| KeyUpdatedFrom     | 上次更新 Azure AD Kerberos Server TGT 加密金鑰的 DC。 |
| CloudId            | Azure AD 物件中的 *識別碼* 。 必須符合上述的 *識別碼* 。 |
| CloudDomainDnsName | Azure AD 物件中的 *DomainDnsName* 。 必須符合上述 *DomainDnsName* 。 |
| CloudKeyVersion    | Azure AD 物件中的 *KeyVersion* 。 必須符合上述 *KeyVersion* 。 |
| CloudKeyUpdatedOn  | Azure AD 物件中的 *KeyUpdatedOn* 。 必須符合上述 *KeyUpdatedOn* 。 |

## <a name="next-steps"></a>後續步驟

若要開始使用 FIDO2 安全性金鑰和內部部署資源的混合式存取，請參閱下列文章：

* [無密碼 FIDO2 安全性金鑰](howto-authentication-passwordless-security-key.md)
* [無密碼 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [無密碼內部部署](howto-authentication-passwordless-security-key-on-premises.md)
