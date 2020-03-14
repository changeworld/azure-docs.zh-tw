---
title: 使用 Azure Active Directory 適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 瞭解如何設定 Azure Active Directory （AAD）以適用於 PostgreSQL 的 Azure 資料庫單一伺服器進行驗證
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: aacffbfdec67d7faa58cb8bd08f99963fb78263a
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299272"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>使用 Azure Active Directory 向于 postgresql 進行驗證

本文將逐步引導您完成如何使用適用於 PostgreSQL 的 Azure 資料庫設定 Azure Active Directory 存取的步驟，以及如何使用 Azure AD token 來連接。

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫的 Azure AD 驗證目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="setting-the-azure-ad-admin-user"></a>設定 Azure AD 系統管理員使用者

只有 Azure AD 的系統管理員使用者可以建立/啟用以 Azure AD 為基礎之驗證的使用者。 若要建立並 Azure AD 系統管理員使用者，請遵循下列步驟

1. 在 Azure 入口網站中，選取您想要為 Azure AD 啟用的適用於 PostgreSQL 的 Azure 資料庫實例。
2. 在 [設定] 下，選取 [Active Directory 管理員]：

![設定 azure ad 系統管理員][2]

3. 請在客戶租使用者中選取有效的 Azure AD 使用者，以 Azure AD 系統管理員。

> [!IMPORTANT]
> 設定系統管理員時，會將新的使用者新增至具有完整系統管理員許可權的適用於 PostgreSQL 的 Azure 資料庫伺服器。 適用於 PostgreSQL 的 Azure 資料庫中的 Azure AD 系統管理使用者會擁有 `azure_ad_admin`角色。

每個于 postgresql 伺服器只能建立一個 Azure AD 系統管理員，選擇另一個則會覆寫為伺服器所設定的現有 Azure AD 管理員。 您可以指定 Azure AD 群組，而不是個別使用者，以擁有多個系統管理員。 請注意，您接著會使用組名登入，以供管理之用。

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>在適用於 PostgreSQL 的 Azure 資料庫中建立 Azure AD 使用者

若要將 Azure AD 使用者新增至您的適用於 PostgreSQL 的 Azure 資料庫資料庫，請在連線之後執行下列步驟（請參閱稍後的如何連接的章節）：

1. 請先確定 Azure AD 使用者 `<user>@yourtenant.onmicrosoft.com` 是 Azure AD 租使用者中的有效使用者。
2. 以 Azure AD 系統管理員使用者身分登入您的適用於 PostgreSQL 的 Azure 資料庫實例。
3. 在適用於 PostgreSQL 的 Azure 資料庫中建立角色 `<user>@yourtenant.onmicrosoft.com`。
4. 使 `<user>@yourtenant.onmicrosoft.com` 成為角色 azure_ad_user 的成員。 這必須僅提供給 Azure AD 使用者。

**範例︰**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> 透過 Azure AD 驗證使用者不會提供使用者任何許可權來存取適用於 PostgreSQL 的 Azure 資料庫資料庫內的物件。 您必須手動授與使用者必要的許可權。

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>在適用於 PostgreSQL 的 Azure 資料庫中建立 Azure AD 群組

若要啟用 Azure AD 群組來存取您的資料庫，請使用與使用者相同的機制，但改為指定組名：

**範例︰**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

登入時，群組的成員會使用其個人存取權杖，但會以指定為使用者名稱的組名簽署。

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>使用 Azure AD 連接到適用於 PostgreSQL 的 Azure 資料庫

下列高階圖表摘要說明搭配適用於 PostgreSQL 的 Azure 資料庫使用 Azure AD 驗證的工作流程：

![驗證流程][1]

我們設計了 Azure AD 整合來處理常見的于 postgresql 工具，例如 psql，這並不 Azure AD 感知，而且只支援在連接到于 postgresql 時指定使用者名稱和密碼。 我們會以密碼的形式傳遞 Azure AD token，如上圖所示。

我們目前已測試過下列用戶端：

- psql 命令列（利用 PGPASSWORD 變數來傳遞權杖，請參閱下文）
- Azure Data Studio （使用於 postgresql 擴充功能）
- 其他以 libpq 為基礎的用戶端（例如常見的應用程式架構和 Orm）

> [!NOTE]
> 請注意，目前不支援搭配 pgAdmin 使用 Azure AD token，因為它的密碼具有256個字元的硬式編碼限制（權杖超過）。

以下是使用者/應用程式將需要使用 Azure AD 進行驗證的步驟，如下所述：

### <a name="step-1-authenticate-with-azure-ad"></a>步驟1：使用 Azure AD 進行驗證

請確定您已[安裝 Azure CLI](/cli/azure/install-azure-cli)。

叫用 Azure CLI 工具，以向 Azure AD 進行驗證。 您需要提供 Azure AD 的使用者識別碼和密碼。

```
az login
```

此命令將會啟動瀏覽器視窗至 Azure AD 驗證頁面。

> [!NOTE]
> 您也可以使用 Azure Cloud Shell 來執行這些步驟。
> 請注意，在 Azure Cloud Shell 中抓取 Azure AD 存取權杖時，您必須明確地呼叫 `az login` 並再次登入（在使用程式碼的另一個視窗中）。 在該登入之後，`get-access-token` 命令就會如預期般運作。

### <a name="step-2-retrieve-azure-ad-access-token"></a>步驟2：取出 Azure AD 存取權杖

叫用 Azure CLI 工具，以取得步驟1中 Azure AD 已驗證使用者的存取權杖，以存取適用於 PostgreSQL 的 Azure 資料庫。

範例（適用于公用雲端）：

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

上述資源值必須完全依照所示指定。 針對其他雲端，可以使用下列內容來查閱資源值：

```shell
az cloud show
```

針對 Azure CLI version 2.0.71 和更新版本，您可以針對所有雲端，在下列更方便的版本中指定命令：

```shell
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

Token 是基底64字串，會將所有已驗證使用者的相關資訊編碼，並以適用於 PostgreSQL 的 Azure 資料庫服務為目標。

> [!NOTE]
> 存取權杖的有效性介於5分鐘到60分鐘之間。 建議您在起始登入以適用於 PostgreSQL 的 Azure 資料庫之前，先取得存取權杖。

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>步驟3：使用權杖做為使用於 postgresql 登入的密碼

連接時，您必須使用存取權杖作為于 postgresql 使用者密碼。

使用 `psql` 命令列用戶端時，必須透過 `PGPASSWORD` 環境變數傳遞存取權杖，因為存取權杖超出了 `psql` 可以直接接受的密碼長度：

Windows 範例：

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Linux/macOS 範例：

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

現在您可以使用適用於 PostgreSQL 的 Azure 資料庫起始連線，如下所示：

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

您現在已使用 Azure AD authentication 來驗證您的于 postgresql 伺服器。

## <a name="token-validation"></a>權杖驗證

適用於 PostgreSQL 的 Azure 資料庫中的 Azure AD 驗證可確保使用者存在於于 postgresql 伺服器中，並藉由驗證權杖的內容來檢查權杖的有效性。 會執行下列權杖驗證步驟：

-   權杖是由 Azure AD 簽署，而且尚未遭到篡改
-   已針對與伺服器相關聯的租使用者 Azure AD 發出權杖
-   Token 尚未過期
-   Token 適用于適用於 PostgreSQL 的 Azure 資料庫資源（而不是其他 Azure 資源）

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>將現有的于 postgresql 使用者遷移至以 Azure AD 為基礎的驗證

您可以為現有的使用者啟用 Azure AD 驗證。 有兩種情況需要考慮：

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>案例1：于 postgresql username 符合 Azure AD 的使用者主體名稱

如果您現有的使用者已經符合 Azure AD 的使用者名稱，您可以將 `azure_ad_user` 角色授與它們，以便 Azure AD 驗證：

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

他們現在能夠以 Azure AD 認證登入，而不是使用先前設定的于 postgresql 使用者密碼。

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>案例2：于 postgresql username 與 Azure AD 使用者主體名稱不同

如果于 postgresql 使用者不存在 Azure AD 或具有不同的使用者名稱，您可以使用 Azure AD 群組來以此于 postgresql 使用者身分進行驗證。 您可以使用符合于 postgresql 使用者的名稱來建立 Azure AD 群組，然後將角色 azure_ad_user 授與現有的于 postgresql 使用者，以將現有適用於 PostgreSQL 的 Azure 資料庫使用者遷移至 Azure AD：

```sql
GRANT azure_ad_user TO "DBReadUser";
```

這會假設您已在 Azure AD 中建立「DBReadUser」群組。 屬於該群組的使用者現在將能夠以這種使用者的身分登入資料庫。

## <a name="next-steps"></a>後續步驟

* 回顧[適用於 PostgreSQL 的 Azure 資料庫-單一伺服器的 Azure Active Directory 驗證](concepts-aad-authentication.md)的整體概念

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
