---
title: 判斷所需的子網大小 & 範圍
titleSuffix: Azure SQL Managed Instance
description: 本主題說明如何計算將部署 Azure SQL 受控執行個體的子網大小。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 02/22/2019
ms.openlocfilehash: 156a4c74eea24b20c28df88be85cb32c0ebe2981
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617633"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>判斷 Azure SQL 受控執行個體所需的子網大小 & 範圍
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 受控執行個體必須部署在 Azure [虛擬網路 (VNet) ](../../virtual-network/virtual-networks-overview.md)內。

可以在 VNet 的子網中部署的受控實例數目，取決於子網的大小 (子網範圍) 。

當您建立受控實例時，Azure 會根據您在布建期間選取的層級，配置數部虛擬機器。 因為這些虛擬機器與您的子網路相關聯，所以它們需要 IP 位址。 為了確保正常作業和服務維護期間的高可用性，Azure 可能會配置額外的虛擬機器。 因此，子網中所需的 IP 位址數目大於該子網中的受控實例數目。

根據設計，受控執行個體在子網路中至少需要 32 個 IP 位址。 因此，在定義子網路 IP 範圍時，您可以使用最小子網路遮罩 /27。 建議您仔細規劃受控執行個體部署的子網路大小。 在規劃期間應考慮的輸入如下：

- 受管理的實例數目，包括下列實例參數：
  - 服務層級
  - 硬體世代
  - 虛擬核心數目
- 規劃擴大/縮小或變更服務層級

> [!IMPORTANT]
> 具有16個 IP 位址的子網大小 (子網路遮罩/28) 可讓您在其中部署受控實例，但它只能用來部署用於評估的單一實例，或是在開發/測試案例中，不會執行調整作業。

## <a name="determine-subnet-size"></a>決定子網路大小

根據未來的實例部署和調整需求來調整子網的大小。 下列參數可協助您形成計算：

- Azure 會在子網路中針對自己的需求使用 5 個 IP 位址
- 每個虛擬叢集會配置額外的位址數目 
- 每個受控實例都會使用取決於定價層和硬體世代的位址數目

> [!IMPORTANT]
> 如果子網中有任何資源，則不可能變更子網位址範圍。 您也無法將受控實例從一個子網移至另一個子網。 可能的話，請考慮使用較大的子網而不是較小的子網，以防止未來發生問題。

GP = 一般用途;BC = 商務關鍵性;VC = 虛擬叢集

| **硬體 gen** | **定價層** | **Azure 使用情況** | **VC 使用方式** | **實例使用方式** | **總*** |
| --- | --- | --- | --- | --- | --- |
| Gen4 | GP | 5 | 1 | 5 | 11 |
| Gen4 | BC | 5 | 1 | 5 | 11 |
| Gen5 | GP | 5 | 6 | 3 | 14 |
| Gen5 | BC | 5 | 6 | 5 | 16 |

  \* [資料行總計] 會顯示在子網中部署一個實例時所要採取的位址數目。 子網中的每個額外實例都會新增以 [實例使用量] 資料行表示的位址數目。 以 Azure 使用量資料行表示的位址會在多個虛擬叢集之間共用，而以 VC usage 資料行表示的位址則會在放置於該虛擬叢集的實例之間共用。

更新作業通常需要調整虛擬叢集大小。 在某些情況下，更新作業將需要建立虛擬叢集 (如需詳細資料，請檢查 [管理作業文章](sql-managed-instance-paas-overview.md#management-operations)) 。 在建立虛擬叢集的案例中，所需的額外位址數目等於 [VC usage] 資料行所代表的位址數目，以及放置於該虛擬叢集中的實例所需的位址， (實例使用量] 資料行) 。

**範例 1**：您計畫要有一個一般用途受控實例 (第4代硬體) 和一個商務關鍵受控實例 (第5代硬體) 。 這表示您需要至少 5 + 1 + 1 * 5 + 6 + 1 * 5 = 22 個 IP 位址，才能進行部署。 當 IP 範圍定義為2的乘冪時，您的子網需要此部署的最小 IP 範圍 32 (2 ^ 5) 。<br><br>
如先前所述，在某些情況下，更新作業將需要建立虛擬叢集。 這表示，在需要建立虛擬叢集的第5代業務關鍵受控實例更新時，您必須有額外的 6 + 5 = 11 個 IP 位址可供使用。 因為您已經在使用32位址的22，所以沒有此作業的可用位址。 因此，您需要保留子網路遮罩為/26 (64 位址) 的子網。

**範例 2**：您計畫有三個一般用途 (第5代硬體) 和兩個業務關鍵受控實例 (第5代硬體) 放置在相同的子網中。 這表示您需要 5 + 6 + 3 * 3 + 2 * 5 = 30 個 IP 位址。 因此，您需要保留子網路遮罩為/26 的子網。 選取/27 的子網路遮罩會導致剩餘的位址數目為 2 (32 – 30) ，這會防止所有實例的更新作業，因為子網中需要額外的位址，才能執行實例調整。

**範例 3**：您計畫有一個一般用途的受控實例 (第5代硬體) 並隨時執行虛擬核心更新作業。 這表示您需要 5 + 6 + 1 * 3 + 3 = 17 個 IP 位址。 因為 IP 範圍是以 2 的乘冪定義，所以您需要 32 (2^5) 個 IP 位址的 IP 範圍。 因此，您需要保留子網路遮罩為 /27 的子網路。

### <a name="address-requirements-for-update-scenarios"></a>更新案例的位址需求

在調整作業期間，暫時需要依賴定價層和硬體世代的額外 IP 容量

| **硬體 gen** | **定價層** | **案例** | **其他位址*** |
| --- | --- | --- | --- |
| Gen4 | GP 或 BC | 調整虛擬核心 | 5 |
| Gen4 | GP 或 BC | 調整儲存體 | 5 |
| Gen4 | GP 或 BC | 從 GP 切換至 BC 或 BC 至 GP | 5 |
| Gen4 | GP | 切換至第5代 * | 9 |
| Gen4 | BC | 切換至第5代 * | 11 |
| Gen5 | GP | 調整虛擬核心 | 3 |
| Gen5 | GP | 調整儲存體 | 0 |
| Gen5 | GP | 切換至 BC | 5 |
| Gen5 | BC | 調整虛擬核心 | 5 |
| Gen5 | BC | 調整儲存體 | 5 |
| Gen5 | BC | 切換至 GP | 3 |

  \* 第4代硬體即將推出，且不再適用于新的部署。 將硬體世代從第4代更新為第5代，以充分利用第5代硬體世代的特定功能。

## <a name="next-steps"></a>後續步驟

- 如需總覽，請參閱 [什麼是 AZURE SQL 受控執行個體？](sql-managed-instance-paas-overview.md)。
- 深入瞭解 [SQL 受控執行個體的連接架構](connectivity-architecture-overview.md)。
- 瞭解如何 [建立您將在其中部署 SQL 受控執行個體的 VNet](virtual-network-subnet-create-arm-template.md)。
- 針對 DNS 問題，請參閱 [設定自訂 DNS](custom-dns-configure.md)。
