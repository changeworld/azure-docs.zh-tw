---
title: 控制 SQL 隨選 (預覽版) 的儲存體帳戶存取
description: 說明 SQL 隨選 (預覽版) 如何存取 Azure 儲存體，以及您如何在 Azure Synapse Analytics 中控制 SQL 隨選的儲存體存取。
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420052"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中控制 SQL 隨選 (預覽版) 的儲存體帳戶存取

SQL 隨選 (預覽版) 查詢會直接從 Azure 儲存體讀取檔案。 因為儲存體帳戶是 SQL 隨選資源以外的物件，所以需要適當的認證。 使用者需要獲派適當權限，才能使用必要的認證。 本文將說明您可以使用的認證類型，以及如何為 SQL 和 Azure AD 使用者制訂認證查閱。

## <a name="supported-storage-authorization-types"></a>支援的儲存體授權類型

已登入 SQL 隨選資源的使用者必須獲得授權，才能存取及查詢 Azure 儲存體中的檔案。 支援三種授權類型：

- [共用存取簽章](#shared-access-signature)
- [受控身分識別](#managed-identity)
- [使用者身分識別](#user-identity)

> [!NOTE]
> 當您建立工作區時，[Azure AD 傳遞](#force-azure-ad-pass-through)是預設的行為。 如果您使用此行為，則不需要為使用 AD 登入來存取的每個儲存體帳戶建立認證。 您可以[停用此行為](#disable-forcing-azure-ad-pass-through)。

您可以在下表中找到支援或即將支援的不同授權類型。

| 授權類型                    | SQL 使用者     | Azure AD 使用者      |
| ------------------------------------- | ------------- | -----------    |
| [SAS](#shared-access-signature)       | 支援     | 支援      |
| [受控身分識別](#managed-identity) | 不支援 | 不支援  |
| [使用者身分識別](#user-identity)       | 不支援 | 支援      |

### <a name="shared-access-signature"></a>共用存取簽章

**共用存取簽章 (SAS)** 可提供您儲存體帳戶中資源的委派存取權。 透過 SAS，客戶可以對用戶端授與儲存體帳戶中資源的存取權，而不必共用帳戶金鑰。 針對具有 SAS 的用戶端，SAS 可讓您精準控制這些用戶端應獲得的存取類型，包括有效性間隔、授與的權限、可接受的 IP 位址範圍，以及可接受的通訊協定 (HTTPs/HTTP)。

若要取得 SAS 權杖，您可以瀏覽至 **Azure 入口網站 -> 儲存體帳戶 -> 共用存取簽章 -> 設定權限 -> 產生 SAS 和連接字串。**

> [!IMPORTANT]
> 產生 SAS 權杖時，權杖的開頭會包含問號 ('?')。 若要在 SQL 隨選中使用權杖，您必須在建立認證時移除問號 ('?')。 例如：
>
> SAS 權杖：?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

### <a name="user-identity"></a>使用者身分識別

**使用者身分識別** (也稱為「傳遞」) 是一種授權類型，其使用登入 SQL 隨選的 Azure AD 使用者身分識別來授與資料存取權。 存取資料之前，Azure 儲存體管理員必須將權限授與 Azure AD 使用者。 如上表所示，SQL 使用者類型不支援此方式。

> [!NOTE]
> 如果您使用 [Azure AD 傳遞](#force-azure-ad-pass-through)，則不需要為使用 AD 登入來存取的每個儲存體帳戶建立認證。

> [!IMPORTANT]
> 您必須具有儲存體 Blob 資料擁有者/參與者/讀者角色，才能使用您的身分識別來存取資料。
> 即使您是儲存體帳戶的擁有者，仍然需要將自己新增至其中一個儲存體 Blob 資料角色。
>
> 若要深入了解 Azure Data Lake Store Gen2 中的存取控制，請參閱 [Azure Data Lake Storage Gen2 文章中的存取控制](../../storage/blobs/data-lake-storage-access-control.md)一文。
>

### <a name="managed-identity"></a>受控識別

**受控識別**也稱為 MSI。 這是 Azure Active Directory (Azure AD) 的功能，可為 SQL 隨選提供 Azure 服務。 此外，也會在 Azure AD 中部署自動受控識別。 在 Azure 儲存體中，此身分識別可用來授與資料存取要求的權限。

存取資料之前，Azure 儲存體管理員必須將權限授與受控識別，才能存取資料。 將權限授與受控識別的方式，與對任何其他 Azure AD 使用者授與權限的方式一樣。

## <a name="create-credentials"></a>建立認證

若要查詢 Azure 儲存體中的檔案，您的 SQL 隨選端點需要其中包含驗證資訊的伺服器層級認證。 您可以執行 [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)來新增認證。 您必須提供 CREDENTIAL NAME 引數。 其必須是儲存體中資料的部分路徑或完整路徑 (如下所示)。

> [!NOTE]
> 不支援 FOR CRYPTOGRAPHIC PROVIDER 引數。

針對所有支援的授權類型，認證可以指向帳戶、容器、任何目錄 (非根目錄) 或單一檔案。

CREDENTIAL NAME 必須符合容器、資料夾或檔案的完整路徑，其格式如下：`<prefix>://<storage_account_path>/<storage_path>`

| 外部資料來源       | 前置詞 | 儲存體帳戶路徑                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob 儲存體         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>.dfs.core.windows.net              |

 '<storage_path>' 是儲存體內的路徑，其指向您想要讀取的資料夾或檔案。

> [!NOTE]
> 特殊的 CREDENTIAL NAME `UserIdentity` 會[強制執行 Azure AD 傳遞](#force-azure-ad-pass-through)。 請了解執行查詢時，此方式對[認證查閱](#credential-lookup)產生的影響。

(選擇性) 若要允許使用者建立或卸載認證，系統管理員可以對使用者執行 GRANT/DENY ALTER ANY CREDENTIAL 權限：

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>支援的儲存體和授權類型

您可以使用下列的授權和 Azure 儲存體類型組合：

|                     | Blob 儲存體   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| SAS                | 支援      | 不支援   | 支援     |
| 受控識別  | 不支援  | 不支援    | 不支援 |
| 使用者身分識別     | 支援      | 支援        | 支援     |

### <a name="examples"></a>範例

視[授權類型](#supported-storage-authorization-types)而定，您可以使用下列 T-SQL 語法來建立認證。

**共用存取簽章和 Blob 儲存體**

請將 <*mystorageaccountname*> 替換為實際的儲存體帳戶名稱，以及將 <*mystorageaccountcontainername*> 替換為實際的容器名稱：

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**使用者身分識別和 Azure Data Lake Storage Gen1**

請將 <*mystorageaccountname*> 替換為實際的儲存體帳戶名稱，以及將 <*mystorageaccountcontainername*> 替換為實際的容器名稱：

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**使用者身分識別和 Azure Data Lake Storage Gen2**

請將 <*mystorageaccountname*> 替換為實際的儲存體帳戶名稱，以及將 <*mystorageaccountcontainername*> 替換為實際的容器名稱：

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>強制執行 Azure AD 傳遞

強制執行 Azure AD 傳遞是使用特殊 CREDENTIAL NAME `UserIdentity` 時的預設行為，這會在佈建 Azure Synapse 工作區期間自動建立。 其會針對每個 Azure AD 登入的每個查詢強制使用 Azure AD 傳遞，而且不論是否有其他認證存在，都會發生此情形。

> [!NOTE]
> Azure AD 傳遞是預設行為。 您不需要為以 AD 登入來存取的每個儲存體帳戶建立認證。

如果您[對每個查詢停用強制執行 Azure AD 傳遞](#disable-forcing-azure-ad-pass-through)，然後想再次啟用，請執行：

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

若要為特定使用者啟用強制執行 Azure AD 傳遞，您可以將認證 `UserIdentity` 的 EFERENCE 權限授與該特定使用者。 下列範例會為 user_name 啟用強制執行 Azure AD 傳遞：

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

若要深入了解 SQL 隨選如何尋找要使用的認證，請參閱[認證查閱](#credential-lookup)。

## <a name="disable-forcing-azure-ad-pass-through"></a>停用強制執行 Azure AD 傳遞

您可以[對每個查詢停用強制執行 Azure AD 傳遞](#force-azure-ad-pass-through)。 若要將其停用，請使用下列方式卸載 `Userdentity` 認證：

```sql
DROP CREDENTIAL [UserIdentity];
```

如果您想要重新啟用，請參閱[強制執行 Azure AD 傳遞](#force-azure-ad-pass-through)一節。

若要對特定使用者停用強制執行 Azure AD 傳遞，您可以拒絕特定使用者認證 `UserIdentity` 上的 REFERENCE 權限。 下列範例會對 user_name 停用強制執行 Azure AD 傳遞：

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

若要深入了解 SQL 隨選如何尋找要使用的認證，請參閱[認證查閱](#credential-lookup)。

## <a name="grant-permissions-to-use-credential"></a>授與使用認證的權限

若要使用認證，使用者必須具有特定認證的 REFERENCES 權限。 若要在 specific_user 的 storage_credential 上授與 REFERENCES 權限，請執行：

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

為確保 Azure AD 傳遞體驗能順利進行，根據預設，所有使用者都有權使用 `UserIdentity` 認證。 這會藉由在 Azure Synapse 工作區佈建時自動執行下列陳述式來達成：

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>認證查閱

授權查詢時，認證查閱會用來存取儲存體帳戶，並以下列規則為基礎：

- 使用者需以 Azure AD 登入的身分登入

  - 如果 UserIdentity 認證存在，而且使用者具有其參考權限，則會使用 Azure AD 傳遞，否則會使用[依路徑查閱認證](#lookup-credential-by-path)

- 使用者以 SQL 登入的身分登入

  - 使用[依路徑查閱認證](#lookup-credential-by-path)

### <a name="lookup-credential-by-path"></a>依路徑查閱認證

若強制執行 Azure AD 傳遞已停用，則會根據儲存體路徑 (深度優先)，以及該特定認證的 REFERENCES 權限是否存在，來進行認證查閱。 當有多個認證可用來存取相同的檔案時，SQL 隨選會使用最明確的一個。  

以下範例會對下列檔案路徑進行查詢：account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX.ext 

認證查閱會依照下列順序完成：

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

如果使用者沒有認證編號 5 的 REFERENCES 權限，則 SQL 隨選會檢查使用者是否在較高層級的認證上有 REFERENCES 權限，直到找到使用者有其 REFERENCES 授權的認證為止。 如果找不到這類權限，則會傳回錯誤訊息。

### <a name="credential-and-path-level"></a>認證和路徑層級

根據您想要的路徑圖形，執行查詢的需求如下：

- 如果查詢的目標是多個檔案 (包含或不含萬用字元的資料夾)，使用者必須至少能夠存取根目錄層級 (容器層級) 上的認證。 此存取層級是必要的，因為清單檔與根目錄相關 (Azure 儲存體限制)
- 如果查詢是以單一檔案為目標，則使用者必須能夠存取任何層級的認證，因為 SQL 隨選會直接存取檔案，也就是不會列出資料夾。

|                  | *帳戶* | *根目錄* | *任何其他目錄* | *檔案*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| 單一檔案     | 支援 | 支援        | 支援             | 支援     |
| 多個檔案  | 支援 | 支援        | 不支援         | 不支援 |

## <a name="next-steps"></a>後續步驟

下列文章將協助您了解查詢不同資料夾類型、檔案類型，以及建立和使用檢視的方式：

- [查詢單一 CSV 檔案](query-single-csv-file.md)
- [查詢資料夾和多個 CSV 檔案](query-folders-multiple-csv-files.md)
- [查詢特定檔案](query-specific-files.md)
- [查詢 Parquet 檔案](query-parquet-files.md)
- [建立和使用檢視](create-use-views.md)
- [查詢 JSON 檔案](query-json-files.md)
- [查詢 Parquet 巢狀型別](query-parquet-nested-types.md)
