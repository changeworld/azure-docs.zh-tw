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
ms.openlocfilehash: 466e1ce0efbdec3f5475634f3857d02554d93d98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049139"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>安裝應用程式 Proxy 代理程式連接器時遇到問題

Microsoft AAD 應用程式 Proxy 連接器是內部網域元件，它會使用輸出連線來建立雲端可用端點至內部網域的連線。

## <a name="general-problem-areas-with-connector-installation"></a>連接器安裝的一般問題區域

連接器安裝失敗時，根本原因通常是下列其中一個區域：

1.  **連線**：若要成功完成安裝，新的連接器必須註冊並建立未來信任內容。 這是透過連線至 AAD 應用程式 Proxy 雲端服務來完成。

2.  **信任建立**：新的連接器會建立自我簽署憑證，並向雲端服務註冊。

3.  **系統管理員的驗證**：在安裝期間，使用者必須提供系統管理員認證才能完成連接器安裝。

> [!NOTE]
> 連接器安裝日誌可以在 %TEMP% 資料夾中找到，並可説明提供有關導致安裝失敗的原因的其他資訊。

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>確認與雲端應用程式 Proxy 服務和 Microsoft 登入頁面之間連線

**目標：** 確認連接器電腦可以連線到 AAD 應用程式 Proxy 註冊端點以及 Microsoft 登入頁面。

1.  在連接器伺服器上，使用[telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet)或其他埠測試控管運行埠測試，以驗證埠 443 和 80 是否打開。

2.  如果其中任何埠未成功，請驗證防火牆或後端代理是否有權訪問所需的域和埠，請參閱[，準備本地環境](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)。

3.  開啟瀏覽器 (個別索引標籤) 並前往下列網頁：`https://login.microsoftonline.com`，確定您可以登入該頁面。

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>確認電腦和後端元件支援應用程式 Prxoy 信任憑證

**目標︰** 確認連接器電腦、後端 Proxy 和防火牆可支援由連接器針對未來信任所建立的憑證。

>[!NOTE]
>連接器會嘗試建立由 TLS1.2 所支援的 SHA512 憑證。 如果電腦或後端防火牆和代理不支援 TLS1.2，則安裝失敗。
>
>

**若要解決此問題：**

1.  確認電腦支援 TLS1.2：2012 R2 之後的所有 Windows 版本都應該支援 TLS 1.2。 如果您的連接器電腦是 2012 R2 或更舊版本，請確定電腦已安裝下列 KB：<https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  連絡您的網路系統管理員，並要求確認後端 Proxy 和防火牆不會針對連出流量封鎖 SHA512。

## <a name="verify-admin-is-used-to-install-the-connector"></a>確認是以系統管理員身分安裝連接器

**目標︰** 確認嘗試安裝連接器的使用者是具有正確認證的系統管理員。 目前，使用者必須至少是應用程式管理員才能成功安裝。

**確認認證是否正確：**

連線至 `https://login.microsoftonline.com` 並使用相同的認證。 確定登入成功。 您可以通過訪問**Azure 活動目錄** -&gt;**使用者和組** -&gt;**所有使用者**來檢查使用者角色。 

選擇您的使用者帳戶，然後在生成的功能表中選擇"目錄角色"。 驗證所選角色是否為"應用程式管理員"。 如果您無法存取這些步驟上的任何頁面，表示您沒有必要的角色。

## <a name="next-steps"></a>後續步驟
[瞭解 Azure AD 應用程式代理連接器](application-proxy-connectors.md)
