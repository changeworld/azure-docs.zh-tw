---
title: 授權資料作業
titleSuffix: Azure Storage
description: 瞭解授權存取 Azure 儲存體的不同方式，包括 Azure Active Directory、共用金鑰授權或共用存取簽章（SAS）。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 94af8dfc0171a5c27514b30968743f9788aee224
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588921"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>授權存取 Azure 儲存體中的資料

每次存取儲存體帳戶中的資料時，用戶端都會透過 HTTP/HTTPS 對 Azure 儲存體發出要求。 針對受保護資源所發出的每個要求都必須經過授權，服務才能確保用戶端具有所需的資料存取權限。

下表說明 Azure 儲存體提供的選項，可授權資源的存取權：

|  |共用金鑰（儲存體帳戶金鑰）  |共用存取簽章 (SAS)  |Azure Active Directory (Azure AD)  |Active Directory |匿名公用讀取權限  |
|---------|---------|---------|---------|---------|---------|
|Azure Blob     |[支援](/rest/api/storageservices/authorize-with-shared-key/)         |[支援](storage-sas-overview.md)         |[支援](storage-auth-aad.md)         |不支援|[支援](../blobs/storage-manage-access-to-resources.md)         |
|Azure 檔案儲存體（SMB）     |[支援](/rest/api/storageservices/authorize-with-shared-key/)         |不支援         |[僅支援 AAD 網域服務](../files/storage-files-active-directory-overview.md)         |[支援，認證必須同步至 Azure AD](../files/storage-files-active-directory-overview.md)|不支援         |
|Azure 檔案儲存體（REST）     |[支援](/rest/api/storageservices/authorize-with-shared-key/)         |[支援](storage-sas-overview.md)         |不支援         |不支援 |不支援         |
|Azure 佇列     |[支援](/rest/api/storageservices/authorize-with-shared-key/)         |[支援](storage-sas-overview.md)         |[支援](storage-auth-aad.md)         |不支援 | 不支援         |
|Azure 資料表     |[支援](/rest/api/storageservices/authorize-with-shared-key/)         |[支援](storage-sas-overview.md)         |不支援         |不支援| 不支援         |

以下簡要說明每個授權選項：

- 適用于 blob 和佇列的**Azure Active Directory （Azure AD）整合**。 Azure AD 提供了角色型存取控制 (RBAC)，供您更細微地控制用戶端對儲存體帳戶資源的存取。 如需有關 blob 和佇列 Azure AD 整合的詳細資訊，請參閱[使用 Azure Active Directory 授權存取 Azure blob 和佇列](storage-auth-aad.md)。

- 檔案的**Azure Active Directory Domain Services （AZURE AD DS）驗證**。 Azure 檔案儲存體透過 Azure AD DS 支援伺服器訊息區（SMB）的身分識別型授權。 您可以使用 RBAC 來精細控制用戶端對儲存體帳戶中 Azure 檔案儲存體資源的存取權。 如需有關使用網域服務 Azure 檔案儲存體驗證的詳細資訊，請參閱我們的[總覽](../files/storage-files-active-directory-overview.md)。

- 檔案的**Active Directory （AD）驗證**。 Azure 檔案儲存體支援透過 AD 以身分識別為基礎的 SMB 授權。 您的 AD 網域服務可以裝載于內部部署機器或 Azure Vm 中。 您可以從內部部署或 Azure 中已加入網域的電腦使用 AD 認證，來支援對檔案的 SMB 存取。 您可以使用 RBAC 來進行目錄/檔案層級許可權強制的共用層級存取控制和 NTFS Dacl。 如需有關使用網域服務 Azure 檔案儲存體驗證的詳細資訊，請參閱我們的[總覽](../files/storage-files-active-directory-overview.md)。

- **共用金鑰授權**，適用於 Blob、檔案、佇列和資料表。 使用共用金鑰的用戶端會在每個要求中，傳遞使用儲存體帳戶存取金鑰所簽署的標頭。 如需詳細資訊，請參閱[使用共用金鑰進行授權](/rest/api/storageservices/authorize-with-shared-key/)。
- **共用存取簽章**，適用於 Blob、檔案、佇列和資料表。 共用存取簽章 (SAS) 可提供儲存體帳戶資源的有限委派存取權。 對簽章的有效時間間隔或對其授與的權限新增條件約束，可讓您彈性管理存取權。 如需詳細資訊，請參閱[使用共用存取簽章 (SAS)](storage-sas-overview.md)。
- **匿名公開讀取存取**，適用於容器和 Blob。 不需要授權。 如需詳細資訊，請參閱 [管理對容器與 Blob 的匿名讀取權限](../blobs/storage-manage-access-to-resources.md)。  

根據預設，Azure 儲存體中的所有資源都會受到保護，並只供帳戶擁有者使用。 雖然您可以使用上述任何授權策略來對用戶端授與儲存體帳戶資源的存取權，但 Microsoft 建議您盡可能使用 Azure AD，以獲得最大安全性並方便您使用。

## <a name="next-steps"></a>後續步驟

- [使用 Azure Active Directory 授權存取 Azure blob 和佇列](storage-auth-aad.md)
- [使用共用金鑰進行授權](/rest/api/storageservices/authorize-with-shared-key/)
- [使用共用存取簽章（SAS）授與 Azure 儲存體資源的有限存取權](storage-sas-overview.md)
