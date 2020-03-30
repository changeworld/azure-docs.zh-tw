---
title: 在 Azure 虛擬機器上部署 Oracle 應用的體系結構 |微軟文檔
description: 在具有 Azure 或 Oracle 雲基礎架構 （OCI） 中的資料庫的 Microsoft Azure 虛擬機器上部署 Oracle 應用程式的應用程式體系結構，包括電子商務套件、JD Edwards EnterpriseOne 和 PeopleSoft。
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: b183a4d4922c89f60ccb19b3e3e978216f33cc9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70100095"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>在 Azure 上部署 Oracle 應用程式的體系結構

Microsoft 和 Oracle 合作，使客戶能夠在雲中部署 Oracle 應用程式，如 Oracle 電子商務套件、JD Edwards EnterpriseOne 和 PeopleSoft。 隨著 Microsoft Azure 和 Oracle 雲基礎結構 （OCI） 之間引入預覽[專用網路連接](configure-azure-oci-networking.md)，Oracle 應用程式現在可以將其後端資料庫部署在 Azure 或 OCI 中。 Oracle 應用程式也可以與 Azure 活動目錄集成，允許您設置單個登錄，以便使用者可以使用 Azure 活動目錄 （Azure AD） 憑據登錄到 Oracle 應用程式。

OCI 為 Oracle 應用程式提供了多個 Oracle 資料庫選項，包括 DBaaS、Exadata 雲服務、Oracle RAC 和基礎架構即服務 （IaaS）。 目前，自治資料庫不是 Oracle 應用程式支援的後端。

在 Azure 中部署 Oracle 應用程式[有多種選項](oracle-overview.md)，包括以高度可用和安全的方式部署。 Azure 還提供[Oracle 資料庫 VM 映射](oracle-vm-solutions.md)，如果選擇完全在 Azure 上運行 Oracle 應用程式，則可以進行部署。

以下各節概述了 Microsoft 和 Oracle 在跨雲配置或完全在 Azure 中部署 Oracle 電子商務套件、JD Edwards EnterpriseOne 和 PeopleSoft 的體系結構建議。 微軟和甲骨文已經測試了這些應用程式，並確認性能符合 Oracle 為這些應用程式設定的標準。

## <a name="architecture-considerations"></a>體系結構注意事項

Oracle 應用程式由多個服務組成，這些服務可以託管在 Azure 中的同一虛擬機器或多個虛擬機器上，也可以在 OCI 中選擇託管。 

可以使用私有終結點或公共終結點設置應用程式實例。 Microsoft 和 Oracle 建議在單獨的子網中設置具有公共 IP 位址的*堡壘主機 VM，* 以便管理應用程式。 然後，僅將私人 IP 位址分配給其他電腦，包括資料庫層。 

在跨雲體系結構中設置應用程式時，需要進行規劃，以確保 Azure 虛擬網路中的 IP 位址空間不會與 OCI 虛擬雲網路中的私人 IP 位址空間重疊。

為了增加安全性，在子網級別設置網路安全性群組，以確保只允許特定埠和 IP 位址上的流量。 例如，中介層中的電腦應僅從虛擬網路內接收流量。 任何外部流量都不應直接到達中介層電腦。

為獲得高可用性，您可以在同一可用性集中或不同的可用性區域中設置不同伺服器的冗余實例。 可用性區域允許您實現 99.99% 的 SLA 上用時間，而可用性集允許您在區域內實現 99.95% 的 SLA 上級。 本文中顯示的示例體系結構分佈在兩個可用性區域。

使用跨雲互連部署應用程式時，可以繼續使用現有的 ExpressRoute 電路將 Azure 環境連接到本地網路。 但是，與連接到本地網路的互連電路相比，您需要單獨的 ExpressRoute 電路才能連接到 OCI。

## <a name="e-business-suite"></a>電子商務套件

Oracle 電子商務套件 （EBS） 是一套應用程式，包括供應鏈管理 （SCM） 和客戶關係管理 （CRM）。 為了利用 OCI 的託管資料庫組合，可以使用 Microsoft Azure 和 OCI 之間的跨雲互連來部署 EBS。 在此配置中，展示層和應用程式層在 Azure 中運行，在 OCI 中的資料庫層運行，如下體系結構圖所示（圖 1）。

![電子商務套件跨雲架構](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*圖 1：電子商務套件跨雲架構* 

在此體系結構中，Azure 中的虛擬網路使用跨雲互連連接到 OCI 中的虛擬雲網路。 應用程式層在 Azure 中設置，而資料庫在 OCI 中設置。 建議將每個元件部署到具有網路安全性群組的自己的子網，以便僅允許來自特定埠上的特定子網的流量。

該體系結構還可以完全適應 Azure 的部署，並在區域的兩個可用性區域使用 Oracle 資料防護配置了高可用性的 Oracle 資料庫。 下圖（圖 2）是此體系結構模式的示例：

![電子商務套件 Azure 專用體系結構](media/oracle-oci-applications/ebs-arch-azure.png)

*圖 2：僅 Azure 電子商務套件體系結構*

以下各節在高級別上描述不同的元件。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>應用程式（中間）層

應用程式層在其自己的子網中隔離。 為容錯和輕鬆的修補程式管理設置了多個虛擬機器。 這些 VM 可以由共用存儲進行備份，該存儲由 Azure NetApp 檔和超 SSD 提供。 此配置允許在不停機的情況下更輕鬆地部署修補程式。 應用程式層中的電腦應由公共負載等化器對前面，以便處理對 EBS 應用程式層的請求，即使層中的一台電腦由於故障而離線也是如此。

### <a name="load-balancer"></a>負載平衡器

Azure 負載等化器允許您跨工作負荷的多個實例分配流量，以確保高可用性。 在這種情況下，將設置公共負載等化器，因為允許使用者通過網路訪問 EBS 應用程式。 負載等化器將負載分配到中介層中的兩台電腦。 為增加安全性，只允許使用網站到網站 VPN 或 ExpressRoute 和網路安全性群組從公司網路訪問系統的使用者進行流量訪問。

### <a name="database-tier"></a>資料庫層

此層承載 Oracle 資料庫，並分隔到其自己的子網中。 建議添加僅允許從應用程式層到特定于 Oracle 的資料庫埠 1521 上的資料庫層的流量的網路安全性群組。

微軟和甲骨文建議採用高可用性設置。 通過使用 Oracle 資料防護在兩個可用性區域中設置兩個 Oracle 資料庫，或者在 OCI 中使用 Oracle 資料庫 Exadata 雲服務，可以實現 Azure 中的高可用性。 使用 Oracle 資料庫 Exadata 雲服務時，資料庫將部署在兩個子網中。 您還可以使用 Oracle 資料保護在兩個可用性域中的 OCI 中的 VM 中設置 Oracle 資料庫。


### <a name="identity-tier"></a>標識層

標識層包含 EBS 斷言器 VM。 EBS 斷言器允許您同步來自 Oracle 標識雲服務 （IDCS） 和 Azure AD 的標識。 EBS 斷言器是必需的，因為 EBS 不支援單一登入協定，如 SAML 2.0 或 OpenID 連接。 EBS 斷言器使用 OpenID 連接權杖（由 IDCS 生成），對其進行驗證，然後在 EBS 中為使用者創建會話。 

雖然此體系結構顯示 IDCS 集成，但 Azure AD 統一訪問和單一登入也可以通過 Oracle 訪問管理器與 Oracle Internet 目錄或 Oracle 統一目錄啟用。 有關詳細資訊，請參閱有關[使用 IDCS 集成部署 Oracle EBS](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf)或[使用 OAM 集成部署 Oracle EBS](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf)的白皮書。

為獲得高可用性，建議您在多個可用性區域部署 EBS Asserter 的冗余伺服器，前面有負載等化器。

設置基礎結構後，可以使用 Oracle 提供的安裝指南安裝電子商務套件。

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

Oracle 的 JD Edwards 企業一號是一套綜合性企業資源規劃軟體的集成應用程式套件。 它是一個多層應用程式，可以使用 Oracle 或 SQL Server 資料庫後端進行設置。 本節討論有關在 OCI 或 Azure 中使用 Oracle 資料庫後端部署 JD Edwards 企業One 的詳細資訊。

在以下建議的體系結構（圖 3）中，管理層、演示文稿層和中介層部署到 Azure 中的虛擬網路。 資料庫部署在 OCI 的虛擬雲網路中。

與電子商務套件一樣，您可以為安全管理目的設置可選的堡壘層。 使用堡壘 VM 主機作為跳轉伺服器訪問應用程式和資料庫實例。

![JD Edwards 企業一個跨雲架構](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*圖 3：JD Edwards 企業一個跨雲架構*

在此體系結構中，Azure 中的虛擬網路使用跨雲互連連接到 OCI 中的虛擬雲網路。 應用程式層在 Azure 中設置，而資料庫在 OCI 中設置。 建議將每個元件部署到具有網路安全性群組的自己的子網，以便僅允許來自特定埠上的特定子網的流量。

該體系結構還可以完全適應 Azure 的部署，並在區域的兩個可用性區域使用 Oracle 資料防護配置了高可用性的 Oracle 資料庫。 下圖（圖 4）是此體系結構模式的示例：

![JD Edwards 企業一個 Azure 體系結構](media/oracle-oci-applications/jdedwards-arch-azure.png)

*圖 4：JD Edwards 企業一個 Azure 專用體系結構*

以下各節在高級別上描述不同的元件。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>管理層

顧名思義，此層用於管理工作。 您可以為管理層劃分單獨的子網。 此層中的服務和伺服器主要用於應用程式的安裝和管理。 因此，這些伺服器的單實例就足夠了。 應用程式的高可用性不需要冗余實例。

此層的元件如下所示：
    
 - **預配伺服器**- 此伺服器用於應用程式的不同元件的端到端部署。 它通過埠 22 與其他層中的實例（包括資料庫層中的實例）進行通信。 它承載 JD Edwards 企業一 體的伺服器管理員主控台。
 - **部署伺服器**- 安裝 JD Edwards 企業一台時，主要需要此伺服器。 在安裝過程中，此伺服器充當所需檔和安裝包的中央存儲庫。 該軟體從此伺服器分發或部署到其他伺服器和用戶端。
 - **開發用戶端**- 此伺服器包含在 Web 瀏覽器中運行的元件以及本機應用程式。

### <a name="presentation-tier"></a>展示層

此層包含各種元件，如應用程式介面服務 （AIS）、應用程式開發框架 （ADF） 和 JAVA 應用程式伺服器 （JAS）。 此層中的伺服器與中介層中的伺服器通信。 它們由負載等化器正面顯示，該負載等化器根據接收流量的埠號和 URL 將流量路由到必要的伺服器。 建議您部署每種伺服器類型的多個實例，以便獲得高可用性。

以下是此層中的元件：
    
- **應用程式介面服務 （AIS）** - AIS 伺服器提供 JD Edwards 企業一號移動企業應用程式和 JD Edwards 企業一號之間的通信介面。
- **JAVA 應用程式伺服器 （JAS）** - JAS 接收來自負載等化器的請求，並將其傳遞到中介層以執行複雜的任務。 JAS 能夠執行簡單的業務邏輯。
- **BI 發佈伺服器 （BIP）** - 此伺服器基於 JD Edwards 企業一號應用程式收集的資料顯示報告。 您可以設計和控制報表如何根據不同的範本顯示資料。
- **業務服務伺服器 （BSS）** - BSS 支援與其他 Oracle 應用程式的資訊交換和互通性。
- **即時事件伺服器 （RTE）** - RTE 伺服器允許您設置有關 JDE 企業一號系統中發生的事務的通知。 它使用訂戶模型並允許協力廠商系統訂閱事件。 要載入對兩個 RTE 伺服器的平衡請求，請確保伺服器位於群集中。
- **應用程式開發框架 （ADF） 伺服器**- ADF 伺服器用於運行使用 Oracle ADF 開發的 JD Edwards 企業一號應用程式。 這部署在具有 ADF 運行時的 Oracle WebLogic 伺服器上。

### <a name="middle-tier"></a>中介層

中介層包含邏輯伺服器和批次處理伺服器。 在這種情況下，兩台伺服器都安裝在同一虛擬機器上。 但是，對於生產方案，建議您將邏輯伺服器和批次處理伺服器部署在單獨的伺服器上。 多個伺服器部署在中介層，跨越兩個可用性區域，以更高的可用性。 應創建 Azure 負載等化器，並將這些伺服器放在其後端池中，以確保兩個伺服器都處於活動狀態並處理請求。

中介層中的伺服器僅接收來自展示層中的伺服器和公共負載等化器的請求。 必須設置網路安全性群組規則，以拒絕來自演示文稿層子網和負載等化器以外的任何位址的流量。 還可以設置 NSG 規則，以允許埠 22 從堡壘主機進行流量以用於管理目的。 您可以使用公共負載平衡器在中介層中的 VM 之間載入平衡請求。

以下兩個元件位於中介層中：

- **邏輯伺服器**- 包含業務邏輯或業務功能。
- **批次處理伺服器**- 用於批次處理

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

Oracle 的 PeopleSoft 應用程式套件包含用於人力資源和財務管理的軟體。 應用程式套件是多層次的，應用程式包括人力資源管理系統 （HRMS）、客戶關係管理 （CRM）、財務和供應鏈管理 （FSCM） 以及企業績效管理 （EPM）。

建議將軟體套件的每一層部署在其自己的子網中。 Oracle 資料庫或 Microsoft SQL Server 需要作為應用程式的後端資料庫。 本節討論有關使用 Oracle 資料庫後端部署 PeopleSoft 的詳細資訊。

以下是用於在跨雲體系結構中部署 PeopleSoft 應用程式套件的規範體系結構（圖 5）。

![人軟跨雲架構](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*圖 5：人軟跨雲架構*

在此示例體系結構中，Azure 中的虛擬網路使用跨雲互連連接到 OCI 中的虛擬雲網路。 應用程式層在 Azure 中設置，而資料庫在 OCI 中設置。 建議將每個元件部署到具有網路安全性群組的自己的子網，以便僅允許來自特定埠上的特定子網的流量。

該體系結構還可以完全適應 Azure 的部署，並在區域的兩個可用性區域使用 Oracle 資料防護配置了高可用性的 Oracle 資料庫。 下圖（圖 6）是此體系結構模式的示例：

![人軟體 Azure 體系結構](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*圖 6：使用者軟體 Azure 體系結構*

以下各節在高級別上描述不同的元件。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>應用程式層

應用程式層包含 PeopleSoft 應用程式伺服器、PeopleSoft Web 服務器、彈性搜索和 PeopleSoft 進程調度程式的實例。 Azure 負載等化器設置為接受來自使用者的請求，這些請求被路由到應用程式層中的相應伺服器。

為獲得高可用性，請考慮在不同的可用性地區設定應用程式層中每個伺服器的冗余實例。 Azure 負載等化器可以使用多個後端池進行設置，以將每個請求定向到正確的伺服器。

### <a name="peopletools-client"></a>人工具用戶端

PeopleTools 用戶端用於執行管理活動，如開發、遷移和升級。 由於實現應用程式的高可用性不需要 PeopleTools 用戶端，因此不需要 PeopleTools 用戶端的冗余伺服器。

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>後續步驟

使用[Terraform 腳本](https://github.com/microsoft/azure-oracle)在 Azure 中設置 Oracle 應用，並與 OCI 建立跨雲連接。

有關 OCI 的詳細資訊和白皮書，請參閱[Oracle 雲](https://docs.cloud.oracle.com/iaas/Content/home.htm)文檔。
