---
title: 效能最佳作法-適用於 MySQL 的 Azure 資料庫
description: 本文說明監視和微調適用於 MySQL 的 Azure 資料庫效能的最佳做法。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 30176e2df850e6d2794ab9c1542bcb6a89d8f89f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880401"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>適用於 MySQL 的 Azure 資料庫-單一伺服器的最佳效能最佳做法

瞭解在使用適用於 MySQL 的 Azure 資料庫單一伺服器時取得最佳效能的最佳做法。 當我們將新功能新增至平臺時，我們會繼續調整本節中詳述的最佳作法。

## <a name="physical-proximity"></a>實體相近

 請務必將應用程式和資料庫部署在相同的區域中。 開始任何效能效能評定執行之前的快速檢查，是使用簡單的 SELECT 1 查詢來判斷用戶端與資料庫之間的網路延遲。 

## <a name="accelerated-networking"></a>加速網路

如果您使用 Azure 虛擬機器、Azure Kubernetes 或應用程式服務，請使用應用程式伺服器的加速網路。 加速網路可以對 VM 啟用單一根目錄 I/O 虛擬化 (SR-IOV)，大幅提升其網路效能。 這個高效能路徑會略過資料路徑的主機，進而減少延遲、抖動和 CPU 使用率，供支援的 VM 類型中最嚴苛的網路工作負載使用。

## <a name="connection-efficiency"></a>連接效率

建立新的連接一律是昂貴且耗時的工作。 當應用程式要求資料庫連接時，它會排定現有閒置資料庫連接的配置，而不是建立新的連接。  以下是適用于良好連接作法的一些選項：

- **ProxySQL**：使用 [ProxySQL](https://proxysql.com/) ，提供內建的連線共用，並在應用程式程式碼的任何變更時，視需要將 [工作負載平衡](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) 至多個讀取複本。

- **Heimdall Data proxy**：或者，您也可以利用 Heimdall Data proxy，也就是廠商中立的專屬 Proxy 解決方案。 它支援使用複寫延遲偵測的查詢快取和讀取/寫入分割。 您也可以參閱如何 [使用 Heimdall proxy 加速 MySQL 效能](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349)。  

- **持續性或 Long-Lived 連接**：如果您的應用程式具有較短的交易或查詢（執行時間 < 5-10 毫秒），則以持續性連接取代簡短的連接。 以持續連線取代簡短的連接只需要對程式碼進行輕微的變更，但在許多一般的應用程式案例中，其效能都有顯著的影響。 當交易完成時，請務必設定 timeout 或 close 連接。

- **複本**：如果您使用複本，請使用 [ProxySQL](https://proxysql.com/) 來平衡主伺服器與可讀取次要複本伺服器之間的負載。 瞭解 [如何設定 ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847)。

## <a name="data-import-configurations"></a>資料匯入設定

- 您可以在開始資料匯入作業之前，暫時將實例調整為較高的 SKU 大小，然後在匯入成功時將其向下調整。
- 您可以使用 [Azure 資料庫移轉服務 (DMS) ](https://datamigration.microsoft.com/) 進行線上或離線遷移，以盡可能縮短停機時間來匯入您的資料。 

## <a name="azure-database-for-mysql-memory-recommendations"></a>適用於 MySQL 的 Azure 資料庫的記憶體建議

適用於 MySQL 的 Azure 資料庫的效能最佳作法是配置足夠的 RAM，讓您的工作集幾乎完全位於記憶體中。 

- 使用[MySQL 伺服器的計量](./concepts-monitoring.md)，檢查是否使用記憶體百分比達到[限制](./concepts-pricing-tiers.md)。 
- 針對這類號碼設定警示，以確保伺服器達到限制時，您可以採取提示動作來修正此問題。 根據所定義的限制，檢查是否相應增加資料庫 SKU （以較高的計算大小或更好的定價層），這會導致效能大幅提升。 
- 相應增加，直到您的效能數位在調整作業之後不再大幅下降為止。 如需監視資料庫實例計量的詳細資訊，請參閱 [MYSQL DB 計量](./concepts-monitoring.md#metrics)。

## <a name="next-steps"></a>後續步驟

- [使用適用於 MySQL 的 Azure 資料庫的伺服器作業最佳做法](concept-operation-excellence-best-practices.md) <br/>
- [監視適用於 MySQL 的 Azure 資料庫的最佳做法](concept-monitoring-best-practices.md)<br/>
- [開始使用適用於 MySQL 的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>