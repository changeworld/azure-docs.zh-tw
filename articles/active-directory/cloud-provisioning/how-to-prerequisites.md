---
title: Azure AD 連接 Azure AD 雲配置的先決條件
description: 本文介紹了雲配置所需的先決條件和硬體要求。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45648170f69d513b15e79cdd76f56e66bbc88bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332074"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Azure AD 連接雲配置的先決條件
本文提供有關如何選擇和使用 Azure 活動目錄 （Azure AD） 將雲資源調配作為標識解決方案的指導。



## <a name="cloud-provisioning-agent-requirements"></a>雲預配代理要求
使用 Azure AD 連接雲配置需要以下內容：
    
- 不是來賓使用者的 Azure AD 租戶的全域管理員帳戶。
- 具有 Windows 2012 R2 或更高版本的預配代理的本機伺服器。
- 本地防火牆配置。

>[!NOTE]
>預配代理目前只能安裝在英語伺服器上。 在非英語伺服器上安裝英語語言包不是有效的解決方法，將導致代理無法安裝。 

文檔的其餘部分提供這些先決條件的分步說明。

### <a name="in-the-azure-active-directory-admin-center"></a>於 Azure Active Directory 管理中心

1. 在 Azure AD 租用戶上建立僅限雲端的全域管理員帳戶。 這樣，如果本機服務失敗或不可用，則可以管理租戶的配置。 瞭解如何[添加僅雲全域管理員帳戶](../active-directory-users-create-azure-portal.md)。 完成此步驟對於確保不會鎖定租戶至關重要。
1. 將一或多個[自訂網域名稱](../active-directory-domains-add-azure-portal.md)新增至 Azure AD 租用戶。 您的使用者可以使用其中一個網域名稱登入。

### <a name="in-your-directory-in-active-directory"></a>在活動目錄中的目錄中

運行[IdFix 工具](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)以準備用於同步的目錄屬性。

### <a name="in-your-on-premises-environment"></a>在內部部署環境中

1. 使用至少 4 GB RAM 和 .NET 4.7.1+ 運行時，標識運行 Windows Server 2012 R2 或更高域的主機伺服器。

1. 如果伺服器和 Azure AD 之間存在防火牆，請配置以下專案：
   - 確定代理程式可透過下列連接埠對 Azure AD 提出*輸出*要求：

        | 連接埠號碼 | 使用方式 |
        | --- | --- |
        | **80** | 在驗證 TLS/SSL 憑證時下載憑證撤銷清單 （CRL）。  |
        | **443** | 處理與服務的所有出站通信。 |
        | **8080** (選擇性) | 如果無法使用連接埠 443，則代理程式會透過連接埠 8080 每 10 分鐘報告其狀態一次。 此狀態顯示在 Azure AD 門戶中。 |
     
   - 如果您的防火牆會根據原始使用者強制執行規則，請開啟這些連接埠，讓來自以網路服務形式執行之 Windows 服務的流量得以通行。
   - 如果您的防火牆或代理允許您指定安全尾碼，請向\*.msappproxy.net 和\*.servicebus.windows.net 添加連接。 如果不允許建立，請允許存取每週更新的 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。
   - 您的代理程式必須可存取 login.windows.net 與 login.microsoftonline.com，才能進行初始註冊。 因此也請針對這些 URL 開啟您的防火牆。
   - 對於證書驗證，取消阻止以下 URL：mscrl.microsoft.com:80、crl.microsoft.com:80、ocsp.msocsp.com:80 和 www\.microsoft.com:80。 這些 URL 用於與其他 Microsoft 產品的證書驗證，因此您可能已經解除了這些 URL 的阻止功能。

### <a name="verify-the-port"></a>驗證埠
要驗證 Azure 正在偵聽埠 443，並且代理是否可以與它通信，請使用以下 URL：

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

此測試驗證代理是否可以通過埠 443 與 Azure 通信。 打開瀏覽器，然後從安裝代理的伺服器轉到以前的 URL。

![埠可到達性驗證](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>其他需求
- [微軟 .NET 框架 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS 需求

>[!NOTE]
>傳輸層安全 （TLS） 是一種提供安全通信的協定。 更改 TLS 設置會影響整個林。 有關詳細資訊，請參閱[更新以啟用 TLS 1.1 和 TLS 1.2 作為 Windows 中的 WinHTTP 中的預設安全協定](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)。

承載 Azure AD Connect 雲預配代理的 Windows 伺服器在安裝 TLS 1.2 之前必須啟用 TLS 1.2。

要啟用 TLS 1.2，請按照以下步驟操作。

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

