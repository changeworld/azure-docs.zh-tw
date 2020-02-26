---
title: 總覽-以身分識別為基礎的授權 Azure 檔案儲存體
description: Azure 檔案儲存體可透過 Azure Active Directory Domain Services （AD DS）和 Active Directory，在 SMB （伺服器訊息區）上支援以身分識別為基礎的驗證。 然後，您加入網域的 Windows 虛擬機器 (VM) 可以使用 Azure AD 認證存取 Azure 檔案共用。
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 673bf3be59d72b2cc62b9f92af353fee235c5ddc
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598811"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>瞭解 SMB 存取的 Azure 檔案儲存體身分識別型驗證支援
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

若要瞭解如何啟用 Azure 檔案共用的 AD 驗證，請參閱[針對 azure 檔案共用啟用透過 SMB 的 Active Directory 驗證](storage-files-identity-auth-active-directory-enable.md)。

若要瞭解如何為 Azure 檔案共用啟用 Azure AD DS 驗證，請參閱[在 Azure 檔案儲存體上啟用 Azure Active Directory Domain Services 驗證](storage-files-identity-auth-active-directory-domain-service-enable.md)。

## <a name="glossary"></a>詞彙 
瞭解 Azure 檔案共用的透過 SMB Azure AD 網域服務驗證的一些重要詞彙，會很有説明：

-   **Kerberos 驗證**

    Kerberos 是用來驗證的使用者或主機身分識別的驗證通訊協定。 如需有關 Kerberos 的詳細資訊，請參閱 [Kerberos 驗證概觀](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)。

-  **伺服器訊息區 (SMB) 通訊協定**

    SMB 是業界標準網路檔案共用通訊協定。 SMB 亦稱為 Common Internet File System 或 CIFS。 如需有關 SMB 的詳細資訊，請參閱 [Microsoft SMB 通訊協定和 CIFS 通訊協定概觀](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)。

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) 是 Microsoft 的多重租用戶雲端式目錄和身分識別管理服務。 Azure AD 將核心目錄服務、應用程式存取管理及身分識別保護結合到單個解決方案。 Azure AD 可讓已加入網域的 Windows 虛擬機器（Vm）使用您的 Azure AD 認證來存取 Azure 檔案共用。 如需詳細資訊，請參閱[什麼是 Azure Active Directory？](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services （Azure AD DS）**

    Azure AD Domain Services （GA）提供受控網域服務，例如加入網域、群組原則、LDAP 和 Kerberos/NTLM 驗證。 這些服候與 Windows Server Active Directory 完全相容。 如需詳細資訊，請參閱 [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md)。

- **Active Directory Domain Services （AD DS，也稱為 AD）**

    Active directory （AD）（預覽）提供儲存目錄資料的方法，讓網路使用者和系統管理員可以使用。 安全性透過登入驗證和對目錄中物件的存取控制，與 Active Directory 整合。 透過單一網路登入，系統管理員可以管理整個網路的目錄資料和組織，而已授權的網路使用者可以存取網路上任何位置的資源。 AD 通常會由內部部署中的企業採用，並使用 AD 認證做為存取控制的身分識別。 如需詳細資訊，請參閱[Active Directory Domain Services 總覽](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。

-   **Azure 角色型存取控制 (RBAC)**

    Azure 角色型存取控制 (RBAC) 可以對 Azure 進行更細緻的存取權管理。 使用 RBAC，您可以對使用者授權執行其執行工作所需的最少權限，以便管理對資源的存取權。 如需 RBAC 的詳細資訊，請參閱[什麼是 Azure 中的角色型存取控制（RBAC）？](../../role-based-access-control/overview.md)。

## <a name="common-use-cases"></a>一般使用案例

在 Azure 檔案儲存體上，以身分識別為基礎的驗證和支援對 Windows Acl 最適用于下列使用案例：

### <a name="replace-on-premises-file-servers"></a>取代內部部署檔案伺服器

淘汰和取代散佈的內部部署檔案伺服器，是每個企業在其 IT 現代化旅程中遇到的常見問題。 當您可以將資料移轉至 Azure 檔案儲存體時，使用 AD （預覽）驗證的 Azure 檔案共用最適合於此處。 完整的遷移可讓您利用高可用性和擴充性的優勢，同時將用戶端的變更降到最低，特別是複雜的 AD 網域基礎結構。 它可為使用者提供順暢的遷移體驗，讓他們可以使用現有已加入網域的電腦，以相同的認證繼續存取其資料。

### <a name="lift-and-shift-applications-to-azure"></a>將應用程式隨即轉移至 Azure

當您將應用程式「隨即轉移」到雲端時，您會想要為您的資料保留相同的驗證模型。 當我們將身分識別型存取控制體驗延伸到 Azure 檔案共用時，就不需要將應用程式變更為新式驗證方法，並加快雲端採用的速度。 Azure 檔案共用可讓您選擇整合 Azure AD DS （GA）或 AD （預覽）以進行驗證。 如果您的計畫是100% 雲端原生，並將管理雲端基礎結構的工作降到最低，Azure AD DS 會更適合做為完全受控的網域服務。 如果您需要與 AD DS （GA）功能的完全相容性，您可能會想要在 Vm 上以自我裝載網域控制站的方式，考慮將 AD 環境擴充至雲端。 不論是哪一種方式，我們都提供彈性來選擇符合您商務需求的網域服務。

### <a name="backup-and-disaster-recovery-dr"></a>備份與嚴重損壞修復（DR）

如果您要將主要檔案存放在內部部署環境，Azure 檔案共用可以做為備份或 DR 的理想儲存體，以改善商務持續性。 您可以使用 Azure 檔案共用，從現有的檔案伺服器備份資料，同時保留 Windows Dacl。 針對 DR 案例，您可以設定驗證選項，以在容錯移轉時支援適當的存取控制強制執行。

## <a name="supported-scenarios"></a>支援的案例

下表摘要說明 Azure AD DS （GA）和 AD （預覽）支援的 Azure 檔案共用驗證案例。 我們建議您選取用戶端環境所採用的網域服務，以便與 Azure 檔案儲存體整合。 如果您已在內部部署或 Azure 上設定 AD （預覽），而您的裝置已加入 AD，則您應該選擇利用 AD （預覽）來進行 Azure 檔案共用驗證。 同樣地，如果您已經採用 Azure AD DS （GA），您應該將其用於 Azure 檔案共用驗證。


|Azure AD DS （GA）驗證  |AD （預覽）驗證  |
|---------|---------|
|已加入網域的 Azure AD DS Windows 機器可以透過 SMB 使用 Azure AD 認證來存取 Azure 檔案共用。     |已加入 AD 網域的 Windows 機器可以使用已同步處理至 SMB Azure AD 的 AD 認證來存取 Azure 檔案共用。         |

### <a name="unsupported-scenarios"></a>不支援的情節

- Azure AD DS （GA）和 AD （預覽）驗證不支援對電腦帳戶進行驗證。 您可以考慮改為使用服務登入帳戶。
- Azure AD DS （GA）驗證不支援對已加入 Azure AD 雲端裝置進行驗證。

## <a name="advantages-of-identity-based-authentication"></a>以身分識別為基礎之驗證的優點
Azure 檔案儲存體以身分識別為基礎的驗證提供了數個使用共用金鑰驗證的優點：

-   **使用 AD 和 Azure AD DS，將傳統身分識別型檔案共用存取體驗延伸到雲端**  
    如果您打算將應用程式「隨即轉移」到雲端，以 Azure 檔案共用取代傳統的檔案伺服器，您可能會想要讓應用程式使用 AD 或 Azure AD 認證進行驗證，以存取檔案資料。 Azure 檔案儲存體支援使用 AD 或 Azure AD 認證，從 AD 或 Azure AD DS 加入網域的 Vm 存取 SMB 的 Azure 檔案共用。

-   **在 Azure 檔案共用上強制執行細微存取控制**  
    您可以在共用、目錄或檔案層級，將許可權授與特定的身分識別。 例如，假設您有多個小組使用同一個 Azure 檔案共用來共同作業某個專案。 您可以授權所有小組存取非敏感目錄，同時將包含敏感財務資料之目錄的存取權僅授與財務小組。 

-   **備份 Windows Acl （也稱為 NTFS）和您的資料**  
    您可以使用 Azure 檔案共用來備份現有的內部部署檔案共用。 當您透過 SMB 將檔案共用備份至 Azure 檔案共用時，Azure 檔案儲存體會保留您的 Acl 以及您的資料。

## <a name="how-it-works"></a>運作方式
Azure 檔案共用支援 kerberos 驗證，以便與 Azure AD DS （GA）或 AD （預覽）整合。 您必須先設定網域環境，才可以在 Azure 檔案共用上啟用驗證。 針對 Azure AD DS （GA）驗證，您應該啟用 Azure AD Domain Services，並加入您打算用來存取檔案資料之 Vm 的網域。 已加入網域的 VM 必須位於與您的 Azure AD Domain Services 相同的虛擬網路（VNET）中。 同樣地，針對 AD （預覽）驗證，您必須設定您的 Active Directory 網域控制站，並將您的電腦或 Vm 加入網域。

當與在 VM 上執行之應用程式相關聯的身分識別嘗試存取 Azure 檔案共用中的資料時，會將要求傳送至 Azure AD Domain Services 來驗證身分識別。 如果驗證成功，Azure AD Domain Services 就會傳回 Kerberos 權杖。 應用程式會傳送包含 Kerberos 權杖的要求，而 Azure 檔案共用會使用該權杖來授權要求。 Azure 檔案共用只會接收權杖，而且不會保存 Azure AD 認證。 AD 驗證的運作方式類似，其中 AD 會提供 Kerberos 權杖。

![顯示「透過 SMB 的 Azure AD 驗證」圖的螢幕擷取畫面](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>啟用以身分識別為基礎的驗證

您可以在新的和現有的儲存體帳戶上，使用適用于 Azure 檔案共用的 Azure AD DS （GA）或 AD （預覽）來啟用身分識別型驗證。 只有一個網域服務可用於儲存體帳戶上的檔案存取驗證，這適用于帳戶中的所有檔案共用。 在[Azure 檔案儲存體 Azure Active Directory Domain Services](storage-files-identity-auth-active-directory-domain-service-enable.md)我們的文章中針對 AD （預覽）使用 Azure AD DS （GA）設定檔案共用的詳細逐步指導方針，請參閱針對[Azure 檔案共用啟用透過 SMB 的 Active Directory 驗證](storage-files-identity-auth-active-directory-enable.md)。

### <a name="configure-share-level-permissions-for-azure-files"></a>設定 Azure 檔案共用層級權限

一旦啟用 Azure AD DS （GA）或 AD （預覽）驗證，您就可以使用內建的 RBAC 角色或設定 Azure AD 身分識別的自訂角色，並將存取權限指派給儲存體帳戶中的任何檔案共用。 指派的許可權可讓授與的身分識別僅取得共用的存取權，而不是任何其他內容，甚至是根目錄。 您仍然需要分別設定目錄或 Azure 檔案共用的檔案層級許可權。

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>設定 Azure 檔案儲存體的目錄或檔案層級許可權

Azure 檔案共用會在目錄和檔案層級（包括根目錄）強制執行標準 Windows 檔案許可權。 SMB 和 REST 都支援目錄或檔案層級許可權的設定。 從您的 VM 掛接目標檔案共用，並使用 Windows File Explorer、Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)或[Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6)命令來設定許可權。

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>為超級使用者權限使用儲存體帳戶金鑰

擁有儲存體帳戶金鑰的使用者可以使用超級使用者權限來存取 Azure 檔案共用。 超級使用者權限會略過所有存取控制限制。

> [!IMPORTANT]
> 我們建議的安全性最佳作法是避免共用您的儲存體帳戶金鑰，並盡可能運用以身分識別為基礎的驗證。

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>將資料匯入至 Azure 檔案共用時保留目錄和檔案 Acl

Azure 檔案儲存體在將資料複製到 Azure 檔案共用時，支援保留目錄或檔案層級的 Acl。 您可以使用 Azure 檔案同步或一般檔案移動工具組，將目錄或檔案上的 Acl 複製到 Azure 檔案共用。 例如，您可以搭配使用[robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)與 `/copy:s` 旗標，將資料和 acl 複製到 Azure 檔案共用。 預設會保留 Acl，您不需要在儲存體帳戶上啟用以身分識別為基礎的驗證來保留 Acl。

## <a name="pricing"></a>價格
不需要額外的服務費用，即可在您的儲存體帳戶上啟用透過 SMB 的身分識別型驗證。 如需有關定價的詳細資訊，請參閱[Azure 檔案儲存體定價](https://azure.microsoft.com/pricing/details/storage/files/)和[Azure AD Domain Services 定價](https://azure.microsoft.com/pricing/details/active-directory-ds/)頁面（如果您要尋找 AAD DS 資訊）。

## <a name="next-steps"></a>後續步驟
如需有關透過 SMB 進行 Azure 檔案儲存體和以身分識別為基礎之驗證的詳細資訊，請參閱下列資源：

- [規劃 Azure 檔案部署](storage-files-planning.md)
- [針對 Azure 檔案共用啟用透過 SMB 進行 Active Directory 驗證](storage-files-identity-auth-active-directory-enable.md)
- [啟用 Azure 檔案儲存體上的 Azure Active Directory Domain Services 驗證](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [常見問題集](storage-files-faq.md)
