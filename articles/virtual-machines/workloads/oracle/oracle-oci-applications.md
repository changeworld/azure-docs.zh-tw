---
title: 在 Azure 虛擬機器上部署 Oracle 應用程式的架構 |Microsoft Docs
description: 用來部署 Oracle 應用程式的應用程式架構（包括電子商務套件、JD Edwards EnterpriseOne 和 PeopleSoft），適用于 Azure 或 Oracle 雲端基礎結構中的資料庫 Microsoft Azure 虛擬機器 (OCI) 。
services: virtual-machines-linux
documentationcenter: ''
author: dbakevlar
manager: ''
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: kegorman
ms.custom: ''
ms.openlocfilehash: 838bd2014f543747a3c3ec7edee7b278f5f4d8df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91274597"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>在 Azure 上部署 Oracle 應用程式的架構

Microsoft 和 Oracle 合作，讓客戶能夠在雲端中部署 Oracle 電子商務套件、JD Edwards EnterpriseOne 和 PeopleSoft 等 Oracle 應用程式。 隨著 Microsoft Azure 與 Oracle 雲端基礎結構之間的預覽 [私人網路互連能力](configure-azure-oci-networking.md) (OCI) ，現在可以使用 AZURE 或 OCI 中的後端資料庫，將 oracle 應用程式部署在 azure 上。 Oracle 應用程式也可以與 Azure Active Directory 整合，讓您可以設定單一登入，讓使用者可以使用其 Azure Active Directory (Azure AD) 認證來登入 Oracle 應用程式。

OCI 為 Oracle 應用程式提供多個 Oracle 資料庫選項，包括 DBaaS、Exadata 雲端服務、Oracle RAC，以及基礎結構即服務 (IaaS) 。 目前，自主資料庫不是 Oracle 應用程式支援的後端。

在 Azure 中部署 Oracle 應用程式有 [多個選項](oracle-overview.md) ，包括以高可用性和安全的方式。 如果您選擇在 Azure 上完全執行您的 Oracle 應用程式，Azure 也會提供您可以部署的 [oracle 資料庫 VM 映射](oracle-vm-solutions.md) 。

下列各節概述 Microsoft 和 Oracle 在跨雲端設定中或完全在 Azure 中部署 Oracle 電子商務套件、JD Edwards EnterpriseOne 和 PeopleSoft 的架構建議。 Microsoft 和 Oracle 已測試過這些應用程式，並確認效能符合 Oracle 針對這些應用程式所設定的標準。

## <a name="architecture-considerations"></a>架構考慮

Oracle 應用程式是由多個服務所組成，這些服務可以裝載在 Azure 中的相同或多部虛擬機器上，並選擇性地在 OCI 中。 

您可以使用私用或公用端點來設定應用程式實例。 Microsoft 和 Oracle 建議在個別的子網中，使用公用 IP 位址來設定防禦 *主機 VM* ，以管理應用程式。 然後，只將私人 IP 位址指派給其他電腦，包括資料庫層。 

在跨雲端架構中設定應用程式時，需要規劃，以確保 Azure 虛擬網路中的 IP 位址空間不會與 OCI 虛擬雲端網路中的私人 IP 位址空間重迭。

為了提高安全性，請在子網層級設定網路安全性群組，以確保只允許特定埠和 IP 位址上的流量。 例如，仲介層中的機器應該只接收來自虛擬網路內的流量。 沒有任何外部流量應直接連線到仲介層電腦。

為了達到高可用性，您可以在相同的可用性設定組或不同的可用性區域中，設定不同伺服器的重複實例。 可用性區域可讓您達到99.99% 的執行時間 SLA，而可用性設定組可讓您在區域中達到99.95% 的執行時間 SLA。 本文所顯示的範例架構會部署在兩個可用性區域中。

使用跨雲端互連部署應用程式時，您可以繼續使用現有的 ExpressRoute 線路將您的 Azure 環境連線至內部部署網路。 不過，與連線到內部部署網路的 OCI 不同，您需要不同的 ExpressRoute 線路來進行互連。

## <a name="e-business-suite"></a>E-Business Suite

Oracle 電子商務套件 (EBS) 是一套應用程式，其中包含供應鏈管理 (SCM) 和客戶關係管理 (CRM) 。 若要利用 OCI 的受控資料庫組合，可以使用 Microsoft Azure 與 OCI 之間的跨雲端互連來部署 EBS。 在此設定中，展示層和應用層會在 Azure 中執行，並在 OCI 中執行資料庫層，如下列架構圖所示 (圖 1) 。

![電子商務套件跨雲端架構](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*圖1：電子商務套件跨雲端架構* 

在此架構中，Azure 中的虛擬網路會使用跨雲端互連連線到 OCI 中的虛擬雲端網路。 應用層會在 Azure 中設定，而資料庫則是在 OCI 中設定。 建議您將每個元件部署至具有網路安全性群組的子網，以只允許來自特定埠上特定子網的流量。

您也可以使用區域中兩個可用性區域內的 Oracle Data Guard 設定的高可用性 Oracle 資料庫，調整此架構以完全在 Azure 上進行部署。 下圖 ([圖 2]) 是此架構模式的範例：

![電子商務套件僅限 Azure 架構](media/oracle-oci-applications/ebs-arch-azure.png)

*圖2：僅限 Azure 的電子商務套件架構*

下列各節描述高階的不同元件。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>應用程式 (中間) 層

應用層會在它自己的子網中隔離。 有多個虛擬機器設定容錯和簡單修補程式管理。 這些 Vm 可由 Azure NetApp Files 和 Ultra Ssd 提供的共用儲存體支援。 這項設定可讓您更輕鬆地部署修補程式，而不需要停機。 應用層中的電腦應由公用負載平衡器前端，如此一來，即使階層中的一部機器因為錯誤而離線，仍會處理 EBS 應用層的要求。

### <a name="load-balancer"></a>負載平衡器

Azure 負載平衡器可讓您將流量分散到工作負載的多個實例，以確保高可用性。 在此情況下，會設定公用負載平衡器，因為允許使用者透過 web 存取 EBS 應用程式。 負載平衡器會將負載分散到仲介層中的兩部電腦。 為了增加安全性，只允許來自使用站對站 VPN 或 ExpressRoute 和網路安全性群組從公司網路存取系統的使用者流量。

### <a name="database-tier"></a>資料庫層

這一層會裝載 Oracle 資料庫，並將其分成自己的子網。 建議您新增網路安全性群組，只允許來自應用層的流量流向 Oracle 特定資料庫埠1521上的資料庫層。

Microsoft 和 Oracle 建議使用高可用性設定。 您可以使用 Oracle Data Guard 在兩個可用性區域中設定兩個 Oracle 資料庫，或在 OCI 中使用 Oracle Database Exadata Cloud 服務，來達成 Azure 的高可用性。 使用 Oracle Database Exadata 雲端服務時，您的資料庫會部署在兩個子網中。 您也可以使用 Oracle Data Guard，在兩個可用性網域中 OCI 的 Vm 中設定 Oracle Database。


### <a name="identity-tier"></a>身分識別層

身分識別層包含 EBS Asserter VM。 EBS Asserter 可讓您同步處理 Oracle Identity Cloud Service (IDCS) 和 Azure AD 的身分識別。 EBS Asserter 是必要的，因為 EBS 不支援 SAML 2.0 或 OpenID Connect 之類的單一登入通訊協定。 EBS Asserter 會使用 IDCS) 所產生的 OpenID connect 權杖 (、驗證該權杖，然後在 EBS 中建立使用者的會話。 

雖然此架構顯示 IDCS 整合，但 Azure AD 整合存取和單一登入，也可以使用 Oracle Internet Directory 或 Oracle 統一目錄來啟用 Oracle 存取管理員。 如需詳細資訊，請參閱 [使用 IDCS 整合部署 ORACLE ebs](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) 或 [使用 OAM 整合部署 oracle ebs](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf)的白皮書。

為了達到高可用性，建議您在多個可用性區域間部署 EBS Asserter 的多餘伺服器，並在其前面加上負載平衡器。

當您的基礎結構設定完成之後，就可以遵循 Oracle 所提供的安裝指南來安裝電子商務套件。

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

Oracle 的 JD Edwards EnterpriseOne 是一套全方位企業資源規劃軟體的整合式應用程式套件。 它是一種多層式應用程式，可以使用 Oracle 或 SQL Server 資料庫後端來設定。 本節討論在 OCI 或 Azure 中使用 Oracle 資料庫後端部署 JD Edwards EnterpriseOne 的詳細資料。

在下列建議的架構中 (圖 3) ，系統管理、簡報和仲介層會部署至 Azure 中的虛擬網路。 資料庫會部署在 OCI 的虛擬雲端網路中。

如同使用電子商務套件，您可以為安全的管理用途設定選用的防禦層。 使用堡壘 VM 主機作為跳躍伺服器，以存取應用程式和資料庫實例。

![JD Edwards EnterpriseOne 跨雲端架構](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*圖3： JD Edwards EnterpriseOne 跨雲端架構*

在此架構中，Azure 中的虛擬網路會使用跨雲端互連連線到 OCI 中的虛擬雲端網路。 應用層會在 Azure 中設定，而資料庫則是在 OCI 中設定。 建議您將每個元件部署至具有網路安全性群組的子網，以只允許來自特定埠上特定子網的流量。

您也可以使用區域中兩個可用性區域內的 Oracle Data Guard 設定的高可用性 Oracle 資料庫，調整此架構以完全在 Azure 上進行部署。 下圖 ([圖 4]) 是此架構模式的範例：

![JD Edwards EnterpriseOne 僅限 Azure 架構](media/oracle-oci-applications/jdedwards-arch-azure.png)

*圖4： JD Edwards EnterpriseOne 僅限 Azure 架構*

下列各節描述高階的不同元件。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>系統管理層

顧名思義，這一層是用於系統管理工作。 您可以切割系統管理層的個別子網。 這一層中的服務和伺服器主要用於安裝和管理應用程式。 因此，這些伺服器的單一實例就已足夠。 應用程式的高可用性不需要重複的實例。

這一層的元件如下所示：
    
 - 布建**伺服器**-此伺服器是用來進行應用程式不同元件的端對端部署。 它會透過埠22，與其他層中的實例（包括資料庫層中的實例）通訊。 它會裝載 JD Edwards EnterpriseOne 的伺服器管理員主控台。
 - **部署伺服器** -安裝 JD Edwards EnterpriseOne 時，主要需要此伺服器。 在安裝過程中，此伺服器會作為必要檔案和安裝套件的中央存放庫。 此軟體會發佈或部署到此伺服器上的其他伺服器和用戶端。
 - **開發用戶端** -此伺服器包含可在網頁瀏覽器和原生應用程式中執行的元件。

### <a name="presentation-tier"></a>展示層

這一層包含各種元件，例如應用程式介面服務 (AIS) 、應用程式開發架構 (ADF) 和 JAVA 應用程式伺服器 (JA) 。 這一層中的伺服器會與仲介層中的伺服器通訊。 它們是由負載平衡器所前端，可根據接收流量的埠號碼和 URL，將流量路由傳送至所需的伺服器。 建議您為每個伺服器類型部署多個實例，以獲得高可用性。

以下是此層中的元件：
    
- **應用程式介面服務 (ais) ** -ais 伺服器提供 JD Edwards EnterpriseOne mobile enterprise APPLICATIONS 和 JD Edwards EnterpriseOne 之間的通訊介面。
- **JAVA 應用程式伺服器 (ja) ** -ja 會接收來自負載平衡器的要求，並將其傳遞到仲介層以執行複雜的工作。 JA 能夠執行簡單的商務邏輯。
- **BI Publisher Server (BIP) ** -此伺服器會根據 JD Edwards EnterpriseOne 應用程式所收集的資料提供報告。 您可以根據不同的範本來設計和控制報表轉譯資料的方式。
- **商務服務伺服器 (bss) ** -BSS 可與其他 Oracle 應用程式進行資訊交換和互通性。
- **即時事件伺服器 (RTE) ** -rte 伺服器可讓您設定 JDE EnterpriseOne 系統中發生交易時的外部系統通知。 它會使用訂閱者模型，並允許協力廠商系統訂閱事件。 若要將要求負載平衡至兩部 RTE 伺服器，請確定伺服器位於叢集中。
- **應用程式開發架構 (adf) Server** -adf 伺服器是用來執行以 Oracle ADF 開發的 JD Edwards EnterpriseOne 應用程式。 這會部署在具有 ADF 執行時間的 Oracle WebLogic 伺服器上。

### <a name="middle-tier"></a>中介層

仲介層包含邏輯伺服器和批次伺服器。 在此情況下，這兩部伺服器都安裝在相同的虛擬機器上。 不過，在實際執行案例中，建議您在不同的伺服器上部署邏輯伺服器和批次伺服器。 跨兩個可用性區域的中介層會部署多部伺服器，以提供更高的可用性。 應該建立一個 Azure 負載平衡器，這些伺服器應放置在其後端集區中，以確保兩部伺服器都是作用中和處理要求。

仲介層中的伺服器會接收來自展示層和公用負載平衡器中伺服器的要求。 必須將網路安全性群組規則設定為拒絕來自展示層子網與負載平衡器以外任何位址的流量。 您也可以設定 NSG 規則，以允許來自防禦主機的埠22上的流量，以供管理之用。 您可以使用公用負載平衡器來對仲介層中的 Vm 之間的要求進行負載平衡。

下列兩個元件位於中介層：

- **邏輯伺服器** ：包含商務邏輯或商務功能。
- **Batch 伺服器** -用於批次處理

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

Oracle 的 PeopleSoft application suite 包含適用于人力資源與財務管理的軟體。 應用程式套件是多層式的應用程式，包括人力資源管理系統 (HRMS) 、客戶關係管理 (CRM) 、財務和供應鏈管理 (FSCM) ，以及企業效能管理 (EPM) 。

建議您在自己的子網中部署軟體套件的每一層。 需要 Oracle 資料庫或 Microsoft SQL Server 作為應用程式的後端資料庫。 本節討論使用 Oracle 資料庫後端部署 PeopleSoft 的詳細資料。

以下是在跨雲端架構中部署 PeopleSoft 應用程式套件的標準架構 (圖 5) 。

![PeopleSoft 跨雲端架構](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*圖5： PeopleSoft 跨雲端架構*

在此範例架構中，Azure 中的虛擬網路會使用跨雲端互連連線到 OCI 中的虛擬雲端網路。 應用層會在 Azure 中設定，而資料庫則是在 OCI 中設定。 建議您將每個元件部署至具有網路安全性群組的子網，以只允許來自特定埠上特定子網的流量。

您也可以使用區域中兩個可用性區域內的 Oracle Data Guard 設定的高可用性 Oracle 資料庫，調整此架構以完全在 Azure 上進行部署。 下圖 (圖 6) 是此架構模式的範例：

![PeopleSoft 僅適用于 Azure 的架構](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*圖6： PeopleSoft 僅限 Azure 的架構*

下列各節描述高階的不同元件。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>應用程式層

應用層包含 PeopleSoft 應用程式伺服器、PeopleSoft web 伺服器、彈性搜尋和 PeopleSoft 進程排程器的實例。 Azure 負載平衡器已設定為接受來自使用者的要求，這些要求會路由傳送至應用層中的適當伺服器。

為了達到高可用性，請考慮跨不同可用性區域，在應用層中設定每部伺服器的重複實例。 您可以使用多個後端集區來設定 Azure 負載平衡器，以將每個要求導向至正確的伺服器。

### <a name="peopletools-client"></a>PeopleTools 用戶端

PeopleTools 用戶端是用來執行管理活動，例如開發、遷移和升級。 因為 PeopleTools 用戶端不需要達到應用程式的高可用性，所以不需要 PeopleTools 用戶端的多餘伺服器。

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>後續步驟

使用 [Terraform 腳本](https://github.com/microsoft/azure-oracle) ，在 Azure 中設定 Oracle 應用程式，並使用 OCI 建立跨雲端的連線能力。

如需 OCI 的詳細資訊和技術白皮書，請參閱 [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) 文件。
