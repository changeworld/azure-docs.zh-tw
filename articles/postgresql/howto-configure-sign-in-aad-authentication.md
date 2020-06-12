---
title: 使用 Azure Active Directory - 適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器
description: 了解如何設定 Azure Active Directory (AAD) 以向適用於 PostgreSQL 的 Azure 資料庫進行驗證 - 單一伺服器
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 91435c2c5ca825793988e002c1ab9f6caacf2b17
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652561"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>使用 Azure Active Directory 向 PostgreSQL 進行驗證

本文將逐步引導您完成如何使用適用於 PostgreSQL 的 Azure 資料庫來設定 Azure Active Directory 存取的步驟，以及如何使用 Azure AD 權杖來連線的步驟。

## <a name="setting-the-azure-ad-admin-user"></a>設定 Azure AD 管理使用者

只有 Azure AD 管理使用者可以建立/啟用 Azure AD 型驗證的使用者。 建議您不要使用 Azure AD 系統管理員來進行一般資料庫作業，因為其具有更高的使用者權限 (例如 CREATEDB)。

若要設定 Azure AD 系統管理員 (您可以使用使用者或群組)，請遵循下列步驟

1. 在 Azure 入口網站中，選取您想要為 Azure AD 啟用的「適用於 PostgreSQL 的 Azure 資料庫」執行個體。
2. 在 [設定] 下，選取 [Active Directory 管理員]：

![設定 Azure AD 系統管理員][2]

3. 在客戶租用戶中選取有效的 Azure AD 使用者，以擔任 Azure AD 系統管理員。

> [!IMPORTANT]
> 在設定系統管理員時，會將具有完整系統管理員權限的新使用者新增至適用於 PostgreSQL 的 Azure 資料庫伺服器。 適用於 PostgreSQL 的 Azure 資料庫中的 Azure AD 管理使用者會擁有 `azure_ad_admin` 角色。

每個於 PostgreSQL 伺服器只能建立一個 Azure AD 系統管理員，選取另一位系統管理員將會覆寫為伺服器設定的現有 Azure AD 管理員。 您可以指定 Azure AD 群組 (而非個別使用者) 以擁有多名系統管理員。 請注意，您接著會使用群組名稱來登入，以進行系統管理。

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>使用 Azure AD 來連線到適用於 PostgreSQL 的 Azure 資料庫

下列高階圖表摘要說明搭配使用 Azure AD 驗證與「適用於 PostgreSQL 的 Azure 資料庫」的工作流程：

![驗證流程][1]

我們將 Azure AD 整合設計為可使用常見的 PostgreSQL 工具，例如 psql，Azure AD 無法感知此工具，而且此工具只支援在連線到 PostgreSQL 時指定使用者名稱和密碼。 我們會以密碼的形式傳遞 Azure AD 權杖，如上圖所示。

我們目前已測試過下列用戶端：

- psql 命令列 (利用 PGPASSWORD 變數來傳遞權杖，請參閱下文)
- Azure Data Studio (使用 PostgreSQL 擴充功能)
- 其他 libpq 型用戶端 (例如常見的應用程式架構和 ORM)

> [!NOTE]
> 請注意，目前不支援搭配 pgAdmin 使用 Azure AD 權杖，因為其密碼具有 256 個字元的硬式編碼限制 (權杖會超過此限制)。

使用者/應用程式為了使用 Azure AD 進行驗證所必須執行的步驟如下所述：

### <a name="step-1-authenticate-with-azure-ad"></a>步驟 1:使用 Azure AD 進行驗證

請確定您已[安裝 Azure CLI](/cli/azure/install-azure-cli)。

叫用 Azure CLI 工具，以向 Azure AD 進行驗證。 您需要向 Azure AD 提供使用者識別碼和密碼。

```azurecli-interactive
az login
```

此命令會啟動瀏覽器視窗並顯示 Azure AD 驗證頁面。

> [!NOTE]
> 您也可以使用 Azure Cloud Shell 來執行這些步驟。
> 請注意，在 Azure Cloud Shell 中擷取 Azure AD 存取權杖時，您必須明確地呼叫 `az login` 並重新登入 (使用程式碼在另一個視窗中進行)。 之後請登入，`get-access-token` 命令就會如預期般運作。

### <a name="step-2-retrieve-azure-ad-access-token"></a>步驟 2:擷取 Azure AD 存取權杖

叫用 Azure CLI 工具，以取得步驟 1 中 Azure AD 已驗證使用者的存取權杖，以便存取適用於 PostgreSQL 的 Azure 資料庫。

範例 (適用於公用雲端)：

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

上述資源值必須完全依所示內容加以指定。 若為其他雲端，則可以使用下列內容來查閱資源值：

```azurecli-interactive
az cloud show
```

針對 Azure CLI 2.0.71 版和更新版本，您可以在下列適用於所有雲端的更方便版本中指定此命令：

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

驗證成功之後，Azure AD 將會傳回存取權杖：

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

權杖是 Base 64 字串，會將所有關於已驗證使用者的資訊編碼，並且會以適用於 PostgreSQL 的 Azure 資料庫服務作為目標。

> [!NOTE]
> 存取權杖的有效時間介於 5 分鐘到 60 分鐘之間。 建議您在要起始對於「適用於 PostgreSQL 的 Azure 資料庫」的登入前一刻，才去取得存取權杖。

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>步驟 3：使用權杖作為使用 PostgreSQL 來登入的密碼

在連線時，您必須使用存取權杖作為 PostgreSQL 使用者密碼。

在使用 `psql` 命令列用戶端時，必須透過 `PGPASSWORD` 環境變數傳遞存取權杖，因為存取權杖超出了 `psql` 可以直接接受的密碼長度：

Windows 範例：

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Linux/macOS 範例：

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

現在您可以像往常一樣使用適用於 PostgreSQL 的 Azure 資料庫來起始連線：

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

您現在已使用 Azure AD 驗證向 PostgreSQL 伺服器進行驗證。

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>在適用於 PostgreSQL 的 Azure 資料庫中建立 Azure AD 使用者

若要將 Azure AD 使用者新增至「適用於 PostgreSQL 的 Azure 資料庫」資料庫，請在連線之後執行下列步驟 (請參閱後面關於如何連線的章節)：

1. 請先確定 Azure AD 使用者 `<user>@yourtenant.onmicrosoft.com` 是 Azure AD 租用戶中的有效使用者。
2. 以 Azure AD 管理使用者身分登入「適用於 PostgreSQL 的 Azure 資料庫」執行個體。
3. 在適用於 PostgreSQL 的 Azure 資料庫中建立角色 `<user>@yourtenant.onmicrosoft.com`。
4. 使 `<user>@yourtenant.onmicrosoft.com` 成為角色 azure_ad_user 的成員。 這只能提供給 Azure AD 使用者。

**範例︰**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> 透過 Azure AD 來驗證使用者並不會向使用者提供任何可在「適用於 PostgreSQL 的 Azure 資料庫」資料庫內存取物件的權限。 您必須手動向使用者授與必要權限。

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>在適用於 PostgreSQL 的 Azure 資料庫中建立 Azure AD 群組

若要啟用 Azure AD 群組以便能夠存取您的資料庫，請使用與使用者相同的機制，但改為指定群組名稱：

**範例︰**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

登入時，群組的成員會使用其個人存取權杖，但會以指定為使用者名稱的群組名稱來登入。

## <a name="token-validation"></a>權杖驗證

「適用於 PostgreSQL 的 Azure 資料庫」中的 Azure AD 驗證可確保使用者存在於 PostgreSQL 伺服器中，並藉由驗證權杖的內容來檢查權杖是否有效。 所會執行的權杖驗證步驟下列：

- 權杖由 Azure AD 簽署，而且尚未遭到篡改
- Azure AD 針對與伺服器相關聯的租用戶發出權杖
- 權杖尚未過期
- 權杖適用於「適用於 PostgreSQL 的 Azure 資料庫」資源 (而不是另一個 Azure 資源)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>將現有的 PostgreSQL 使用者遷移至 Azure AD 型驗證

您可以為現有使用者啟用 Azure AD 驗證。 有兩種情況需要考慮：

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>案例 1：PostgreSQL 使用者名稱符合 Azure AD 使用者主體名稱

如果是現有使用者已符合 Azure AD 使用者名稱的罕見情況，您可以向其授與 `azure_ad_user` 角色，以便為其啟用 Azure AD 驗證：

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

這些使用者現在能夠使用 Azure AD 認證 (而不是使用先前設定的 PostgreSQL 使用者密碼) 來登入。

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>案例 2：PostgreSQL 使用者名稱與 Azure AD 使用者主體名稱不同

如果 PostgreSQL 使用者不存在於 Azure AD 中或具有不同的使用者名稱，則可以使用 Azure AD 群組來以此 PostgreSQL 使用者身分進行驗證。 您可以使用符合 PostgreSQL 使用者的名稱來建立 Azure AD 群組，然後將角色 azure_ad_user 授與給現有 PostgreSQL 使用者，以將現有的「適用於 PostgreSQL 的 Azure 資料庫」使用者遷移至 Azure AD：

```sql
GRANT azure_ad_user TO "DBReadUser";
```

這會假設您已在 Azure AD 中建立「DBReadUser」群組。 屬於該群組的使用者現在將能夠以這位使用者的身分登入資料庫。

## <a name="next-steps"></a>後續步驟

* 檢閱[使用適用於 PostgreSQL 的 Azure 資料庫進行 Azure Active Directory 驗證 - 單一伺服器](concepts-aad-authentication.md)的整體概念

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
