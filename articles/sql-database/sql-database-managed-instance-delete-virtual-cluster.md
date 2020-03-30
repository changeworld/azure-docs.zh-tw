---
title: 刪除託管實例後刪除子網
description: 瞭解如何在刪除 Azure SQL 資料庫託管實例後刪除 Azure 虛擬網路。
services: sql-database
ms.service: sql-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496d67a73207fd17182c31c5adad25c1fbe60c4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820471"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>刪除 Azure SQL 資料庫託管實例後刪除子網

本文提供有關在刪除駐留在該子網的最後一個 Azure SQL 資料庫託管實例後如何手動刪除子網的指南。

託管實例部署到[虛擬叢集](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)中。 每個虛擬叢集都與子網相關聯。 虛擬叢集在最後一個實例刪除後按設計保留 12 小時，以便可以更快地在同一子網路中創建託管實例。 保留空虛擬叢集不收取任何費用。 在這段期間，與虛擬叢集相關聯的子網路將無法刪除。

如果您不想等待 12 小時，並且希望更快地刪除虛擬叢集及其子網，則可以手動刪除虛擬叢集及其子網。 使用 Azure 門戶或虛擬叢集 API 手動刪除虛擬叢集。

> [!IMPORTANT]
> - 虛擬叢集不應包含任何用於成功刪除的託管實例。 
> - 刪除虛擬叢集是一項長時間運行的操作，持續約 1.5 小時（請參閱當前虛擬叢集刪除時間的[託管實例管理操作](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations)），在此期間，虛擬叢集在門戶中仍將可見，直到此過程完成。

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>從 Azure 門戶中刪除虛擬叢集

要使用 Azure 門戶刪除虛擬叢集，請搜索虛擬叢集資源。

![Azure 門戶的螢幕截圖，突出顯示搜索框](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

找到要刪除的虛擬叢集後，選擇此資源，然後選擇 **"刪除**"。 系統將提示您確認虛擬叢集刪除。

![Azure 門戶虛擬叢集儀表板的螢幕截圖，突出顯示了"刪除"選項](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Azure 門戶通知將顯示一個確認，確認已成功提交刪除虛擬叢集的請求。 刪除操作本身將持續大約 1.5 小時，在此期間，虛擬叢集仍將在門戶中可見。 該過程完成後，虛擬叢集將不再可見，與其關聯的子網將被釋放以供重用。

> [!TIP]
> 如果虛擬叢集中未顯示託管實例，並且無法刪除虛擬叢集，請確保沒有正在進行的實例部署。 這包括仍在啟動和取消的部署。 這是因為這些操作仍將使用虛擬叢集來鎖定它，使其不刪除。 查看實例部署到的資源組的部署選項卡將指示正在進行的任何部署。 在這種情況下，等待部署完成，刪除託管實例，然後刪除虛擬叢集。

## <a name="delete-virtual-cluster-by-using-the-api"></a>使用 API 刪除虛擬叢集

要通過 API 刪除虛擬叢集，請使用[虛擬叢集刪除方法](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)中指定的 URI 參數。

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱[受控執行個體是什麼](sql-database-managed-instance.md)。
- 深入了解[受控執行個體連線架構](sql-database-managed-instance-connectivity-architecture.md)。
- 了解如何[為受控執行個體修改現有的虛擬網路](sql-database-managed-instance-configure-vnet-subnet.md)。
- 如需示範如何建立虛擬網路、建立受控執行個體，以及從資料庫備份還原資料庫的教學課程，請參閱[建立 Azure SQL Database 受控執行個體](sql-database-managed-instance-get-started.md)。
- 有關 DNS 問題，請參閱[配置自訂 DNS](sql-database-managed-instance-custom-dns.md)。
