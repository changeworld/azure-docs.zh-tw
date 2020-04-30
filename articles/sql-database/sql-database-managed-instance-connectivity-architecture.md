---
title: 受控實例的連線架構
description: 深入瞭解 Azure SQL Database 受控實例通訊和連線架構，以及元件如何將流量導向至受控實例。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: e4d6098b7b4de76461e924fc7d42d039046d7ce5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81677160"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Azure SQL Database 中受控實例的連線架構

本文說明 Azure SQL Database 受控實例中的通訊。 它也會說明連線架構，以及元件如何將流量導向至受控實例。  

SQL Database 受控實例會放在 Azure 虛擬網路內，以及專門用於受控實例的子網中。 此部署提供：

- 安全的私人 IP 位址。
- 將內部部署網路連線至受控實例的能力。
- 將受控實例連線到連結的伺服器或其他內部部署資料存放區的能力。
- 將受控實例連線到 Azure 資源的能力。

## <a name="communication-overview"></a>通訊概觀

下圖顯示連接到受控實例的實體。 它也會顯示需要與受控實例通訊的資源。 圖表底部的通訊進程代表客戶應用程式和工具，可連接到受控實例做為資料來源。  

![連線性架構中的實體](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

受控實例是一種平臺即服務（PaaS）供應專案。 Microsoft 會使用自動化代理程式（管理、部署和維護）來根據遙測資料流程來管理此服務。 由於 Microsoft 負責管理，因此客戶無法透過遠端桌面通訊協定（RDP）來存取受管理的實例虛擬叢集機器。

某些由終端使用者或應用程式啟動的 SQL Server 作業，可能需要受控實例與平臺互動。 其中一個案例是建立受控實例資料庫。 此資源會透過 Azure 入口網站、PowerShell、Azure CLI 和 REST API 公開。

受控實例取決於 Azure 服務，例如備份 Azure 儲存體、遙測 Azure 事件中樞、用於驗證的 Azure Active Directory、Azure Key Vault 透明資料加密（TDE），以及提供安全性和可支援性功能的幾個 Azure 平臺服務。 受控實例會連接到這些服務。

所有通訊都會使用憑證來加密和簽署。 為了檢查通訊方的可信度，受控實例會透過憑證撤銷清單持續驗證這些憑證。 如果憑證遭到撤銷，受控實例就會關閉連線來保護資料。

## <a name="high-level-connectivity-architecture"></a>高層級連線架構

就高層級而言，受控實例是一組服務元件。 這些元件裝載于一組專用的獨立虛擬機器上，並在客戶的虛擬網路子網內執行。 這些機器會形成虛擬叢集。

虛擬叢集可以裝載多個受控實例。 如有需要，當客戶變更子網中已布建的實例數目時，叢集會自動展開或合約。

客戶應用程式可以連線到受控實例，並且可以查詢和更新虛擬網路、對等互連虛擬網路內的資料庫，或透過 VPN 或 Azure ExpressRoute 連接的網路。 此網路必須使用端點和私人 IP 位址。  

![連線性架構圖表](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Microsoft 管理和部署服務會在虛擬網路外部執行。 受控實例和 Microsoft 服務會透過具有公用 IP 位址的端點進行連接。 當受控實例建立輸出連線時，接收端網路位址轉譯（NAT）會讓連線看起來就像是來自此公用 IP 位址。

管理流量會流經客戶的虛擬網路。 這表示虛擬網路基礎結構的元素可能會讓實例失敗並變成無法使用，來傷害管理流量。

> [!IMPORTANT]
> 為了改善客戶體驗和服務可用性，Microsoft 會在 Azure 虛擬網路基礎結構元素上套用網路意圖原則。 此原則可能會影響受控實例的運作方式。 此平臺機制會以透明的方式將網路需求傳達給使用者。 原則的主要目標是要防止網路設定錯誤，並確保正常的受控實例作業。 當您刪除受控實例時，也會一併移除網路意圖原則。

## <a name="virtual-cluster-connectivity-architecture"></a>虛擬叢集連線架構

讓我們進一步深入探討受控實例的連線架構。 下圖顯示虛擬叢集的概念性配置。

![虛擬叢集的連線架構](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

用戶端會使用具有格式`<mi_name>.<dns_zone>.database.windows.net`的主機名稱來連接到受控實例。 此主機名稱會解析為私人 IP 位址，雖然它是在公用網域名稱系統（DNS）區域中註冊，而且可公開解析。 `zone-id`當您建立叢集時，會自動產生。 如果新建立的叢集裝載次要受控實例，它會與主要叢集共用其區域識別碼。 如需詳細資訊，請參閱[使用自動容錯移轉群組來啟用多個資料庫的透明和協調容錯移轉](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets)。

此私人 IP 位址屬於受控實例的內部負載平衡器。 負載平衡器會將流量導向至受控實例的閘道。 因為多個受控實例可以在相同的叢集中執行，所以閘道會使用受控實例的主機名稱，將流量重新導向至正確的 SQL 引擎服務。

管理和部署服務會使用對應到外部負載平衡器的[管理端點](#management-endpoint)，連接到受控實例。 只有在只有受控實例的管理元件使用的一組預先定義的埠上接收到時，才會將流量路由傳送至節點。 節點上的內建防火牆設定為只允許來自 Microsoft IP 範圍的流量。 憑證會相互驗證管理元件與管理平面之間的所有通訊。

## <a name="management-endpoint"></a>管理端點

Microsoft 會使用管理端點來管理受控實例。 此端點位於實例的虛擬叢集內。 管理端點會受到網路層級內建防火牆的保護。 在應用層級上，它會受到相互憑證驗證的保護。 若要尋找端點的 IP 位址，請參閱[判斷管理端點的 ip 位址](sql-database-managed-instance-find-management-endpoint-ip-address.md)。

當連線在受控實例內啟動時（如同備份和 audit 記錄），流量會顯示為從管理端點的公用 IP 位址開始。 您可以設定防火牆規則，只允許受控實例的 IP 位址，以限制從受控實例存取公用服務。 如需詳細資訊，請參閱[驗證受控實例的內建防火牆](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)。

> [!NOTE]
> 移至受控實例區域內部 Azure 服務的流量會經過優化，因此不會 NATed 至受控實例管理端點的公用 IP 位址。 基於這個理由，如果您需要使用以 IP 為基礎的防火牆規則，最常見的是儲存體，服務必須與受控實例位於不同的區域。

## <a name="service-aided-subnet-configuration"></a>服務輔助的子網路組態

為了解決客戶的安全性和管理能力需求，受控執行個體從手動轉換至服務輔助子網設定。

使用服務輔助子網設定使用者可完全控制資料（TDS）流量，而受控執行個體會負責確保管理流量不中斷，以履行 SLA。

服務輔助子網設定會建立在虛擬網路[子網委派](../virtual-network/subnet-delegation-overview.md)功能之上，以提供自動網路設定管理，並啟用服務端點。 服務端點可用來在保留備份/審核記錄的儲存體帳戶上設定虛擬網路防火牆規則。

### <a name="network-requirements"></a>網路需求 

在虛擬網路內的專用子網中部署受控實例。 子網必須具有下列特性：

- **專用子網：** 受控實例的子網不能包含與其相關聯的任何其他雲端服務，也不能是閘道子網。 子網不能包含任何資源，而是受控實例，而且您稍後無法在子網中新增其他類型的資源。
- **子網委派：** 受控實例的子網必須委派給`Microsoft.Sql/managedInstances`資源提供者。
- **網路安全性群組（NSG）：** NSG 必須與受控實例的子網相關聯。 當受控實例設定為重新導向連線時，您可以使用 NSG 來控制對受控實例資料端點的存取權，方法是篩選埠1433和埠11000-11999 上的流量。 服務會自動布建並保留所需的最新[規則](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)，以允許不中斷的管理流量流量。
- **使用者定義的路由（UDR）資料表：** UDR 資料表必須與受控實例的子網相關聯。 您可以將專案新增至路由表，以透過虛擬網路閘道或虛擬網路設備（NVA），將具有內部部署私人 IP 範圍的流量路由傳送至目的地。 服務會自動布建並保留所需的最新[專案](#user-defined-routes-with-service-aided-subnet-configuration)，以允許不中斷的管理流量流量。
- **足夠的 IP 位址：** 受控實例子網必須至少有16個 IP 位址。 建議的最小值為32個 IP 位址。 如需詳細資訊，請參閱[決定受控實例的子網大小](sql-database-managed-instance-determine-size-vnet-subnet.md)。 設定受控實例以滿足[受控實例的網路需求](#network-requirements)之後，您就可以在[現有的網路](sql-database-managed-instance-configure-vnet-subnet.md)中部署受管理的實例。 否則，請建立[新的網路和子網](sql-database-managed-instance-create-vnet-subnet.md)。

> [!IMPORTANT]
> 當您建立受控實例時，會在子網上套用網路意圖原則，以防止不相容的網路設定變更。 從子網移除最後一個實例之後，也會一併移除網路意圖原則。

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>具有服務輔助子網設定的必要輸入安全性規則 

| Name       |連接埠                        |通訊協定|來源           |Destination|動作|
|------------|----------------------------|--------|-----------------|-----------|------|
|管理  |9000、9003、1438、1440、1452|TCP     |SqlManagement    |MI SUBNET  |Allow |
|            |9000、9003                  |TCP     |CorpnetSaw       |MI SUBNET  |Allow |
|            |9000、9003                  |TCP     |CorpnetPublic    |MI SUBNET  |Allow |
|mi_subnet   |任意                         |任意     |MI SUBNET        |MI SUBNET  |Allow |
|health_probe|任意                         |任意     |AzureLoadBalancer|MI SUBNET  |Allow |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>具有服務輔助子網設定的必要輸出安全性規則 

| Name       |連接埠          |通訊協定|來源           |Destination|動作|
|------------|--------------|--------|-----------------|-----------|------|
|管理  |443、12000    |TCP     |MI SUBNET        |AzureCloud |Allow |
|mi_subnet   |任意           |任意     |MI SUBNET        |MI SUBNET  |Allow |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>使用服務輔助子網設定的使用者定義路由 

|Name|位址首碼|下一個躍點|
|----|--------------|-------|
|子網到 vnetlocal|MI SUBNET|虛擬網路|
|mi-13-64-11-nexthop-網際網路|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-網際網路|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-網際網路|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-網際網路|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-網際網路|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-網際網路|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-網際網路|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-網際網路|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-網際網路|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-網際網路|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-網際網路|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-網際網路|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-網際網路|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-網際網路|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-網際網路|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-網際網路|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-網際網路|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-網際網路|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-網際網路|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-網際網路|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-網際網路|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-網際網路|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-網際網路|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-網際網路|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-網際網路|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-網際網路|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-網際網路|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-網際網路|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-網際網路|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-網際網路|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-網際網路|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-網際網路|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-網際網路|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-網際網路|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-網際網路|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-網際網路|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-網際網路|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-網際網路|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-網際網路|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-網際網路|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-網際網路|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-網際網路|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-網際網路|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-網際網路|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-網際網路|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-網際網路|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-網際網路|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-網際網路|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-網際網路|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-網際網路|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-網際網路|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-網際網路|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-網際網路|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-網際網路|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-網際網路|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-網際網路|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-網際網路|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-網際網路|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-網際網路|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-網際網路|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-網際網路|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-網際網路|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-網際網路|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-網際網路|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-網際網路|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-網際網路|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-網際網路|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-網際網路|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-網際網路|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-網際網路|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-網際網路|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-網際網路|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-網際網路|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-網際網路|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-網際網路|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-網際網路|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-網際網路|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-網際網路|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-網際網路|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-網際網路|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-網際網路|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-網際網路|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-網際網路|131.253.128.0/17|Internet|
|mi-132-245-16-nexthop-網際網路|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-網際網路|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-網際網路|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-網際網路|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-網際網路|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-網際網路|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-網際網路|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-網際網路|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-網際網路|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-網際網路|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-網際網路|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-網際網路|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-網際網路|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-網際網路|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-網際網路|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-網際網路|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-網際網路|157.60.0.0/16|Internet|
|mi-167-105-16-nexthop-網際網路|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-網際網路|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-網際網路|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-網際網路|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-網際網路|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-網際網路|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-網際網路|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-網際網路|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-網際網路|192.84.160.0/23|Internet|
|mi-192-197-157-24-nexthop-網際網路|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-網際網路|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-網際網路|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-網際網路|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-網際網路|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-網際網路|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-網際網路|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-網際網路|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-網際網路|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-網際網路|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-網際網路|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-網際網路|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-網際網路|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-網際網路|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-網際網路|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-網際網路|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-網際網路|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-網際網路|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-網際網路|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-網際網路|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-網際網路|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-網際網路|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-網際網路|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-網際網路|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-網際網路|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-網際網路|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-網際網路|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-網際網路|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-網際網路|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-網際網路|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-網際網路|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-網際網路|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-網際網路|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-網際網路|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-網際網路|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-網際網路|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-網際網路|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-網際網路|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-網際網路|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-網際網路|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-網際網路|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-網際網路|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-網際網路|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-網際網路|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-網際網路|216.220.208.0/20|Internet|
|mi-23-96-13-nexthop-網際網路|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-網際網路|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-網際網路|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-網際網路|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-網際網路|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-網際網路|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-網際網路|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-網際網路|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-網際網路|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-網際網路|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-網際網路|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-網際網路|204.79.180.0/24|Internet|
||||

\*MI 子網指的是子網的 IP 位址範圍，格式為 x. x. x/y。 您可以在 [Azure 入口網站] 的 [子網] 屬性中找到這項資訊。

此外，您可以將專案新增至路由表，以透過虛擬網路閘道或虛擬網路設備（NVA）將具有內部部署私人 IP 範圍的流量路由傳送至目的地。

如果虛擬網路包含自訂 DNS，則自訂 DNS 伺服器必須能夠解析公用 dns 記錄。 使用 Azure AD 驗證之類的其他功能可能需要解析額外的 Fqdn。 如需詳細資訊，請參閱[設定自訂 DNS](sql-database-managed-instance-custom-dns.md)。

### <a name="networking-constraints"></a>網路限制

在**輸出連線上強制執行 tls 1.2**：在2020年1月，Microsoft 會針對所有 Azure 服務中的服務內流量強制執行 tls 1.2。 針對 Azure SQL Database 受控實例，這會導致在用於複寫的輸出連接和連結的伺服器連線上，強制執行 TLS 1.2 以進行 SQL Server。 如果您使用受控執行個體的 SQL Server 2016 舊版本，請確定已套用[TLS 1.2 特定更新](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)。

受控執行個體目前不支援下列虛擬網路功能：
- **Microsoft 對等互連**：在 express route 線路上啟用[microsoft 對等互連](../expressroute/expressroute-faqs.md#microsoft-peering)對等互連直接或透過虛擬網路進行轉移，其中受控執行個體的位置會影響虛擬網路和服務中受控執行個體元件之間的流量，這取決於導致可用性問題。 受控執行個體部署到已啟用 Microsoft 對等互連的虛擬網路，預期會失敗。
- **全域虛擬網路對等互連**：跨 Azure 區域的[虛擬網路對等](../virtual-network/virtual-network-peering-overview.md)互連連線因[記錄的負載平衡器條件約束](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)而不適用於受控執行個體。
- **AzurePlatformDNS**：使用 AzurePlatformDNS[服務](../virtual-network/service-tags-overview.md)標籤來封鎖平臺 DNS 解析，會導致受控執行個體無法使用。 雖然受控執行個體支援客戶定義的 DNS，以便在引擎內進行 DNS 解析，但還是會相依于平臺作業的平臺 DNS。
- **Nat 閘道**：使用[虛擬網路 NAT](../virtual-network/nat-overview.md)以控制特定公用 IP 位址的輸出連線能力，受控執行個體無法使用。 受控執行個體服務目前僅限於使用基本負載平衡器，不提供虛擬網路 NAT 的輸入和輸出流量共存。

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>不再不含服務輔助子網設定的網路需求

在虛擬網路內的專用子網中部署受控實例。 子網必須具有下列特性：

- **專用子網：** 受控實例的子網不能包含與其相關聯的任何其他雲端服務，也不能是閘道子網。 子網不能包含任何資源，而是受控實例，而且您稍後無法在子網中新增其他類型的資源。
- **網路安全性群組（NSG）：** 與虛擬網路相關聯的 NSG 必須在任何其他規則之前定義[輸入安全性規則](#mandatory-inbound-security-rules)和[輸出安全性規則](#mandatory-outbound-security-rules)。 當受控實例設定為重新導向連線時，您可以使用 NSG 來控制對受控實例資料端點的存取權，方法是篩選埠1433和埠11000-11999 上的流量。
- **使用者定義的路由（UDR）資料表：** 與虛擬網路相關聯的 UDR 資料表必須包含特定的[專案](#user-defined-routes)。
- **沒有服務端點：** 不應該有任何服務端點與受控實例的子網相關聯。 當您建立虛擬網路時，請確定已停用 [服務端點] 選項。
- **足夠的 IP 位址：** 受控實例子網必須至少有16個 IP 位址。 建議的最小值為32個 IP 位址。 如需詳細資訊，請參閱[決定受控實例的子網大小](sql-database-managed-instance-determine-size-vnet-subnet.md)。 設定受控實例以滿足[受控實例的網路需求](#network-requirements)之後，您就可以在[現有的網路](sql-database-managed-instance-configure-vnet-subnet.md)中部署受管理的實例。 否則，請建立[新的網路和子網](sql-database-managed-instance-create-vnet-subnet.md)。

> [!IMPORTANT]
> 如果目的地子網缺少這些特性，您就無法部署新的受控實例。 當您建立受控實例時，會在子網上套用網路意圖原則，以防止不相容的網路設定變更。 從子網移除最後一個實例之後，也會一併移除網路意圖原則。

### <a name="mandatory-inbound-security-rules"></a>必要輸入安全性規則

| Name       |連接埠                        |通訊協定|來源           |Destination|動作|
|------------|----------------------------|--------|-----------------|-----------|------|
|管理  |9000、9003、1438、1440、1452|TCP     |任意              |MI SUBNET  |Allow |
|mi_subnet   |任意                         |任意     |MI SUBNET        |MI SUBNET  |Allow |
|health_probe|任意                         |任意     |AzureLoadBalancer|MI SUBNET  |Allow |

### <a name="mandatory-outbound-security-rules"></a>必要輸出安全性規則

| Name       |連接埠          |通訊協定|來源           |Destination|動作|
|------------|--------------|--------|-----------------|-----------|------|
|管理  |443、12000    |TCP     |MI SUBNET        |AzureCloud |Allow |
|mi_subnet   |任意           |任意     |MI SUBNET        |MI SUBNET  |Allow |

> [!IMPORTANT]
> 請確定埠9000、9003、1438、1440、1452和一個用於埠443、12000的輸出規則都只有一個輸入規則。 如果為每個埠分別設定輸入和輸出規則，透過 Azure Resource Manager 部署的受控執行個體布建將會失敗。 如果這些埠是在不同的規則中，部署將會失敗，並出現錯誤代碼`VnetSubnetConflictWithIntendedPolicy`

\*MI 子網指的是子網的 IP 位址範圍，格式為 x. x. x/y。 您可以在 [Azure 入口網站] 的 [子網] 屬性中找到這項資訊。

> [!IMPORTANT]
> 雖然必要的輸入安全性規則允許來自埠9000、9003、1438、1440和1452的_任何_來源流量，但這些埠會受到內建防火牆的保護。 如需詳細資訊，請參閱[判斷管理端點位址](sql-database-managed-instance-find-management-endpoint-ip-address.md)。

> [!NOTE]
> 如果您在受控實例中使用異動複寫，而且您使用任何實例資料庫做為發行者或散發者，請在子網的安全性規則中開啟埠445（TCP 輸出）。 此埠可讓您存取 Azure 檔案共用。

### <a name="user-defined-routes"></a>使用者定義的路由

|Name|位址首碼|下一個躍點|
|----|--------------|-------|
|subnet_to_vnetlocal|MI SUBNET|虛擬網路|
|mi-13-64-11-nexthop-網際網路|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-網際網路|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-網際網路|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-網際網路|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-網際網路|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-網際網路|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-網際網路|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-網際網路|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-網際網路|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-網際網路|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-網際網路|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-網際網路|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-網際網路|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-網際網路|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-網際網路|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-網際網路|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-網際網路|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-網際網路|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-網際網路|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-網際網路|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-網際網路|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-網際網路|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-網際網路|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-網際網路|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-網際網路|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-網際網路|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-網際網路|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-網際網路|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-網際網路|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-網際網路|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-網際網路|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-網際網路|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-網際網路|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-網際網路|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-網際網路|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-網際網路|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-網際網路|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-網際網路|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-網際網路|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-網際網路|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-網際網路|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-網際網路|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-網際網路|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-網際網路|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-網際網路|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-網際網路|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-網際網路|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-網際網路|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-網際網路|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-網際網路|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-網際網路|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-網際網路|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-網際網路|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-網際網路|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-網際網路|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-網際網路|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-網際網路|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-網際網路|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-網際網路|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-網際網路|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-網際網路|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-網際網路|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-網際網路|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-網際網路|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-網際網路|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-網際網路|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-網際網路|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-網際網路|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-網際網路|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-網際網路|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-網際網路|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-網際網路|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-網際網路|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-網際網路|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-網際網路|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-網際網路|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-網際網路|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-網際網路|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-網際網路|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-網際網路|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-網際網路|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-網際網路|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-網際網路|131.253.128.0/17|Internet|
|mi-132-245-16-nexthop-網際網路|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-網際網路|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-網際網路|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-網際網路|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-網際網路|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-網際網路|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-網際網路|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-網際網路|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-網際網路|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-網際網路|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-網際網路|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-網際網路|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-網際網路|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-網際網路|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-網際網路|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-網際網路|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-網際網路|157.60.0.0/16|Internet|
|mi-167-105-16-nexthop-網際網路|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-網際網路|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-網際網路|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-網際網路|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-網際網路|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-網際網路|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-網際網路|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-網際網路|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-網際網路|192.84.160.0/23|Internet|
|mi-192-197-157-24-nexthop-網際網路|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-網際網路|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-網際網路|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-網際網路|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-網際網路|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-網際網路|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-網際網路|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-網際網路|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-網際網路|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-網際網路|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-網際網路|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-網際網路|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-網際網路|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-網際網路|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-網際網路|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-網際網路|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-網際網路|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-網際網路|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-網際網路|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-網際網路|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-網際網路|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-網際網路|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-網際網路|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-網際網路|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-網際網路|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-網際網路|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-網際網路|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-網際網路|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-網際網路|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-網際網路|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-網際網路|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-網際網路|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-網際網路|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-網際網路|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-網際網路|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-網際網路|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-網際網路|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-網際網路|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-網際網路|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-網際網路|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-網際網路|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-網際網路|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-網際網路|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-網際網路|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-網際網路|216.220.208.0/20|Internet|
|mi-23-96-13-nexthop-網際網路|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-網際網路|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-網際網路|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-網際網路|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-網際網路|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-網際網路|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-網際網路|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-網際網路|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-網際網路|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-網際網路|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-網際網路|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-網際網路|204.79.180.0/24|Internet|
||||

## <a name="next-steps"></a>後續步驟

- 如需總覽，請參閱 [SQL Database advanced data security](sql-database-managed-instance.md)。
- 瞭解如何[設定新的 azure 虛擬網路](sql-database-managed-instance-create-vnet-subnet.md)或[現有的 azure 虛擬網路](sql-database-managed-instance-configure-vnet-subnet.md)，您可以在其中部署受控實例。
- 計算您要在其中部署受控實例的[子網大小](sql-database-managed-instance-determine-size-vnet-subnet.md)。
- 瞭解如何建立受控實例：
  - 從[Azure 入口網站](sql-database-managed-instance-get-started.md)。
  - 使用[PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)。
  - 藉由使用[Azure Resource Manager 範本](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)。
  - 藉由使用[Azure Resource Manager 範本（使用 JumpBox，隨附 SSMS）](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/)。 
