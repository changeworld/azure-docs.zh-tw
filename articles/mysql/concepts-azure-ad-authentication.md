---
title: Active Directory 驗證 - 適用於 MySQL 的 Azure 資料庫
description: 了解 Azure Active Directory 的概念以向適用於 MySQL 的 Azure 資料庫進行驗證
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 12316abd4a738d54e01f88873498e4b299d6053d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85556374"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>使用 Azure Active Directory 向 MySQL 進行驗證

Microsoft Azure Active Directory (Azure AD) 驗證是使用 Azure AD 中所定義的身分識別來連線到「適用於 MySQL 的 Azure 資料庫」的機制。
透過 Azure AD 驗證，您可以在集中的位置管理資料庫使用者的身分識別和其他 Microsoft 服務，從而簡化權限管理。

使用 Azure AD 的優點包括：

- 以一致的方式跨 Azure 服務來驗證使用者
- 在單一位置管理密碼原則和密碼輪替
- Azure Active Directory 可支援多種形式的驗證，從而避免儲存密碼的需要
- 客戶可以使用外部 (Azure AD) 群組來管理資料庫權限。
- Azure AD 驗證會使用 MySQL 資料庫使用者，在資料庫層級驗證身分
- 可針對連線到「適用於 MySQL 的 Azure 資料庫」的應用程式支援權杖型驗證

若要設定及使用 Azure Active Directory 驗證，請使用下列程序：

1. 視需要建立並填入 Azure Active Directory 與使用者身分識別。
2. (選擇性) 和目前與 Azure 訂用帳戶相關聯的 Active Directory 產生關聯或加以變更。
3. 為「適用於 MySQL 的 Azure 資料庫」伺服器建立 Azure AD 系統管理員。
4. 在對應至 Azure AD 身分識別的資料庫中建立資料庫使用者。
5. 藉由擷取 Azure AD 身分識別的權杖並登入，來連線到您的資料庫。

> [!NOTE]
> 若要了解如何建立和填入 Azure AD，然後使用「適用於 MySQL 的 Azure 資料庫」設定 Azure AD，請參閱[使用「適用於 MySQL 的 Azure 資料庫」的 Azure AD 來進行設定和登入](howto-configure-sign-in-azure-ad-authentication.md)。

## <a name="architecture"></a>架構

下列高階圖表摘要說明如何搭配使用 Azure AD 驗證與「適用於 MySQL 的 Azure 資料庫」來啟用驗證。 箭頭表示通訊路徑。

![驗證流程][1]

## <a name="administrator-structure"></a>系統管理員結構

使用 Azure AD 驗證時，MySQL 伺服器會有兩個系統管理員帳戶：原始的 MySQL 系統管理員和 Azure AD 系統管理員。 只有以 Azure AD 帳戶為基礎的系統管理員可以在使用者資料庫中建立第一個 Azure AD 自主資料庫使用者。 Azure AD 系統管理員登入可以是 Azure AD 使用者或 Azure AD 群組。 當系統管理員是群組帳戶時，其可以供任何群組成員使用，而讓 MySQL 伺服器可以有多個 Azure AD 系統管理員。 以系統管理員的身分使用群組帳戶，可讓您集中新增和移除 Azure AD 中的群組成員，而不需要變更 MySQL 伺服器中的使用者或權限，藉以增強管理性。 一律只能設定一個 Azure AD 系統管理員 (使用者或群組)。

![系統管理員結構][2]

## <a name="permissions"></a>權限

若要建立可使用 Azure AD 進行驗證的新使用者，您必須是設計的 Azure AD 系統管理員。 若要指派這個使用者，您可以為特定的「適用於 MySQL 的 Azure 資料庫」伺服器設定 Azure AD 系統管理員帳戶。

若要建立新的 Azure AD 資料庫使用者，您必須以 Azure AD 系統管理員的身分進行連線。 [使用「適用於 MySQL 的 Azure 資料庫」的 Azure AD 來設定和登入](howto-configure-sign-in-azure-ad-authentication.md)中會有相關示範。

只有在為「適用於 MySQL 的 Azure 資料庫」建立 Azure AD 系統管理員後，才可以進行任何 Azure AD 驗證。 如果已從伺服器移除 Azure Active Directory 系統管理員，則先前建立的現有 Azure Active Directory 使用者便無法再使用其 Azure Active Directory 認證連線到資料庫。

## <a name="connecting-using-azure-ad-identities"></a>使用 Azure AD 身分識別連接

Azure Active Directory 驗證支援下列方法，使用 Azure AD 身分識別連接至資料庫：

- Azure Active Directory 密碼
- Azure Active Directory 整合式
- 包含 MFA 的 Active Directory 通用驗證
- 使用 Active Directory 應用程式的憑證或用戶端密碼
- [受控身分識別](howto-connect-with-managed-identity.md)

針對 Active Directory 進行驗證後就可以擷取權杖。 此權杖是您用來登入的密碼。

請注意，目前只有 Azure AD 的使用者角色才支援管理作業，例如新增使用者。

> [!NOTE]
> 如需如何使用 Active Directory 權杖來連線的詳細資訊，請參閱[使用「適用於 MySQL 的 Azure 資料庫」的 Azure AD 來進行設定和登入](howto-configure-sign-in-azure-ad-authentication.md)。

## <a name="additional-considerations"></a>其他考量

- Azure Active Directory 驗證僅適用于 MySQL 5.7 和更新版本。
- 任何時候都只能為「適用於 MySQL 的 Azure 資料庫」設定一個 Azure AD 系統管理員。
- 只有適用於 MySQL 的 Azure AD 系統管理員可以一開始就使用 Azure Active Directory 帳戶連線到「適用於 MySQL 的 Azure 資料庫」。 Active Directory 系統管理員可以設定後續的 Azure AD 資料庫使用者。
- 如果從 Azure AD 中刪除某個使用者，該使用者就無法再使用 Azure AD 進行驗證，因此將無法再取得該使用者的存取權杖。 在此情況下，雖然相符的使用者仍然會在資料庫中，但已無法使用該使用者來連線到伺服器。
> [!NOTE]
> 在權杖到期之前 (從權杖發出後最多 60 分鐘)，仍可使用已刪除的 Azure AD 使用者來登入。  如果您也從「適用於 MySQL 的 Azure 資料庫」中移除該使用者，則會立即撤銷此存取權。
- 如果 Azure AD 系統管理員已從伺服器中移除，伺服器就不會再與 Azure AD 租用戶相關聯，因此將會停用該伺服器的所有 Azure AD 登入。 從相同的租用戶新增 Azure AD 系統管理員，則會重新啟用 Azure AD 登入。
- 「適用於 MySQL 的 Azure 資料庫」會使用使用者的唯一 Azure AD 使用者識別碼來比對「適用於 MySQL 的 Azure 資料庫」使用者的存取權杖，而不是使用使用者名稱。 這表示如果在 Azure AD 中刪除 Azure AD 使用者，並以相同的名稱建立新的使用者，則「適用於 MySQL 的 Azure 資料庫」會將其視為是不同的使用者。 因此，如果從 Azure AD 刪除使用者，然後新增具有相同名稱的新使用者，則新使用者將無法與現有使用者連接。

## <a name="next-steps"></a>後續步驟

- 若要了解如何建立和填入 Azure AD，然後使用「適用於 MySQL 的 Azure 資料庫」設定 Azure AD，請參閱[使用「適用於 MySQL 的 Azure 資料庫」的 Azure AD 來進行設定和登入](howto-configure-sign-in-azure-ad-authentication.md)。
- 如需有關登入以及適用於 MySQL 的 Azure 資料庫的資料庫使用者的概述，請參閱[在適用於 MySQL 的 Azure 資料庫中建立使用者](howto-create-users.md)。

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
