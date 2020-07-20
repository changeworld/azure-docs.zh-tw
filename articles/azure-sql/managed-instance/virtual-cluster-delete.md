---
title: 刪除 SQL 受控執行個體的受管理的實例之後，刪除子網
description: 瞭解如何在刪除 Azure SQL 受控執行個體的受控實例之後刪除 Azure 虛擬網路。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 87e73ed25ac33777c19c561223f0361d589282b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84696995"
---
# <a name="delete-a-subnet-after-deleting-a-managed-instance-of-sql-managed-instance"></a>刪除 SQL 受控執行個體的受管理的實例之後，刪除子網
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文提供如何在刪除位於其上的 Azure SQL 受控執行個體最後一個受管理的實例之後，手動刪除子網的指導方針。

受控實例會部署到[虛擬叢集](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)。 每個虛擬叢集會與子網建立關聯。 虛擬叢集會在最後一個實例刪除之後的12個小時後持續保留，讓您更快速地在相同的子網中建立受控實例。 保留空的虛擬叢集不會收費。 在這段期間，與虛擬叢集相關聯的子網路將無法刪除。

如果您不想等待12小時，而且想要更快刪除虛擬叢集及其子網，您可以手動執行此動作。 使用 Azure 入口網站或虛擬叢集 API，手動刪除虛擬叢集。

> [!IMPORTANT]
> - 虛擬叢集不應包含任何受控實例，因此無法成功刪除。 
> - 刪除虛擬叢集是長時間執行的作業，持續大約1.5 小時（請參閱[受控實例管理作業](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations)，以取得最新的虛擬叢集刪除時間）。 在此程式完成之前，虛擬叢集仍然會顯示在入口網站中。

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>從 Azure 入口網站刪除虛擬叢集

若要使用 Azure 入口網站來刪除虛擬叢集，請搜尋虛擬叢集資源。

![Azure 入口網站的螢幕擷取畫面，並反白顯示搜尋方塊](./media/virtual-cluster-delete/virtual-clusters-search.png)

找到您想要刪除的虛擬叢集之後，請選取 [此資源]，然後選取 [**刪除**]。 系統會提示您確認是否刪除虛擬叢集。

![[Azure 入口網站虛擬叢集] 儀表板的螢幕擷取畫面，其中已反白顯示 [刪除] 選項](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Azure 入口網站通知會向您顯示確認刪除虛擬叢集的要求已成功提交。 刪除作業本身會持續大約1.5 小時，在此期間，虛擬叢集仍然會顯示在入口網站中。 完成此程式之後，將不再顯示虛擬叢集，而且會釋放與它相關聯的子網以供重複使用。

> [!TIP]
> 如果虛擬叢集中沒有顯示任何受控實例，而且您無法刪除虛擬叢集，請確定您沒有進行中的實例部署。 這包括已啟動和已取消的部署仍在進行中。 這是因為這些作業仍會使用虛擬叢集，並將其鎖定為刪除。 查看已部署實例之資源群組的 [**部署**] 索引標籤，將會指出任何進行中的部署。 在此情況下，請等候部署完成、刪除受控實例，然後再刪除虛擬叢集。

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>使用 API 刪除虛擬叢集

若要透過 API 刪除虛擬叢集，請使用[虛擬叢集刪除方法](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)中指定的 URI 參數。

## <a name="next-steps"></a>後續步驟

- 如需總覽，請參閱[什麼是 AZURE SQL 受控執行個體？](sql-managed-instance-paas-overview.md)。
- 深入瞭解[SQL 受控執行個體中的連接架構](connectivity-architecture-overview.md)。
- 瞭解如何[修改 SQL 受控執行個體的現有虛擬網路](vnet-existing-add-subnet.md)。
- 如需示範如何建立虛擬網路、建立受控實例，以及從資料庫備份還原資料庫的教學課程，請參閱[建立受控實例](instance-create-quickstart.md)。
- 如需 DNS 問題，請參閱[設定自訂 DNS](custom-dns-configure.md)。
