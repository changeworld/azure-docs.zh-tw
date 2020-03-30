---
title: 創建使用者 - 超大規模（Citus） - 用於後格雷SQL的 Azure 資料庫
description: 本文介紹如何創建新使用者帳戶，以便與 Azure 資料庫進行後格雷SQL - 超大規模 （Citus） 的交互。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484922"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>在 Azure 資料庫中為後格雷SQL - 超縮放（Citus） 創建使用者

> [!NOTE]
> 術語"使用者"是指超大規模 （Citus） 伺服器組中的使用者。 要瞭解 Azure 訂閱使用者及其許可權，請訪問[基於 Azure 角色的存取控制 （RBAC） 一文](../role-based-access-control/built-in-roles.md)或查看[如何自訂角色](../role-based-access-control/custom-roles.md)。

## <a name="the-server-admin-account"></a>伺服器系統管理員帳戶

PostgreSQL 引擎使用[角色](https://www.postgresql.org/docs/current/sql-createrole.html)來控制對資料庫物件的訪問，新創建的超大規模 （Citus） 伺服器組附帶了幾個預定義的角色：

* [預設 PostgreSQL 角色](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

由於 Hyperscale 是託管 PaaS 服務，因此只有 Microsoft`postgres`才能使用超級使用者角色登錄。 對於有限的管理訪問，超大規模提供該`citus`角色。

角色的許可權`citus`：

* 讀取所有組態變數，甚至通常僅對超級使用者可見的變數也是如此。
* 閱讀所有\_pg\_ \* stat 視圖並使用各種與統計資訊相關的擴展-甚至視圖或擴展通常只對超級使用者可見。
* 執行監視功能，這些功能可能會在表上獲取 ACCESS SHARE 鎖，可能長時間運行。
* [創建 PostgreSQL 擴展](concepts-hyperscale-extensions.md)（因為角色是 的成員`azure_pg_admin`）。

值得注意的是，該`citus`角色有一些限制：

* 無法創建角色
* 無法創建資料庫

## <a name="how-to-create-additional-user-roles"></a>如何創建其他使用者角色

如前所述，`citus`管理員帳戶缺少創建其他使用者的許可權。 要添加使用者，請使用 Azure 門戶介面。

1. 轉到超大規模伺服器組**的角色**頁面，然後按一下 **" 添加**：

   ![角色頁](media/howto-hyperscale-create-users/1-role-page.png)

2. 輸入角色名稱和密碼。 按一下 [儲存]****。

   ![新增角色](media/howto-hyperscale-create-users/2-add-user-fields.png)

將在伺服器組的協調器節點上創建使用者，並傳播到所有輔助節點。 通過 Azure 門戶創建的角色具有`LOGIN`該屬性，這意味著這些角色是真正的使用者，可以登錄到資料庫。

## <a name="how-to-modify-privileges-for-user-role"></a>如何修改使用者角色的許可權

新的使用者角色通常用於提供具有受限許可權的資料庫存取權限。 要修改使用者許可權，請使用標準 PostgreSQL 命令，使用 PgAdmin 或 psql 等工具。 （請參閱在超量程（Citus）快速啟動中[連接到 psql。](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql)

例如，要允許`db_user`讀取`mytable`，請授予許可權：

```sql
GRANT SELECT ON mytable TO db_user;
```

超大規模 （Citus） 通過整個群集傳播單表 GRANT 語句，將它們應用於所有輔助節點。 但是，系統範圍的 GRANT（例如，對於架構中的所有表）需要在每個日期節點上運行。  使用`run_command_on_workers()`説明器功能：

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>如何刪除使用者角色或更改其密碼

要更新使用者，請訪問超大規模伺服器組**的角色**頁面，然後按一下使用者旁邊的省略號 **...** 橢圓將打開一個功能表以刪除使用者或重置其密碼。

   ![編輯角色](media/howto-hyperscale-create-users/edit-role.png)

該`citus`角色是特權，無法刪除。

## <a name="next-steps"></a>後續步驟

打開新使用者電腦的 IP 位址的防火牆，使他們能夠連接：[使用 Azure 門戶創建和管理超大規模 （Citus） 防火牆規則](howto-hyperscale-manage-firewall-using-portal.md)。

有關資料庫使用者帳戶管理的詳細資訊，請參閱 PostgreSQL 產品文檔：

* [資料庫角色和特權](https://www.postgresql.org/docs/current/static/user-manag.html)
* [格蘭特語法](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [權限](https://www.postgresql.org/docs/current/static/ddl-priv.html)
