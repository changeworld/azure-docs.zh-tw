---
title: 無密碼安全金鑰登錄到本地資源（預覽） - Azure 活動目錄
description: 瞭解如何使用 Azure 活動目錄（預覽）將無密碼安全金鑰登錄啟用到本地資源
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b29f84931c169ffe1c2c81d5e32201cbc63fc88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942866"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>使用 Azure 活動目錄（預覽）為本地資源啟用無密碼安全金鑰登錄

本文檔重點介紹為**Azure AD 聯接**和混合 Azure AD**加入**Windows 10 設備的環境啟用無密碼身份驗證。 此功能使用 Microsoft 相容的安全金鑰為本地資源提供無縫的單一登入 （SSO）。

|     |
| --- |
| FIDO2 安全金鑰是 Azure 活動目錄的公共預覽功能。 有關預覽的詳細資訊，請參閱 Microsoft [Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>使用 FIDO2 金鑰將 SSO 到本地資源

Azure 活動目錄 （AD） 可以為一個或多個活動目錄域頒發 Kerberos 票證授予票證 （TGT）。 此功能允許使用者使用 FIDO2 安全金鑰等現代憑據登錄到 Windows 並訪問基於活動目錄的傳統資源。 Kerberos 服務票證和授權繼續由本地活動目錄網域控制站控制。

在本地活動目錄中創建 Azure AD Kerberos Server 物件，然後安全地發佈到 Azure 活動目錄。 該物件不與任何物理伺服器關聯。 它只是一個資源，Azure 活動目錄可以使用它為活動目錄域生成 Kerberos TGT。

![從 Azure AD 和 AD DS 獲取票證授予票證 （TGT）](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. 使用者使用 FIDO2 安全金鑰登錄到其 Windows 10 設備，並驗證 Azure AD。
1. Azure AD 檢查目錄的 Kerberos 伺服器金鑰與使用者的本地 AD 域匹配。
   1. Azure AD 為使用者的本地 AD 域生成 Kerberos TGT。 TGT 僅包括使用者的 SID。 TGT 中不包含任何授權資料。
1. TGT 及其 Azure AD 主刷新權杖 （PRT） 將返回給用戶端。
1. 用戶端電腦與本地 AD 網域控制站聯繫，並將部分 TGT 交易為完全成型的 TGT。
1. 用戶端電腦現在具有 Azure AD PRT 和完整的活動目錄 TGT，可以訪問雲和本地資源。

## <a name="requirements"></a>需求

在完成本文中的步驟之前，組織必須完成向[Windows 10 設備（預覽）啟用無密碼安全金鑰符號](howto-authentication-passwordless-security-key.md)的步驟。

組織還必須滿足以下軟體要求。

- 設備必須運行 Windows 10 內部人員版本 18945 或更新。
- 您必須具有[Azure AD 連接](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)的版本 1.4.32.0 或更高版本。
  - 有關可用的 Azure AD 混合身份驗證選項的詳細資訊，請參閱[為 Azure Active Directory 混合標識解決方案選擇正確的身份驗證方法](../../security/fundamentals/choose-ad-authn.md)，並[選擇用於 Azure AD 連接的安裝類型](../hybrid/how-to-connect-install-select-installation.md)。
- 您的 Windows Server 網域控制站必須安裝以下修補程式：
    - 對於 Windows 伺服器 2016 -https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - 對於 Windows 伺服器 2019 -https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>支援的案例

在以下兩種情況下，該方案都支援單一登入 （SSO）：

- 對於雲資源，如 Office 365 和其他啟用 SAML 的應用程式。
- 用於本地資源，以及對網站的 Windows 集成身份驗證。 這些資源可以包括需要 IIS 身份驗證的網站和 SharePoint 網站以及/或使用 NTLM 身份驗證的資源。

### <a name="unsupported-scenarios"></a>不支援的情節

不支援以下方案：

- Windows 伺服器活動目錄域服務 （AD DS） 域已加入（僅限本地設備）部署。
- 使用安全金鑰的 RDP、VDI 和 Citrix 方案。
- 使用安全金鑰的 S/MIME。
- 使用安全金鑰"以樣運行"。
- 使用安全金鑰登錄到伺服器。

## <a name="create-kerberos-server-object"></a>創建 Kerberos 伺服器物件

管理員使用 Azure AD Connect 伺服器中的 PowerShell 工具在其本地目錄中創建 Azure AD Kerberos Server 物件。 在組織中包含 Azure AD 使用者的每個域和林中運行以下步驟：

1. 升級到最新版本的 Azure AD 連接。 這些說明假定您已配置 Azure AD 連接以支援混合環境。
1. 在 Azure AD 連接伺服器上，打開提升的 PowerShell 提示符，然後導航到`C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. 運行以下 PowerShell 命令，在本地活動目錄域和 Azure 活動目錄租戶中創建新的 Azure AD Kerberos 伺服器物件。

> [!NOTE]
> 在`contoso.corp.com`下面的示例中，將替換為本地活動目錄功能變數名稱。

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>查看和驗證 Azure AD Kerberos 伺服器

您可以使用以下命令查看和驗證新創建的 Azure AD Kerberos 伺服器：

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

此命令輸出 Azure AD Kerberos 伺服器的屬性。 您可以查看屬性以驗證一切是否井然有序。

| 屬性 | 描述 |
| --- | --- |
| ID | AD DS DC 物件的唯一 ID。 此 ID 有時稱為"插槽"或"分支 ID"。 |
| DomainDnsName | 活動目錄域的 DNS 功能變數名稱。 |
| ComputerAccount | Azure AD Kerberos Server 物件（DC）的電腦帳戶物件。 |
| UserAccount | 保存 Azure AD Kerberos 伺服器 TGT 加密金鑰的已禁用使用者帳戶物件。 此帳戶的 DN 是`CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| 金鑰版本 | Azure AD Kerberos 伺服器 TGT 加密金鑰的金鑰版本。 創建金鑰時分配版本。 然後，每次旋轉鍵時都會遞增版本。 增量基於複製中繼資料，可能大於 1。 例如，初始*金鑰版本*可以是*192272*。 第一次旋轉金鑰時，版本可以前進到*212621*。 需要驗證的重要的事情是，本機物件的*KeyVersion*和雲物件的*CloudKeyVersion*是否相同。 |
| 金鑰更新 | 更新或創建的 Azure AD Kerberos 伺服器 TGT 加密金鑰的日期和時間。 |
| 金鑰從 | 上次更新 Azure AD Kerberos 伺服器 TGT 加密金鑰的 DC。 |
| 雲 Id | 來自 Azure AD 物件的 ID。 必須與上面的 ID 匹配。 |
| 雲域Dns名稱 | Azure AD 物件的*域 Dns 名稱*。 必須與上面的*功能變數名稱*匹配。 |
| 雲鍵版本 | 來自 Azure AD 物件的*金鑰版本*。 必須與上面*的金鑰版本*匹配。 |
| 雲鍵更新 | 從 Azure AD 物件*更新的鍵*。 必須與上面的 *"更新金鑰"匹配*。 |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>旋轉 Azure AD Kerberos 伺服器金鑰

Azure AD Kerberos 伺服器加密 krbtgt 金鑰應定期輪換。 建議您遵循用於旋轉所有其他活動目錄網域控制站 krbtgt 金鑰的相同計畫。

> [!WARNING]
> 還有其他工具可以旋轉 krbtgt 鍵，但是，您必須使用本文檔中提到的工具來旋轉 Azure AD Kerberos Server 的 krbtgt 鍵。 這可確保在本地 AD 和 Azure AD 中更新金鑰。

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>刪除 Azure AD Kerberos 伺服器

如果要還原方案並從本地活動目錄和 Azure 活動目錄中刪除 Azure AD Kerberos 伺服器，請運行以下命令：

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>多林和多域方案

Azure AD Kerberos 伺服器物件在 Azure AD 中表示為*KerberosDomain*物件。 每個本地活動目錄域在 Azure AD 中表示為單個*KerberosDomain*物件。

例如，您的組織具有具有兩個域的活動目錄林`contoso.com`和`fabrikam.com`。 如果選擇允許 Azure AD 為整個林頒發 Kerberos TGT，則 Azure AD 中有兩個*KerberosDomain*物件。 一個*KerberosDomain*`contoso.com`物件，一`fabrikam.com`個用於 。 如果有多個活動目錄林，則每個林中的每個域都有一個*KerberosDomain*物件。

您需要運行步驟，在包含 Azure AD 使用者的組織中的每個域和林中[創建 Kerberos 伺服器物件](#create-kerberos-server-object)。

## <a name="known-behavior"></a>已知行為

如果您的密碼已過期，則阻止使用 FIDO 登錄。 期望使用者在使用 FIDO 登錄之前重置其密碼。

## <a name="troubleshooting-and-feedback"></a>故障排除和回饋

如果您想在預覽此功能時共用回饋或遇到問題，請使用以下步驟通過 Windows 回饋中樞應用共用：

1. 啟動**回饋中樞**並確保您已登錄。
1. 根據以下分類提交回饋：
   - 類別：安全和隱私
   - 子類別： FIDO
1. 要捕獲日誌，請使用 選項**重新創建問題**

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="does-this-work-in-my-on-premises-environment"></a>這在我的本地環境中工作嗎？

此功能不適合純本地活動目錄域服務 （AD DS） 環境。

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>我的組織需要兩個因素身份驗證才能訪問資源。 我可以做些什麼來支援這一要求？

安全金鑰有多種外形。 請與感興趣的設備製造商聯繫，討論如何使用 PIN 或生物識別作為第二個因素來啟用其設備。

### <a name="can-admins-set-up-security-keys"></a>管理員可以設置安全金鑰嗎？

我們正在開發此功能，以便此功能的一般可用性 （GA）。

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>我在哪裡可以找到符合安全金鑰的金鑰？

[FIDO2 安全金鑰](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>如果我丟失了安全金鑰，該怎麼辦？

您可以通過導航到**安全資訊**頁並刪除安全金鑰從 Azure 門戶中刪除金鑰。

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>創建混合 Azure AD 聯接電腦後，無法立即使用 FIDO

如果乾淨地安裝混合 Azure AD 聯接電腦，則在域加入和重新開機過程後，必須使用密碼登錄並等待策略同步，然後才能使用 FIDO 登錄。

- `dsregcmd /status`通過鍵入命令視窗來檢查目前狀態，並檢查*AzureAd並和**域加入*均顯示 *"是*"。
- 此延遲是域加入設備的已知限制，不特定于 FIDO。

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>使用 FIDO 登錄並獲得認證提示後，我無法將 SSO 連接到我的 NTLM 網路資源

確保修補足夠的網域控制站，以便及時回應以服務資源請求。 要檢查是否可以看到運行該功能的網域控制站，請查看 的`nltest /dsgetdc:contoso /keylist /kdc`輸出。

## <a name="next-steps"></a>後續步驟

[瞭解有關無密碼的更多](concept-authentication-passwordless.md)
