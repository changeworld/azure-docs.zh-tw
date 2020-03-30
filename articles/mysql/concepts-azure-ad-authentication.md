---
title: 活動目錄身份驗證 - MySQL 的 Azure 資料庫
description: 瞭解 Azure 活動目錄的概念，以便使用 MySQL 的 Azure 資料庫進行身份驗證
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 960536c3f80aa7870d6f2056d8e95cd1a4338dfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299017"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>使用 Azure 活動目錄使用 MySQL 進行身份驗證

Microsoft Azure 活動目錄 （Azure AD） 身份驗證是使用 Azure AD 中定義的身份連接到 MySQL 的 Azure 資料庫的機制。
使用 Azure AD 身份驗證，您可以在中心位置管理資料庫使用者標識和其他 Microsoft 服務，從而簡化版權管理。

> [!IMPORTANT]
> MySQL Azure 資料庫的 Azure AD 身份驗證當前處於公共預覽版中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 Azure AD 的好處包括：

- 以統一的方式跨 Azure 服務對使用者進行身份驗證
- 在單個位置管理密碼原則和密碼輪換
- Azure 活動目錄支援的多種形式的身份驗證，無需存儲密碼
- 客戶可以使用外部 (Azure AD) 群組來管理資料庫權限。
- Azure AD 身份驗證使用 MySQL 資料庫使用者在資料庫級別驗證標識
- 支援為 MySQL 連接到 Azure 資料庫的應用程式提供基於權杖的身份驗證

要配置和使用 Azure 活動目錄身份驗證，請使用以下過程：

1. 根據需要使用使用者標識創建和填充 Azure 活動目錄。
2. 可以選擇關聯或更改當前與 Azure 訂閱關聯的活動目錄。
3. 為 MySQL 伺服器的 Azure 資料庫創建 Azure AD 管理員。
4. 在資料庫中創建映射到 Azure AD 標識的資料庫使用者。
5. 通過檢索 Azure AD 標識的權杖並登錄連接到資料庫。

> [!NOTE]
> 要瞭解如何創建和填充 Azure AD，然後使用 MySQL 的 Azure 資料庫配置 Azure AD，請參閱[為 MySQL 配置 Azure 資料庫並與 Azure AD 登錄](howto-configure-sign-in-azure-ad-authentication.md)。

## <a name="architecture"></a>架構

以下高級關係圖總結了身份驗證如何使用 Azure AD 身份驗證與 MySQL 的 Azure 資料庫一起使用。 箭頭表示通訊路徑。

![身份驗證流][1]

## <a name="administrator-structure"></a>系統管理員結構

使用 Azure AD 身份驗證時，MySQL 伺服器有兩個管理員帳戶;因此，使用 Azure AD 身份驗證時，MySQL 伺服器有兩個管理員帳戶。原始 MySQL 管理員和 Azure AD 管理員。 只有以 Azure AD 帳戶為基礎的系統管理員可以在使用者資料庫中建立第一個 Azure AD 自主資料庫使用者。 Azure AD 系統管理員登入可以是 Azure AD 使用者或 Azure AD 群組。 當管理員是群組帳戶時，任何組成員都可以使用它，為 MySQL 伺服器啟用多個 Azure AD 管理員。 使用群組帳戶作為管理員，允許您在 Azure AD 中集中添加和刪除組成員，而無需更改 MySQL 伺服器中的使用者或許可權，從而增強可管理性。 一律只能設定一個 Azure AD 系統管理員 (使用者或群組)。

![系統管理員結構][2]

## <a name="permissions"></a>權限

要創建新使用者，可以使用 Azure AD 進行身份驗證，您必須是設計的 Azure AD 管理員。 此使用者是通過為 MySQL 伺服器配置特定 Azure 資料庫的 Azure AD 管理員帳戶來分配的。

要創建新的 Azure AD 資料庫使用者，必須作為 Azure AD 管理員進行連接。 這表現在[為 MySQL 的 Azure 資料庫配置和登錄 Azure 資料庫](howto-configure-sign-in-azure-ad-authentication.md)。

僅當為 MySQL 為 Azure 資料庫創建 Azure AD 管理員時，才可能進行任何 Azure AD 身份驗證。 如果 Azure 活動目錄管理員從伺服器中刪除，則以前創建的現有 Azure 活動目錄使用者無法再使用其 Azure 活動目錄憑據連接到資料庫。

## <a name="connecting-using-azure-ad-identities"></a>使用 Azure AD 身分識別連接

Azure Active Directory 驗證支援下列方法，使用 Azure AD 身分識別連接至資料庫：

- Azure Active Directory 密碼
- Azure Active Directory 整合式
- 包含 MFA 的 Active Directory 通用驗證
- 使用活動目錄應用程式證書或用戶端機密

對活動目錄進行身份驗證後，然後檢索權杖。 此權杖是您登錄的密碼。

> [!NOTE]
> 有關如何使用活動目錄權杖進行連接的更多詳細資訊，請參閱[為 MySQL 配置 Azure 資料庫的 Azure AD](howto-configure-sign-in-azure-ad-authentication.md)並登錄。

## <a name="additional-considerations"></a>其他考量

- 任何時候都只能為 MySQL 伺服器配置一個 Azure 資料庫管理員。
- 只有 MySQL 的 Azure AD 管理員才能最初使用 Azure 活動目錄帳戶連接到 MySQL 的 Azure 資料庫。 Active Directory 系統管理員可以設定後續的 Azure AD 資料庫使用者。
- 如果從 Azure AD 中刪除使用者，該使用者將不再能夠使用 Azure AD 進行身份驗證，因此將無法再獲取該使用者的訪問權杖。 在這種情況下，儘管匹配的使用者仍將在資料庫中，但無法與該使用者連接到伺服器。
> [!NOTE]
> 使用已刪除的 Azure AD 使用者登錄仍可在權杖過期之前完成（權杖頒發最多 60 分鐘）。  如果同時從 Azure 資料庫中刪除 MySQL 的使用者，此存取權限將立即被吊銷。
- 如果 Azure AD 管理員從伺服器中刪除，則伺服器將不再與 Azure AD 租戶關聯，因此伺服器將禁用所有 Azure AD 登錄名。 從同一租戶添加新的 Azure AD 管理員將重新啟用 Azure AD 登錄名。
- MySQL 的 Azure 資料庫使用使用者的唯一 Azure AD 使用者 ID（而不是使用使用者名）匹配 MySQL 使用者對 Azure 資料庫的訪問權杖。 這意味著，如果在 Azure AD 中刪除 Azure AD 使用者，並且使用相同名稱創建新使用者，則 MySQL 的 Azure 資料庫將認為該使用者為其他使用者。 因此，如果從 Azure AD 中刪除使用者，然後添加了同名的新使用者，則新使用者將無法與現有使用者連接。

## <a name="next-steps"></a>後續步驟

- 要瞭解如何創建和填充 Azure AD，然後使用 MySQL 的 Azure 資料庫配置 Azure AD，請參閱[為 MySQL 配置 Azure 資料庫並與 Azure AD 登錄](howto-configure-sign-in-azure-ad-authentication.md)。
- 有關 MySQL 的登錄名和資料庫使用者的概述，請參閱[為 MySQL 在 Azure 資料庫中創建使用者](howto-create-users.md)。

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
