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
ms.openlocfilehash: 4d773e6302edf0b799e6dfccc702750a9cc74f60
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406691"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>安裝應用程式 Proxy 代理程式連接器時遇到問題

Microsoft AAD 應用程式 Proxy 連接器是內部網域元件，它會使用輸出連線來建立雲端可用端點至內部網域的連線。

## <a name="general-problem-areas-with-connector-installation"></a>連接器安裝的一般問題區域

連接器安裝失敗時，根本原因通常是下列其中一個區域：

1.  **連線**：若要成功完成安裝，新的連接器必須註冊並建立未來信任內容。 這是透過連線至 AAD 應用程式 Proxy 雲端服務來完成。

2.  **信任建立**：新的連接器會建立自我簽署憑證，並向雲端服務註冊。

3.  **系統管理員的驗證**：在安裝期間，使用者必須提供系統管理員認證才能完成連接器安裝。

> [!NOTE]
> 連接器安裝日誌可以在 %TEMP% 資料夾中找到,並可説明提供有關導致安裝失敗的原因的其他資訊。

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>確認與雲端應用程式 Proxy 服務和 Microsoft 登入頁面之間連線

**目標：** 確認連接器電腦可以連線到 AAD 應用程式 Proxy 註冊端點以及 Microsoft 登入頁面。

1.  在連接器伺服器上,使用[telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet)或其他埠測試工具運行埠測試,以驗證埠 443 和 80 是否打開。

2.  如果其中任何埠未成功,請驗證防火牆或後端代理是否有權存取所需的網域和埠,請參閱[,準備本地環境](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)。

3.  開啟瀏覽器 (個別索引標籤) 並前往下列網頁：`https://login.microsoftonline.com`，確定您可以登入該頁面。

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>驗證應用程式代理信任憑證的電腦與後端元件支援

**目標:** 驗證連接器電腦、後端代理和防火牆是否可以支援連接器為將來信任創建的證書,以及該證書是否有效。

>[!NOTE]
>連接器會嘗試建立由 TLS1.2 所支援的 SHA512 憑證。 如果電腦或後端防火牆和代理不支援 TLS1.2,則安裝失敗。
>
>

**檢視需要的先決條件:**

1.  確認電腦支援 TLS1.2：2012 R2 之後的所有 Windows 版本都應該支援 TLS 1.2。 如果您的連接器電腦是 2012 R2 或更舊版本，請確定電腦已安裝下列 KB：<https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  連絡您的網路系統管理員，並要求確認後端 Proxy 和防火牆不會針對連出流量封鎖 SHA512。

**要驗證客戶端憑證:**

驗證當前用戶端證書的指紋。 憑證儲存在 %程式資料%\微軟_微軟 AAD 應用程式代理連線器_Config_TrustSettings.xml 中找不到

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

以下是可能的**IsInUserStore**值和含義:

- **false** - 用戶端證書是在註冊-AppProxyConnector 命令啟動的安裝或註冊期間創建的。 儲存在本地電腦的憑證儲存中的個人容器中。 

依步驟驗證憑證:

1. 執行**憑證.msc**
2. 在管理控制台中展開個人容器,然後單擊證書
3. 找到**由connectorregistrationca.msappproxy.net**頒發的證書

- **true** - 自動續訂的憑證儲存在網路服務的使用者憑證儲存中的個人容器中。 

依步驟驗證憑證:

1. 下載[PsTools.zip](https://docs.microsoft.com/sysinternals/downloads/pstools)
2. 從包中提取[psExec,](https://docs.microsoft.com/sysinternals/downloads/psexec)並從提升的指令提示符中執行**psexec -i-u"nt 許可權_網路服務"cmd.exe。**
3. 在新出現的指令提示中執行**certmgr.msc**
2. 在管理控制台中展開個人容器,然後單擊證書
3. 找到 #connectorregistrationca.msappproxy.ne 頒發的憑證

**要續訂用戶端憑證:**

如果連接器有數個月未連線至服務，它的憑證可能會過期。 證書續訂失敗會導致證書過期。 這使得連接器服務停止工作。 事件 1000 紀錄連線在連接器的管理紀錄中:

連接器重新註冊失敗:連接器信任證書已過期。 在連接器運行的電腦上運行 PowerShell cmdlet 寄存器-AppProxyConnector,以重新註冊連接器。

在這種情況下,卸載並重新安裝連接器以觸發註冊,或者您可以運行以下 PowerShell 命令:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

要瞭解有關註冊-AppProxyConnector 命令的更多詳細資訊,請參閱[為 Azure AD 應用程式代理連接器建立無人參與安裝腳本](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-register-connector-powershell)

## <a name="verify-admin-is-used-to-install-the-connector"></a>確認是以系統管理員身分安裝連接器

**目標︰** 確認嘗試安裝連接器的使用者是具有正確認證的系統管理員。 目前,用戶必須至少是應用程式管理員才能成功安裝。

**確認認證是否正確：**

連線至 `https://login.microsoftonline.com` 並使用相同的認證。 確定登入成功。 您可以通過存**取 Azure 活動目錄** -&gt;**使用者和群組** -&gt;**所有使用者**來檢查使用者角色。 

選擇您的使用者帳戶,然後在生成的功能表中選擇"目錄角色" 驗證所選角色是否為"應用程式管理員" 如果您無法存取這些步驟上的任何頁面，表示您沒有必要的角色。

## <a name="next-steps"></a>後續步驟
[瞭解 Azure AD 應用程式代理連線器](application-proxy-connectors.md)
