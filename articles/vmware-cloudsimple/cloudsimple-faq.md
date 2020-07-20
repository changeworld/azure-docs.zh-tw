---
title: 常見問題-依 CloudSimple 的 Azure VMware 解決方案
description: Azure VMware Solution by CloudSimple 的常見問題
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d8c2974ea372dd59d15a9f2cc31ed171acd932
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77025056"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>VMware Solution by CloudSimple 的常見問題

## <a name="cloudsimple-service"></a>CloudSimple 服務

**什麼是 Azure VMware Solution by CloudSimple？**

Azure VMware 解決方案藉由 CloudSimple 在短短幾分鐘內，將 VMware 工作負載轉換並延伸至 Azure 上的私用雲端專用雲端。 CloudSimple 會負責布建、管理基礎結構，以及協調內部部署與 Azure 之間的工作負載。 由於您的應用程式在內部部署和 Azure 中執行的方式完全相同，因此您可以從雲端的彈性和服務獲益，而不需要重新架構應用程式的複雜性。 CloudSimple 透過雲端耗用量模型降低您的擁有權總成本，提供隨選布建、隨用隨付和容量優化。  如需功能、優點和案例，請參閱[Azure 上的 VMware 解決方案 CloudSimple](cloudsimple-vmware-solutions-overview.md) 。

**什麼是 CloudSimple 私用雲端？**

CloudSimple 私用雲端是私用的專用雲端，其中包含部署在 Azure 位置的 Microsoft Azure 基礎結構（硬體和資料中心空間）上的高效能計算、儲存體和網路環境。  私用雲端提供原生 VMware 的「平臺即服務」。 在 VMware 方面，每個私人雲端都只包含一個 vCenter Server 實例。 此 vCenter Server 會管理一或多個 vSphere 叢集包含的多個 ESXi 節點，以及對應的虛擬 SAN （vSAN）儲存體。 CloudSimple 服務可在您的 Azure 訂用帳戶中包含多個私人雲端。  如需詳細資訊，請參閱[私用雲端總覽](cloudsimple-private-cloud.md)。

**CloudSimple 服務可在何處取得？**

CloudSimple 適用于美國東部、美國西部和西歐區域，還有其他區域即將推出。

**如何? 啟用 CloudSimple 的訂用帳戶嗎？**

您可以在上聯絡您的 Microsoft 帳戶代表 [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) ，以啟用 CloudSimple 服務的訂用帳戶。 在您要啟用 CloudSimple 服務的電子郵件中，提供您的訂用帳戶識別碼。  

**如何? 存取 CloudSimple 入口網站嗎？**

您會從 Azure 入口網站存取 CloudSimple 入口網站。  如需詳細資訊，請參閱[Azure 入口網站的從 CloudSimple 入口網站存取 VMware 解決方案](access-cloudsimple-portal.md)。

**如何? 增加私人雲端的容量嗎？**

若要增加容量，請從 Azure 入口網站購買額外的節點，然後使用節點從 CloudSimple 入口網站擴充您的私人雲端。  您可以將其他節點新增至現有的 vSphere 叢集，或將它們新增至新的 vSphere 叢集中。  如需詳細資訊，請參閱[擴充 CloudSimple 私用雲端](expand-private-cloud.md)。

**在維護期間，我的私人雲端會發生什麼事？**

CloudSimple 會在排定的維護間隔幾天前提供通知。  維護是以非干擾性的方式進行，以確保私用雲端的可用性。  維護可以是下列類型：

* **CloudSimple 基礎結構**。  CloudSimple 基礎結構設計為高可用性。  在這種維護間隔中，會一次更新一個重複的元件，以避免任何服務中斷。 您可以維護對私人雲端 vCenter、所有虛擬機器、來自私人雲端的網際網路連線，以及內部部署或 Azure 連接的存取權。
* **CloudSimple 入口網站**。 在這種維護間隔中，可能會停用或無法存取 CloudSimple 入口網站上的某些功能。  維護間隔之前的通知會包含進行維護時的功能限制詳細資料。

## <a name="connectivity"></a>連線能力

**CloudSimple 區域網路的連線選項有哪些？**

CloudSimple 提供下列連線選項，以連線到您的 CloudSimple 區域網路。 可以同時使用多個選項。

* **從您的內部部署資料中心到 CloudSimple 區域網路的 ExpressRoute**連線。 這是一種高速、低延遲、安全的私人連線，可使用全球觸達您的內部部署 ExpressRoute 電路來橋接您的 CloudSimple ExpressRoute 線路。 如需設定連線的指示，請參閱[使用 ExpressRoute 從內部部署連接到 CloudSimple](on-premises-connection.md)。
* **從您的 Azure 虛擬網路到 CloudSimple 區域網路的 ExpressRoute**連線。 這是一種高速、低延遲的安全私人連線，其使用虛擬網路閘道，將 Azure 上的虛擬網路橋接到您的 CloudSimple ExpressRoute 線路。 如需設定連線的指示，請參閱[使用 ExpressRoute 將您的 CloudSimple 私用雲端環境連接到 Azure 虛擬網路](azure-expressroute-connection.md)。
* **從內部部署資料中心到 CloudSimple 區域網路的站對站 VPN**連線。 這是從您的內部部署 VPN 裝置到 CloudSimple 私用雲端區域的安全虛擬私人網路。  如需詳細資訊，請參閱[在 CloudSimple 網路上設定 VPN 閘道](vpn-gateway.md)。

**如何? 連接到私人雲端？**

您可以在 CloudSimple 入口網站中查看私人雲端的詳細資料。 若要連線到與您的私人雲端對應的 vCenter，請先確認已使用站對站 VPN、點對站 VPN 或 ExpressRoute 來建立網路連線。 然後，從 Azure 入口網站啟動 CloudSimple 入口網站，然後在首頁或私人雲端詳細資料頁面上，按一下 [**啟動 VSphere 用戶端**]。

**ExpressRoute 線路的優點為何？**

Azure ExpressRoute 線路是高速、低延遲、安全的連線。  CloudSimple 會針對每個客戶提供每個區域專用的 ExpressRoute 線路。  使用此線路，您可以從內部部署或 Azure 訂用帳戶建立安全的連線。

**連接到 CloudSimple 的網路成本為何？ CloudSimple 與 Azure 之間或跨區域，是否有任何輸出費用？**

網路輸出沒有 CloudSimple 費用。  Azure 標準費率適用于來自虛擬網路或內部部署 ExpressRoute 線路的任何輸出流量。

## <a name="networking"></a>網路功能

**我的私人雲端有哪些網路功能可供使用？**

您可以布建 Vlan （及其子網）和防火牆資料表，並指派對應至在私人雲端中執行之虛擬機器的公用 IP 位址。 如需網路功能的詳細資訊，請參閱[vlan 和子網總覽](cloudsimple-vlans-subnets.md)、[防火牆資料表總覽](cloudsimple-firewall-tables.md)和[公用 IP 位址總覽](cloudsimple-public-ip-address.md)。

**如何? 為我的私人雲端中的應用程式設定不同的子網嗎？**

您可以從 CloudSimple 入口網站在私人雲端上建立 Vlan。  建立 VLAN 之後，您可以使用 VLAN 在私人雲端 vCenter 上建立分散式通訊埠群組，並建立連線至分散式通訊埠群組的虛擬機器。  您可以啟用 VLAN/子網的防火牆資料表，並定義防火牆規則來保護網路流量。

**我的私人雲端有哪些防火牆設定？**

您可以設定北南部和東部-西部流量的規則。  這些規則是在防火牆資料表中定義。  防火牆資料表可以連接到私人雲端上的 Vlan。  如需詳細資訊，請參閱[設定私人雲端的防火牆資料表和規則](firewall.md)。

**我可以將公用 IP 位址指派給私人雲端環境中的 Vm 嗎？**

在 CloudSimple 入口網站中，您可以配置新的公用 IP 位址，並將它與虛擬機器或設備的私人 IP 位址產生關聯。  您也可以建立新的防火牆規則，或套用現有的防火牆規則，以允許來自入口網站中特定埠和 IP 位址的流量。 如需詳細資訊，請參閱[配置私人雲端環境的公用 IP 位址](public-ips.md)。

## <a name="security"></a>安全性

**我在 CloudSimple 上有哪些安全性選項？**

CloudSimple 提供下列安全性功能來保護您的私用雲端環境：

* 待用**資料加密**。 您可以加密位於私人雲端中 vSAN 儲存體上的待用資料。 vSAN 支援可在您的 Azure vNet 或內部部署環境中部署的外部金鑰管理伺服器。  如需詳細資訊，請參閱[為您的 CloudSimple 私人雲端設定 vSAN 加密](vsan-encryption.md)。
* **網路安全性**。 使用在您的私人雲端與網際網路、私人雲端和內部部署環境，或私人雲端的子網內套用的防火牆規則，控制網路流量流程。
* **安全的私用連接**。 系統會在您的內部部署網路與 Azure 訂用帳戶之間建立安全的私人連線。

## <a name="compute"></a>計算

**有何種主機可供使用？**

CloudSimple 提供下列主機類型：

* **CS28 節點：** CPU： 2x 2.2 GHz，總計28核心，48 HT。  RAM： 256 GB。  儲存體： 1600 GB NVMe 快取，5760 GB 資料（全部-Flash）。 網路： 4x25Gbe NIC
* **CS36 節點：** CPU 2x 2.3 GHz，總36核心，72 HT。  RAM： 512 GB。  儲存體： 3200 GB NVMe 快取 11520 GB 資料（全部-Flash）。  網路： 4x25Gbe NIC
* **CS36m-節點：** CPU 2x 2.3 GHz，總36核心，72 HT。  RAM： 576 GB。  儲存體： 3200 GB NVMe 快取 13360 GB 資料（全部-Flash）。  網路： 4x25Gbe NIC

**如何處理任何硬體失敗？**

CloudSimple 平臺和我們的服務營運小組會持續監視所有 CloudSimple 基礎結構。  如果偵測到硬體失敗，則會將新的節點新增至您的私人雲端，並移除失敗的節點。

## <a name="storage"></a>儲存體

**私人雲端支援哪種類型的儲存體？**

CloudSimple 透過每個私人雲端提供所有 flash 的 VMware vSAN 儲存體。  每個 vSphere 都是使用自己的 vSAN 資料存放區所建立。  如需詳細資訊，請參閱[私用雲端 VMware 元件-vSAN 儲存體](vmware-components.md#vsan-storage)。

**是否支援加密資料？**
是。  您可以在私人雲端上設定 vSAN 儲存體，以使用部署在內部部署或 Azure 上的金鑰管理伺服器（KMS）來加密 vSAN 上儲存的資料。

**如何處理失敗的磁片？**

CloudSimple 會持續監視私人雲端的所有硬體元件。  如果偵測到磁片失敗或磁片識別為失敗（根據啟發學習法），則會自動將新的節點新增至私人雲端。  具有失敗或失敗磁片的節點會從私人雲端移除。

## <a name="vmware"></a>VMware

**如何? 在內部部署環境中執行應用程式和資料的大規模上傳或遷移？**

CloudSimple 提供原生 VMware vSphere 解決方案。  所有適用于大量資料移轉的 VMware 工具都可以搭配您的私用雲端使用。  這些選項包括：

* 用於大量遷移資料的 VMware HCX。
* 使用從內部部署到 CloudSimple 的儲存體 vMotion 來進行資料的冷遷移。

**我可以安裝任何 VMware 工具嗎？**

CloudSimple 提供原生 VMware vSphere 解決方案。  所有用來管理內部部署 vSphere 環境的 VMware 工具都可以在 CloudSimple 上使用。  CloudSimple 支援「自備授權」（BYOL）模型來安裝 VMware 工具。

**更新和升級的管理方式為何？**

CloudSimple 以無干擾性的方式管理和更新私人雲端的所有基礎結構元件。  VMware 或基礎結構廠商所發行的所有更新和安全性修補程式，會在 CloudSimple 限定之後立即進行更新。

CloudSimple 不會對安裝在私人雲端上的應用程式執行升級或更新。

## <a name="azure-integration"></a>Azure 整合

**支援哪些 Azure 服務？**

CloudSimple 提供 azure ExpressRoute 連線給您在 Azure 上的訂用帳戶。  在您的訂用帳戶中執行的所有服務都可以連接到您的私人雲端。  範例包括：

* **Azure Active Directory**作為 CloudSimple vCenter 的身分識別來源。
* **Azure 儲存體**，用於儲存來自私人雲端的備份、映射和其他資料。
* **混合式應用程式**，具有跨公用和私人雲端的應用程式架構。  例如，您可以在 Azure 中建立 webservers，以存取私用雲端上的應用程式和資料庫伺服器。
* **Azure 監視器**和**azure 資訊安全中心**，適用于在 VMware 上執行的工作負載支援記錄、效能計量和安全性管理。

**如何? 將我的 VMware 租使用者對應到 Azure 嗎？**

CloudSimple 提供獨特的功能，從 Azure 入口網站管理私人雲端上的 VMware Vm。  使用所需資源條件約束設定的 vCenter 資源集區，可由您的全域管理員對應至您的訂用帳戶。  

**我可以使用 Azure 取得哪些授權權益？**

透過 CloudSimple，您可以利用 Azure 混合式使用權益，並省下高達90% 的授權。 這項權益可保留您對 Microsoft 授權的投資，並降低您與其他雲端解決方案相關的 TCO。 您也可以取得 Windows Server 2008 和 Microsoft SQL Server 2008 的擴充安全性更新。  「自備授權」（BYOL）模型可協助您為常見的應用程式（例如 Veeam 和 Zerto）維持低成本。  
