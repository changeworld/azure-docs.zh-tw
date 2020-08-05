---
title: 總覽-內部部署 AD DS 對 Azure 檔案共用的驗證
description: 深入瞭解 Active Directory Domain Services (AD DS Azure 檔案共用) 驗證。 本文將探討支援案例、可用性，並說明 AD DS 與 Azure active directory 之間的許可權運作方式。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 07/12/2020
ms.author: rogarana
ms.openlocfilehash: d7aab7f5db6256d2f2525ff4a58c10ff5aa517f7
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553167"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>總覽-適用于 Azure 檔案共用的內部部署 Active Directory Domain Services 透過 SMB 進行驗證

[Azure 檔案儲存體](storage-files-introduction.md)  透過兩種類型的網域服務，在伺服器訊息區上支援以身分識別為基礎的驗證 (SMB) ：內部部署 Active Directory Domain Services (AD DS) 和 Azure Active Directory Domain Services (DS Azure AD。我們強烈建議您參閱[其運作方式一節](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview#how-it-works)，以選取正確的網域服務進行驗證。 安裝程式會有所不同，取決於您選擇的網域服務。 這一系列的文章著重在啟用和設定內部部署 AD DS，以使用 Azure 檔案共用進行驗證。

如果您是 Azure 檔案共用的新手，建議您先閱讀我們的[規劃指南](storage-files-planning.md)，再閱讀下列系列文章。

## <a name="supported-scenarios-and-restrictions"></a>支援的案例和限制

- 用於 Azure 檔案儲存體內部部署 AD DS 驗證的 AD DS 身分識別必須同步處理到 Azure AD。 密碼雜湊同步處理是選擇性的。 
- 支援由 Azure 檔案同步管理的 Azure 檔案共用。
- 支援使用具有 RC4-HMAC 加密的 AD 進行 Kerberos 驗證。 尚不支援 AES Kerberos 加密。
- 支援單一登入體驗。
- 只有在比 Windows 7 或 Windows Server 2008 R2 更新的作業系統版本上執行的用戶端才支援。
- 僅針對儲存體帳戶註冊所在的 AD 樹系支援。 根據預設，您只能使用來自單一樹系的 AD DS 認證來存取 Azure 檔案共用。 如果您需要從不同的樹系存取 Azure 檔案共用，請確定您已設定適當的樹系信任，如需詳細資訊，請參閱[常見問題](storage-files-faq.md#ad-ds--azure-ad-ds-authentication)。
- 不支援對 AD DS 中建立的電腦帳戶進行驗證。 

當您啟用透過 SMB 的 Azure 檔案共用 AD DS 時，已加入 AD DS 的電腦可以使用現有的 AD DS 認證來掛接 Azure 檔案共用。 這項功能可以使用裝載于內部內部部署電腦或裝載于 Azure 中的 AD DS 環境來啟用。

> [!NOTE]
> 為了協助您為一些常見的使用案例設定 Azure 檔案儲存體 AD 驗證，我們發佈了兩段影片，其中包含下列案例的逐步指導方針：
> - [將內部部署檔案伺服器取代為 Azure 檔案儲存體 (包括檔案和 AD 驗證的私人連結上的安裝程式) ](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
> - [使用 Azure 檔案儲存體做為 Windows 虛擬桌面 (的設定檔容器，包括在 AD 驗證和 FsLogix 設定上安裝程式) ](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="prerequisites"></a>必要條件 

為 Azure 檔案共用啟用 AD DS 驗證之前，請確定您已完成下列必要條件： 

- 選取或建立您的[AD DS 環境](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)，並將[其同步至](../../active-directory/hybrid/how-to-connect-install-roadmap.md)Azure AD Connect 的 Azure AD。 

    您可以在新的或現有的內部部署 AD DS 環境上啟用此功能。 用於存取的身分識別必須同步至 Azure AD。 Azure AD 租使用者和您要存取的檔案共用必須與相同的訂用帳戶相關聯。

- 網域-將內部部署機器或 Azure VM 加入內部部署 AD DS。 如需如何加入網域的相關資訊，請參閱將[電腦加入網域](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)。

    如果您的電腦未加入網域的 AD DS，如果您的電腦具有 AD 網域控制站的連線，您仍然可以利用 AD 認證來進行驗證。

- 選取或建立 Azure 儲存體帳戶。  為了達到最佳效能，我們建議您將儲存體帳戶部署在您打算用來存取共用的用戶端所在的相同區域中。 然後，使用您的儲存體帳戶金鑰[掛接 Azure 檔案共用](storage-how-to-use-files-windows.md)。 使用儲存體帳戶金鑰進行掛接會驗證連線能力。

    請確定尚未設定包含檔案共用的儲存體帳戶進行 Azure AD DS 驗證。 如果 Azure 檔案儲存體在儲存體帳戶上啟用 Azure AD DS 驗證，則必須先將其停用，才能變更為使用內部部署 AD DS。 這表示在 Azure AD DS 環境中設定的現有 Acl 必須重新設定，才能強制執行適當的許可權。

    如果您在連接到 Azure 檔案儲存體時遇到問題，請參閱[我們針對 Windows 上的 Azure 檔案儲存體掛接錯誤所發行的疑難排解工具](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)。 我們也會提供[指引](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access)，以解決埠445遭到封鎖的案例。 

- 啟用並設定 Azure 檔案共用的 AD DS 驗證之前，請先進行任何相關的網路設定。 如需詳細資訊，請參閱[Azure 檔案儲存體網路功能考慮](storage-files-networking-overview.md)。

## <a name="regional-availability"></a>區域可用性

[所有 Azure 公用和 Gov 區域](https://azure.microsoft.com/global-infrastructure/locations/)皆可使用 AD DS 的 Azure 檔案儲存體驗證。

## <a name="overview"></a>概觀

如果您打算在檔案共用上啟用任何網路設定，建議您先閱讀[網路考慮](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview)一文並完成相關設定，然後再啟用 AD DS 驗證。

針對您的 Azure 檔案共用啟用 AD DS 驗證，可讓您使用內部內部部署 AD DS 認證向 Azure 檔案共用進行驗證。 此外，它可讓您更有效地管理您的許可權，以允許細微的存取控制。 若要這麼做，您必須將內部內部部署 AD DS 的身分識別，與 AD connect Azure AD 同步處理。 您可以使用同步處理到 Azure AD 的身分識別來控制共用層級存取，同時使用內部內部部署 AD DS 認證來管理檔案/共用層級存取。

接下來，請遵循下列步驟來設定 AD DS Authentication 的 Azure 檔案儲存體： 

1. [第一部：在您的儲存體帳戶上啟用 AD DS 驗證](storage-files-identity-ad-ds-enable.md)

1. [第二部分：將共用的存取權限指派給 Azure AD 身分識別 (與目標 AD 身分識別同步的使用者、群組或服務主體) ](storage-files-identity-ad-ds-assign-permissions.md)

1. [第三部分：透過 SMB 設定目錄和檔案的 Windows Acl](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [第四部分：將 Azure 檔案共用掛接至已加入您的 AD DS 的 VM](storage-files-identity-ad-ds-mount-file-share.md)

1. [在 AD DS 中更新儲存體帳戶身分識別的密碼](storage-files-identity-ad-ds-update-password.md)

下圖說明針對 Azure 檔案共用啟用透過 SMB 進行 Azure AD 驗證的端對端工作流程。 

![檔案 AD workflow 圖表](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

用來存取 Azure 檔案共用的身分識別必須同步至 Azure AD，才能透過[角色型存取控制 (RBAC) ](../../role-based-access-control/overview.md)模型來強制執行共用層級的檔案許可權。 在從現有檔案伺服器上執行的檔案/目錄上， [Windows 樣式的 dacl](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN)將會保留並強制執行。 這可與您的企業 AD DS 環境緊密整合。 當您使用 Azure 檔案共用來取代內部部署檔案伺服器時，現有的使用者可以透過單一登入體驗，從其目前的用戶端存取 Azure 檔案共用，而不需要變更使用中的認證。  

## <a name="next-steps"></a>後續步驟

若要為您的 Azure 檔案共用啟用內部部署 AD DS 驗證，請繼續進行下一篇文章：

[第一部：為您的帳戶啟用 AD DS 驗證](storage-files-identity-ad-ds-enable.md)
