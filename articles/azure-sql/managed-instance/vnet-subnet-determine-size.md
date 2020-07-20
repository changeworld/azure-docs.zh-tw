---
title: 判斷所需的子網大小 & 範圍
titleSuffix: Azure SQL Managed Instance
description: 本主題說明如何計算將部署 Azure SQL 受控執行個體的子網大小。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 562766ada8fb9a2620fa83875dc98d02ab752d95
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85338558"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>判斷 Azure SQL 受控執行個體的必要子網大小 & 範圍
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 受控執行個體必須部署在 Azure[虛擬網路（VNet）](../../virtual-network/virtual-networks-overview.md)內。

可在 VNet 的子網中部署的受控實例數目，取決於子網的大小（子網範圍）。

當您建立受控實例時，Azure 會根據您在布建期間選取的層級，配置一些虛擬機器。 因為這些虛擬機器與您的子網路相關聯，所以它們需要 IP 位址。 為了確保正常作業和服務維護期間的高可用性，Azure 可能會配置額外的虛擬機器。 因此，子網中所需的 IP 位址數目會大於該子網中受管理的實例數目。

根據設計，受控實例在子網中需要至少32個 IP 位址。 因此，定義子網 IP 範圍時，您可以使用/27 的最小子網路遮罩。 建議您仔細規劃受控實例部署的子網大小。 在規劃期間應納入考慮的輸入如下：

- 受管理的實例數目，包括下列實例參數：
  - 服務層
  - 硬體世代
  - 虛擬核心數目
- 規劃相應增加/減少或變更服務層級

> [!IMPORTANT]
> 具有16個 IP 位址（子網路遮罩/28）的子網大小將允許在其中部署受控實例，但它只能用於部署用於評估的單一實例或在開發/測試案例中，而不會執行調整作業。

## <a name="determine-subnet-size"></a>決定子網路大小

根據未來的實例部署和調整需求來調整子網的大小。 下列參數可協助您形成計算：

- Azure 會在子網路中針對自己的需求使用 5 個 IP 位址
- 每個虛擬叢集會配置額外的位址數目 
- 每個受控實例都會使用取決於定價層和硬體世代的位址數目

> [!IMPORTANT]
> 如果子網中有任何資源存在，就無法變更子網位址範圍。 也不可能將受控實例從一個子網移至另一個。 可能的話，請考慮使用更大的子網，而不是較小的，以避免未來發生問題。

GP = 一般用途;BC = 業務關鍵;VC = 虛擬叢集

| **硬體 gen** | **定價層** | **Azure 使用情況** | **VC 使用方式** | **實例使用量** | **總數*** |
| --- | --- | --- | --- | --- | --- |
| Gen4 | GP | 5 | 1 | 5 | 11 |
| Gen4 | BC | 5 | 1 | 5 | 11 |
| Gen5 | GP | 5 | 6 | 3 | 14 |
| Gen5 | BC | 5 | 6 | 5 | 16 |

  \*[資料行總計] 顯示在子網中部署某個實例時，所要採取的位址數目。 子網中的每個額外實例會加入以實例使用量資料行表示的位址數目。 使用 Azure usage 資料行表示的位址會在多個虛擬叢集之間共用，而以 VC usage 資料行表示的位址則會在放置於該虛擬叢集中的實例之間共用。

更新作業通常需要重設虛擬叢集。 在某些情況下，更新作業將需要建立虛擬叢集（如需詳細資訊，請參閱檢查[管理作業一文](sql-managed-instance-paas-overview.md#management-operations)）。 在建立虛擬叢集時，所需的其他位址數目等於 VC usage 資料行所代表的位址數目，加上與放置在該虛擬叢集（實例使用方式資料行）中的實例所需的位址。

**範例 1**：您計畫有一個一般用途的受控實例（第4代硬體）和一個商務關鍵性受控實例（第5代硬體）。 這表示您至少需要 5 + 1 + 1 * 5 + 6 + 1 * 5 = 22 個 IP 位址，才能進行部署。 當 IP 範圍定義為2的乘冪時，您的子網會要求此部署的最小 IP 範圍為32（2 ^ 5）。<br><br>
如上所述，在某些情況下，更新作業會需要建立虛擬叢集。 這表示，舉例來說，在需要建立虛擬叢集的第5代業務關鍵受控實例更新時，您必須有額外的 6 + 5 = 11 個可用的 IP 位址。 因為您已經使用了32個位址的22個，所以此作業沒有可用的位址。 因此，您必須保留子網路遮罩為/26 （64位址）的子網。

**範例 2**：您計畫要有三個一般用途（第5代硬體）和兩個業務關鍵受控實例（第5代硬體）放在相同的子網中。 這表示您需要 5 + 6 + 3 * 3 + 2 * 5 = 30 個 IP 位址。 因此，您必須使用子網路遮罩（/26）來保留子網。 選取的子網路遮罩/27 會導致剩餘的位址數目為2（32–30），這會防止所有實例的更新作業，因為子網中需要有額外的位址，才能執行實例調整。

**範例 3**：您計畫有一個一般用途的受控實例（第5代硬體），並在一段時間後執行虛擬核心更新作業。 這表示您需要 5 + 6 + 1 * 3 + 3 = 17 個 IP 位址。 因為 IP 範圍是以 2 的乘冪定義，所以您需要 32 (2^5) 個 IP 位址的 IP 範圍。 因此，您需要保留子網路遮罩為 /27 的子網路。

### <a name="address-requirements-for-update-scenarios"></a>更新案例的解決需求

在調整作業期間，暫時需要額外的 IP 容量，視定價層和硬體產生而定

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

  \*第4代硬體即將淘汰，不再適用于新的部署。 將硬體世代從第4代更新為第5代，以利用第5代硬體世代特有的功能。

## <a name="next-steps"></a>後續步驟

- 如需總覽，請參閱[什麼是 AZURE SQL 受控執行個體？](sql-managed-instance-paas-overview.md)。
- 深入瞭解[SQL 受控執行個體的連接架構](connectivity-architecture-overview.md)。
- 請參閱如何[建立 VNet，您將在其中部署 SQL 受控執行個體](virtual-network-subnet-create-arm-template.md)。
- 如需 DNS 問題，請參閱[設定自訂 DNS](custom-dns-configure.md)。
