---
title: Active Directory 驗證-適用於 MySQL 的 Azure 資料庫
description: 瞭解使用適用於 MySQL 的 Azure 資料庫驗證 Azure Active Directory 的概念
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 960536c3f80aa7870d6f2056d8e95cd1a4338dfe
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299017"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>使用 Azure Active Directory 來向 MySQL 進行驗證

Microsoft Azure Active Directory （Azure AD）驗證是使用 Azure AD 中定義的身分識別連接到適用於 MySQL 的 Azure 資料庫的機制。
透過 Azure AD authentication，您可以在集中位置管理資料庫使用者身分識別和其他 Microsoft 服務，以簡化版權管理。

> [!IMPORTANT]
> 適用於 MySQL 的 Azure 資料庫的 Azure AD 驗證目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 Azure AD 的優點包括：

- 以一致的方式跨 Azure 服務驗證使用者
- 在單一位置管理密碼原則和密碼輪替
- Azure Active Directory 支援多種形式的驗證，這可以避免儲存密碼的需要
- 客戶可以使用外部 (Azure AD) 群組來管理資料庫權限。
- Azure AD authentication 會使用 MySQL 資料庫使用者來驗證資料庫層級的身分識別
- 支援連接到適用於 MySQL 的 Azure 資料庫之應用程式的權杖型驗證

若要設定及使用 Azure Active Directory 驗證，請使用下列程式：

1. 視需要建立並填入 Azure Active Directory 的使用者身分識別。
2. 選擇性地建立或變更目前與您的 Azure 訂用帳戶相關聯的 Active Directory。
3. 建立適用於 MySQL 的 Azure 資料庫伺服器的 Azure AD 系統管理員。
4. 在您的資料庫中建立對應至 Azure AD 身分識別的資料庫使用者。
5. 藉由抓取 Azure AD 身分識別的權杖並登入，以連接到您的資料庫。

> [!NOTE]
> 若要瞭解如何建立和填入 Azure AD，然後使用適用於 MySQL 的 Azure 資料庫設定 Azure AD，請參閱[使用適用於 MySQL 的 Azure 資料庫的 Azure AD 設定和登入](howto-configure-sign-in-azure-ad-authentication.md)。

## <a name="architecture"></a>架構

下列高階圖表摘要說明使用 Azure AD 驗證與適用於 MySQL 的 Azure 資料庫，驗證的運作方式。 箭頭表示通訊路徑。

![驗證流程][1]

## <a name="administrator-structure"></a>系統管理員結構

使用 Azure AD 驗證時，MySQL 伺服器有兩個系統管理員帳戶：原始的 MySQL 系統管理員和 Azure AD 系統管理員。 只有以 Azure AD 帳戶為基礎的系統管理員可以在使用者資料庫中建立第一個 Azure AD 自主資料庫使用者。 Azure AD 系統管理員登入可以是 Azure AD 使用者或 Azure AD 群組。 當系統管理員是群組帳戶時，可以由任何群組成員使用，讓 MySQL 伺服器有多個 Azure AD 系統管理員。 以系統管理員身分使用群組帳戶，可讓您在 Azure AD 集中新增和移除群組成員，而不需要變更 MySQL 伺服器中的使用者或許可權，藉此增強管理能力。 一律只能設定一個 Azure AD 系統管理員 (使用者或群組)。

![系統管理員結構][2]

## <a name="permissions"></a>權限

若要建立可使用 Azure AD 進行驗證的新使用者，您必須是設計的 Azure AD 系統管理員。 此使用者是藉由設定特定適用於 MySQL 的 Azure 資料庫伺服器的 Azure AD 系統管理員帳戶來指派。

若要建立新的 Azure AD 資料庫使用者，您必須以 Azure AD 系統管理員的身分進行連接。 這會在[使用適用於 MySQL 的 Azure 資料庫的 Azure AD 進行設定和登](howto-configure-sign-in-azure-ad-authentication.md)入中示範。

只有在為適用於 MySQL 的 Azure 資料庫建立 Azure AD 系統管理員時，才可以進行任何 Azure AD 驗證。 如果 Azure Active Directory 系統管理員已從伺服器移除，則先前建立的現有 Azure Active Directory 使用者就無法再使用其 Azure Active Directory 認證連接到資料庫。

## <a name="connecting-using-azure-ad-identities"></a>使用 Azure AD 身分識別連接

Azure Active Directory 驗證支援下列方法，使用 Azure AD 身分識別連接至資料庫：

- Azure Active Directory 密碼
- Azure Active Directory 整合式
- 包含 MFA 的 Active Directory 通用驗證
- 使用 Active Directory 應用程式憑證或用戶端秘密

當您對 Active Directory 進行驗證之後，就可以取出權杖。 此權杖是您用來登入的密碼。

> [!NOTE]
> 如需如何使用 Active Directory token 進行連線的詳細資訊，請參閱[使用 Azure AD 設定和登入適用於 MySQL 的 Azure 資料庫](howto-configure-sign-in-azure-ad-authentication.md)。

## <a name="additional-considerations"></a>其他考量

- 一次只能為一部適用於 MySQL 的 Azure 資料庫伺服器設定一個 Azure AD 系統管理員。
- 只有 MySQL 的 Azure AD 系統管理員可以使用 Azure Active Directory 帳戶一開始連接到適用於 MySQL 的 Azure 資料庫。 Active Directory 系統管理員可以設定後續的 Azure AD 資料庫使用者。
- 如果從 Azure AD 刪除使用者，該使用者將無法再使用 Azure AD 進行驗證，因此將無法再取得該使用者的存取權杖。 在此情況下，雖然相符的使用者仍會在資料庫中，但無法使用該使用者連接到伺服器。
> [!NOTE]
> 使用已刪除之 Azure AD 使用者的登入，仍然可以在權杖到期之前完成（從權杖發出的時間最多60分鐘）。  如果您也將使用者從適用於 MySQL 的 Azure 資料庫移除，則會立即撤銷此存取權。
- 如果 Azure AD 系統管理員已從伺服器移除，伺服器就不會再與 Azure AD 的租使用者相關聯，因此將會停用伺服器的所有 Azure AD 登入。 從相同的租使用者新增新的 Azure AD 系統管理員，將會重新啟用 Azure AD 登入。
- 適用於 MySQL 的 Azure 資料庫會使用使用者的唯一 Azure AD 使用者識別碼來比對適用於 MySQL 的 Azure 資料庫使用者的存取權杖，而不是使用 username。 這表示如果在 Azure AD 中刪除 Azure AD 的使用者，並以相同的名稱建立新的使用者，適用於 MySQL 的 Azure 資料庫會考慮到不同的使用者。 因此，如果從 Azure AD 刪除使用者，然後新增具有相同名稱的新使用者，新的使用者將無法與現有的使用者連接。

## <a name="next-steps"></a>後續步驟

- 若要瞭解如何建立和填入 Azure AD，然後使用適用於 MySQL 的 Azure 資料庫設定 Azure AD，請參閱[使用適用於 MySQL 的 Azure 資料庫的 Azure AD 設定和登入](howto-configure-sign-in-azure-ad-authentication.md)。
- 如需登入的總覽，以及適用於 MySQL 的 Azure 資料庫的資料庫使用者，請參閱[在適用於 MySQL 的 Azure 資料庫中建立使用者](howto-create-users.md)。

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
