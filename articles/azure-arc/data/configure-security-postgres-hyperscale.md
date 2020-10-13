---
title: 為已啟用 Azure Arc 的 PostgreSQL 超大規模資料庫伺服器群組設定安全性
description: 為已啟用 Azure Arc 的 PostgreSQL 超大規模資料庫伺服器群組設定安全性
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4f89ace7130e95ba109edcf6becca1e15c8d32c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273195"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>為已啟用 Azure Arc 的 PostgreSQL 超大規模資料庫伺服器群組設定安全性

本檔說明與伺服器群組安全性相關的各方面：
- 待用加密
- 使用者管理
   - 一般觀點
   - 變更 _postgres_ 系統管理使用者的密碼

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>待用加密
您可以藉由加密儲存資料庫的磁片，以及/或使用資料庫函式來加密您插入或更新的資料，來執行待用加密。

### <a name="hardware-linux-host-volume-encryption"></a>硬體： Linux 主機磁片區加密
執行系統資料加密，以保護您 Azure Arc 啟用的資料服務安裝程式所使用之磁片上的任何資料。 您可以閱讀更多有關本主題的資訊：
- Linux 上的待用[資料加密](https://wiki.archlinux.org/index.php/Data-at-rest_encryption)（一般） 
- 使用 LUKS `cryptsetup` 加密命令 (Linux) # B2 的磁片加密 https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) ，特別是在您提供的實體基礎結構上執行 Azure Arc 啟用的資料服務時，您必須負責保護基礎結構。

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>軟體： `pgcrypto` 在您的伺服器群組中使用於 postgresql 延伸模組
除了加密用來裝載 Azure Arc 安裝程式的磁片之外，您還可以設定啟用 Azure Arc 的于 postgresql 超大規模伺服器群組，以公開您的應用程式可用來加密資料庫 () 中資料的機制。 `pgcrypto`延伸模組是 Postgres 延伸模組的一部分 `contrib` ，可在您的 Azure Arc 啟用的于 postgresql 超大規模伺服器群組中使用。 您可以在這裡找到有關延伸模組的詳細資料 `pgcrypto` 。 [here](https://www.postgresql.org/docs/current/pgcrypto.html)
總而言之，您可以使用下列命令來啟用擴充功能，並加以建立和使用：


#### <a name="create-the-pgcrypto-extension"></a>建立 `pgcrypto` 延伸模組
使用您選擇的用戶端工具連接到您的伺服器群組，並執行標準于 postgreSQL 查詢：
```console
CREATE EXTENSION pgcrypto;
```

> 您可以在 [這裡](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md) 找到如何連接的詳細資料。

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>確認已準備好在您的伺服器群組中使用的延伸模組清單
您可以藉 `pgcrypto` 由列出伺服器群組中可用的擴充功能，確認延伸模組是否可供使用。
使用您選擇的用戶端工具連接到您的伺服器群組，並執行標準于 postgreSQL 查詢：
```console
select * from pg_extension;
```
您應該會看到 `pgcrypto` 您是否已啟用，並使用上述命令來建立它。

#### <a name="use-the-pgcrypto-extension"></a>使用 `pgcrypto` 延伸模組
現在您可以調整應用程式的程式碼，使其使用所提供的任何功能 `pgcrypto` ：
- 一般雜湊函數
- 密碼雜湊函數
- PGP 加密函式
- 原始加密函式
- 亂數據函數

例如，用來產生雜湊值。 執行命令：

```console
Select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

傳回下列雜湊：

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

或者，例如：

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

傳回下列雜湊：

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

或者，例如，儲存加密的資料（例如密碼）：

假設我的應用程式將秘密儲存在下表中：

```console
create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
```

而我會在建立使用者時加密其密碼：

```console
insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
```

我現在看到密碼已加密：

```console
select * from mysecrets;
```

輸出：

- USERid：1
- 使用者名稱：我
- USERpassword： $ 1 $ Uc7jzZOp $ NTfcGo7F10zGOkXOwjHy31

當我使用我的應用程式進行連線，並傳遞密碼時，它會在表格中查閱， `mysecrets` 如果提供給應用程式的密碼與資料表中儲存的密碼相符，就會傳回使用者的名稱。 例如：

- 我傳遞錯誤的密碼：
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   輸出 

   ```returns
    USERname
   ---------
   (0 rows)
   ```
- 我傳遞的密碼正確：

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ``` 

   輸出：

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

這個小範例示範您可以使用 Postgres 擴充功能來加密待用資料 (儲存加密的資料) Azure Arc 啟用的于 postgresql 超大規模 `pgcrypto` ，而且您的應用程式可以使用提供的函式 `pgcrypto` 來操作這個加密的資料。

## <a name="user-management"></a>使用者管理
### <a name="general-perspectives"></a>一般觀點
您可以使用標準 Postgres 方式來建立使用者或角色。 不過，如果您這樣做，這些成品將僅適用於協調器角色。 在預覽期間，這些使用者/角色尚無法存取協調器節點外部散發的資料，以及伺服器群組背景工作角色節點上的資料。 這是因為在預覽版中，使用者定義不會複寫到背景工作角色節點。

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>變更 _postgres_ 系統管理使用者的密碼
Azure Arc 啟用的于 postgresql 超大規模隨附于您在建立伺服器群組時設定密碼的標準 Postgres 系統管理使用者 _Postgres_ 。
變更其密碼的命令一般格式為：
```console
azdata arc postgres server edit --name <server group name> --admin-password
```

其中--admin-password 是與 AZDATA_PASSWORD **會話**的環境變數中的值存在相關的布林值。
如果 AZDATA_PASSWORD **會話**的環境變數存在且具有值，執行上述命令會將 postgres 使用者的密碼設定為此環境變數的值。

如果 AZDATA_PASSWORD **會話**的環境變數存在，但沒有值或 AZDATA_PASSWORD **會話**的環境變數不存在，則執行上述命令會提示使用者以互動方式輸入密碼

#### <a name="changing-the-password-of-the-postgres-administrative-user-in-an-interactive-way"></a>以互動方式變更 postgres 系統管理使用者的密碼：
1. 刪除 AZDATA_PASSWORD **會話**的環境變數或刪除其值
2. 執行命令：
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   例如：
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   系統會提示您輸入密碼並確認它：
   ```console
   Postgres Server password:
   Confirm Postgres Server password:
   ```
   更新密碼時，命令的輸出會顯示：
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```
   
#### <a name="changing-the-password-of-the-postgres-administrative-user-using-the-azdata_password-sessions-environment-variable"></a>使用 AZDATA_PASSWORD **會話**的環境變數，變更 postgres 系統管理使用者的密碼：
1. 將 AZDATA_PASSWORD **會話**的環境變數值設定為您想要作為密碼的密碼。
2. 執行命令：
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   例如：
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   
   更新密碼時，命令的輸出會顯示：
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```

> [!NOTE]
> 若要確認 AZDATA_PASSWORD 會話的環境變數是否存在，以及它所具有的值，請執行：
> - 在 Linux 用戶端上：
> ```console
> printenv AZDATA_PASSWORD
> ```
>
> - 在具有 PowerShell 的 Windows 用戶端上：
> ```console
> echo $env:AZDATA_PASSWORD
> ```



## <a name="next-steps"></a>後續步驟
- 請參閱這裡的 `pgcrypto` 延伸[here](https://www.postgresql.org/docs/current/pgcrypto.html)模組詳細資料。
- 如需如何使用 Postgres 延伸模組的詳細 [資訊，請](using-extensions-in-postgresql-hyperscale-server-group.md)參閱。

