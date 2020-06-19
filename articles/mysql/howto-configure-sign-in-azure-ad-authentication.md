---
title: 使用 Azure Active Directory - 適用於 MySQL 的 Azure 資料庫
description: 了解如何設定 Azure Active Directory (Azure AD)，以向「適用於 MySQL 的 Azure 資料庫」進行驗證
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 1fa34deaa12400a164602d38b6b2d349a64850c6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652243"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>使用 Azure Active Directory 向 MySQL 進行驗證

本文將逐步引導您完成如何使用「適用於 MySQL 的 Azure 資料庫」來設定 Azure Active Directory 存取的步驟，以及如何使用 Azure AD 權杖來連線的步驟。

## <a name="setting-the-azure-ad-admin-user"></a>設定 Azure AD 管理使用者

只有 Azure AD 管理使用者可以建立/啟用 Azure AD 型驗證的使用者。 若要建立 Azure AD 管理使用者，請遵循以下步驟

1. 在 Azure 入口網站中，選取您想要為 Azure AD 啟用的「適用於 MySQL 的 Azure 資料庫」執行個體。
2. 在 [設定] 下，選取 [Active Directory 管理員]：

![設定 Azure AD 管理員][2]

3. 在客戶租用戶中選取有效的 Azure AD 使用者，以擔任 Azure AD 管理員。

> [!IMPORTANT]
> 在設定管理員時，會將具有完整管理員權限的新使用者新增至「適用於 MySQL 的 Azure 資料庫」伺服器。

每個 MySQL 伺服器只能建立一個 Azure AD 管理員，選取另一位管理員將會覆寫為伺服器設定的現有 Azure AD 管理員。

在未來版本中，我們將支援指定 Azure AD 群組，而不是個別使用者擁有多個管理員，但目前尚不支援此功能。

設定管理員之後，您現在可以登入：

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>使用 Azure AD 來連線到「適用於 MySQL 的 Azure 資料庫」

下列高階圖表摘要說明搭配使用 Azure AD 驗證與「適用於 MySQL 的 Azure 資料庫」的工作流程：

![驗證流程][1]

我們將 Azure AD 整合設計為可使用常見的 MySQL 工具，例如 mysql CLI，Azure AD 無法感知此工具，而且此工具只支援在連線到 MySQL 時，指定使用者名稱和密碼。 我們會以密碼的形式傳遞 Azure AD 權杖，如上圖所示。

我們目前已測試過下列用戶端：

- MySQLWorkbench 
- Mysql CLI

我們也已測試最常見的應用程式驅動程式，您可以在此頁面最後查看詳細資料。

使用者/應用程式為了使用 Azure AD 進行驗證所必須執行的步驟如下所述：

### <a name="step-1-authenticate-with-azure-ad"></a>步驟 1:使用 Azure AD 進行驗證

請確定您已[安裝 Azure CLI](/cli/azure/install-azure-cli)。

叫用 Azure CLI 工具，以向 Azure AD 進行驗證。 您需要提供 Azure AD 的使用者識別碼和密碼。

```
az login
```

此命令會啟動 Azure AD 驗證頁面的瀏覽器視窗。

> [!NOTE]
> 您也可以使用 Azure Cloud Shell 來執行這些步驟。
> 請注意，在 Azure Cloud Shell 中擷取 Azure AD 存取權杖時，您必須明確地呼叫 `az login` 並重新登入 (使用程式碼在另一個視窗中進行)。 在登入之後，`get-access-token` 命令就會如預期般運作。

### <a name="step-2-retrieve-azure-ad-access-token"></a>步驟 2:擷取 Azure AD 存取權杖

叫用 Azure CLI 工具，以取得步驟 1 中 Azure AD 已驗證使用者的存取權杖，以便存取「適用於 MySQL 的 Azure 資料庫」。

範例 (適用於公用雲端)：

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

上述資源值必須完全依所示內容加以指定。 若為其他雲端，則可以使用下列內容以查看資源值：

```shell
az cloud show
```

針對 Azure CLI 2.0.71 版和更新版本，您可以在下列適用於所有雲端的更方便版本中指定此命令：

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

權杖是 Base 64 字串，會將所有關於已驗證使用者的資訊編碼，並且會以「適用於 MySQL 的 Azure 資料庫」服務作為目標。

> [!NOTE]
> 存取權杖的有效時間介於 5 分鐘到 60 分鐘之間。 建議您在要起始對於「適用於 MySQL 的 Azure 資料庫」的登入前一刻，才去取得存取權杖。

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>步驟 3：使用權杖作為使用 MySQL 登入的密碼

在連線時，您必須使用存取權杖作為 MySQL 使用者密碼。 使用 GUI 用戶端 (例如 MySQLWorkbench)，您可以使用上述方法擷取權杖。 

使用 CLI 時，您可以使用此速記來連線： 

**範例 (Linux/macOS)：**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

請注意，[enable-cleartext-plugin] 設定 – 您需要將類似的設定與其他用戶端搭配使用，以確保權杖傳送至伺服器，而不會進行雜湊處理。

您現在已使用 Azure AD 驗證向 MySQL 伺服器進行驗證。

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>在「適用於 MySQL 的 Azure 資料庫」中建立 Azure AD 使用者

若要將 Azure AD 使用者新增至「適用於 MySQL 的 Azure 資料庫」資料庫，請在連線之後執行下列步驟 (請參閱後面關於如何連線的章節)：

1. 請先確定 Azure AD 使用者 `<user>@yourtenant.onmicrosoft.com` 是 Azure AD 租用戶中的有效使用者。
2. 以 Azure AD 管理使用者身分登入「適用於 MySQL 的 Azure 資料庫」執行個體。
3. 在「適用於 MySQL 的 Azure 資料庫」中建立使用者 `<user>@yourtenant.onmicrosoft.com`。

**範例︰**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

對於超過 32 個字元的使用者，建議您改為使用別名，以便在連線時使用： 

範例：

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> 透過 Azure AD 來驗證使用者並不會向使用者提供任何可在「適用於 MySQL 的 Azure 資料庫」資料庫內存取物件的權限。 您必須手動向使用者授與必要權限。

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>在「適用於 MySQL 的 Azure 資料庫」中建立 Azure AD 群組

若要啟用 Azure AD 群組以便能夠存取您的資料庫，請使用與使用者相同的機制，但改為指定群組名稱：

**範例︰**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

登入時，群組的成員會使用其個人存取權杖，但會以指定為使用者名稱的群組名稱來登入。

## <a name="token-validation"></a>權杖驗證

「適用於 MySQL 的 Azure 資料庫」中的 Azure AD 驗證可確保使用者存在於 MySQL 伺服器中，並藉由驗證權杖的內容來檢查權杖是否有效。 會執行以下權杖驗證步驟：

-   由 Azure AD 簽署權杖，而且尚未遭到篡改
-   Azure AD 針對與伺服器相關聯的租用戶發出權杖
-   權杖尚未過期
-   權杖適用於「適用於 MySQL 的 Azure 資料庫」資源 (而不是另一個 Azure 資源)

## <a name="compatibility-with-application-drivers"></a>與應用程式驅動程式的相容性

支援大部分驅動程式，請務必使用以純文字傳送密碼的設定，以便在不修改的情況下，傳送權杖。

* C/C++
  * libmysqlclient：支援
  * mysql-connector-c++：支援
* Java
  * 連接器/J (mysql-connector-java)：支援，必須使用 `useSSL` 設定
* Python
  * 連接器/Python：支援
* Ruby
  * mysql2：支援
* .NET
  * mysql-connector-net：支援，需要為 mysql_clear_password 新增外掛程式
  * mysql-net/MySqlConnector：支援
* Node.js
  * mysqljs：不支援 (在沒有修補程式的情況下，不會以純文字傳送權杖)
  * node-mysql2：支援
* Perl
  * DBD::mysql：支援
  * Net::MySQL：不支援
* Go
  * go-sql-driver：支援，將 `?tls=true&allowCleartextPasswords=true` 新增至連接字串

## <a name="next-steps"></a>後續步驟

* 檢閱[使用「適用於 MySQL 的 Azure 資料庫」進行 Azure Active Directory 驗證](concepts-azure-ad-authentication.md)的整體概念

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
