---
title: 常見問題解答 - Azure 資料庫移轉服務
description: 有關使用 Azure 資料庫移轉服務執行資料庫移轉的常見問題。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: a664f12843585ac7524cf8d51aef156d15d32504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650976"
---
# <a name="faq-about-using-azure-database-migration-service"></a>有關使用 Azure 資料庫移轉服務的常見問題解答

本文列出了有關使用 Azure 資料庫移轉服務以及相關答案的常見問題。

## <a name="overview"></a>總覽

**問：什麼是 Azure 資料庫移轉服務？**
Azure 資料庫移轉服務是一個完全託管的服務，旨在以最少的停機時間從多個資料庫源無縫遷移到 Azure 資料平臺。 該服務目前處於通用性，正在進行的開發工作側重于：

* 可靠性和效能。
* 反覆新增來源目標組。
* 持續投資無衝突的移轉。

**問：Azure 資料庫移轉服務目前支援哪些源/目標對？**
該服務當前支援各種源/目標對或遷移方案。 如需每個可用移轉案例狀態的完整清單，請參閱 [Azure 資料庫移轉服務支援的移轉案例狀態](https://docs.microsoft.com/azure/dms/resource-scenario-status)一文。

其他遷移方案處於預覽狀態，需要通過 DMS 預覽網站提交提名。 有關預覽版中方案的完整清單並註冊以參與其中一個產品，請參閱[DMS 預覽網站](https://aka.ms/dms-preview/)。

**問：Azure 資料庫移轉服務支援哪些版本的 SQL Server 作為源？**
從 SQL Server 遷移時，Azure 資料庫移轉服務的支援源是 SQL Server 2005 到 SQL Server 2019。

**問：使用 Azure 資料庫移轉服務時，離線遷移和連線遷移之間的區別是什麼？**
可以使用 Azure 資料庫移轉服務執行離線和連線遷移。 使用*離線*遷移時，應用程式停機時間從遷移開始時開始。 使用*連線*遷移時，停機時間僅限於在遷移結束時縮短的時間。 建議您先測試離線移轉來決定停機時間是否在容忍範圍內；如果無法容忍，則請進行線上移轉。

> [!NOTE]
> 若要使用「Azure 資料庫移轉服務」來執行線上移轉，必須根據「進階」定價層建立執行個體。 有關詳細資訊，請參閱 Azure 資料庫移轉服務[定價](https://azure.microsoft.com/pricing/details/database-migration/)頁。

**問：Azure 資料庫移轉服務與其他 Microsoft 資料庫移轉工具（如資料庫移轉助理 （DMA） 或 SQL Server 遷移助理 （SSMA））相比如何？**
Azure 資料庫移轉服務是大規模資料庫移轉到 Microsoft Azure 的首選方法。 有關 Azure 資料庫移轉服務與其他 Microsoft 資料庫移轉工具的比較以及有關將服務用於各種方案的建議的更多詳細資訊，請參閱發佈["區分 Microsoft 資料庫移轉工具和服務"的](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529)博客。

**問：Azure 資料庫移轉服務與 Azure 遷移產品相比如何？**
Azure 遷移有助於將本地虛擬機器遷移到 Azure IaaS。 此服務會評估移轉適用性，以及如何根據效能來調整大小，並估計在 Azure 中執行內部部署機器的成本。 Azure Migrate 可用來將內部部署的 VM 型工作負載隨即移轉到 Azure IaaS VM。 但是，與 Azure 資料庫移轉服務不同，Azure 遷移不是 Azure PaaS 關係資料庫平臺（如 Azure SQL 資料庫或 Azure SQL 資料庫託管實例）的專用資料庫移轉服務。

## <a name="setup"></a>安裝程式

**問：使用 Azure 資料庫移轉服務的先決條件是什麼？**
執行資料庫移轉時，需要幾個先決條件來確保 Azure 資料庫移轉服務平穩運行。 某些必要條件會套用到服務所支援的所有案例 (來源目標組)，而有些必要條件則是特定案例獨有的。

Azure 資料庫移轉服務必要條件在所有支援的移轉案例中都是通用的，包含以下需求：

* 通過使用 Azure 資源管理器部署模型為 Azure 資料庫移轉服務創建 Microsoft Azure 虛擬網路，該模型通過使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)提供到本地源伺服器的網站到網站的連接。
* 確保您的虛擬網路網路安全性群組規則不會阻止以下通訊連接埠 443、53、9354、445、12000。 有關虛擬網路 NSG 流量篩選的更多詳細資訊，請參閱文章["使用網路安全性群組篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)"。
* 使用來源資料庫前面的防火牆應用裝置時，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取來源資料庫，以進行移轉。

有關使用 Azure 資料庫移轉服務競爭特定遷移方案所需的所有先決條件的清單，請參閱有關docs.microsoft.com的 Azure 資料庫移轉服務[文檔中](https://docs.microsoft.com/azure/dms/dms-overview)的相關教程。

**問：如何查找 Azure 資料庫移轉服務的 IP 位址，以便為用於訪問源資料庫進行遷移的防火牆規則創建允許清單？**
您可能需要添加防火牆規則，允許 Azure 資料庫移轉服務訪問源資料庫進行遷移。 服務的 IP 位址是動態的，但如果您使用的是 ExpressRoute，則此位址由公司網路私下分配。 標識適當 IP 位址的最簡單方法是在資源組中查找與預配的 Azure 資料庫移轉服務資源相同的資源組，以查找關聯的網路介面。 網路介面資源的名稱通常會以 NIC 前置詞作為開頭，後面接著獨特字元和數字序列，例如 NIC-jj6tnztnmarpsskr82rbndyp。 藉由選取此網路介面資源，您即可在 Azure 入口網站的 [資源概觀] 頁面上，看到必須包含在允許清單中的 IP 位址。

此外，您可能還需要在允許清單上包含 SQL Server 所接聽的連接埠來源。 根據預設，來源是連接埠 1433，但您也可以將來源 SQL Server 設定為接聽其他連接埠。 在此情況下，您也必須在允許清單上包含這些連接埠。 您可以使用動態管理檢視查詢來判斷 SQL Server 所接聽的連接埠：

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

您也可以藉由查詢 SQL Server 錯誤記錄來判斷 SQL Server 所接聽的連接埠：

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**問：如何設置 Microsoft Azure 虛擬網路？**
雖然多個 Microsoft 教程可以引導您完成設置虛擬網路的過程，但官方文檔將顯示在[文章 Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)中。

## <a name="usage"></a>使用量

**問：使用 Azure 資料庫移轉服務執行資料庫移轉所需的步驟摘要是什麼？**
在典型的簡單資料庫移轉期間，您必須：

1. 建立目標資料庫。
2. 評估源資料庫。
    * 對於同質遷移，請使用[DMA](https://www.microsoft.com/download/details.aspx?id=53595)評估現有資料庫。
    * 對於異構遷移（來自競爭源），使用[SSMA](https://aka.ms/get-ssma)評估現有資料庫。 您還可以使用 SSMA 轉換資料庫物件並將架構遷移到目標平臺。
3. 建立 Azure 資料庫移轉服務的執行個體。
4. 創建一個遷移專案，指定源資料庫、目標資料庫和要遷移的表。
5. 啟動滿載。
6. 挑選後續驗證。
7. 將生產環境手動轉換至新的雲端型資料庫。

## <a name="troubleshooting-and-optimization"></a>故障排除和優化

**問：我在 DMS 中設置遷移專案，並且很難連接到源資料庫。我該怎麼辦？**
如果在處理遷移時連接到源資料庫系統時遇到問題，請在虛擬網路中創建一個虛擬機器，以便您設置 DMS 實例。 在虛擬機器中，您應該能夠運行連接測試，例如使用 UDL 檔測試到 SQL Server 的連接或下載 Robo 3T 來測試 MongoDB 連接。 如果連接測試成功，則不應有連接到源資料庫的問題。 如果連接測試不成功，請與網路系統管理員聯繫。

**問：為什麼我的 Azure 資料庫移轉服務不可用或停止？**
如果使用者顯式停止 Azure 資料庫移轉服務 （DMS），或者該服務在 24 小時內處於非活動狀態，則服務將處於已停止或自動暫停狀態。 在每個情況下，服務都將無法使用並處於停止的狀態。  若要繼續使用中的移轉，請重新啟動服務。

**問：是否有任何優化 Azure 資料庫移轉服務性能的建議？**
若要提升使用服務來移轉資料庫的速度，您可以執行幾項工作：

* 在建立服務執行個體時，請使用多重 CPU 一般用途定價層，以讓服務充分利用多個 vCPU 來進行平行處理，提升資料轉送速度。
* 在資料移轉作業期間，暫時將 Azure SQL Database 目標執行個體相應增加為進階層 SKU，讓使用較低層級 SKU 時可能會影響資料轉送活動的 Azure SQL Database 節流減到最少。

## <a name="next-steps"></a>後續步驟

如需 Azure 資料庫移轉服務和區域可用性的概觀，請參閱[什麼是 Azure 資料庫移轉服務](dms-overview.md)一文。
