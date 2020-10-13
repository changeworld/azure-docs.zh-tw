---
title: 使用 Arc 啟用的于 postgresql 超大規模伺服器群組來散發資料和相應放大的概念
titleSuffix: Azure Arc enabled data services
description: 使用 Arc 啟用的于 postgresql 超大規模伺服器群組散發資料的概念
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: c01da4aed9e27296ea7b570420bb190b16749848
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934571"
---
# <a name="concepts-for-distributing-data-with-arc-enabled-postgresql-hyperscale-server-group"></a>使用 Arc 啟用的于 postgresql 超大規模伺服器群組散發資料的概念

本文說明重要的概念，這些概念對於充分利用 Azure Arc 啟用的于 postgresql 超大規模而言很重要。
以下連結的文章指向適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 所說明的概念。 這與 Azure Arc 啟用于 postgresql 超大規模的技術相同，因此適用相同的概念和觀點。

**兩者之間有何差異？**
- _適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus)_

這是 Postgres 資料庫引擎的超大規模形式規格，可作為 Azure 中的「資料庫即服務」 (PaaS) 。 它是由啟用超大規模體驗的 Citus 延伸模組所驅動。 這種形式的因素是，服務會在 Microsoft 資料中心內執行，並由 Microsoft 操作。

- _Azure Arc 啟用的于 postgresql 超大規模_

這是 Azure Arc 啟用的資料服務所提供之 Postgres 資料庫引擎的超大規模外型規格。 在此外型規格中，我們的客戶會提供裝載系統的基礎結構，並加以操作。

以下摘要說明 Azure Arc enabled 于 postgresql 超大規模的主要概念：

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="nodes-and-tables"></a>節點和資料表
請務必瞭解下列概念，以充分利用 Azure Arc 啟用的 Postgres 超大規模：
- Azure Arc enabled 于 postgresql 超大規模中的特製化 Postgres 節點：協調器與背景工作角色
- 資料表的類型：分散式資料表、參考資料表和本機資料表
- 碎片

如需詳細資訊，請參閱 [適用於 PostgreSQL 的 Azure 資料庫中的節點和資料表-超大規模 (Citus) ](../../postgresql/concepts-hyperscale-nodes.md)。 

## <a name="determine-the-application-type"></a>判斷應用程式類型
清楚地識別您所建立的應用程式類型是很重要的。 為何會這樣？ 因為在啟用 Azure Arc 的于 postgresql 超大規模伺服器群組上執行有效率的查詢，所以必須在伺服器之間正確散發資料表。 建議的散發會依應用程式類型及其查詢模式而有所不同。 在啟用 Azure Arc 的 Postgres 超大規模上，有很廣泛的應用程式可正常運作：
- 多租使用者應用程式
- Real-Time 應用程式

資料模型化的第一個步驟，是要識別哪些專案與您的應用程式更相近。

請參閱 [判斷應用程式類型](../../postgresql/concepts-hyperscale-app-type.md)的詳細資料。


## <a name="choose-a-distribution-column"></a>選擇散發資料行
為何選擇分散式資料行？

這是您將進行的其中一個最重要的模型化決策。 Azure Arc enabled 于 postgresql 超大規模會根據資料列的散發資料行值，將資料列儲存在分區中。 正確的選擇會將相關的資料一起分組在相同的實體節點上，以加快查詢速度並加入所有 SQL 功能的支援。 不正確的選擇會讓系統的執行速度變慢，而且不支援跨節點的所有 SQL 功能。 本文提供兩個最常見超大規模案例的散發資料行秘訣。

請參閱 [選擇散發資料行](../../postgresql/concepts-hyperscale-choose-distribution-column.md)的詳細資料。


## <a name="table-colocation"></a>資料表共置

共置是關於將相關的資訊一起儲存在相同的節點上。 當所有必要的資料都可供使用時，如果沒有任何網路流量，查詢就可以快速完成。 在不同的節點上共置相關資料，可讓查詢在每個節點上以平行方式有效率地執行。

請參閱 [資料表共置](../../postgresql/concepts-hyperscale-colocation.md)的詳細資料。


## <a name="next-steps"></a>後續步驟
- [瞭解如何建立 Azure Arc 啟用的于 postgresql 超大規模](create-postgresql-hyperscale-server-group.md)
- [瞭解如何向外延展 Azure Arc 已在 Arc 資料控制器中建立的于 postgresql 超大規模伺服器群組](scale-out-postgresql-hyperscale-server-group.md)
- [瞭解 Azure Arc 啟用的資料服務](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [瞭解 Azure Arc](https://aka.ms/azurearc)

