---
title: 常見問題-Azure 資料庫移轉服務
description: 關於使用 Azure 資料庫移轉服務來執行資料庫移轉的常見問題。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 200753c6458698d515b02252933e4fdf6d88df55
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019086"
---
# <a name="faq-about-using-azure-database-migration-service"></a>使用 Azure 資料庫移轉服務的常見問題

本文列出有關使用 Azure 資料庫移轉服務搭配相關答案的常見問題。

## <a name="overview"></a>概觀

**問。什麼是 Azure 資料庫移轉服務？**
Azure 資料庫移轉服務是一個完全受控的服務，其設計目的是要讓多個資料庫來源順暢地從多個資料庫來源遷移到 Azure 資料平臺。 服務目前已正式運作，持續進行開發工作著重于：

* 可靠性和效能。
* 反覆新增來源目標組。
* 持續投資無衝突的移轉。

**問。Azure 資料庫移轉服務目前支援何種來源/目標配對？**
服務目前支援各種來源/目標配對或遷移案例。 如需每個可用移轉案例狀態的完整清單，請參閱 [Azure 資料庫移轉服務支援的移轉案例狀態](./resource-scenario-status.md)一文。

其他遷移案例仍處於預覽狀態，需要透過 DMS 預覽網站提交提名。 如需預覽版案例的完整清單，以及註冊參與這些供應專案的其中一項，請參閱 [DMS 預覽網站](https://aka.ms/dms-preview/)。

**問。Azure 資料庫移轉服務支援作為來源的 SQL Server 版本為何？**
從 SQL Server 進行遷移時，Azure 資料庫移轉服務的支援來源是 SQL Server 2005 到 SQL Server 2019。

**問：使用 Azure 資料庫移轉服務時，離線和線上遷移之間有何差異？**
您可以使用 Azure 資料庫移轉服務來執行離線和線上遷移。 在 *離線* 遷移的情況下，應用程式會在開始遷移時啟動。 透過 *線上* 遷移，停機時間僅限於遷移結束時所需的時間。 建議您先測試離線移轉來決定停機時間是否在容忍範圍內；如果無法容忍，則請進行線上移轉。

> [!NOTE]
> 若要使用「Azure 資料庫移轉服務」來執行線上移轉，必須根據「進階」定價層建立執行個體。 如需詳細資訊，請參閱 Azure 資料庫移轉服務的[定價](https://azure.microsoft.com/pricing/details/database-migration/)頁面。

**問。Azure 資料庫移轉服務與其他 Microsoft 資料庫移轉工具（例如資料庫 Migration Assistant (DMA) 或 SQL Server 移轉小幫手 (SSMA) ）有何不同？**
Azure 資料庫移轉服務是將資料庫移轉至大規模 Microsoft Azure 的慣用方法。 如需有關 Azure 資料庫移轉服務如何與其他 Microsoft 資料庫移轉工具相較之下的詳細資訊，以及在各種案例中使用此服務的建議，請參閱將 [Microsoft 的資料庫移轉工具和服務區分](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529)的 blog 張貼。

**問。Azure 資料庫移轉服務與 Azure Migrate 供應專案有何不同？**
Azure Migrate 有助於將內部部署虛擬機器遷移至 Azure IaaS。 此服務會評估移轉適用性，以及如何根據效能來調整大小，並估計在 Azure 中執行內部部署機器的成本。 Azure Migrate 可用來將內部部署的 VM 型工作負載隨即移轉到 Azure IaaS VM。 不過，不同于 Azure 資料庫移轉服務，Azure Migrate 不是適用于 Azure PaaS 關係資料庫平臺（例如 Azure SQL Database 或 Azure SQL 受控執行個體）的特殊資料庫移轉服務供應專案。

**問。資料庫移轉服務會儲存客戶資料嗎？**
不會。 資料庫移轉服務不會儲存客戶資料。

## <a name="setup"></a>安裝程式

**問。使用 Azure 資料庫移轉服務的必要條件為何？**
有幾個必要條件，以確保在執行資料庫移轉時，Azure 資料庫移轉服務可順暢地執行。 某些必要條件會套用到服務所支援的所有案例 (來源目標組)，而有些必要條件則是特定案例獨有的。

Azure 資料庫移轉服務必要條件在所有支援的移轉案例中都是通用的，包含以下需求：

* 使用 Azure Resource Manager 部署模型建立 Azure 資料庫移轉服務的 Microsoft Azure 虛擬網路，以使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 為您的內部部署來源伺服器提供站對站連線能力。
* 確定您的虛擬網路網路安全性群組規則不會封鎖下列通訊埠443、53、5671-5672、9350-9354、445、12000。 如需虛擬網路 NSG 流量篩選的詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](../virtual-network/virtual-network-vnet-plan-design-arm.md)。
* 使用來源資料庫前面的防火牆應用裝置時，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取來源資料庫，以進行移轉。

如需使用 Azure 資料庫移轉服務來競爭特定遷移案例所需的所有必要條件清單，請參閱 docs.microsoft.com 上 Azure 資料庫移轉服務 [檔](./dms-overview.md) 中的相關教學課程。

**問。如何? 尋找 Azure 資料庫移轉服務的 IP 位址，讓我可以針對用來存取源資料庫以進行遷移的防火牆規則建立允許清單嗎？**
您可能必須新增防火牆規則，以允許 Azure 資料庫移轉服務存取您的源資料庫以進行遷移。 服務的 IP 位址是動態的，但如果您使用 ExpressRoute，則會由公司網路私下指派此位址。 若要識別適當的 IP 位址，最簡單的方式就是查看與您布建的 Azure 資料庫移轉服務資源相同的資源群組，以找出相關聯的網路介面。 網路介面資源的名稱通常會以 NIC 前置詞作為開頭，後面接著獨特字元和數字序列，例如 NIC-jj6tnztnmarpsskr82rbndyp。 藉由選取此網路介面資源，您即可在 Azure 入口網站的 [資源概觀] 頁面上，看到必須包含在允許清單中的 IP 位址。

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

**問。如何? 設定 Microsoft Azure 虛擬網路？**
雖然有多個 Microsoft 教學課程可逐步引導您完成設定虛擬網路的程式，但官方檔會出現在 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)一文中。

## <a name="usage"></a>使用方式

**問。使用 Azure 資料庫移轉服務來執行資料庫移轉所需步驟的摘要為何？**
在典型的簡單資料庫移轉期間，您必須：

1. 建立目標資料庫。
2.  (的) 來評定您的源資料庫。
    * 針對同質遷移，使用 [DMA](https://www.microsoft.com/download/details.aspx?id=53595)來評估您現有的資料庫 () 。
    * 針對從競爭來源)  (的異類遷移，請評估您現有的資料庫 (以 [SSMA](/sql/ssma/sql-server-migration-assistant)) 。 您也可以使用 SSMA 來轉換資料庫物件，並將架構遷移至目標平臺。
3. 建立 Azure 資料庫移轉服務的執行個體。
4. 建立指定源資料庫 (s) 、目標資料庫 (s) 以及要遷移之資料表的遷移專案。
5. 開始完整載入。
6. 挑選後續驗證。
7. 將生產環境手動轉換至新的雲端型資料庫。

## <a name="troubleshooting-and-optimization"></a>疑難排解和優化

**問。我正在設定 DMS 中的遷移專案，而且我無法連接到源資料庫。我該怎麼做？**
如果您在進行遷移時無法連線到您的源資料庫系統，請在您用來設定 DMS 實例的虛擬網路中建立虛擬機器。 在虛擬機器中，您應該能夠執行連接測試，例如使用 UDL 檔案來測試連線，以 SQL Server 或下載 Robo 3T 來測試 MongoDB 連接。 如果連接測試成功，您就不應該有連接至源資料庫的問題。 如果連線測試失敗，請洽詢您的網路系統管理員。

**問。為什麼我的 Azure 資料庫移轉服務無法使用或停止？**
如果使用者明確地停止 Azure 資料庫移轉服務 (DMS) 或服務在24小時內處於非使用中狀態，則服務會處於停止或自動暫停狀態。 在每個情況下，服務都將無法使用並處於停止的狀態。  若要繼續使用中的移轉，請重新啟動服務。

**問。是否有任何建議可將 Azure 資料庫移轉服務的效能優化？**
若要提升使用服務來移轉資料庫的速度，您可以執行幾項工作：

* 在建立服務執行個體時，請使用多重 CPU 一般用途定價層，以讓服務充分利用多個 vCPU 來進行平行處理，提升資料轉送速度。
* 在資料移轉作業期間，暫時將 Azure SQL Database 目標執行個體相應增加為進階層 SKU，讓使用較低層級 SKU 時可能會影響資料轉送活動的 Azure SQL Database 節流減到最少。

## <a name="next-steps"></a>後續步驟

如需 Azure 資料庫移轉服務和區域可用性的概觀，請參閱[什麼是 Azure 資料庫移轉服務](dms-overview.md)一文。
