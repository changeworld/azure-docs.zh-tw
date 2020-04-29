---
title: 使用 Azure Active Directory 適用於 MySQL 的 Azure 資料庫
description: 瞭解如何設定 Azure Active Directory （Azure AD）以使用適用於 MySQL 的 Azure 資料庫進行驗證
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 0403edadd491609c2c88d5b5ac6980d97163f8d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79299000"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>使用 Azure Active Directory 來向 MySQL 進行驗證

本文將逐步引導您完成如何使用適用於 MySQL 的 Azure 資料庫設定 Azure Active Directory 存取的步驟，以及如何使用 Azure AD token 來連接。

> [!IMPORTANT]
> 適用於 MySQL 的 Azure 資料庫的 Azure AD 驗證目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="setting-the-azure-ad-admin-user"></a>設定 Azure AD 系統管理員使用者

只有 Azure AD 的系統管理員使用者可以建立/啟用以 Azure AD 為基礎之驗證的使用者。 若要建立並 Azure AD 系統管理員使用者，請遵循下列步驟

1. 在 [Azure 入口網站中，選取您想要為 Azure AD 啟用的適用於 MySQL 的 Azure 資料庫實例。
2. 在 [設定] 下，選取 [Active Directory 管理員]：

![設定 azure ad 系統管理員][2]

3. 請在客戶租使用者中選取有效的 Azure AD 使用者，以 Azure AD 系統管理員。

> [!IMPORTANT]
> 設定系統管理員時，會將新的使用者新增至具有完整系統管理員許可權的適用於 MySQL 的 Azure 資料庫伺服器。

每個 MySQL 伺服器只能建立一個 Azure AD 系統管理員，選取另一個則會覆寫為伺服器所設定的現有 Azure AD 管理員。

在未來版本中，我們將支援指定 Azure AD 群組，而不是個別使用者擁有多個系統管理員，但目前尚不支援此功能。

設定系統管理員之後，您現在可以登入：

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>使用 Azure AD 連接到適用於 MySQL 的 Azure 資料庫

下列高階圖表摘要說明搭配適用於 MySQL 的 Azure 資料庫使用 Azure AD 驗證的工作流程：

![驗證流程][1]

我們設計了 Azure AD 整合來處理常見的 MySQL 工具，例如 mysql CLI，這並不 Azure AD 感知，而且只支援在連線到 MySQL 時指定使用者名稱和密碼。 我們會以密碼的形式傳遞 Azure AD token，如上圖所示。

我們目前已測試過下列用戶端：

- MySQLWorkbench 
- Mysql CLI

我們也已測試最常見的應用程式驅動程式，您可以在此頁面結尾查看詳細資料。

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
> 請注意，在 Azure Cloud Shell 中抓取 Azure AD 存取權杖時，您必須明確地呼叫`az login`並重新登入（在使用程式碼的另一個視窗中）。 該登入之後， `get-access-token`命令就會如預期般運作。

### <a name="step-2-retrieve-azure-ad-access-token"></a>步驟2：取出 Azure AD 存取權杖

叫用 Azure CLI 工具，以取得步驟1中 Azure AD 已驗證使用者的存取權杖，以存取適用於 MySQL 的 Azure 資料庫。

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

Token 是基底64字串，會將所有已驗證使用者的相關資訊編碼，並以適用於 MySQL 的 Azure 資料庫服務為目標。

> [!NOTE]
> 存取權杖的有效性介於5分鐘到60分鐘之間。 建議您在起始登入以適用於 MySQL 的 Azure 資料庫之前，先取得存取權杖。

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>步驟3：使用權杖做為使用 MySQL 登入的密碼

連接時，您必須使用存取權杖作為 MySQL 使用者密碼。 使用 GUI 用戶端（例如 MySQLWorkbench）時，您可以使用上述方法來取得權杖。 

使用 CLI 時，您可以使用這個簡短的來連接： 

**範例（Linux/macOS）：**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

請注意 [啟用-純文字-外掛程式] 設定–您必須搭配其他用戶端使用類似的設定，以確保權杖會傳送至伺服器，而不會進行雜湊處理。

您現在已使用 Azure AD authentication 向 MySQL 伺服器進行驗證。

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>在適用於 MySQL 的 Azure 資料庫中建立 Azure AD 使用者

若要將 Azure AD 使用者新增至您的適用於 MySQL 的 Azure 資料庫資料庫，請在連線之後執行下列步驟（請參閱稍後的如何連接的章節）：

1. 請先確定 Azure AD 使用者`<user>@yourtenant.onmicrosoft.com`是 Azure AD 租使用者中的有效使用者。
2. 以 Azure AD 系統管理員使用者身分登入您的適用於 MySQL 的 Azure 資料庫實例。
3. 在適用於 MySQL 的 Azure 資料庫`<user>@yourtenant.onmicrosoft.com`中建立使用者。

**範例：**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

對於超過32個字元的使用者名稱，建議您改為使用別名，以便在連接時使用： 

範例：

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> 透過 Azure AD 驗證使用者不會提供使用者任何許可權來存取適用於 MySQL 的 Azure 資料庫資料庫內的物件。 您必須手動授與使用者必要的許可權。

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>在適用於 MySQL 的 Azure 資料庫中建立 Azure AD 群組

若要啟用 Azure AD 群組來存取您的資料庫，請使用與使用者相同的機制，但改為指定組名：

**範例：**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

登入時，群組的成員會使用其個人存取權杖，但會以指定為使用者名稱的組名簽署。

## <a name="token-validation"></a>權杖驗證

適用於 MySQL 的 Azure 資料庫中的 Azure AD 驗證可確保使用者存在於 MySQL 伺服器中，並藉由驗證權杖的內容來檢查權杖的有效性。 會執行下列權杖驗證步驟：

-   權杖是由 Azure AD 簽署，而且尚未遭到篡改
-   已針對與伺服器相關聯的租使用者 Azure AD 發出權杖
-   Token 尚未過期
-   Token 適用于適用於 MySQL 的 Azure 資料庫資源（而不是其他 Azure 資源）

## <a name="compatibility-with-application-drivers"></a>與應用程式驅動程式的相容性

支援大部分的驅動程式，但請務必使用以純文字傳送密碼的設定，以便在不修改的情況下傳送權杖。

* C/C++
  * libmysqlclient：支援
  * mysql-連接器-c + +：支援
* Java
  * Connector/J （mysql-連接器-java）：支援，必須使用`useSSL`設定
* Python
  * 連接器/Python：支援
* Ruby
  * mysql2：支援
* .NET
  * mysql-connector-net：支援，需要為 mysql_clear_password 新增外掛程式
  * mysql-net/MySqlConnector：支援
* Node.js
  * mysqljs：不支援（不會以純文字傳送權杖，沒有 patch）
  * 節點-mysql2：支援
* Perl
  * DBD：： mysql：支援
  * Net：： MySQL：不支援
* Go
  * go-sql 驅動程式：支援，新增`?tls=true&allowCleartextPasswords=true`至連接字串

## <a name="next-steps"></a>後續步驟

* 請參閱[使用適用於 MySQL 的 Azure 資料庫 Azure Active Directory 驗證](concepts-azure-ad-authentication.md)的整體概念

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
