---
title: Azure AD 中 Azure AD Connect 雲端布建的必要條件
description: 本文說明雲端布建所需的必要條件和硬體需求。
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
ms.openlocfilehash: 382c588ca005f95f4ae38e7506c0e3e8d842bd2c
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298644"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Azure AD Connect 雲端布建的必要條件
本文提供如何選擇及使用 Azure Active Directory （Azure AD）連接雲端布建作為身分識別解決方案的指引。



## <a name="cloud-provisioning-agent-requirements"></a>雲端布建代理程式需求
您需要下列各項，才能使用 Azure AD Connect 雲端布建：
    
- Azure AD 租使用者的全域系統管理員帳戶，不是來賓使用者。
- 適用于使用 Windows 2012 R2 或更新版本之布建代理程式的內部部署伺服器。
- 內部部署防火牆設定。

>[!NOTE]
>布建代理程式目前只能安裝在英文版的伺服器上。 在非英文的伺服器上安裝英文語言套件不是有效的因應措施，並會導致代理程式無法安裝。 

檔的其餘部分會提供這些必要條件的逐步指示。

### <a name="in-the-azure-active-directory-admin-center"></a>於 Azure Active Directory 管理中心

1. 在 Azure AD 租用戶上建立僅限雲端的全域管理員帳戶。 如此一來，如果您的內部部署服務失敗或變得無法使用，您就可以管理租使用者的設定。 深入瞭解如何[新增僅限雲端的全域系統管理員帳戶](../active-directory-users-create-azure-portal.md)。 完成此步驟對於確保您的租使用者不會遭到鎖定是不可或缺的。
1. 將一或多個[自訂網域名稱](../active-directory-domains-add-azure-portal.md)新增至 Azure AD 租用戶。 您的使用者可以使用其中一個網域名稱登入。

### <a name="in-your-directory-in-active-directory"></a>在您目錄中的 Active Directory

執行[IdFix 工具](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)來準備目錄屬性以進行同步處理。

### <a name="in-your-on-premises-environment"></a>在內部部署環境中

1. 識別執行 Windows Server 2012 R2 或更新版本的已加入網域主機伺服器，至少要有 4 GB RAM 和 .NET 4.7.1 + 執行時間。

1. 如果您的伺服器與 Azure AD 之間有防火牆，請設定下列專案：
   - 確定代理程式可透過下列連接埠對 Azure AD 提出*輸出*要求：

        | 連接埠號碼 | 使用方式 |
        | --- | --- |
        | **80** | 在驗證 SSL 憑證時下載憑證撤銷清單（Crl）。  |
        | **443** | 處理與服務的所有輸出通訊。 |
        | **8080** (選擇性) | 如果無法使用連接埠 443，則代理程式會透過連接埠 8080 每 10 分鐘報告其狀態一次。 此狀態會顯示在 Azure AD 入口網站中。 |
     
   - 如果您的防火牆會根據原始使用者強制執行規則，請開啟這些連接埠，讓來自以網路服務形式執行之 Windows 服務的流量得以通行。
   - 如果您的防火牆或 proxy 允許您指定安全尾碼，請將連接新增至 \*msappproxy.net 和 \*. servicebus.windows.net。 如果不允許建立，請允許存取每週更新的 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。
   - 您的代理程式需要存取 login.windows.net 和 login.microsoftonline.com，才能進行初始註冊。 因此也請針對這些 URL 開啟您的防火牆。
   - 針對憑證驗證，請解除封鎖下列 Url： mscrl.microsoft.com:80、crl.microsoft.com:80、ocsp.msocsp.com:80 和 www\.microsoft.com:80。 這些 Url 會用於其他 Microsoft 產品的憑證驗證，因此您可能已將這些 Url 解除封鎖。

### <a name="verify-the-port"></a>驗證埠
若要確認 Azure 正在接聽埠443，且您的代理程式可以與其通訊，請使用下列 URL：

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

此測試會驗證您的代理程式是否可透過埠443與 Azure 通訊。 開啟瀏覽器，並移至安裝代理程式之伺服器上的先前 URL。

![驗證埠可連線性](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>其他需求
- [Microsoft .NET Framework 4.7。1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS 需求

>[!NOTE]
>傳輸層安全性（TLS）是提供安全通訊的通訊協定。 變更 TLS 設定會影響整個樹系。 如需詳細資訊，請參閱[更新以啟用 tls 1.1 和 tls 1.2 做為 Windows 中 WinHTTP 中的預設安全通訊協定](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)。

裝載 Azure AD Connect 雲端布建代理程式的 Windows server 必須先啟用 TLS 1.2，才能進行安裝。

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

