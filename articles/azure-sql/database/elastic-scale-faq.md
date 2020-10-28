---
title: 彈性調整常見問題
description: 有關 Azure SQL Database 彈性延展的常見問題集。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 51e15a8dc5e9f918c630397d6d6593f5bf561755
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92786899"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>彈性資料庫工具常見問題集 (FAQ)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>如果我的每一分區各有單一租用戶，卻沒有分區金鑰，我要如何填入結構描述資訊的分區金鑰

結構描述資訊物件只是用來分割合併案例。 如果應用程式原本就是單一租用戶，則其並不需要分割合併工具，因此就不需要填入結構描述資訊物件。

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>我已佈建資料庫並已擁有分區對應管理員，要如何將這個新資料庫註冊為分區

請參閱[用彈性資料庫用戶端程式庫新增分區到應用程式](elastic-scale-add-a-shard.md)。

## <a name="how-much-do-elastic-database-tools-cost"></a>使用彈性資料庫工具的成本要多少

使用彈性資料庫用戶端程式庫不會產生任何成本。 成本只會針對您用於分區和分區對應管理員的 Azure SQL Database 中的資料庫，以及您為分割合併工具布建的 web/背景工作角色而產生。

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>為什麼從其他伺服器新增分區時，我的認證無法使用

請勿使用 “User ID=username@servername 格式的認證，只需使用 “User ID = username”。  此外，請確認 "username" 登入擁有分區的權限。

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>每次啟動應用程式時，是否都需要建立分區對應管理員和填入分區

否，建立分區對應管理員 (例如，[ShardMapManagerFactory.CreateSqlShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) 是一次性作業。  您的應用程式應在應用程式啟動時使用 [ShardMapManagerFactory.TryGetSqlShardMapManager()](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) 呼叫。  每個應用程式域只能有一個這類呼叫。

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>我有關於使用彈性資料庫工具的疑問，要如何尋求解答

請在 [Microsoft 問&SQL Database 的問題頁面](/answers/topics/azure-sql-database.html)中與我們聯繫。

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>當我使用分區化索引鍵連接資料庫時，我仍然可以在相同的分區上查詢其他分區化索引鍵的資料。  這是原先的設計嗎

彈性延展 API 可讓您針對分區化索引鍵連接至正確的資料庫，但是不提供分區化索引鍵篩選。  如有必要，請新增 **WHERE** 子句到您的查詢，以將範圍限制在提供的分區化索引鍵。

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>是否可以在分區集中針對個別的分區使用不同的 SQL Database 版本

可以，分區是個別的資料庫，因此可以有一個分區是「高階」版，另一個是「標準」版。 此外，在分區存留期間，分區的版本可以多次相應增加或相應減少。

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>分割合併工具是否會在分割或合併作業期間佈建 (或刪除) 資料庫

否。 如果是「分割」  作業，目標資料庫必須有適當的結構描述，而且必須向分區對應管理員登錄。  如果是「合併」  作業，您必須從分區對應管理員刪除分區，然後再刪除資料庫。

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]