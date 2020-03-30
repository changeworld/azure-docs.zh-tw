---
title: 使用 Azure 活動目錄 - MySQL 的 Azure 資料庫
description: 瞭解如何為 MySQL 的 Azure 資料庫設置 Azure 活動目錄 （Azure AD） 進行身份驗證
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 0403edadd491609c2c88d5b5ac6980d97163f8d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299000"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>使用 Azure 活動目錄使用 MySQL 進行身份驗證

本文將介紹如何使用 MySQL 的 Azure 資料庫配置 Azure 活動目錄訪問以及如何使用 Azure AD 權杖進行連接的步驟。

> [!IMPORTANT]
> MySQL Azure 資料庫的 Azure AD 身份驗證當前處於公共預覽版中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="setting-the-azure-ad-admin-user"></a>設置 Azure AD 管理員使用者

只有 Azure AD 管理員使用者可以創建/啟用使用者才能進行基於 Azure AD 的身份驗證。 要創建和 Azure AD 管理員使用者，請按照以下步驟操作

1. 在 Azure 門戶中，選擇要為 Azure AD 啟用的 MySQL 的 Azure 資料庫實例。
2. 在"設置"下，選擇活動目錄管理員：

![設置 Azure 廣告管理員][2]

3. 在客戶租戶中選擇有效的 Azure AD 使用者，作為 Azure AD 管理員。

> [!IMPORTANT]
> 設置管理員時，新使用者將添加到具有完全管理員許可權的 MySQL 伺服器的 Azure 資料庫。

每個 MySQL 伺服器只能創建一個 Azure AD 管理員，而另一個伺服器的選擇將覆蓋為伺服器配置的現有 Azure AD 管理員。

在將來的版本中，我們將支援指定 Azure AD 組，而不是單個使用者，以具有多個管理員，但目前尚不支援這樣做。

建構管理員後，您現在可以登錄：

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>使用 Azure AD 連接到 MySQL 的 Azure 資料庫

以下高級關係圖總結了使用 Azure 資料庫進行 MySQL 的 Azure AD 身份驗證的工作流：

![身份驗證流][1]

我們設計了 Azure AD 集成，以便使用常見的 MySQL 工具（如 mysql CLI），這些工具不了解 Azure AD，並且僅在連接到 MySQL 時支援指定使用者名和密碼。 我們將 Azure AD 權杖作為密碼傳遞，如上圖所示。

我們目前已經測試了以下客戶：

- MySQLWork工作臺 
- Mysql CLI

我們還測試了最常見的應用程式驅動程式，您可以在此頁面的末尾看到詳細資訊。

以下是使用者/應用程式需要執行以下描述的 Azure AD 身份驗證的步驟：

### <a name="step-1-authenticate-with-azure-ad"></a>第 1 步：使用 Azure AD 進行身份驗證

確保安裝了 Azure [CLI。](/cli/azure/install-azure-cli)

調用 Azure CLI 工具以使用 Azure AD 進行身份驗證。 它要求您提供 Azure AD 使用者 ID 和密碼。

```
az login
```

此命令將啟動到 Azure AD 身份驗證頁的瀏覽器視窗。

> [!NOTE]
> 您還可以使用 Azure 雲外殼執行這些步驟。
> 請注意，在 Azure 雲外殼中檢索 Azure AD 訪問權杖時，需要顯式調用`az login`並重新登錄（在帶有代碼的單獨視窗中）。 該登錄後，`get-access-token`命令將按預期工作。

### <a name="step-2-retrieve-azure-ad-access-token"></a>第 2 步：檢索 Azure AD 訪問權杖

調用 Azure CLI 工具以從步驟 1 獲取 Azure AD 已身份驗證使用者的訪問權杖，以訪問 MySQL 的 Azure 資料庫。

示例（公共雲）：

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

上述資源值必須完全按照所示指定。 對於其他雲，可以使用以下情況來查看資源值：

```shell
az cloud show
```

對於 Azure CLI 版本 2.0.71 及更高版本，可以在以下更方便版本中為所有雲指定該命令：

```shell
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

權杖是一個 Base 64 字串，用於編碼有關經過身份驗證的使用者的所有資訊，該字串針對 MySQL 服務的 Azure 資料庫。

> [!NOTE]
> 訪問權杖的有效期在 5 分鐘到 60 分鐘之間。 我們建議您在開始登錄 MySQL 的 Azure 資料庫之前獲取訪問權杖。

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>第 3 步：使用權杖作為密碼使用 MySQL 登錄

連接時，您需要使用訪問權杖作為 MySQL 使用者密碼。 使用 GUI 用戶端（如 MySQLWorkbench）時，可以使用上述方法檢索權杖。 

使用 CLI 時，您可以使用此速記連接： 

**示例（Linux/macOS）：**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

請注意"啟用-明文外掛程式"設置 - 您需要與其他用戶端使用類似的配置，以確保權杖發送到伺服器而不進行雜湊處理。

現在，使用 Azure AD 身份驗證對 MySQL 伺服器進行身份驗證。

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>在 Azure 資料庫中為 MySQL 創建 Azure AD 使用者

要將 Azure AD 使用者添加到 MySQL 資料庫的 Azure 資料庫，請在連接後執行以下步驟（請參閱後面有關如何連接的部分）：

1. 首先，確保 Azure AD`<user>@yourtenant.onmicrosoft.com`使用者是 Azure AD 租戶中的有效使用者。
2. 以 Azure AD 管理員使用者身份登錄到 MySQL 實例的 Azure 資料庫。
3. 在`<user>@yourtenant.onmicrosoft.com`Azure 資料庫中為 MySQL 創建使用者。

**例子：**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

對於超過 32 個字元的使用者名，建議您改用別名，在連接時使用： 

範例：

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> 通過 Azure AD 對使用者進行身份驗證不會授予使用者訪問 MySQL 資料庫 Azure 資料庫中的物件的任何許可權。 您必須手動授予使用者所需的許可權。

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>在 Azure 資料庫中為 MySQL 創建 Azure AD 組

要啟用 Azure AD 組以訪問資料庫，請使用與使用者相同的機制，而是指定組名稱：

**例子：**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

登錄時，組成員將使用其個人訪問權杖，但使用指定為使用者名的組名稱進行簽名。

## <a name="token-validation"></a>權杖驗證

MySQL Azure 資料庫中的 Azure AD 身份驗證可確保使用者存在於 MySQL 伺服器中，並且它通過驗證權杖的內容來檢查權杖的有效性。 執行以下權杖驗證步驟：

-   權杖由 Azure AD 簽名，未被篡改
-   權杖由 Azure AD 頒發給與伺服器關聯的租戶
-   權杖尚未過期
-   權杖用於 MySQL 資源的 Azure 資料庫（而不是另一個 Azure 資源）

## <a name="compatibility-with-application-drivers"></a>與應用程式驅動程式相容

大多數驅動程式都受支援，但請確保使用設置以明文形式發送密碼，因此權杖無需修改即可發送。

* C/C++
  * 利米sql用戶端：支援
  * mysql-連接器-c++：支援
* Java
  * 連接器/J（mysql-連接器-java）：支援，必須利用`useSSL`設置
* Python
  * 連接器/Python：支援
* Ruby
  * mysql2： 支援
* .NET
  * mysql 連接器網：支援，需要添加外掛程式mysql_clear_password
  * mysql 網/MySqlConnector：支援
* Node.js
  * mysqljs： 不支援（不發送明文中的權杖，沒有修補程式）
  * 節點-mysql2：支援
* Perl
  * DBD：：mysql：支援
  * 淨：：MySQL：不支援
* Go
  * go-sql 驅動程式：支援，添加到`?tls=true&allowCleartextPasswords=true`連接字串

## <a name="next-steps"></a>後續步驟

* 使用[MySQL 的 Azure 資料庫查看 Azure 活動目錄身份驗證](concepts-azure-ad-authentication.md)的總體概念

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
