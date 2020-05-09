---
title: 建立使用者-超大規模資料庫（Citus）-適用於 PostgreSQL 的 Azure 資料庫
description: 本文說明如何建立新的使用者帳戶，以便與適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）互動。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 684116f92544e61a892b3653f8539f9f8f03e0c9
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584090"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>在適用於 PostgreSQL 的 Azure 資料庫中建立使用者超大規模資料庫（Citus）

> [!NOTE]
> 「使用者」一詞指的是超大規模資料庫（Citus）伺服器群組中的使用者。 若要深入瞭解 Azure 訂用帳戶使用者及其許可權，請造訪[azure 角色型存取控制（RBAC）一文](../role-based-access-control/built-in-roles.md)，或參閱[如何自訂角色](../role-based-access-control/custom-roles.md)。

## <a name="the-server-admin-account"></a>伺服器系統管理員帳戶

于 postgresql 引擎會使用[角色](https://www.postgresql.org/docs/current/sql-createrole.html)來控制資料庫物件的存取權，而新建立的超大規模資料庫（Citus）伺服器群組則隨附數個預先定義的角色：

* [預設于 postgresql 角色](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

由於超大規模資料庫是受控 PaaS 服務，因此只有 Microsoft 可以使用`postgres`超級使用者角色登入。 對於有限的系統管理存取，超大規模資料庫`citus`會提供角色。

`citus`角色的許可權：

* 讀取所有設定變數，甚至是只有超級人員才看得見的變數。
* 讀取所有 pg\_的\_ \* stat views，並使用各種統計資料相關的擴充功能，甚至只有超級型的流覽或延伸模組通常是可見的。
* 執行可能會在資料表上取得存取共用鎖定的監視功能，可能會很長一段時間。
* [建立于 postgresql 延伸](concepts-hyperscale-extensions.md)模組（因為角色是的成員`azure_pg_admin`）。

值得注意的`citus`是，此角色有一些限制：

* 無法建立角色
* 無法建立資料庫

## <a name="how-to-create-additional-user-roles"></a>如何建立其他使用者角色

如前所述，管理帳戶`citus`缺少建立其他使用者的許可權。 若要新增使用者，請使用 Azure 入口網站介面。

1. 移至超大規模資料庫伺服器群組的 [**角色**] 頁面，然後按一下 [ **+ 新增**]：

   ![[角色] 頁面](media/howto-hyperscale-create-users/1-role-page.png)

2. 輸入 [角色名稱] 和 [密碼]。 按一下 [檔案]  。

   ![新增角色](media/howto-hyperscale-create-users/2-add-user-fields.png)

系統會在伺服器群組的協調器節點上建立使用者，並將其傳播到所有背景工作角色節點。 透過 Azure 入口網站建立的`LOGIN`角色具有屬性，這表示它們是可以登入資料庫的真實使用者。

## <a name="how-to-modify-privileges-for-user-role"></a>如何修改使用者角色的許可權

新的使用者角色通常用來以限制許可權提供資料庫存取權。 若要修改使用者權限，請使用 PgAdmin 或 psql 這類工具的標準于 postgresql 命令。 （請參閱超大規模資料庫（Citus）快速入門中的[連接 psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) 。）

例如，若要允許`db_user`讀取`mytable`，請授與許可權：

```sql
GRANT SELECT ON mytable TO db_user;
```

超大規模資料庫（Citus）會透過整個叢集傳播單一資料表授與語句，並將其套用至所有背景工作節點。 不過，必須在每個日期節點上執行全系統的授與（例如，針對架構中的所有資料表）。  使用`run_command_on_workers()` helper 函式：

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>如何刪除使用者角色或變更其密碼

若要更新使用者，請造訪超大規模資料庫伺服器群組的 [**角色**] 頁面，然後按一下使用者旁邊的省略號 **...** 。 省略號會開啟功能表，以刪除使用者或重設其密碼。

   ![編輯角色](media/howto-hyperscale-create-users/edit-role.png)

`citus`角色具有特殊許可權，因此無法刪除。

## <a name="next-steps"></a>後續步驟

針對新使用者電腦的 IP 位址開啟防火牆，讓他們能夠連線：[使用 Azure 入口網站建立和管理超大規模資料庫（Citus）防火牆規則](howto-hyperscale-manage-firewall-using-portal.md)。

如需有關資料庫使用者帳戶管理的詳細資訊，請參閱于 postgresql 產品檔：

* [資料庫角色和許可權](https://www.postgresql.org/docs/current/static/user-manag.html)
* [GRANT 語法](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [權限](https://www.postgresql.org/docs/current/static/ddl-priv.html)
