---
title: 安裝應用程式 Proxy 代理程式連接器時遇到問題 | Microsoft Docs
description: 如何針對在安裝應用程式 Proxy 代理程式連接器時可能遇到的問題進行疑難排解
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f73d46b612c1dcf94554e10b4820c3f2442248f
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "82172401"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>安裝應用程式 Proxy 代理程式連接器時遇到問題

Microsoft AAD 應用程式 Proxy 連接器是內部網域元件，它會使用輸出連線來建立雲端可用端點至內部網域的連線。

## <a name="general-problem-areas-with-connector-installation"></a>連接器安裝的一般問題區域

連接器安裝失敗時，根本原因通常是下列其中一個區域：

1.  **連線**：若要成功完成安裝，新的連接器必須註冊並建立未來信任內容。 這是透過連線至 AAD 應用程式 Proxy 雲端服務來完成。

2.  **信任建立**：新的連接器會建立自我簽署憑證，並向雲端服務註冊。

3.  **系統管理員的驗證**：在安裝期間，使用者必須提供系統管理員認證才能完成連接器安裝。

> [!NOTE]
> 連接器安裝記錄可以在% TEMP% 資料夾中找到，並可協助提供有關導致安裝失敗原因的其他資訊。

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>確認與雲端應用程式 Proxy 服務和 Microsoft 登入頁面之間連線

**目標：** 確認連接器電腦可以連線到 AAD 應用程式 Proxy 註冊端點以及 Microsoft 登入頁面。

1.  在連接器伺服器上，使用[telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet)或其他埠測試控管執行埠測試，以確認埠443和80已開啟。

2.  如果其中有任何埠未成功，請確認防火牆或後端 proxy 可存取所需的網域和埠，請參閱[準備您的內部部署環境](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)。

3.  開啟瀏覽器 (個別索引標籤) 並前往下列網頁：`https://login.microsoftonline.com`，確定您可以登入該頁面。

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>驗證電腦和後端元件是否支援應用程式 Proxy 信任憑證

**目標：** 確認連接器電腦、後端 proxy 和防火牆可支援連接器針對未來信任所建立的憑證，以及該憑證是否有效。

>[!NOTE]
>連接器會嘗試建立由 TLS1.2 所支援的 SHA512 憑證。 如果電腦或後端防火牆和 proxy 不支援 TLS 1.2，則安裝會失敗。
>
>

**請檢查所需的必要條件：**

1.  確認電腦支援 TLS1.2：2012 R2 之後的所有 Windows 版本都應該支援 TLS 1.2。 如果您的連接器電腦是 2012 R2 或更舊版本，請確定電腦已安裝下列 KB：<https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  連絡您的網路系統管理員，並要求確認後端 Proxy 和防火牆不會針對連出流量封鎖 SHA512。

**若要確認用戶端憑證：**

確認目前用戶端憑證的指紋。 憑證存放區可以在%ProgramData%\microsoft\Microsoft AAD 應用程式 Proxy Connector\Config\TrustSettings.xml 中找到。

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

以下是可能的**IsInUserStore**值和意義：

- **false** -用戶端憑證是在安裝期間建立的，或由 AppProxyConnector 命令所起始的註冊。 它會儲存在本機電腦憑證存放區的個人容器中。 

請遵循下列步驟來驗證憑證：

1. 執行**certlm.msc**
2. 在管理主控台中，展開 [個人] 容器，然後按一下 [憑證]
3. 找出**connectorregistrationca.msappproxy.net**所簽發的憑證

- **true** -自動更新的憑證會儲存在網路服務的 [使用者] 憑證存放區的 [個人] 容器中。 

請遵循下列步驟來驗證憑證：

1. 下載[PsTools .zip](https://docs.microsoft.com/sysinternals/downloads/pstools)
2. 從封裝中解壓縮[psexec](https://docs.microsoft.com/sysinternals/downloads/psexec) ，並從提高許可權的命令提示字元執行**psexec-i-u "nt authority\network service" cmd.exe** 。
3. 在新出現的命令提示字元中執行**certmgr.msc**
2. 在管理主控台中，展開 [個人] 容器，然後按一下 [憑證]
3. 找出**connectorregistrationca.msappproxy.net**所簽發的憑證

**若要更新用戶端憑證：**

如果連接器有數個月未連線至服務，它的憑證可能會過期。 憑證更新失敗會導致憑證過期。 這會讓連接器服務停止運作。 事件1000會記錄在連接器的系統管理員記錄中：

「連接器重新註冊失敗：連接器信任憑證已過期。 在執行連接器的電腦上執行 PowerShell Cmdlet AppProxyConnector，以重新註冊您的連接器。」

在此情況下，請卸載並重新安裝連接器以觸發註冊，或者您可以執行下列 PowerShell 命令：

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

若要深入瞭解 AppProxyConnector 命令，請參閱[為 Azure AD 應用程式 Proxy 連接器建立自動安裝腳本](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-register-connector-powershell)

## <a name="verify-admin-is-used-to-install-the-connector"></a>確認是以系統管理員身分安裝連接器

**目標︰** 確認嘗試安裝連接器的使用者是具有正確認證的系統管理員。 目前，使用者至少必須是應用程式系統管理員，安裝才會成功。

**確認認證是否正確：**

連線至 `https://login.microsoftonline.com` 並使用相同的認證。 確定登入成功。 您可以前往 [ **Azure Active Directory**  - &gt; **使用者] 和 [群組** - &gt; ] [**所有使用者**] 來檢查使用者角色。 

選取您的使用者帳戶，然後在產生的功能表中選取 [目錄角色]。 確認選取的角色為 [應用程式系統管理員]。 如果您無法存取這些步驟上的任何頁面，表示您沒有必要的角色。

## <a name="next-steps"></a>後續步驟
[瞭解 Azure AD 應用程式 Proxy 連接器](application-proxy-connectors.md)
