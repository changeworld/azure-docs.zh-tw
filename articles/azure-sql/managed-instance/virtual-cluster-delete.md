---
title: 刪除 SQL 受控執行個體的受管理的實例之後，刪除子網
description: 瞭解如何在刪除 Azure SQL 受控執行個體受控實例之後刪除 Azure 虛擬網路。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 07534b834fb0b882d3c37b670f93d2a00296a248
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323255"
---
# <a name="delete-a-subnet-after-deleting-a-managed-instance-of-sql-managed-instance"></a>刪除 SQL 受控執行個體的受管理的實例之後，刪除子網
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文提供如何在刪除 Azure SQL 受控執行個體的最後一個受管理的實例之後，手動刪除子網的指導方針。

受控實例會部署到 [虛擬叢集](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)中。 每個虛擬叢集都與子網相關聯。 在最後一次刪除實例之後，虛擬叢集會依設計保留12小時，讓您可以更快速地在相同的子網中建立受控實例。 保留空的虛擬叢集不需付費。 在這段期間，與虛擬叢集相關聯的子網路將無法刪除。

如果您不想要等待12小時，而且想要更快刪除虛擬叢集及其子網，您可以手動進行。 使用 Azure 入口網站或虛擬叢集 API 手動刪除虛擬叢集。

> [!IMPORTANT]
> - 虛擬叢集不應包含任何受控實例，刪除作業才會成功。 
> - 刪除虛擬叢集是長時間執行的作業，持續約1.5 小時 (請參閱 [受控實例管理作業](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 以取得最新的虛擬叢集刪除時間) 。 在此程式完成之前，虛擬叢集仍會顯示在入口網站中。

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>從 Azure 入口網站中刪除虛擬叢集

若要使用 Azure 入口網站刪除虛擬叢集，請搜尋虛擬叢集資源。

![Azure 入口網站的螢幕擷取畫面，其中已反白顯示搜尋方塊](./media/virtual-cluster-delete/virtual-clusters-search.png)

找出您想要刪除的虛擬叢集之後，請選取 [此資源]，然後選取 [ **刪除**]。 系統會提示您確認是否刪除虛擬叢集。

![Azure 入口網站虛擬叢集儀表板的螢幕擷取畫面，其中已反白顯示 [刪除] 選項](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Azure 入口網站通知會顯示確認已成功提交刪除虛擬叢集的要求。 刪除作業本身將會持續約1.5 小時，在這段期間，虛擬叢集仍會顯示在入口網站中。 程式完成後，將不再顯示虛擬叢集，並且會釋出與其相關聯的子網以供重複使用。

> [!TIP]
> 如果虛擬叢集中沒有顯示受控實例，而且您無法刪除虛擬叢集，請確定您沒有正在進行中的實例部署。 這包括已啟動和已取消的部署仍在進行中。 這是因為這些作業仍會使用虛擬叢集，而將其鎖定為無法刪除。 檢查部署實例之資源群組的 [ **部署** ] 索引標籤，將會指出正在進行任何部署。 在此情況下，請等候部署完成、刪除受控實例，然後刪除虛擬叢集。

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>使用 API 刪除虛擬叢集

若要透過 API 刪除虛擬叢集，請使用 [虛擬叢集 delete 方法](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)中指定的 URI 參數。

## <a name="next-steps"></a>後續步驟

- 如需總覽，請參閱 [什麼是 AZURE SQL 受控執行個體？](sql-managed-instance-paas-overview.md)。
- 深入瞭解 [SQL 受控執行個體的連接架構](connectivity-architecture-overview.md)。
- 瞭解如何 [針對 SQL 受控執行個體修改現有的虛擬網路](vnet-existing-add-subnet.md)。
- 如需說明如何建立虛擬網路、建立受控實例，以及從資料庫備份還原資料庫的教學課程，請參閱 [建立受控實例](instance-create-quickstart.md)。
- 針對 DNS 問題，請參閱 [設定自訂 DNS](custom-dns-configure.md)。
