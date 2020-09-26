---
title: 建立使用者-超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 本文說明如何建立新的使用者帳戶，以與適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 互動。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/8/2019
ms.openlocfilehash: 3d23ee6119b625e11ce44bb9ad11ce4b3ee0280d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91295731"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>在適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus 中建立使用者) 

> [!NOTE]
> 「使用者」一詞是指超大規模 (Citus) server 群組內的使用者。 若要深入瞭解 Azure 訂用帳戶使用者及其許可權，請造訪 [azure 角色型存取控制 (AZURE RBAC) 一文](../role-based-access-control/built-in-roles.md) ，或查看 [如何自訂角色](../role-based-access-control/custom-roles.md)。

## <a name="the-server-admin-account"></a>伺服器系統管理員帳戶

于 postgresql 引擎會使用 [角色](https://www.postgresql.org/docs/current/sql-createrole.html) 來控制對資料庫物件的存取，而新建立的超大規模 (Citus) 伺服器群組會有數個預先定義的角色：

* [預設于 postgresql 角色](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

由於超大規模 (Citus) 是受控 PaaS 服務，因此只有 Microsoft 才能以 `postgres` 超級使用者角色登入。 針對有限的系統管理存取權，超大規模 (Citus) 提供 `citus` 角色。

角色的許可權 `citus` ：

* 讀取所有設定變數，即使是通常只有超級人員才能看到的變數。
* 讀取所有 pg \_ 狀態 \_ \* 視圖，並使用各種統計資料相關的擴充功能，甚至通常只有超級人員才能看到視圖或延伸模組。
* 執行可能會在資料表上取得存取共用鎖定的監視函式，可能很長一段時間。
*  ([建立于 postgresql 延伸](concepts-hyperscale-extensions.md)模組，因為角色是) 的成員 `azure_pg_admin` 。

值得注意的是， `citus` 角色有一些限制：

* 無法建立角色
* 無法建立資料庫

## <a name="how-to-create-additional-user-roles"></a>如何建立額外的使用者角色

如前所述，系統 `citus` 管理員帳戶缺少建立其他使用者的許可權。 若要加入使用者，請使用 Azure 入口網站介面。

1. 移至超大規模 (Citus) server 群組的 [ **角色** ] 頁面，然後按一下 [ **+ 新增**：

   :::image type="content" source="media/howto-hyperscale-create-users/1-role-page.png" alt-text="角色頁面":::

2. 輸入角色名稱和密碼。 按一下 [檔案]  。

   :::image type="content" source="media/howto-hyperscale-create-users/2-add-user-fields.png" alt-text="新增角色":::

系統會在伺服器群組的協調器節點上建立使用者，並傳播至所有背景工作節點。 透過 Azure 入口網站建立的角色具有 `LOGIN` 屬性，這表示它們是可登入資料庫的真實使用者。

## <a name="how-to-modify-privileges-for-user-role"></a>如何修改使用者角色的許可權

新的使用者角色通常用來提供具有限制許可權的資料庫存取權。 若要修改使用者權限，請使用 PgAdmin 或 psql 等工具，以使用標準的于 postgresql 命令。  (請參閱超大規模 (Citus) 快速入門中的 [psql 連接](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) 。 ) 

例如，若要允許 `db_user` 讀取 `mytable` ，請授與許可權：

```sql
GRANT SELECT ON mytable TO db_user;
```

超大規模 (Citus) 會透過整個叢集傳播單一資料表 GRANT 語句，並將其套用到所有背景工作節點上。 此外，它也會傳播系統範圍 (的授權，例如針對架構中的所有資料表) ：

```sql
-- applies to the coordinator node and propagates to workers
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>如何刪除使用者角色或變更其密碼

若要更新使用者，請造訪超大規模 (Citus) server 群組的 [**角色**] 頁面，然後按一下使用者旁邊的省略號 **。** 省略號會開啟功能表以刪除使用者，或重設其密碼。

   :::image type="content" source="media/howto-hyperscale-create-users/edit-role.png" alt-text="編輯角色":::

`citus`角色具有特殊許可權，無法刪除。

## <a name="next-steps"></a>後續步驟

為新使用者的電腦 IP 位址開啟防火牆，讓使用者能夠連線： [使用 Azure 入口網站建立及管理超大規模 (Citus) 防火牆規則](howto-hyperscale-manage-firewall-using-portal.md)。

如需有關資料庫使用者帳戶管理的詳細資訊，請參閱于 postgresql 產品檔：

* [資料庫角色和許可權](https://www.postgresql.org/docs/current/static/user-manag.html)
* [GRANT 語法](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [特權](https://www.postgresql.org/docs/current/static/ddl-priv.html)
