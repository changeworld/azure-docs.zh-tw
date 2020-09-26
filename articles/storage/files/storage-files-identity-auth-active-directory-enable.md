---
title: 總覽-Azure 檔案共用的內部部署 AD DS 驗證
description: 瞭解 Active Directory Domain Services (AD DS) Azure 檔案共用的驗證。 本文將探討支援案例和可用性，並說明您的 AD DS 和 Azure active directory 之間的許可權如何運作。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: f64cad731998fefb2cfa694314e42f0dfb629eb4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322065"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>總覽-Azure 檔案共用的內部部署 Active Directory Domain Services 透過 SMB 進行驗證

[Azure 檔案儲存體](storage-files-introduction.md)  透過兩種類型的網域服務，支援透過伺服器訊息區進行以身分識別為基礎的驗證 (SMB) ：內部部署 Active Directory Domain Services (AD DS) 和 Azure Active Directory Domain Services (DS Azure AD。 強烈建議您複習 [它的運作方式一節](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview#how-it-works) ，以選取正確的網域服務進行驗證。 根據您選擇的網域服務而定，安裝程式會有所不同。 這一系列的文章著重在啟用和設定內部部署 AD DS，以透過 Azure 檔案共用進行驗證。

如果您不熟悉 Azure 檔案共用，建議您閱讀我們的 [規劃指南](storage-files-planning.md) ，再閱讀下列系列文章。

## <a name="supported-scenarios-and-restrictions"></a>支援的案例和限制

- 用於 Azure 檔案儲存體內部部署 AD DS 驗證的 AD DS 識別必須同步至 Azure AD。 密碼雜湊同步處理是選擇性的。 
- 支援受 Azure 檔案同步管理的 Azure 檔案共用。
- 支援使用具有 RC4-HMAC 和 [AES 256 加密](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption)的 AD 進行 Kerberos 驗證。 目前尚不支援 AES 128 Kerberos 加密。
- 支援單一登入體驗。
- 僅支援在作業系統版本比 Windows 7 或 Windows Server 2008 R2 更新的用戶端上。
- 僅針對儲存體帳戶註冊所在的 AD 樹系支援。 根據預設，您只能使用來自單一樹系的 AD DS 認證來存取 Azure 檔案共用。 如果您需要從不同的樹系存取 Azure 檔案共用，請確定您已設定適當的樹系信任，請參閱 [常見問題](storage-files-faq.md#ad-ds--azure-ad-ds-authentication) 以取得詳細資料。
- 不支援針對 AD DS 中建立的電腦帳戶進行驗證。
- 不支援針對網路檔案系統 (NFS) 檔案共用進行驗證。

當您透過 SMB 啟用 Azure 檔案共用的 AD DS 時，您 AD DS 加入的機器可以使用現有的 AD DS 認證來掛接 Azure 檔案共用。 這項功能可透過裝載于內部內部部署電腦或裝載于 Azure 的 AD DS 環境來啟用。

> [!NOTE]
> 為了協助您針對一些常見的使用案例設定 Azure 檔案儲存體 AD 驗證，我們針對下列案例發佈兩段影片，並提供逐步指引：
> - [使用 Azure 檔案儲存體 (來取代內部部署檔案伺服器，包括檔案和 AD 驗證的私用連結上的安裝程式) ](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
> - [使用 Azure 檔案儲存體作為 Windows 虛擬桌面的設定檔容器 (包括 AD 驗證和 FsLogix 設定上的設定) ](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="prerequisites"></a>Prerequisites 

針對 Azure 檔案共用啟用 AD DS authentication 之前，請確定您已完成下列必要條件： 

- 選取或建立您的 [AD DS 環境](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) ，並 [將其同步處理至](../../active-directory/hybrid/how-to-connect-install-roadmap.md) Azure AD Connect 的 Azure AD。 

    您可以在新的或現有的內部部署 AD DS 環境中啟用此功能。 用於存取的身分識別必須同步至 Azure AD。 Azure AD 租使用者和您要存取的檔案共用必須與相同的訂用帳戶相關聯。

- 將內部部署機器或 Azure VM 網域加入內部部署 AD DS。 如需有關如何加入網域的詳細資訊，請參閱將 [電腦加入網域](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)。

    如果您的電腦未加入 AD DS 的網域，如果您的電腦有 AD 網域控制站的連線，您仍然可以利用 AD 認證進行驗證。

- 選取或建立 Azure 儲存體帳戶。  為了達到最佳效能，建議您將儲存體帳戶部署在您打算用來存取共用的用戶端所在的相同區域中。 然後，使用您的儲存體帳戶金鑰來 [掛接 Azure 檔案共用](storage-how-to-use-files-windows.md) 。 使用儲存體帳戶金鑰來掛接會驗證連線能力。

    請確定尚未針對 Azure AD DS 驗證設定包含您檔案共用的儲存體帳戶。 如果已在儲存體帳戶上啟用 Azure 檔案儲存體的 Azure AD DS 驗證，則必須先將它停用，才能變更為使用內部部署 AD DS。 這表示在 Azure AD DS 環境中設定的現有 Acl 將需要重新設定，才能強制執行適當的許可權。

    如果您在連接到 Azure 檔案儲存體時遇到問題，請參閱 [我們針對 Windows 上的 Azure 檔案儲存體掛接錯誤所發佈的疑難排解工具](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)。 我們也會在埠445遭到封鎖時，提供解決案例的 [指引](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) 。 

- 在啟用和設定 Azure 檔案共用的 AD DS authentication 之前，請先進行任何相關的網路設定。 如需詳細資訊，請參閱 [Azure 檔案儲存體網路功能考慮](storage-files-networking-overview.md) 。

## <a name="regional-availability"></a>區域可用性

使用 AD DS 的 Azure 檔案儲存體驗證可在 [所有 Azure 公用和 Gov 區域](https://azure.microsoft.com/global-infrastructure/locations/)中使用。

## <a name="overview"></a>概觀

如果您打算在檔案共用上啟用任何網路設定，建議您先閱讀 [網路考慮](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) 一文，並完成相關的設定，再啟用 AD DS authentication。

為您的 Azure 檔案共用啟用 AD DS authentication，可讓您使用內部部署 AD DS 認證向 Azure 檔案共用進行驗證。 此外，它可讓您更妥善地管理您的許可權，以允許細微的存取控制。 這麼做需要從內部內部部署 AD DS 將身分識別同步處理至與 AD connect Azure AD。 您可以使用同步至 Azure AD 的身分識別來控制共用層級存取，同時使用內部部署 AD DS 認證來管理檔案/共用層級存取。

接下來，請遵循下列步驟來設定 AD DS Authentication 的 Azure 檔案儲存體： 

1. [第一個部分：在您的儲存體帳戶上啟用 AD DS authentication](storage-files-identity-ad-ds-enable.md)

1. [第二部分：將共用的存取權限指派給 Azure AD 身分識別， (與目標 AD 身分識別同步的使用者、群組或服務主體) ](storage-files-identity-ad-ds-assign-permissions.md)

1. [第三部分：透過 SMB 設定目錄和檔案的 Windows Acl](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [第四部分：將 Azure 檔案共用掛接至已加入您 AD DS 的 VM](storage-files-identity-ad-ds-mount-file-share.md)

1. [在 AD DS 中更新儲存體帳戶身分識別的密碼](storage-files-identity-ad-ds-update-password.md)

下圖說明針對 Azure 檔案共用啟用透過 SMB 進行 Azure AD 驗證的端對端工作流程。 

![Files AD workflow 圖表](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

用來存取 Azure 檔案共用的身分識別必須同步至 Azure AD，才能透過 [azure 角色型存取控制 (AZURE RBAC) ](../../role-based-access-control/overview.md) 模型，來強制執行共用層級的檔案許可權。 在從現有檔案伺服器執行的檔案/目錄上， [Windows 樣式的 dacl](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN)將會保留並強制執行。 這提供與您企業 AD DS 環境的緊密整合。 當您以 Azure 檔案共用取代內部內部部署檔案伺服器時，現有的使用者就可以透過單一登入體驗，從其目前的用戶端存取 Azure 檔案共用，而不需要變更使用中的認證。  

## <a name="next-steps"></a>後續步驟

若要啟用 Azure 檔案共用的內部部署 AD DS 驗證，請繼續閱讀下一篇文章：

[第一個部分：為您的帳戶啟用 AD DS authentication](storage-files-identity-ad-ds-enable.md)