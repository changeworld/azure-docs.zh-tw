---
title: 伺服器信任群組
titleSuffix: Azure SQL Managed Instance
description: 深入瞭解伺服器信任群組，以及如何管理 Azure SQL 受控實例之間的信任。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: sstein, bonova
ms.date: 10/08/2020
ms.openlocfilehash: 6154625f1e943007d0ed4c3341dc1265657f3bfc
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046327"
---
# <a name="use-server-trust-groups-to-set-up-and-manage-trust-between-sql-managed-instances"></a>使用伺服器信任群組來設定及管理 SQL 受控實例之間的信任
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

伺服器信任群組是用來管理 Azure SQL 受控實例之間信任的概念。 藉由建立群組，就會在其成員之間建立以憑證為基礎的信任。 此信任可用於不同的跨實例案例。 從群組移除伺服器或刪除群組，將會移除伺服器之間的信任。 若要建立或刪除伺服器信任群組，使用者必須具備受控執行個體的寫入權限。
[伺服器信任群組](https://aka.ms/mi-server-trust-group-arm) 是 Azure 入口網站中標示為 **SQL 信任群組** 的 Azure Resource Manager 物件。

> [!NOTE]
> 伺服器信任群組會在 Azure SQL 受控實例之間的分散式交易公開預覽中引進，而且目前有一些限制，本文稍後將會說明。

## <a name="server-trust-group-setup"></a>伺服器信任群組設定

下一節說明伺服器信任群組的設定。

1. 移至 [Azure 入口網站](https://portal.azure.com/)。

2. 流覽至您打算新增至新建立之伺服器信任群組的 Azure SQL 受控執行個體。

3. 在 [ **安全性** 設定] 中，選取 [ **SQL 信任群組** ] 索引標籤。

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="伺服器信任群組":::

4. 在 [伺服器信任群組設定] 頁面上，選取 [ **新增群組** ] 圖示。

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="伺服器信任群組":::

5. 在 **SQL trust group** create Blade 設定 **組名**。 在群組成員所在的所有區域中，它必須是唯一的。 **信任範圍** 會定義以伺服器信任群組啟用的跨實例案例類型。 在預覽中，唯一適用的信任範圍是 **分散式交易**，因此它已預先選取且無法變更。 所有 **群組成員** 都必須屬於相同的 **訂** 用帳戶，但可以位於不同的資源群組底下。 選取 **資源群組** 和 **SQL Server/實例** ，以選擇將成為群組成員的 Azure SQL 受控執行個體。

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="伺服器信任群組":::

6. 填入所有必要欄位之後，按一下 [ **儲存**]。

## <a name="server-trust-group-maintenance-and-deletion"></a>伺服器信任群組維護和刪除

無法編輯服務器信任群組。 若要從群組中移除受控執行個體，您必須刪除群組並建立新群組。

下一節說明伺服器信任群組刪除進程。 
1. 前往 Azure 入口網站。
2. 流覽至屬於信任群組的受控執行個體。
3. 在 [ **安全性** 設定] 中，選取 [ **SQL 信任群組** ] 索引標籤。
4. 選取您要刪除的信任群組。
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="伺服器信任群組":::
5. 按一下 [ **刪除群組**]。
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="伺服器信任群組":::
6. 輸入伺服器信任組名以確認刪除，然後按一下 [ **刪除**]。
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="伺服器信任群組":::

> [!NOTE]
> 刪除伺服器信任群組可能不會立即移除兩個受控實例之間的信任。 您可以叫用受控實例的 [容錯移轉](https://docs.microsoft.com/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) 來強制移除信任。 請查看 [已知問題](https://docs.microsoft.com/azure/azure-sql/database/doc-changes-updates-release-notes?tabs=managed-instance#known-issues) ，以取得此的最新更新。

## <a name="limitations"></a>限制

在公開預覽期間，下列限制適用于伺服器信任群組。
 * 伺服器信任群組的名稱在其成員所在的所有區域中都必須是唯一的。
 * 群組只能包含 Azure SQL 受控實例，且必須位於相同的 Azure 訂用帳戶底下。
 * 在預覽中，群組可以只有兩個受控實例。 若要在兩個以上的受控實例上執行分散式交易，您必須為每一組受管理的實例建立伺服器信任群組。
 * 分散式交易是唯一適用于伺服器信任群組的範圍。
 * 伺服器信任群組只能從 Azure 入口網站管理。 PowerShell 和 CLI 支援將于稍後推出。
 * 無法在 Azure 入口網站上編輯服務器信任群組。 只能建立或卸載。
 * 分散式交易的其他限制可能與您的案例有關。 最值得注意的是，您必須透過 VNET 或 VNET 對等互連，將受控實例之間的連線能力提供給私人端點。 請確定您知道受控執行個體的目前 [分散式交易限制](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview#limitations)。

## <a name="next-steps"></a>後續步驟

* 如需有關 Azure SQL 受控執行個體中分散式交易的詳細資訊，請參閱 [分散式交易](../database/elastic-transactions-overview.md)。
* 如需發行更新和已知問題狀態，請參閱 [受控執行個體版本](../database/doc-changes-updates-release-notes.md)資訊。
* 如果您有功能要求，請將它們新增至 [受控執行個體論壇](https://feedback.azure.com/forums/915676-sql-managed-instance)。