---
title: 使用 Azure 活動目錄 - Azure 資料庫用於後格雷SQL - 單個伺服器
description: 瞭解如何設置 Azure 活動目錄 （AAD） 以便使用 Azure 資料庫進行 PostgreSQL - 單伺服器身份驗證
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a9f12849525daeea69ece6e81077446f062e8889
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384393"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>使用 Azure 活動目錄使用 PostgreSQL 進行身份驗證

本文將介紹如何使用 Azure 資料庫為 PostgreSQL 配置 Azure 活動目錄訪問以及如何使用 Azure AD 權杖進行連接的步驟。

> [!IMPORTANT]
> 用於 PostgreSQL 的 Azure 資料庫的 Azure AD 身份驗證當前處於公共預覽版中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="setting-the-azure-ad-admin-user"></a>設置 Azure AD 管理員使用者

只有 Azure AD 管理員使用者可以創建/啟用使用者才能進行基於 Azure AD 的身份驗證。 要創建和 Azure AD 管理員使用者，請按照以下步驟操作

1. 在 Azure 門戶中，選擇要為 Azure AD 啟用的 PostgreSQL 的 Azure 資料庫實例。
2. 在"設置"下，選擇活動目錄管理員：

![設置 Azure 廣告管理員][2]

3. 在客戶租戶中選擇有效的 Azure AD 使用者，作為 Azure AD 管理員。

> [!IMPORTANT]
> 設置管理員時，新使用者將添加到具有完全管理員許可權的 PostgreSQL 伺服器的 Azure 資料庫中。 Azure 資料庫中的 Azure Ad 管理員使用者將具有 該角色`azure_ad_admin`。

每個 PostgreSQL 伺服器只能創建一個 Azure AD 管理員，而另一個伺服器的選擇將覆蓋為伺服器配置的現有 Azure AD 管理員。 您可以指定 Azure AD 組，而不是單個使用者，以具有多個管理員。 請注意，您隨後將使用組名稱登錄以進行管理。

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>在 Azure 資料庫中為 PostgreSQL 創建 Azure AD 使用者

要將 Azure AD 使用者添加到用於 PostgreSQL 資料庫的 Azure 資料庫，請在連接後執行以下步驟（請參閱後面有關如何連接的部分）：

1. 首先，確保 Azure AD`<user>@yourtenant.onmicrosoft.com`使用者是 Azure AD 租戶中的有效使用者。
2. 以 Azure AD 管理員使用者身份登錄到 Azure 資料庫以獲取 PostgreSQL 實例。
3. 在`<user>@yourtenant.onmicrosoft.com`Azure 資料庫中為 PostgreSQL 創建角色。
4. 成為`<user>@yourtenant.onmicrosoft.com`角色的成員azure_ad_user。 這只能提供給 Azure AD 使用者。

**例子：**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> 通過 Azure AD 對使用者進行身份驗證不會授予使用者訪問 PostgreSQL 資料庫 Azure 資料庫中的物件的任何許可權。 您必須手動授予使用者所需的許可權。

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>在 Azure 資料庫中為 PostgreSQL 創建 Azure AD 組

要啟用 Azure AD 組以訪問資料庫，請使用與使用者相同的機制，而是指定組名稱：

**例子：**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

登錄時，組成員將使用其個人訪問權杖，但使用指定為使用者名的組名稱進行簽名。

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>使用 Azure AD 連接到 Azure 資料庫以進行 PostgreSQL

以下高級關係圖總結了使用 Azure 資料庫進行 Azure AD 身份驗證的工作流，

![身份驗證流][1]

我們設計了 Azure AD 集成，以便使用常見的 PostgreSQL 工具（如 psql），這些工具不了解 Azure AD，並且僅在連接到 PostgreSQL 時支援指定使用者名和密碼。 我們將 Azure AD 權杖作為密碼傳遞，如上圖所示。

我們目前已經測試了以下客戶：

- psql 命令列（利用 PGPASSWORD 變數傳遞權杖，見下文）
- Azure 資料工作室（使用 PostgreSQL 擴展）
- 其他基於 libpq 的用戶端（例如通用應用程式框架和 ORM）

> [!NOTE]
> 請注意，當前不支援將 Azure AD 權杖與 pgAdmin 一起使用，因為它對密碼的硬編碼限制為 256 個字元（權杖超過）。

以下是使用者/應用程式需要執行以下描述的 Azure AD 身份驗證的步驟：

### <a name="step-1-authenticate-with-azure-ad"></a>第 1 步：使用 Azure AD 進行身份驗證

確保安裝了 Azure [CLI。](/cli/azure/install-azure-cli)

調用 Azure CLI 工具以使用 Azure AD 進行身份驗證。 它要求您提供 Azure AD 使用者 ID 和密碼。

```azurecli-interactive
az login
```

此命令將啟動到 Azure AD 身份驗證頁的瀏覽器視窗。

> [!NOTE]
> 您還可以使用 Azure 雲外殼執行這些步驟。
> 請注意，在 Azure 雲外殼中檢索 Azure AD 訪問權杖時，需要顯式調用`az login`並重新登錄（在帶有代碼的單獨視窗中）。 該登錄後，`get-access-token`命令將按預期工作。

### <a name="step-2-retrieve-azure-ad-access-token"></a>第 2 步：檢索 Azure AD 訪問權杖

調用 Azure CLI 工具以從步驟 1 獲取 Azure AD 身份驗證使用者的訪問權杖，以訪問 PostgreSQL 的 Azure 資料庫。

示例（公共雲）：

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

上述資源值必須完全按照所示指定。 對於其他雲，可以使用以下情況來查看資源值：

```azurecli-interactive
az cloud show
```

對於 Azure CLI 版本 2.0.71 及更高版本，可以在以下更方便版本中為所有雲指定該命令：

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

身份驗證成功後，Azure AD 將返回訪問權杖：

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

權杖是一個 Base 64 字串，用於編碼有關經過身份驗證的使用者的所有資訊，該字串針對 PostgreSQL 服務的 Azure 資料庫。

> [!NOTE]
> 訪問權杖的有效期在 5 分鐘到 60 分鐘之間。 我們建議您在啟動 PostgreSQL 的 Azure 資料庫登錄之前獲取訪問權杖。

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>第 3 步：使用權杖作為密碼使用 PostgreSQL 登錄

連接時，您需要使用訪問權杖作為 PostgreSQL 使用者密碼。

使用`psql`命令列用戶端時，需要通過`PGPASSWORD`環境變數傳遞訪問權杖，因為訪問權杖超過可以直接接受的`psql`密碼長度：

視窗示例：

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Linux/macOS 示例：

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

現在，您可以像通常一樣啟動與 Azure 資料庫的連接， 以便進行 PostgreSQL：

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

現在，使用 Azure AD 身份驗證對 PostgreSQL 伺服器進行身份驗證。

## <a name="token-validation"></a>權杖驗證

Azure 資料庫中的 Azure AD 身份驗證用於 PostgreSQL 可確保使用者存在於 PostgreSQL 伺服器中，並且它通過驗證權杖的內容來檢查權杖的有效性。 執行以下權杖驗證步驟：

- 權杖由 Azure AD 簽名，未被篡改
- 權杖由 Azure AD 頒發給與伺服器關聯的租戶
- 權杖尚未過期
- 權杖用於 PostgreSQL 資源的 Azure 資料庫（而不是另一個 Azure 資源）

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>將現有 PostgreSQL 使用者遷移到基於 Azure AD 的身份驗證

您可以為現有使用者啟用 Azure AD 身份驗證。 有兩種情況需要考慮：

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>案例 1：PostgreSQL 使用者名與 Azure AD 使用者主體名稱匹配

如果現有使用者已匹配 Azure AD 使用者名，則可以將`azure_ad_user`角色授予他們，以便啟用它們進行 Azure AD 身份驗證：

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

他們現在將能夠使用 Azure AD 憑據登錄，而不是使用以前配置的 PostgreSQL 使用者密碼。

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>案例 2：PostgreSQL 使用者名不同于 Azure AD 使用者主體名稱

如果 PostgreSQL 使用者在 Azure AD 中不存在或具有其他使用者名，則可以使用 Azure AD 組作為此 PostgreSQL 使用者進行身份驗證。 通過創建名稱與 PostgreSQL 使用者匹配的 Azure AD 組，然後將角色azure_ad_user授予現有 PostgreSQL 使用者，可以將現有用於 PostgreSQL 使用者的現有 Azure 資料庫移轉到 Azure AD：

```sql
GRANT azure_ad_user TO "DBReadUser";
```

這假定您在 Azure AD 中創建了一個組"DBReadUser"。 屬於該組的使用者現在可以作為此使用者登錄到資料庫。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 資料庫查看 Azure 活動目錄身份驗證的總體概念，用於 PostgreSQL - 單個伺服器](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
