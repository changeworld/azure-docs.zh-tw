---
title: 使用 Azure 的目錄 - Azure 資料庫用於後格雷SQL - 單一伺服器
description: 瞭解如何設定 Azure 活動目錄 (AAD) 以便使用 Azure 資料庫進行 PostgreSQL - 單伺服器認證
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f5588503825281f407ddbbc2c1c57cd94a9c7ee6
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804702"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>使用 Azure 活動目錄使用 PostgreSQL 進行身分驗證

本文將介紹如何使用 Azure 資料庫為 PostgreSQL 配置 Azure 活動目錄存取以及如何使用 Azure AD 權杖進行連接的步驟。

> [!IMPORTANT]
> 用於 PostgreSQL 的 Azure 資料庫的 Azure AD 身份驗證當前處於公共預覽版中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="setting-the-azure-ad-admin-user"></a>設定 Azure AD 管理員使用者

只有 Azure AD 管理員使用者可以創建/啟用使用者進行基於 Azure AD 的身份驗證。 我們建議不要將 Azure AD 管理員用於常規資料庫操作,因為它具有提升的用戶許可權(例如,創建 B)。

要設定 Azure AD 管理員(可以使用使用者或群組),請按照以下步驟操作

1. 在 Azure 門戶中,選擇要為 Azure AD 啟用的 PostgreSQL 的 Azure 資料庫實例。
2. 在「設定」下,選擇活動目錄管理員:

![設定 Azure 廣告管理員][2]

3. 在客戶租戶中選擇有效的 Azure AD 用戶,作為 Azure AD 管理員。

> [!IMPORTANT]
> 設置管理員時,新使用者將添加到具有完全管理員許可權的 PostgreSQL 伺服器的 Azure 資料庫中。 Azure 資料庫中的 Azure Ad 管理員使用者`azure_ad_admin`將具有 該角色 。

每個 PostgreSQL 伺服器只能建立 Azure AD 管理員,而另一個伺服器的選擇將覆蓋為伺服器配置的現有 Azure AD 管理員。 您可以指定 Azure AD 組,而不是單個使用者,以具有多個管理員。 請注意,您隨後將使用組名稱登錄以進行管理。

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>使用 Azure AD 連線到 Azure 資料庫以進行 PostgreSQL

以下高級關係圖總結了使用 Azure 資料庫進行 Azure AD 身份驗證的工作流,

![驗證串流][1]

我們設計了 Azure AD 整合,以便使用常見的 PostgreSQL 工具(如 psql),這些工具不瞭解 Azure AD,並且僅在連接到 PostgreSQL 時支援指定使用者名和密碼。 我們將 Azure AD 權杖作為密碼傳遞,如上圖所示。

我們目前已經測試了以下客戶:

- psql 指令列 (利用 PGPASSWORD 變數傳遞權杖,見下文 )
- Azure 資料工作室(使用 PostgreSQL 擴充)
- 其他基於 libpq 用戶端(例如通用應用程式框架和 ORM)

> [!NOTE]
> 請注意,當前不支援將 Azure AD 令牌與 pgAdmin 一起使用,因為它對密碼的硬編碼限制為 256 個字元(令牌超過)。

以下是使用者/應用程式需要執行以下描述的 Azure AD 身份驗證的步驟:

### <a name="step-1-authenticate-with-azure-ad"></a>第一步:使用 Azure AD 進行身份驗證

確保安裝了 Azure [CLI。](/cli/azure/install-azure-cli)

呼叫 Azure CLI 工具以使用 Azure AD 進行身份驗證。 它要求您提供 Azure AD 使用者 ID 和密碼。

```azurecli-interactive
az login
```

此命令將啟動到 Azure AD 身份驗證頁的瀏覽器視窗。

> [!NOTE]
> 您還可以使用 Azure 雲外殼執行這些步驟。
> 請注意,在 Azure 雲外殼中檢索 Azure AD 訪問權杖時,`az login`需要顯式調用 並重新登錄(在帶有代碼的單獨視窗中)。 該登錄後,`get-access-token`命令將按預期工作。

### <a name="step-2-retrieve-azure-ad-access-token"></a>第二步:檢索 Azure AD 存取權杖

呼叫 Azure CLI 工具以從步驟 1 取得 Azure AD 身份驗證使用者的訪問權杖,以造訪 PostgreSQL 的 Azure 資料庫。

範例(公共雲):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

上述資源值必須完全按照所示指定。 對於其他雲,可以使用以下情況來查看資源值:

```azurecli-interactive
az cloud show
```

對於 Azure CLI 版本 2.0.71 及更高版本,可以在以下更方便版本中為所有雲指定該命令:

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

認證成功後,Azure AD 將傳回存取權杖:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

令牌是一個 Base 64 字串,用於編碼有關經過身份驗證的使用者的所有資訊,該字串針對 PostgreSQL 服務的 Azure 資料庫。

> [!NOTE]
> 訪問權杖的有效期在5分鐘到60分鐘之間。 我們建議您在啟動 PostgreSQL 的 Azure 資料庫登錄之前獲取訪問權杖。

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>第三步:使用權杖作為密碼使用 PostgreSQL 登入

連接時,您需要使用訪問權杖作為 PostgreSQL 使用者密碼。

使用`psql`命令列客戶端時,`PGPASSWORD`需要透過環境變數傳遞存取權杖,因為存取權杖超過可以`psql`直接接受的 密碼長度:

視窗範例:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Linux/macOS 範例:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

現在,您可以像通常一樣啟動與 Azure 資料庫的連線, 以便進行 PostgreSQL:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

現在,使用 Azure AD 身份驗證對 PostgreSQL 伺服器進行身份驗證。

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>在 Azure 資料庫中為 PostgreSQL 建立 Azure AD 使用者

要將 Azure AD 使用者新增到用於 PostgreSQL 資料庫的 Azure 資料庫,請在連接後執行以下步驟(請參考後面有關如何連線的部分):

1. 首先,確保 Azure`<user>@yourtenant.onmicrosoft.com`AD 使用者是 Azure AD 租戶中的有效使用者。
2. 以 Azure AD 管理員使用者身份登錄到 Azure 資料庫以獲取 PostgreSQL 實例。
3. 在`<user>@yourtenant.onmicrosoft.com`Azure 資料庫中為 PostgreSQL 創建角色。
4. 成為`<user>@yourtenant.onmicrosoft.com`角色的成員azure_ad_user。 這只能提供給 Azure AD 使用者。

**範例:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> 通過 Azure AD 對使用者進行身份驗證不會授予使用者訪問 PostgreSQL 資料庫 Azure 資料庫中的物件的任何許可權。 您必須手動授予使用者所需的許可權。

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>在 Azure 資料庫中為 PostgreSQL 建立 Azure AD 群組

要啟用 Azure AD 組以存取資料庫,請使用與使用者相同的機制,而是指定群組名稱:

**範例:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

登錄時,組成員將使用其個人訪問權杖,但使用指定為使用者名的組名稱進行簽名。

## <a name="token-validation"></a>權杖驗證

Azure 資料庫中的 Azure AD 認證用於 PostgreSQL 可確保使用者存在於 PostgreSQL 伺服器中,並且它透過驗證權碼的內容來檢查權杖的有效性。 執行以下令牌驗證步驟:

- 權杖由 Azure AD 簽名,未被篡改
- 權杖由 Azure AD 發行給與伺服器關聯的租戶
- 權杖尚未過期
- 權杖用於 PostgreSQL 資源的 Azure 資料庫(而不是另一個 Azure 資源)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>將現有 PostgreSQL 使用者移轉到基於 Azure AD 的身份驗證

您可以為現有使用者啟用 Azure AD 身份驗證。 有兩種情況需要考慮:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>案例 1:PostgreSQL 使用者名稱與 Azure AD 使用者主體名稱符合

如果現有使用者已匹配 Azure AD 使用者名,`azure_ad_user`則可以將 角色授予他們,以便啟用它們進行 Azure AD 身份驗證:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

他們現在將能夠使用 Azure AD 認證登錄,而不是使用以前配置的 PostgreSQL 使用者密碼。

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>案例 2:PostgreSQL 使用者名稱不同於 Azure AD 使用者主體名稱

如果 PostgreSQL 使用者在 Azure AD 中不存在或具有其他使用者名,則可以使用 Azure AD 組作為此 PostgreSQL 使用者進行身份驗證。 透過建立名稱與 PostgreSQL 使用者符合的 Azure AD 組,然後將角色azure_ad_user授予現有 PostgreSQL 使用者,可以將現有用於 PostgreSQL 使用者的現有 Azure 資料庫遷移到 Azure AD:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

這假定您在 Azure AD 中創建了一個組「DBReadUser」。。 屬於該組的用戶現在可以作為此使用者登錄到資料庫。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 資料庫檢視 Azure 的目錄身份驗證的概念,用於 PostgreSQL - 單一伺服器](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
