---
title: 在 Azure AD 中進行 Azure AD Connect 雲端佈建的先決條件
description: 本文描述雲端佈建所需的先決條件和硬體需求。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cf072ae9544cd479aeca02d9b9fcd670b8eb5fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89226891"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Azure AD Connect 雲端佈建的先決條件
本文提供如何選擇及使用 Azure Active Directory (Azure AD) Connect 雲端佈建作為身分識別的指引。



## <a name="cloud-provisioning-agent-requirements"></a>雲端佈建代理程式需求
需要下列各項才能使用 Azure AD Connect 雲端佈建：
    
- 不是來賓使用者的 Azure AD 租使用者的混合式身分識別系統管理員帳戶。
- 佈建代理程式的 Windows 2012 R2 或更新版本內部部署伺服器。  此伺服器應該是以 [Active Directory 系統管理層模型](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)為基礎的第0層伺服器。
- 內部部署防火牆設定。

>[!NOTE]
>佈建代理程式目前只能安裝在英文版伺服器上。 在非英文版伺服器上安裝英文語言套件不是有效的因應措施，且會導致代理程式無法安裝。 

本文件其餘部分會提供這些先決條件的逐步指示。

### <a name="in-the-azure-active-directory-admin-center"></a>於 Azure Active Directory 管理中心

1. 在您的 Azure AD 租使用者上建立僅限雲端的混合式身分識別系統管理員帳戶。 如此一來，如果內部部署服務失敗或無法使用，即可管理租用戶設定。 瞭解如何 [新增僅限雲端的混合式身分識別系統管理員帳戶](../fundamentals/add-users-azure-active-directory.md)。 完成此步驟對於確保不會被租用戶封鎖至關重要。
1. 將一或多個[自訂網域名稱](../fundamentals/add-custom-domain.md)新增至 Azure AD 租用戶。 您的使用者可以使用其中一個網域名稱登入。

### <a name="in-your-directory-in-active-directory"></a>在 Active Directory 目錄中

執行 [IdFix 工具](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)來準備目錄屬性以進行同步處理。

### <a name="in-your-on-premises-environment"></a>在內部部署環境中

1. 識別已加入網域、執行 Windows Server 2012 R2 或更新版本，且至少有 4 GB RAM 和 .NET 4.7.1+ 執行階段的主機伺服器。

1. 本機伺服器上的 PowerShell 執行原則必須設定為 Undefined 或 RemoteSigned。

1. 如果伺服器與 Azure AD 之間有防火牆，請設定下列項目：
   - 確定代理程式可透過下列連接埠對 Azure AD 提出*輸出*要求：

        | 連接埠號碼 | 使用方式 |
        | --- | --- |
        | **80** | 驗證 TLS/SSL 憑證時下載憑證撤銷清單 (CRL)。  |
        | **443** | 處理服務的所有輸出通訊。 |
        | **8080** (選擇性) | 如果無法使用連接埠 443，則代理程式會透過連接埠 8080 每 10 分鐘報告其狀態一次。 此狀態會顯示在 Azure 入口網站中。 |
     
   - 如果您的防火牆會根據原始使用者強制執行規則，請開啟這些連接埠，讓來自以網路服務形式執行之 Windows 服務的流量得以通行。
   - 如果防火牆或 Proxy 允許指定安全尾碼，請將連線新增至 \*.msappproxy.net 和 \*.servicebus.windows.net。 如果不允許建立，請允許存取每週更新的 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。
   - 代理程式必須可存取 login.windows.net 與 login.microsoftonline.com，才能進行初始註冊。 因此也請針對這些 URL 開啟您的防火牆。
   - 為了驗證憑證，請解除封鎖下列 URL：mscrl.microsoft.com:80、crl.microsoft.com:80、ocsp.msocsp.com:80 和 www\.microsoft.com:80。 這些 URL 會用於其他 Microsoft 產品的憑證驗證，因此可能已將這些 URL 解除封鎖。

>[!NOTE]
> 不支援在 Windows Server Core 上安裝雲端佈建代理程式。


### <a name="additional-requirements"></a>其他需求
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS 需求

>[!NOTE]
>傳輸層安全性 (TLS) 是為了安全通訊所提供的通訊協定。 變更 TLS 設定會影響整個樹系。 如需詳細資訊，請參閱[更新為啟用 TLS 1.1 和 TLS 1.2 作為 Windows WinHTTP 中的預設安全通訊協定](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)。

裝載 Azure AD Connect 雲端佈建代理程式的 Windows Server 必須先啟用 TLS 1.2，才能進行安裝。

若要啟用 TLS 1.2，請遵循下列步驟。

1. 設定下列登錄機碼：
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. 重新啟動伺服器。


## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)