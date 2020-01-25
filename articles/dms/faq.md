---
title: 常見問題-Azure 資料庫移轉服務
description: 關於使用 Azure 資料庫移轉服務來執行資料庫移轉的常見問題。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 9fdbf3888b4302946fe20259a333842caa122836
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717784"
---
# <a name="faq-about-using-azure-database-migration-service"></a>使用 Azure 資料庫移轉服務的常見問題

本文列出有關搭配使用 Azure 資料庫移轉服務與相關解答的常見問題。

## <a name="overview"></a>概觀

**問：什麼是 Azure 資料庫移轉服務？**
Azure 資料庫移轉服務是完全受控的服務，其設計目的是要在停機時間最短的情況之下，從多個資料庫來源順暢地遷移至 Azure 資料平臺。 此服務目前為正式運作，持續進行中的開發工作著重于：

* 可靠性和效能。
* 反覆新增來源目標組。
* 持續投資無衝突的移轉。

**問： Azure 資料庫移轉服務目前支援哪些來源/目標配對？**
此服務目前支援各種來源/目標配對或遷移案例。 如需每個可用移轉案例狀態的完整清單，請參閱 [Azure 資料庫移轉服務支援的移轉案例狀態](https://docs.microsoft.com/azure/dms/resource-scenario-status)一文。

其他遷移案例處於預覽狀態，需要透過 DMS 預覽網站提交提名。 如需預覽中案例的完整清單，以及註冊以參與其中一項供應專案，請參閱[DMS 預覽網站](https://aka.ms/dms-preview/)。

**問： Azure 資料庫移轉服務支援作為來源的 SQL Server 版本為何？**
從 SQL Server 進行遷移時，Azure 資料庫移轉服務支援的來源是 SQL Server 2005 到 SQL Server 2019。

**問：使用 Azure 資料庫移轉服務時，離線和線上遷移有何不同？**
您可以使用 Azure 資料庫移轉服務來執行離線和線上遷移。 使用*離線*遷移時，應用程式停機會在開始遷移時啟動。 透過*線上*遷移，停機時間僅限於在遷移結束時切換。 建議您先測試離線移轉來決定停機時間是否在容忍範圍內；如果無法容忍，則請進行線上移轉。

> [!NOTE]
> 若要使用「Azure 資料庫移轉服務」來執行線上移轉，必須根據「進階」定價層建立執行個體。 如需詳細資訊，請參閱 Azure 資料庫移轉服務的[定價](https://azure.microsoft.com/pricing/details/database-migration/)頁面。

**問： Azure 資料庫移轉服務與其他 Microsoft 資料庫移轉工具（例如資料庫移轉小幫手（DMA）或 SQL Server 移轉小幫手（SSMA））有何不同？**
Azure 資料庫移轉服務是資料庫移轉到大規模 Microsoft Azure 的慣用方法。 如需有關 Azure 資料庫移轉服務與其他 Microsoft 資料庫移轉工具相較之下的詳細資料，以及針對各種案例使用服務的建議，請參閱將[Microsoft 的資料庫移轉工具和服務區分](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/)在一起的文章。

**問： Azure 資料庫移轉服務與 Azure Migrate 供應專案有何不同？**
Azure Migrate 可協助將內部部署虛擬機器遷移至 Azure IaaS。 此服務會評估移轉適用性，以及如何根據效能來調整大小，並估計在 Azure 中執行內部部署機器的成本。 Azure Migrate 可用來將內部部署的 VM 型工作負載隨即移轉到 Azure IaaS VM。 不過，與 Azure 資料庫移轉服務不同的是，Azure Migrate 不是適用于 Azure PaaS 關係資料庫平臺（例如 Azure SQL Database 或 Azure SQL Database 受控執行個體）的特製化資料庫移轉服務供應專案。

## <a name="setup"></a>安裝程式

**問：使用 Azure 資料庫移轉服務的必要條件為何？**
有幾個必要條件，以確保 Azure 資料庫移轉服務在執行資料庫移轉時順暢執行。 某些必要條件會套用到服務所支援的所有案例 (來源目標組)，而有些必要條件則是特定案例獨有的。

Azure 資料庫移轉服務必要條件在所有支援的移轉案例中都是通用的，包含以下需求：

* 使用 Azure Resource Manager 部署模型建立 Azure 資料庫移轉服務的 Microsoft Azure 虛擬網路，以使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)為您的內部部署來源伺服器提供站對站連線能力。
* 請確定您的虛擬網路網路安全性群組規則不會封鎖下列通訊埠443、53、9354、445、12000。 如需虛擬網路 NSG 流量篩選的詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)一文。
* 使用來源資料庫前面的防火牆應用裝置時，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取來源資料庫，以進行移轉。

如需使用 Azure 資料庫移轉服務來競爭特定遷移案例的必要條件清單，請參閱 docs.microsoft.com 上的 Azure 資料庫移轉服務[檔](https://docs.microsoft.com/azure/dms/dms-overview)中的相關教學課程。

**問：如何? 尋找 Azure 資料庫移轉服務的 IP 位址，讓我可以針對用來存取源資料庫以進行遷移的防火牆規則建立允許清單嗎？**
您可能需要新增防火牆規則，以允許 Azure 資料庫移轉服務存取您的源資料庫以進行遷移。 服務的 IP 位址是動態的，但如果您使用的是 ExpressRoute，則此位址會由您的公司網路私下指派。 若要識別適當的 IP 位址，最簡單的方式就是查看與您布建的 Azure 資料庫移轉服務資源相同的資源群組，以尋找相關聯的網路介面。 網路介面資源的名稱通常會以 NIC 前置詞作為開頭，後面接著獨特字元和數字序列，例如 NIC-jj6tnztnmarpsskr82rbndyp。 藉由選取此網路介面資源，您即可在 Azure 入口網站的 [資源概觀] 頁面上，看到必須包含在允許清單中的 IP 位址。

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

**問：如何? 設定 Microsoft Azure 虛擬網路嗎？**
雖然有多個 Microsoft 教學課程可引導您完成設定虛擬網路的程式，但官方檔會出現在[Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)一文。

## <a name="usage"></a>用量

**問：使用 Azure 資料庫移轉服務執行資料庫移轉所需步驟的摘要為何？**
在典型的簡單資料庫移轉期間，您必須：

1. 建立目標資料庫。
2. 評估您的源資料庫。
    * 針對同質遷移，使用[DMA](https://www.microsoft.com/download/details.aspx?id=53595)評估現有的資料庫。
    * 針對異類遷移（從競爭來源），使用[SSMA](https://aka.ms/get-ssma)評估現有的資料庫。 您也可以使用 SSMA 來轉換資料庫物件，並將架構遷移至目標平臺。
3. 建立 Azure 資料庫移轉服務的執行個體。
4. 建立一個可指定源資料庫、目標資料庫和要遷移之資料表的遷移專案。
5. 啟動完整載入。
6. 挑選後續驗證。
7. 將生產環境手動轉換至新的雲端型資料庫。

## <a name="troubleshooting-and-optimization"></a>疑難排解和優化

**問：我在 DMS 中設定了一個遷移專案，而我難以連接到我的源資料庫。我該怎麼做？**
如果您在處理遷移時無法連接到源資料庫系統，請在您用來設定 DMS 實例的虛擬網路中建立虛擬機器。 在虛擬機器中，您應該能夠執行連接測試，例如使用 UDL 檔案來測試連線，以 SQL Server 或下載 Robo 3T 來測試 MongoDB 連線。 如果連線測試成功，則連接到源資料庫時，您應該不會發生問題。 如果連線測試不成功，請洽詢您的網路系統管理員。

**問：我的 Azure 資料庫移轉服務為何無法使用或停止？**
如果使用者明確停止 Azure 資料庫移轉服務（DMS），或如果服務在24小時內處於非使用中狀態，服務將會處於已停止或自動暫停的狀態。 在每個情況下，服務都將無法使用並處於停止的狀態。  若要繼續使用中的移轉，請重新啟動服務。

**問：將 Azure 資料庫移轉服務的效能優化是否有任何建議？**
若要提升使用服務來移轉資料庫的速度，您可以執行幾項工作：

* 在建立服務執行個體時，請使用多重 CPU 一般用途定價層，以讓服務充分利用多個 vCPU 來進行平行處理，提升資料轉送速度。
* 在資料移轉作業期間，暫時將 Azure SQL Database 目標執行個體相應增加為進階層 SKU，讓使用較低層級 SKU 時可能會影響資料轉送活動的 Azure SQL Database 節流減到最少。

## <a name="next-steps"></a>後續步驟

如需 Azure 資料庫移轉服務和區域可用性的概觀，請參閱[什麼是 Azure 資料庫移轉服務](dms-overview.md)一文。
