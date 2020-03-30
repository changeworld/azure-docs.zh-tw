---
title: 常見問題解答 - Azure VMware 解決方案（按雲簡單）
description: 雲簡單 Azure VMware 解決方案的常見問題
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d8c2974ea372dd59d15a9f2cc31ed171acd932
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025056"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>關於 VMware 解決方案的常見問題

## <a name="cloudsimple-service"></a>雲簡單服務

**什麼是 Azure VMware 解決方案（按雲簡單）？**

雲簡單 Azure VMware 解決方案在幾分鐘內即可將 VMware 工作負載轉換為 Azure 上的專用雲。 CloudSimple 負責配置、管理基礎結構以及協調本地和 Azure 之間的工作負載。 由於應用在本地和 Azure 中運行完全相同，因此您將受益于雲的彈性和服務，而無需重新構建應用的複雜性。 CloudSimple 通過提供按需預配、隨增長付費和容量優化的雲消費模型，可降低您的擁有權總成本。  有關功能、優點和方案，請參閱[Azure 上的 VMware 解決方案。簡單。](cloudsimple-vmware-solutions-overview.md)

**什麼是雲簡單私有雲？**

CloudSimple 私有雲是專用專用雲，由部署在 Azure 位置的 Microsoft Azure 基礎結構（硬體和資料中心空間）上的高性能計算、存儲和網路環境組成。  私有雲提供本機 VMware"平臺即服務"。 在 VMware 術語中，每個私有雲都包含 vCenter 伺服器的一個實例。 vCenter 伺服器管理一個或多個 vSphere 群集中包含的多個 ESXi 節點，以及相應的虛擬 SAN （vSAN） 存儲。 雲簡單服務可以在 Azure 訂閱中包含多個私有雲。  有關詳細資訊，請參閱[私有雲概述](cloudsimple-private-cloud.md)。

**雲簡單服務在哪裡可用？**

CloudSimple 在美國東部、美國西部和西歐地區提供，其他區域即將推出。

**如何啟用雲簡單訂閱？**

您可以聯繫 Microsoft 帳戶代表[azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com)，以便啟用 CloudSimple 服務的訂閱。 在您希望為其啟用 CloudSimple 服務的電子郵件中提供訂閱 ID。  

**如何訪問雲簡單門戶？**

從 Azure 門戶訪問雲簡單門戶。  有關詳細資訊，請參閱[從 Azure 門戶通過雲簡單門戶訪問 VMware 解決方案](access-cloudsimple-portal.md)。

**如何增加私有雲的容量？**

要增加容量，請從 Azure 門戶購買其他節點，然後使用這些節點從 CloudSimple 門戶擴展私有雲。  您可以將其他節點添加到現有 vSphere 群集，或將它們添加到新的 vSphere 群集。  有關詳細資訊，請參閱[擴展雲簡單私有雲](expand-private-cloud.md)。

**在維護期間，我的私有雲會發生什麼情況？**

CloudSimple 在計畫維護間隔前幾天提供通知。  維護以無中斷的方式完成，以確保私有雲的可用性。  維護可以是以下類型：

* **雲簡單基礎架構**。  雲簡單基礎架構旨在高度可用。  在這種類型的維護間隔內，冗余元件一次更新一個，以避免任何服務中斷。 您可以保持對私有雲 vCenter、所有虛擬機器、來自私有雲的互聯網連接以及到本地或 Azure 的連接的存取權限。
* **雲簡單門戶**。 在這種類型的維護間隔內，雲簡單門戶上的某些功能可能會被禁用或無法訪問。  維護間隔前的通知包括有關進行維護時功能限制的詳細資訊。

## <a name="connectivity"></a>連線能力

**我與雲簡單區域網路的連接選項是什麼？**

CloudSimple 提供了以下連接選項，可連接到雲簡單區域網路。 可以同時使用多個選項。

* **從本地資料中心到雲簡單區域網路的快速路由連接**。 這是一個高速、低延遲、安全的專用連線，使用 Global Reach 將本地快速路由電路橋接到雲簡單快速路由電路。 有關設置連接的說明，請參閱[使用 ExpressRoute 從本地連接到雲簡單](on-premises-connection.md)。
* **從 Azure 虛擬網路到雲簡單區域網路的快速路由連接**。 這是一種高速、低延遲、安全的專用連線，使用虛擬網路閘道將 Azure 上的虛擬網路橋接到 CloudSimple ExpressRoute 電路。 有關設置連接的說明，請參閱[使用 ExpressRoute 將雲簡單私有雲環境連接到 Azure 虛擬網路](azure-expressroute-connection.md)。
* **網站到網站 VPN 連接，從本地資料中心到雲簡單區域網路**。 這是一個安全虛擬私人網路絡，從本地 VPN 設備到雲簡單私有雲區域。  有關詳細資訊，請參閱[在雲簡單網路上設置 VPN 閘道](vpn-gateway.md)。

**如何連接到私有雲？**

您可以在雲簡單門戶中查看私有雲的詳細資訊。 要連接到與私有雲對應的 vCenter，請先驗證是否使用網站到網站 VPN、點對點 VPN 或 ExpressRoute 建立了網路連接。 然後，從 Azure 門戶啟動 CloudSimple 門戶，然後按一下主頁或私有雲詳細資訊頁上**的啟動 vSphere 用戶端**。

**快速路由電路的優勢是什麼？**

Azure ExpressRoute 電路是一種高速、低延遲、安全的連接。  CloudSimple 為每位客戶提供專用的 ExpressRoute 電路。  使用此電路，可以從本地或 Azure 訂閱建立安全連線。

**連接到雲簡單網路的成本是多少？ 雲簡單和 Azure 之間或跨區域是否收取任何出口費用？**

網路出口不收取雲簡單費用。  Azure 標準費率適用于來自虛擬網路或本地 ExpressRoute 電路的任何出口流量。

## <a name="networking"></a>網路

**我的私有雲可以使用哪些網路功能？**

您可以預配 VLAN（及其子網）和防火牆表，並將映射到在私有雲中運行的虛擬機器的公共 IP 位址分配。 有關網路功能的詳細資訊，請參閱[VLAN 和子網概述](cloudsimple-vlans-subnets.md)、[防火牆表概述](cloudsimple-firewall-tables.md)和[公共 IP 位址概述](cloudsimple-public-ip-address.md)。

**如何在私有雲中為應用程式設定不同的子網？**

您可以在私有雲上從雲簡單門戶創建 VLAN。  創建 VLAN 後，可以使用 VLAN 在私有雲 vCenter 上創建分散式埠組，並創建連接到分散式埠組的虛擬機器。  您可以為 VLAN/子網啟用防火牆表，並定義防火牆規則以保護網路流量。

**我的私有雲可以使用哪些防火牆設置？**

您可以為南北交通和東西交通配置規則。  規則在防火牆表中定義。  防火牆表可以附加到私有雲上的 VLAN。  有關詳細資訊，請參閱[為私有雲設置防火牆表和規則](firewall.md)。

**我可以將公共 IP 位址分配給私有雲環境中的 VM 嗎？**

在 CloudSimple 門戶中，您可以分配新的公共 IP 位址並將其與虛擬機器或設備的私人 IP 位址相關聯。  您還可以創建新的防火牆規則或應用現有防火牆規則，以允許來自門戶中特定埠和 IP 位址的流量。 有關詳細資訊，請參閱[為私有雲環境分配公共 IP 位址](public-ips.md)。

## <a name="security"></a>安全性

**我在雲簡單上的安全選項是什麼？**

CloudSimple 提供了以下安全功能，用於保護您的私有雲環境：

* **靜態資料加密**。 您可以加密駐留在私有雲中的 vSAN 存儲的靜態資料。 vSAN 支援外部金鑰管理伺服器，這些伺服器可以部署在 Azure vNet 或本地環境中。  有關詳細資訊，請參閱[為雲簡單私有雲配置 vSAN 加密](vsan-encryption.md)。
* **網路安全**。 使用適用于私有雲和 Internet、私有雲和本地環境之間或私有雲子網中的防火牆規則控制網路流量。
* **安全、私人連接**。 在本地網路和 Azure 訂閱之間建立安全的私人連接。

## <a name="compute"></a>計算

**可用的主機類型是什麼？**

CloudSimple 提供以下主機類型：

* **CS28 節點：** CPU：2x 2.2 GHz，共28個內核，48個HT。  記憶體：256 GB。  存儲：1600 GB NVMe 緩存，5760 GB 資料（全快閃記憶體）。 網路： 4x25Gbe NIC
* **CS36 節點：** CPU 2x 2.3 GHz，共 36 個內核，72 個 HT。  記憶體：512 GB。  存儲：3200 GB NVMe 緩存 11520 GB 資料（全快閃記憶體）。  網路： 4x25Gbe NIC
* **CS36m-節點：** CPU 2x 2.3 GHz，共 36 個內核，72 個 HT。  記憶體：576 GB。  存儲：3200 GB NVMe 緩存 13360 GB 資料（全快閃記憶體）。  網路： 4x25Gbe NIC

**如何處理任何硬體故障？**

雲簡單平臺和我們的服務運營團隊持續監控所有雲簡單基礎架構。  如果檢測到硬體故障，將新節點添加到私有雲，並刪除故障節點。

## <a name="storage"></a>存放裝置

**私有雲支援哪種類型的存儲？**

CloudSimple 提供全快閃記憶體 VMware vSAN 存儲，包括每個私有雲。  每個 vSphere 都使用自己的 vSAN 資料存儲創建。  有關詳細資訊，請參閱[私有雲 VMware 元件 - vSAN 存儲](vmware-components.md#vsan-storage)。

**是否支援資料加密？**
是。  您可以在私有雲上設置 vSAN 存儲，以便使用部署在本地或 Azure 上的關鍵管理伺服器 （KMS） 來加密存儲在 vSAN 上的資料。

**如何處理故障磁片？**

CloudSimple 持續監控私有雲的所有硬體元件。  如果檢測到磁片故障或磁片標識為故障（基於啟發式），則新節點將自動添加到私有雲中。  具有失敗或失敗磁片的節點將從私有雲中刪除。

## <a name="vmware"></a>VMware

**如何從本地執行應用程式和資料的大規模上載或遷移？**

CloudSimple 提供了原生 VMware vSphere 解決方案。  所有用於批量資料移轉的 VMware 工具都可以與私有雲一起使用。  選項包括：

* VMware HCX 用於批量遷移資料。
* 使用存儲 vMotion 從本地遷移到雲簡單資料進行冷遷移。

**我可以安裝任何 VMware 工具嗎？**

CloudSimple 提供了原生 VMware vSphere 解決方案。  用於管理本地 vSphere 環境的所有 VMware 工具都可以在 CloudSimple 上使用。  CloudSimple 支援自帶許可證 （BYOL） 模型來安裝 VMware 工具。

**如何管理更新和升級？**

CloudSimple 以無縫無中斷的方式管理和更新私有雲的所有基礎架構元件。  VMware 或基礎結構供應商發佈的所有更新和安全修補程式一旦通過 CloudSimple 認證，即可進行更新。

CloudSimple 不執行私有雲上安裝的應用程式的升級或更新。

## <a name="azure-integration"></a>Azure 整合

**支援哪些 Azure 服務？**

CloudSimple 提供 Azure 快速路由連接到 Azure 上的訂閱。  訂閱中運行的所有服務都可以連接到您的私有雲。  範例包括：

* **Azure 活動目錄**作為雲簡單 vCenter 的標識源。
* **用於**存儲來自私有雲的備份、映射和其他資料的 Azure 存儲。
* 具有跨公共雲和私有雲的應用程式體系結構**的混合應用程式**。  例如，您可以在 Azure 中創建訪問私有雲上的應用程式和資料庫伺服器的 Web 服務器。
* **在**VMware 上運行的工作負載的 Azure 監視器和**Azure 安全中心**支援日誌記錄、性能指標和安全管理。

**如何將 VMware 租戶映射到 Azure？**

CloudSimple 提供了從 Azure 門戶管理私有雲上的 Vm 的獨特功能。  全域管理員可以映射到您的訂閱，該資源池配置了所需的資源約束。  

**使用 Azure 可以獲得哪些許可權益？**

借助 CloudSimple，您可以利用 Azure 混合使用優勢，並在許可證上節省高達 90% 的許可證。 此優勢可保留您對 Microsoft 許可證的投資，並降低相對於其他雲解決方案的 TCO。 您還可以獲得 Windows Server 2008 和 Microsoft SQL Server 2008 的擴展安全更新。  自帶許可證 （BYOL） 模型可説明您降低 Veeam 和 Zerto 等常見應用的成本。  
