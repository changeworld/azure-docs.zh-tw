---
title: 概述 - Azure 檔基於標識的授權
description: Azure 檔通過 Azure 活動目錄域服務 （AD DS） 和活動目錄支援通過 SMB（伺服器訊息區）進行基於身份的身份驗證。 然後，您加入網域的 Windows 虛擬機器 (VM) 可以使用 Azure AD 認證存取 Azure 檔案共用。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 737cdfaddca3a5f7532620bdafd86149e4d61f9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061060"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>基於 Azure 檔的基於身份的 SMB 訪問的身份驗證支援的概述
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

要瞭解如何為 Azure 檔共用啟用 AD 身份驗證，請參閱[通過 SMB 為 Azure 檔共用啟用活動目錄身份驗證](storage-files-identity-auth-active-directory-enable.md)。

要瞭解如何為 Azure 檔共用啟用 Azure AD DS 身份驗證，請參閱[在 Azure 檔上啟用 Azure 活動目錄域服務身份驗證](storage-files-identity-auth-active-directory-domain-service-enable.md)。

## <a name="glossary"></a>字彙表 
瞭解與 Azure 檔共用的 SMB 上通過 SMB 進行 Azure AD 域服務身份驗證的一些關鍵術語很有説明：

-   **Kerberos 身份驗證**

    Kerberos 是用來驗證的使用者或主機身分識別的驗證通訊協定。 如需有關 Kerberos 的詳細資訊，請參閱 [Kerberos 驗證概觀](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)。

-  **伺服器訊息區 (SMB) 通訊協定**

    SMB 是業界標準網路檔案共用通訊協定。 SMB 亦稱為 Common Internet File System 或 CIFS。 如需有關 SMB 的詳細資訊，請參閱 [Microsoft SMB 通訊協定和 CIFS 通訊協定概觀](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)。

-   **Azure 活動目錄（Azure AD）**

    Azure 活動目錄 （Azure AD） 是 Microsoft 的多租戶基於雲的目錄和標識管理服務。 Azure AD 將核心目錄服務、應用程式存取管理及身分識別保護結合到單個解決方案。 Azure AD 使加入域的 Windows 虛擬機器 （VM） 能夠使用 Azure AD 憑據訪問 Azure 檔共用。 有關詳細資訊，請參閱什麼是[Azure 活動目錄？](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD 域服務（Azure AD DS）**

    Azure AD 域服務 （GA） 提供託管域服務，如域聯接、群組原則、LDAP 和 Kerberos/NTLM 身份驗證。 這些服候與 Windows Server Active Directory 完全相容。 如需詳細資訊，請參閱 [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md)。

- **活動目錄域服務（AD DS，也稱為 AD）**

    活動目錄 （AD） （預覽） 提供了存儲目錄資料的方法，同時使網路使用者和管理員可以使用它。 通過登錄身份驗證和對目錄中物件的存取控制，安全性與 Active Directory 集成。 通過單個網路登入，管理員可以管理整個網路中的目錄資料和組織，並且經過授權的網路使用者可以訪問網路上的任意位置的資源。 AD 通常被本地企業採用，並使用 AD 憑據作為存取控制標識。 有關詳細資訊，請參閱[活動目錄域服務概述](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。

-   **Azure 角色型存取控制 (RBAC)**

    Azure 角色型存取控制 (RBAC) 可以對 Azure 進行更細緻的存取權管理。 使用 RBAC，您可以對使用者授權執行其執行工作所需的最少權限，以便管理對資源的存取權。 有關 RBAC 的詳細資訊，請參閱[Azure 中什麼是基於角色的存取控制 （RBAC）？](../../role-based-access-control/overview.md)

## <a name="common-use-cases"></a>一般使用案例

Azure 檔上的基於標識的身份驗證和支援最適合使用以下用例：

### <a name="replace-on-premises-file-servers"></a>替換本地檔案伺服器

棄用和替換分散的本地檔案伺服器是每個企業在 IT 現代化旅程中遇到的常見問題。 當可以將資料移轉到 Azure 檔時，與 AD（預覽）身份驗證共用是最適合的。 完全遷移將允許您利用高可用性和可擴充性優勢，同時最大限度地減少用戶端更改，尤其是複雜的 AD 域基礎結構。 它為最終使用者提供了無縫的遷移體驗，因此他們可以繼續使用其現有的域聯接電腦使用相同的憑據訪問其資料。

### <a name="lift-and-shift-applications-to-azure"></a>提升應用程式並將其移至 Azure

當您將應用程式"提升並移位"到雲時，您希望為數據保留相同的身份驗證模型。 隨著我們將基於身份的存取控制體驗擴展到 Azure 檔共用，無需將應用程式更改為現代身份驗證方法並加快雲採用。 Azure 檔共用提供了與 Azure AD DS （GA） 或 AD（預覽）集成以進行身份驗證的選項。 如果計畫是 100% 雲原生，並最大限度地減少管理雲基礎架構的努力，Azure AD DS 將更適合作為完全託管的域服務。 如果您需要與 AD DS （GA） 功能完全相容，您可能需要考慮通過在 VM 上自託管網域控制站將 AD 環境擴展到雲。 無論哪種方式，我們都提供靈活性，選擇適合您業務需求的域服務。

### <a name="backup-and-disaster-recovery-dr"></a>備份和災害復原 （DR）

如果要將主檔案存儲保留在本地，Azure 檔共用可用作備份或 DR 的理想存儲，以提高業務連續性。 您可以使用 Azure 檔共用從現有檔案伺服器備份資料，同時保留 Windows DACL。 對於 DR 方案，您可以配置身份驗證選項，以支援容錯移轉時的正確存取控制實施。

## <a name="supported-scenarios"></a>支援的案例

下表總結了支援的 Azure 檔共用 Azure AD DS （GA） 和 AD（預覽）的身份驗證方案。 我們建議選擇為用戶端環境採用的域服務，以便與 Azure 檔集成。 如果已在本地或 Azure 上設置 AD（預覽），設備已加入到 AD 的域，則應選擇利用 AD（預覽）進行 Azure 檔共用身份驗證。 同樣，如果您已經採用 Azure AD DS （GA），則應將它用於 Azure 檔共用身份驗證。


|Azure AD DS （GA） 身份驗證  |AD（預覽）身份驗證  |
|---------|---------|
|Azure AD DS 域聯接的 Windows 電腦可以通過 SMB 訪問具有 Azure AD 憑據的 Azure 檔共用。     |加入 AD 域的 Windows 電腦可以使用通過 SMB 同步到 Azure AD 的 AD 憑據訪問 Azure 檔共用。         |

### <a name="unsupported-scenarios"></a>不支援的情節

- Azure AD DS （GA） 和 AD（預覽）身份驗證不支援針對電腦帳戶的身份驗證。 您可以考慮改用服務登錄帳戶。
- Azure AD DS （GA） 身份驗證不支援針對 Azure AD 雲聯接設備的身份驗證。

## <a name="advantages-of-identity-based-authentication"></a>基於身份的身份驗證的優勢
與使用共用金鑰身份驗證不同，基於標識的 Azure 檔身份驗證提供了幾個好處：

-   **通過 AD 和 Azure AD DS 將傳統的基於標識的檔共用訪問體驗擴展到雲**  
    如果計畫將應用程式"提升"並移至雲，用 Azure 檔共用替換傳統檔案伺服器，則可能需要應用程式使用 AD 或 Azure AD 憑據進行身份驗證以訪問檔資料。 Azure 檔支援使用 AD 或 Azure AD 憑據從 AD 或 Azure AD DS 域聯接的 VM 通過 SMB 訪問 Azure 檔共用。

-   **在 Azure 檔案共用上強制執行細微存取控制**  
    您可以在共用、目錄或檔級別向特定標識授予許可權。 例如，假設您有多個小組使用同一個 Azure 檔案共用來共同作業某個專案。 您可以授權所有小組存取非敏感目錄，同時將包含敏感財務資料之目錄的存取權僅授與財務小組。 

-   **備份 Windows ACL（也稱為 NTFS）以及您的資料**  
    可以使用 Azure 檔共用備份現有的本地檔共用。 當您通過 SMB 將檔共用備份到 Azure 檔共用時，Azure 檔會保留 ACL 和資料。

## <a name="how-it-works"></a>運作方式
Azure 檔共用支援 kerberos 身份驗證，以便與 Azure AD DS （GA） 或 AD（預覽）集成。 在 Azure 檔共用上啟用身份驗證之前，必須先設置域環境。 對於 Azure AD DS （GA） 身份驗證，應啟用 Azure AD 域服務和域加入計畫從中訪問檔資料的 VM。 加入域的 VM 必須與 Azure AD 域服務駐留在同一虛擬網路 （VNET）。 同樣，對於 AD（預覽）身份驗證，您需要設置 Active Directory 網域控制站，並加入電腦或 VM。

當與在 VM 上運行的應用程式關聯的標識嘗試訪問 Azure 檔共用中的資料時，請求將發送到 Azure AD 域服務以驗證該標識。 如果驗證成功，Azure AD Domain Services 就會傳回 Kerberos 權杖。 應用程式發送包含 Kerberos 權杖的請求，Azure 檔共用使用該權杖授權請求。 Azure 檔共用僅接收權杖，不保留 Azure AD 憑據。 AD 身份驗證的工作方式類似，其中 AD 提供 Kerberos 權杖。

![顯示「透過 SMB 的 Azure AD 驗證」圖的螢幕擷取畫面](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>啟用基於身份的身份驗證

您可以使用 Azure AD DS （GA） 或 AD（預覽）為新存儲帳戶和現有存儲帳戶上的 Azure 檔共用啟用基於身份的身份驗證。 存儲帳戶上只能使用一個域服務來進行檔訪問身份驗證，該身份驗證適用于帳戶中的所有檔共用。 在本文中[，在"啟用 Azure 檔上的 Azure 活動目錄域服務身份驗證](storage-files-identity-auth-active-directory-domain-service-enable.md)"一文中，有關使用 Azure AD DS （GA） 設置檔共用以進行身份驗證的詳細分步指南，以及另一篇文章中的 AD（預覽）指南，[即為 Azure 檔共用啟用通過 SMB 進行活動目錄身份驗證](storage-files-identity-auth-active-directory-enable.md)。

### <a name="configure-share-level-permissions-for-azure-files"></a>設定 Azure 檔案共用層級權限

啟用 Azure AD DS （GA） 或 AD（預覽）身份驗證後，可以使用內置 RBAC 角色或為 Azure AD 標識配置自訂角色，並將存取權限分配給存儲帳戶中的任何檔共用。 分配的許可權允許授予的標識僅訪問共用，沒有其他內容，甚至無法訪問根目錄。 您仍然需要單獨配置 Azure 檔共用的目錄或檔級許可權。

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>為 Azure 檔配置目錄或檔級許可權

Azure 檔共用在目錄和檔級別（包括根目錄）強制實施標準的 Windows 檔許可權。 SMB 和 REST 都支援目錄或檔級許可權的配置。 從 VM 裝載目的檔案共用，並使用 Windows 檔資源管理器、Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)或[Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6)命令配置許可權。

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>為超級使用者權限使用儲存體帳戶金鑰

擁有存儲帳戶金鑰的使用者可以訪問具有超級使用者許可權的 Azure 檔共用。 超級使用者許可權繞過所有存取控制限制。

> [!IMPORTANT]
> 我們推薦的安全最佳實踐是避免共用存儲帳戶金鑰，並盡可能利用基於身份的身份驗證。

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>將資料導入 Azure 檔共用時保留目錄和檔 ACL

Azure 檔支援將資料複製到 Azure 檔共用時保留目錄或檔級別 ACL。 您可以使用 Azure 檔同步或公共檔移動工具集將目錄或檔中的 ACL 複製到 Azure 檔共用。 例如，可以將[robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)與`/copy:s`標誌一起複製資料以及 ACL 複製到 Azure 檔共用。 預設情況下，ACL 將保留，您不需要在存儲帳戶上啟用基於身份的身份驗證來保留 ACL。

## <a name="pricing"></a>定價
存儲帳戶上通過 SMB 啟用基於身份的身份驗證不會收取額外的服務費。 有關定價的詳細資訊，請參閱[Azure 檔定價](https://azure.microsoft.com/pricing/details/storage/files/)和[Azure AD 域服務定價](https://azure.microsoft.com/pricing/details/active-directory-ds/)頁，如果您要查找 AAD DS 資訊。

## <a name="next-steps"></a>後續步驟
有關通過 SMB 進行 Azure 檔和基於標識的身份驗證的詳細資訊，請參閱以下資源：

- [規劃 Azure 檔案服務部署](storage-files-planning.md)
- [通過 SMB 為 Azure 檔共用啟用活動目錄身份驗證](storage-files-identity-auth-active-directory-enable.md)
- [在 Azure 檔上啟用 Azure 活動目錄域服務身份驗證](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [常見問題集](storage-files-faq.md)
