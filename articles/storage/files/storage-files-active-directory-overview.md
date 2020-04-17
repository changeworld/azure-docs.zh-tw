---
title: 概述 - Azure 檔案基於識別的授權
description: Azure 檔案透過 Azure 活動目錄網域服務 (AD DS) 和活動目錄支援透過 SMB(伺服器訊息區塊)進行基於身份的身份驗證。 然後，您加入網域的 Windows 虛擬機器 (VM) 可以使用 Azure AD 認證存取 Azure 檔案共用。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: rogarana
ms.openlocfilehash: 7d9f8ccb4273d1378c4826dea420c4edca2f8ac3
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536570"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>基於 Azure 檔案的基礎的 SMB 存取的身分驗證支援的概述
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

要瞭解如何為 Azure 檔案分享(預覽)啟用本地活動目錄網域服務身份驗證,請參閱[透過 SMB 為 Azure 檔案分享啟用本地活動目錄域服務身份驗證](storage-files-identity-auth-active-directory-enable.md)。

要瞭解如何為 Azure 檔案分享啟用 Azure AD DS 身份驗證,請參閱[在 Azure 檔上啟用 Azure 活動目錄網域服務身份驗證](storage-files-identity-auth-active-directory-domain-service-enable.md)。

## <a name="glossary"></a>詞彙 
瞭解與 Azure 檔案分享的 SMB 上透過 SMB 進行 Azure AD 網域服務身份驗證的一些關鍵術語很有説明:

-   **Kerberos 驗證**

    Kerberos 是用來驗證的使用者或主機身分識別的驗證通訊協定。 如需有關 Kerberos 的詳細資訊，請參閱 [Kerberos 驗證概觀](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)。

-  **伺服器訊息區 (SMB) 通訊協定**

    SMB 是業界標準網路檔案共用通訊協定。 SMB 亦稱為 Common Internet File System 或 CIFS。 如需有關 SMB 的詳細資訊，請參閱 [Microsoft SMB 通訊協定和 CIFS 通訊協定概觀](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)。

-   **Azure 動作目錄(Azure AD)**

    Azure 活動目錄 (Azure AD) 是 Microsoft 的多租戶基於雲端的目錄和識別管理服務。 Azure AD 將核心目錄服務、應用程式存取管理及身分識別保護結合到單個解決方案。 Azure AD 加入的 Windows 虛擬機器 (VM) 可以使用 Azure AD 認證存取 Azure 檔案分享。 有關詳細資訊,請參閱什麼是[Azure 活動目錄?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS 提供託管域服務,如域聯接、組策略、LDAP 和 Kerberos/NTLM 身份驗證。 這些服務與活動目錄域服務完全相容。 有關詳細資訊,請參閱[Azure 活動目錄網域服務](../../active-directory-domain-services/overview.md)。

- **本地活動目錄網域服務 (AD DS)**

    與 Azure 檔(預覽)的本地活動目錄域服務 (AD DS) 整合提供儲存目錄資料,同時使其可供網路使用者和管理員使用的方法。 通過登錄身份驗證和對目錄中對象的存取控制,安全性與 AD DS 整合。 通過單個網路登錄,管理員可以管理整個網路中的目錄數據和組織,並且經過授權的網路使用者可以訪問網路上的任意位置的資源。 AD DS 通常被企業在本地環境中採用,AD DS 認證控制的身份。 有關詳細資訊,請參閱[活動目錄網域服務概述](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。

-   **Azure 角色型存取控制 (RBAC)**

    Azure 角色型存取控制 (RBAC) 可以對 Azure 進行更細緻的存取權管理。 使用 RBAC，您可以對使用者授權執行其執行工作所需的最少權限，以便管理對資源的存取權。 有關 RBAC 的詳細資訊,請參閱[Azure 中什麼是基於角色的訪問控制 (RBAC)?](../../role-based-access-control/overview.md)

## <a name="common-use-cases"></a>一般使用案例

Azure 檔案上的識別碼識別碼識別和支援最適合使用以下用例:

### <a name="replace-on-premises-file-servers"></a>取代本地端檔案伺服器

棄用和替換分散的本地文件伺服器是每個企業在 IT 現代化旅程中遇到的常見問題。 當您可以將資料遷移到 Azure 檔時,使用本地 AD DS(預覽)身份驗證的 Azure 檔共享是最適合的。 完全遷移將允許您利用高可用性和可擴充性優勢,同時最大限度地減少用戶端更改。 它為最終使用者提供了無縫的遷移體驗,因此他們可以繼續使用其現有的域聯接電腦使用相同的憑據訪問其數據。

### <a name="lift-and-shift-applications-to-azure"></a>提升應用程式並將其移至 Azure

提升應用程式並將其移至雲時,需要為數據保留相同的身份驗證模型。 隨著我們將基於身份的存取控制體驗擴展到 Azure 檔共用,無需將應用程式更改為現代身份驗證方法並加快雲端採用。 Azure 檔案共用提供了與 Azure AD DS 或本地 AD DS(預覽)整合式驗證的選項。 如果計劃是 100% 雲原生,並最大限度地減少管理雲基礎架構的努力,Azure AD DS 將更適合作為完全託管的域服務。 如果您需要與 AD DS 功能完全相容,您可能需要考慮透過 VM 上自託管域控制器將 AD DS 環境擴展到雲端。 無論哪種方式,我們都提供靈活性,選擇適合您業務需求的域服務。

### <a name="backup-and-disaster-recovery-dr"></a>備份與災難復原 (DR)

如果要將主檔案存儲保留在本地,Azure 檔案共用可用作備份或 DR 的理想存儲,以提高業務連續性。 您可以使用 Azure 檔案共享從現有檔案伺服器備份數據,同時保留 Windows DACL。 對於 DR 方案,您可以配置身份驗證選項,以支援故障轉移時的正確存取控制實施。

## <a name="supported-scenarios"></a>支援的案例

下表總結了支援的 Azure 檔共用 Azure AD DS 和本地 AD DS(預覽)的身份驗證方案。 我們建議選擇為客戶端環境採用的域服務,以便與 Azure 檔整合。 如果已在本地或 Azure 中設置 AD DS(預覽),設備已加入到 AD 的域,則應選擇利用 AD DS(預覽)進行 Azure 檔共享身份驗證。 同樣,如果您已經採用 Azure AD DS (GA),則應將它用於 Azure 檔共享身份驗證。


|Azure AD DS 認證  | 本地 AD DS(預覽)身份驗證  |
|---------|---------|
|Azure AD DS 聯接的 Windows 電腦可以透過 SMB 存取具有 Azure AD 認證的 Azure 檔案共用。     |本地 AD DS 聯接的 Windows 計算機可以使用透過 SMB 同步到 Azure AD 的本地活動目錄認證 Azure 檔案分享。         |

### <a name="unsupported-scenarios"></a>不支援的情節

- Azure AD DS 和本地 ADDS 身份驗證不支援針對計算機帳戶的身份驗證。 您可以考慮改用服務登錄帳戶。
- Azure AD DS 身份驗證不支援針對 Azure AD 加入的設備進行身份驗證。

## <a name="advantages-of-identity-based-authentication"></a>基於身份的身份驗證的優勢
與使用分享金鑰身份驗證不同,基於識別的 Azure 檔身份驗證提供幾個好處:

-   **使用本地 AD DS 和 Azure AD DS 將傳統的識別碼的檔案分享存取體驗延伸到雲端**  
    如果計劃提升應用程式並將其移至雲,用 Azure 檔共享替換傳統檔伺服器,則可能需要應用程式使用本地 AD DS 或 Azure AD DS 認證進行身份驗證以存取檔案資料。 Azure 檔案支援使用本地 AD DS 或 Azure AD DS 認證從本地 AD DS 或 Azure AD DS 網域聯接的 VM 透過 SMB 存取 Azure 檔案分享。

-   **在 Azure 檔案共用上強制執行細微存取控制**  
    您可以在共用、目錄或檔級別向特定標識授予許可權。 例如，假設您有多個小組使用同一個 Azure 檔案共用來共同作業某個專案。 您可以授權所有小組存取非敏感目錄，同時將包含敏感財務資料之目錄的存取權僅授與財務小組。 

-   **備份 Windows ACL(也稱為 NTFS)以及您的資料**  
    可以使用 Azure 檔案共享備份現有的本地檔案共用。 當您透過 SMB 將檔案共享備份到 Azure 檔案共享時,Azure 檔案會保留 ACL 和資料。

## <a name="how-it-works"></a>運作方式

Azure 檔案共用支援 Kerberos 身份驗證,以便與 Azure AD DS 或本地 AD DS(預覽)整合。 在 Azure 檔共享上啟用身份驗證之前,必須先設置域環境。 對於 Azure AD DS 身份驗證,應啟用 Azure AD 域服務和域加入計畫從中存取檔資料的 VM。 加入網域的 VM 必須與 Azure AD DS 駐留在同一虛擬網路 (VNET) 中。 同樣,對於本地 AD DS(預覽)身份驗證,您需要設置域控制器和域加入電腦或 VM。

當與在 VM 上運行的應用程式關聯的標識嘗試訪問 Azure 檔共享中的數據時,請求將發送到 Azure AD DS 以驗證該標識。 如果身份驗證成功,Azure AD DS 將返回 Kerberos 令牌。 應用程式發送包含 Kerberos 權杖的請求,Azure 檔案共用使用該權杖授權請求。 Azure 檔案共用僅接收權杖,不保留 Azure AD DS 認證。 本地 ADDS 身份驗證的工作方式類似,AD DS 提供 Kerberos 令牌。

![顯示「透過 SMB 的 Azure AD 驗證」圖的螢幕擷取畫面](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>開啟基礎的身份驗證

您可以使用 Azure AD DS 或本地 AD DS(預覽)為新儲存帳戶和現有儲存帳戶上的 Azure 檔案共享啟用基於身份的身份驗證。 存儲帳戶上只能使用一個域服務來進行文件訪問身份驗證,該身份驗證適用於帳戶中的所有文件共用。 在本文[「 在 Azure 檔案」 開啟 Azure 的目錄網域服務身份驗證](storage-files-identity-auth-active-directory-domain-service-enable.md)「一文中, 有關使用 Azure AD DS 設定檔案分享以進行身份驗證的詳細指南,以及另一篇文章中的本地 AD DS(預覽)指南,[即為 Azure 檔案分享啟用本地活動目錄域服務身份驗證](storage-files-identity-auth-active-directory-enable.md)。

### <a name="configure-share-level-permissions-for-azure-files"></a>設定 Azure 檔案共用層級權限

啟用 Azure AD DS 或本地 AD DS(預覽)身份驗證後,可以使用內建 RBAC 角色或為 Azure AD 標識配置自定義角色,並將訪問許可權分配給儲存帳戶中的任何文件共用。 分配的許可權允許授予的標識僅訪問共用,沒有其他內容,甚至無法訪問根目錄。 您仍然需要單獨設定 Azure 檔共享的目錄或檔級許可權。

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>為 Azure 檔設定目錄或檔案層限

Azure 檔案共享在目錄和檔案等級(包括根目錄)強制實施標準 Windows 檔案許可權。 SMB 和 REST 都支援目錄或檔級許可權的配置。 從 VM 載入目標檔案共用,並使用 Windows 檔案資源管理器、Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)或[Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6)命令設定許可權。

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>為超級使用者權限使用儲存體帳戶金鑰

具有儲存帳戶金鑰的使用者可以存取具有超級用戶許可權的 Azure 檔案共用。 超級用戶許可權繞過所有訪問控制限制。

> [!IMPORTANT]
> 我們推薦的安全最佳實踐是避免共用存儲帳戶密鑰,並盡可能利用基於身份的身份驗證。

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>將資料匯入 Azure 檔案分享時保留目錄和檔案 ACL

Azure 檔案支援將資料複製到 Azure 檔案共用時保留目錄或檔等級 ACL。 您可以使用 Azure 檔案同步或公共檔案行動工具集將目錄或檔中的 ACL 複製到 Azure 檔案共用。 例如,可以將[robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)與`/copy:s`標誌一起複製數據以及 ACL 複製到 Azure 檔案共用。 預設情況下,ACL 將保留,您不需要在存儲帳戶上啟用基於身份的身份驗證來保留 ACL。

## <a name="pricing"></a>定價
存儲帳戶上通過 SMB 啟用基於身份的身份驗證不會收取額外的服務費。 有關定價的詳細資訊,請參閱[Azure 檔案定價](https://azure.microsoft.com/pricing/details/storage/files/)和[Azure AD 網域服務定價](https://azure.microsoft.com/pricing/details/active-directory-ds/)。

## <a name="next-steps"></a>後續步驟
有關透過 SMB 進行 Azure 檔和基於識別身分驗證的詳細資訊,請參閱以下資源:

- [規劃 Azure 檔案服務部署](storage-files-planning.md)
- [以 SMB 為 Azure 檔案分享啟用本地端目錄網域服務身份驗證](storage-files-identity-auth-active-directory-enable.md)
- [在 Azure 檔案開啟 Azure 的目錄網域服務認證](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [常見問題集](storage-files-faq.md)
