---
title: SAP HANA 在 Azure 上的基礎結構設定和作業 | Microsoft Docs
description: 部署在 Azure 虛擬機器上之 SAP HANA 系統的操作指南。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 602e3f58ac5f8f194ad4704a4e792d4f0aec3a3e
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978776"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>SAP HANA 在 Azure 上的基礎結構設定和作業
此文件提供設定 Azure 基礎結構和已部署在 Azure 原生虛擬機器 (VM) 上之 SAP Hana 系統的作業指導方針。 此文件也包含 M128s VM SKU 的 SAP HANA 相應放大設定資訊。 這份文件並非用以取代標準 SAP 文件，包含下列內容：

- [SAP 管理指南](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP 安裝指南](https://service.sap.com/instguides)
- [SAP 附註](https://service.sap.com/notes)

## <a name="prerequisites"></a>必要條件
若要使用本指南，您需要下列 Azure 元件的基本知識：

- [Azure 虛擬機器](../../linux/tutorial-manage-vm.md)
- [Azure 網路和虛擬網路](../../linux/tutorial-virtual-network.md)
- [Azure 儲存體](../../linux/tutorial-manage-disks.md)

若要了解有關 SAP NetWeaver 及 Azure 上其他 SAP 元件的詳細資訊，請參閱 [Azure 文件](../../../index.yml)的 [Azure 上的 SAP](./get-started.md)。

## <a name="basic-setup-considerations"></a>基本設定考量
下列各節說明在 Azure VM 上部署 SAP HANA 系統的基本安裝考量。

### <a name="connect-into-azure-virtual-machines"></a>連線到 Azure 虛擬機器
如 [Azure 虛擬機器規劃指南](./planning-guide.md)中所述，有兩種基本方法可連線到 Azure VM：

- 透過網際網路和跳接 VM 上的公用端點進行連線，或是在執行 SAP Hana 的 VM 上進行連線。
- 透過 [VPN](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 或 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 進行連線。

生產案例需要透過 VPN 或 ExpressRoute 的站對站連線能力。 饋送至使用 SAP 軟體之生產案例中的非生產案例也需要這種類型的連線。 下圖顯示跨站台連線能力的範例：

![跨站台連線能力](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>選擇 Azure VM 類型
可以用於生產案例的 Azure VM 類型會列在 [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)。 針對非實際執行案例，有更多種類的原生 Azure VM 類型可供使用。

>[!NOTE]
> 針對非生產案例，請使用 [SAP 附註 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 中所列的 VM 類型。 如需了解適用於生產案例的 Azure 虛擬機器使用方式，請在 SAP 發佈的[認證 IaaS 平台清單](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中查看經 SAP HANA 認證的虛擬機器。

使用下列方式在 Azure 中部署 VM：

- Azure 入口網站。
- Azure PowerShell Cmdlet。
- Azure CLI。

您也可以透過 [SAP Cloud Platform](https://cal.sap.com/)，將完整安裝的 SAP Hana 平台部署到 Azure VM 服務上。 [在 Azure 上部署 SAP S/4HANA 或 BW/4HANA](./cal-s4h.md) 中會敘述安裝程序，或者使用[這裡](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)發行的自動化安裝。

>[!IMPORTANT]
> 若要使用 M208xx_v2 VM，您必須從 Azure VM 映像資源庫中小心選取您的 Linux 映像。 若要閱讀詳細資料，請閱讀[記憶體最佳化的虛擬機器大小](../../mv2-series.md)一文。
> 


### <a name="storage-configuration-for-sap-hana"></a>SAP HANA 的儲存體設定
如需在 Azure 中與 SAP HANA 搭配使用的儲存體設定和儲存體類型，請參閱 [SAP HANA Azure 虛擬機器儲存體設定](./hana-vm-operations-storage.md)一文


### <a name="set-up-azure-virtual-networks"></a>設定 Azure 虛擬網路
當您有透過 VPN 或 ExpressRoute 對 Azure 的站對站連線時，必須至少有一個 Azure 虛擬網路，可透過虛擬閘道連線到 VPN 或 ExpressRoute 線路。 在簡單部署中，也可以將虛擬閘道部署在裝載 SAP HANA 執行個體的 Azure 虛擬網路 (VNet) 子網路中。 若要安裝 SAP HANA，您可以在 Azure 虛擬網路內建立兩個額外子網路。 一個子網路會裝載執行 SAP HANA 執行個體的 VM。 另一個子網路會執行裝載 SAP HANA Studio、其他管理軟體或應用程式軟體的 Jumpbox 或管理 VM。

> [!IMPORTANT]
> 除了功能以外，更重要的是基於效能理由，在 SAP NetWeaver、Hybris 或 S/4HANA 型 SAP 系統的 SAP 應用程式與 DBMS 層之間的通訊路徑中，不支援設定 [Azure 網路虛擬設備](https://azure.microsoft.com/solutions/network-appliances/)。 SAP 應用程式層和 DBMS 層之間的通訊必須是直接的。 這項限制不包含 [Azure ASG 和 NSG 規則](../../../virtual-network/network-security-groups-overview.md)，只要這些 ASG 和 NSG 規則允許直接通訊即可。 如 [SAP NetWeaver 在 SUSE Linux Enterprise Server for SAP Applications 的 Azure VM 上的高可用性](./high-availability-guide-suse.md)所述，不支援 NVA 的進一步案例位於代表 Linux Pacemaker 叢集節點與 SBD 裝置的 Azure VV 之間的通訊路徑中。 或是在如[在 Azure 中使用檔案共用於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](./sap-high-availability-guide-wsfc-file-share.md)所述設定的 Azure VM 與 Windows Server SOFS 之間的通訊路徑中。 通訊路徑中的 NVA 可以輕易地使兩個通訊合作夥伴之間的網路延遲加倍，可以限制在 SAP 應用程式層與 DBMS 層之間重要路徑中的輸送量。 在客戶觀察到的某些情況下，如果 Pacemaker Linux 叢集節點之間的通訊需要透過 NVA 來對其 SBD 裝置通訊，NVA 可能會導致 Linux Pacemaker 叢集失敗。  
> 

> [!IMPORTANT]
> **不**支援的另一種設計是將 SAP 應用程式層與 DBMS 層隔離到不同的 Azure 虛擬網路，而這些虛擬網路彼此間沒有[對等互連](../../../virtual-network/virtual-network-peering-overview.md)。 建議您使用 Azure 的虛擬網路內的子網路來區隔 SAP 應用程式層和 DBMS 層，而不使用不同的 Azure 虛擬網路。 如果您決定不遵循建議，而是要將兩個層隔離到不同的虛擬網路，則這兩個虛擬網路必須要[對等互連](../../../virtual-network/virtual-network-peering-overview.md)。 請注意，兩個[對等互連](../../../virtual-network/virtual-network-peering-overview.md) Azure 虛擬網路之間的網路流量會產生傳輸成本。 由於 SAP 應用程式層和 DBMS 層之間會交換龐大資料量 (以 TB 計算)，如果 SAP 應用程式層和 DBMS 層分別在兩個對等互連的 Azure 虛擬網路中，則會累積大量成本。 

當您安裝執行 SAP HANA 的 VM 時，VM 將會需要：

- 已安裝兩個虛擬 NIC：其中一個 NIC 會連線到管理子網路，另一個 NIC 則會從內部部署網路或其他網路，連線到 Azure VM 中的 SAP Hana 執行個體。
- 已針對這兩個虛擬 NIC 部署的靜態私人 IP 位址。

> [!NOTE]
> 您應該透過 Azure 方式將靜態 IP 位址指派給個別 vNIC。 您不應該將客體 OS 內的靜態 IP 位址指派給 vNIC。 某些 Azure 服務 (例如 Azure 備份服務) 依賴至少將主要 vNIC 設為 DHCP 的事實，而不是設為靜態 IP 位址。 另請參閱[針對 Azure 虛擬機器備份進行疑難排解](../../../backup/backup-azure-vms-troubleshoot.md#networking)文件。 如果您需要將多個靜態 IP 位址指派給 VM，必須將多個 vNIC 指派給 VM。
>
>

不過，對於持續進行的部署，您需要在 Azure 中建立虛擬資料中心網路架構。 此架構建議將會連線到內部部署的 Azure VNet 閘道分隔在個別的 Azure VNet 中。 此個別 VNet 應裝載要送至內部部署或網際網路的所有流量。 這種方法可讓您在此個別的中樞 VNet 部署軟體，稽核和記錄進入 Azure 虛擬資料中心的流量。 因此，您會有一個 VNet，裝載與 Azure 部署的傳入和傳出流量相關的所有軟體和設定。

[Azure 虛擬資料中心：網路觀點](/azure/architecture/vdc/networking-virtual-datacenter)與 [Azure 虛擬資料中心和 Enterprise 控制平面](/azure/architecture/vdc/)等文章，可為虛擬資料中心方法與相關 Azure VNet 設計提供詳細資訊。


>[!NOTE]
>使用 [Azure VNet 同儕節點](../../../virtual-network/virtual-network-peering-overview.md)在中樞 VNet 與輪輻 VNet 之間流動的流量，則是額外[成本](https://azure.microsoft.com/pricing/details/virtual-network/)。 根據這些成本，您可能需要在執行嚴格中樞與輪輻網路設計，以及執行多個 [Azure ExpressRoute 閘道](../../../expressroute/expressroute-about-virtual-network-gateways.md)，連線到「輪輻」以略過 VNet 同儕節點，這兩者之間考量折衷辦法。 不過，Azure ExpressRoute 閘道也會產生額外[成本](https://azure.microsoft.com/pricing/details/vpn-gateway/)。 您用來進行網路流量記錄、稽核及監視的協力廠商軟體，也可能會產生額外成本。 取決於透過 VNet 同儕節點和另一端進行資料交換的成本，以及其他 Azure ExpressRoute 閘道與額外軟體授權產生的成本，您可以決定在一個 VNet 進行微型分割時，要使用子網路作為隔離單位，而不是 VNet。


如需以不同方法指派 IP 位址的概觀，請參閱 [Azure 中的 IP 位址類型及配置方法](../../../virtual-network/public-ip-addresses.md)。 

針對執行 SAP HANA 的虛擬機器，您應該使用指派的靜態 IP 位址。 原因是 HANA 的某些組態屬性會參考 IP 位址。

[Azure 網路安全性群組 (NSG)](../../../virtual-network/virtual-network-vnet-plan-design-arm.md) 是用來將路由傳送至 SAP HANA 執行個體或 Jumpbox 的流量進行導向。 NSG 和[應用程式安全性群組](../../../virtual-network/network-security-groups-overview.md#application-security-groups)最終都會與 SAP HANA 子網路和管理子網路相關聯。

下圖顯示遵循中樞與輪輻 VNet 架構的 SAP HANA 粗略部署結構描述概觀：

![SAP Hana 粗略部署結構描述](media/hana-vm-operations/hana-simple-networking-dmz.png)

若要在 Azure 中部署不具站台對站台連線能力的 SAP HANA，您仍想要保護 SAP HANA 執行個體不受公用網際網路危害，並隱藏在正向 Proxy 後方。 在這個基本案例中，部署會仰賴 Azure 的內建 DNS 服務來解析主機名稱。 在使用公眾對應 IP 位址的較複雜部署中，Azure 內建的 DNS 服務特別重要。 使用 Azure NSG 與 [Azure NVA](https://azure.microsoft.com/solutions/network-appliances/) 來控制、監視從網際網路到 Azure 中 Azure VNet 架構的路由。 下圖顯示以中樞與輪輻 VNet 架構部署不具站對站連線能力的 SAP HANA 概略結構描述：
  
![不具站台對站台連線能力的 SAP Hana 粗略部署結構描述](media/hana-vm-operations/hana-simple-networking-dmz.png)
 

另一個如何使用 Azure NVA 以控制和監視來自網際網路的存取，而不使用中樞與輪輻 VNet 架構的描述，請參閱[部署高可用性的網路虛擬設備](/azure/architecture/reference-architectures/dmz/nva-ha)一文。


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>設定 SAP HANA 相應放大的 Azure 基礎結構
若要找出已通過 OLAP 擴增或 S/4HANA 擴增認證的 Azure VM 類型，請檢查 [SAP HANA 硬體目錄](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。 「叢集」資料行中的核取記號指出支援擴增。 應用程式類型指出支援 OLAP 擴增或 S/4HANA 擴增。 如需每個 VM 的擴增認證節點的詳細資訊，請查看 SAP HANA 硬體目錄中所列之特定 VM SKU 中的項目詳細資料。

在 Azure VM 中部署擴增設定的最低 OS 版本，請檢查 SAP Hana 硬體目錄中所列的特定 VM SKU 中內含項目的詳細資料。 在 n 節點 OLAP 擴增設定中，一個節點會作為主要節點。 其他節點 (數目上限受憑證限制) 則作為背景工作角色節點。 其他待命節點不會計入已認證的節點數目中

>[!NOTE]
> 包含待命節點之 SAP HANA 的 Azure VM 擴增部署，僅能使用 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 儲存體。 沒有其他 SAP HANA 認證的 Azure 儲存體可讓您設定 SAP HANA 待命節點
>

針對 /hana/shared，我們也建議使用 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)。 

因此，使用擴增所設定單一節點的典型基本設計，看起來就像下面這樣：

![單一節點的相應放大基本概念](media/hana-vm-operations/scale-out-basics-anf-shared.PNG)

SAP HANA 相應放大的 VM 節點基本設定看起來像這樣：

- 針對 **/hana/shared**，您可以使用透過 Azure NetApp Files 提供的原生 NFS 服務。 
- 所有其他的磁碟區都不會在不同節點間共用，而且也不是以 NFS 為基礎。 使用非共用 **/hana/data** 與 **hana/log** 之擴增 HANA 安裝的安裝設定與步驟，稍後都會在此文件中進一步提供。 如需可使用的 HANA 認證儲存體，請參閱 [SAP HANA Azure 虛擬機器儲存體設定](./hana-vm-operations-storage.md)一文。


若要調整磁碟區或磁碟的大小，您必須查看 [SAP Hana TDI 儲存體需求](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) \(英文\) 文件，以了解根據背景工作角色節點的數目來決定的所需大小。 文件會釋出公式，您需要套用該公式以取得磁碟區所需的容量

擴增 SAP HANA VM 的單一節點設定圖形中顯示的另一個設計準則，就是 VNet 或更好的子網路設定。 SAP 強烈建議從 HANA 節點間的通訊分隔用戶端/應用程式對應流量。 如圖所示，將兩個不同的 vNIC 附加到 VM，即可達成此目的。 這兩個 vNIC 位在不同的子網路，有兩個不同的 IP 位址。 接著，您可以使用 NSG 或使用者定義的路由，透過路由規則控制流量的流動。

特別是在 Azure 中，無法對特定的 vNIC 強制使用服務品質與配額。 因此，分隔用戶端/應用程式對應流量與節點間通訊，才不會有機會將一個流量串流設定為優先於其他串流。 相反地，仍可以透過分隔來保護相應放大設定的節點間通訊安全。  

>[!NOTE]
>SAP 建議分隔用戶端/應用程式端的網路流量與節點間流量，如此文件中所述。 因此，建議您如最後一個圖形所示來建立架構。 也請諮詢您的安全性與合規性小組，以取得偏離建議的需求 
>

從網路的觀點來看，最小的必要網路架構如下：

![單一節點的相應放大基本概念](media/hana-vm-operations/overview-scale-out-networking.png)



### <a name="installing-sap-hana-scale-out-n-azure"></a>在 Azure 中安裝 SAP HANA 相應放大
安裝相應放大 SAP 設定，您需要執行的概略步驟如下：

- 部署新的或採用現有的 Azure VNet 基礎結構
- 使用 Azure 受控進階儲存體、Ultra 磁碟區和/或以 ANF 為基礎的 NFS 磁碟區，來部署新的 VM
- - 採用網路路由以確保像是 VM 之間的節點間通訊不會透過 [NVA](https://azure.microsoft.com/solutions/network-appliances/) 路由傳送。 
- 安裝 SAP HANA 主要節點。
- 採用 SAP HANA 主要節點的設定參數
- 繼續安裝 SAP HANA 背景工作角色節點

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>以相應放大設定安裝 SAP HANA
當您部署 Azure VM 基礎結構且所有其他的準備工作都已完成時，您需要以下列步驟安裝 SAP HANA 相應放大設定：

- 根據 SAP 的文件，安裝 SAP HANA 主要節點
- 如果使用 Azure 進階儲存體或 Ultra 磁碟儲存體搭配 /hana/data 與 /hana/log 的非共用磁碟，您需要變更 global.ini 檔案，並將參數 'basepath_shared = no' 新增至 global.ini 檔案。 此參數會讓 SAP HANA 以擴增方式執行，而不需要在節點之間「共用的」 **/hana/data** 與 **/hana/log** 磁碟區。 詳細資料記載於 [SAP 附註編號 2080991](https://launchpad.support.sap.com/#/notes/2080991) \(英文\)。 如果您因為 /hana/data 與 /hana/log 的緣故，而使用以 ANF 為基礎的 NFS 磁碟區，就不需要進行此變更
- 在 global.ini 參數中進行最終變更之後，請重新啟動 SAP HANA 執行個體
- 新增其他背景工作角色節點。 另請參閱 <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>。 在安裝期間或安裝後，使用本機 hdblcm 之類，指定用於進行 SAP HANA 內部節點間通訊的內部網路。 如需詳細文件，請參閱 [SAP 附註編號 2183363](https://launchpad.support.sap.com/#/notes/2183363) \(英文\)。 

如需在 SUSE Linux 上使用待命節點設定 SAP HANA 擴增系統的細節，請參閱[在 SUSE Linux Enterprise Server 上使用 Azure NetApp Files 於 Azure VM 上部署 SAP HANA 擴增系統與待命節點](./sap-hana-scale-out-standby-netapp-files-suse.md) \(部分機器翻譯\) 中的詳細說明。 如需 Red Hat 的對等文件，請參閱[在 Red Hat Enterprise Linux 上使用 Azure NetApp Files 在 Azure VM 上部署 SAP HANA 擴增系統與待命節點](./sap-hana-scale-out-standby-netapp-files-rhel.md) \(部分機器翻譯\) 一文。 


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>適用於 Azure 虛擬機器的 SAP HANA Dynamic Tiering 2.0

除了 Azure M 系列 VM 上的 SAP HANA 認證，Microsoft Azure 也支援 SAP HANA Dynamic Tiering 2.0 (請參閱更底下的 SAP HANA Dynamic Tiering 文件連結)。 安裝或操作此產品時雖沒有任何差異，(例如，透過 Azure 虛擬機器中的 SAP HANA Cockpit)，但是在 Azure 上正式支援有一些必要的重要事項。 以下說明這些關鍵點。 在此文章中，將使用縮寫 "DT 2.0"，而不是使用全名 Dynamic Tiering 2.0。

SAP BW 或 S4HANA 不支援 SAP HANA Dynamic Tiering 2.0。 主要使用案例現在為原生 HANA 應用程式。


### <a name="overview"></a>概觀

下圖提供有關 Microsoft Azure 上的 DT 2.0 支援概觀。 必須遵循一組必要需求，才能符合正式憑證：

- DT 2.0 必須安裝在專用的 Azure VM 上。 無法在 SAP HANA 執行所在的相同 VM 上執行
- SAP HANA 和 DT 2.0 VM 必須部署在相同的 Azure Vnet 內
- 必須部署已啟用 Azure 加速網路的 SAP HANA 和 DT 2.0 VM
- DT 2.0 VM 的儲存體類型必須是 Azure 進階儲存體
- 必須將多個 Azure 磁碟連結至 DT 2.0 VM
- 必須利用等量分割於多個 Azure 磁碟來建立軟體 raid/等量磁碟區 (透過 lvm 或 mdadm)

下列各節將會說明更多詳細資料。

![SAP HANA DT 2.0 架構概觀](media/hana-vm-operations/hana-data-tiering.png)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>SAP HANA DT 2.0 專用的 Azure VM

在 Azure IaaS 上，只有專用 VM 才支援 DT 2.0。 不得在 HANA 執行個體執行所在的相同 Azure VM 上執行 DT 2.0。 一開始有兩個 VM 類型可用於執行 SAP HANA DT 2.0：

- M64-32ms 
- E32sv3 

如需 VM 類型說明，請參閱[這裡](../../sizes-memory.md)

考量到 DT 2.0 的基本概念 (關於卸載「暖」資料來節省成本)，使用對應的 VM 大小合乎情理。 可能的組合沒有相關的嚴格規則。 這取決於特定客戶工作負載。

建議的組態如下：

| SAP HANA VM 類型 | DT 2.0 VM 類型 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


SAP HANA 認證 M 系列 VM 與所支援 DT 2.0 VM (M64-32ms 和 E32sv3) 的所有組合都可行。


### <a name="azure-networking-and-sap-hana-dt-20"></a>Azure 網路功能與 SAP HANA DT 2.0

在專用 VM 上安裝 DT 2.0 時，DT 2.0 VM 與 SAP HANA VM 之間需要最少 10 Gb 的網路輸送量。 因此，一定要將所有 VM 放在相同的 Azure Vnet 內並啟用 Azure 加速網路。

如需 Azure 加速網路的其他資訊，請參閱[這裡](../../../virtual-network/create-vm-accelerated-networking-cli.md)

### <a name="vm-storage-for-sap-hana-dt-20"></a>適用於 SAP HANA DT 2.0 的 VM 儲存體

根據 DT 2.0 最佳做法指引，每個實體核心的最小磁碟 IO 輸送量應該是 50 MB/秒。 查看 DT 2.0 支援的兩個 Azure VM 類型的規格，VM 的最大磁碟 IO 輸送量限制看起來會像下面這樣：

- E32sv3    ： 768 MB/sec (未快取) 表示每個實體核心的速率為 48 MB/秒
- M64-32ms  ：1000 MB/sec (未快取) 表示每個實體核心的速率為 62.5 MB/秒

必須將多個 Azure 磁碟連結至 DT 2.0 VM，並在 OS 層級建立軟體 raid (等量分割)，以達到每個 VM 的磁碟輸送量最大限制。 單一 Azure 磁碟無法提供可達到這方面最大 VM 限制的輸送量。 需有 Azure 進階儲存體才能執行 DT 2.0。 

- 如需 Azure 磁碟類型的詳細資訊，請參閱[這裡](../../disks-types.md)
- 有關透過 mdadm 建立軟體 raid 的詳細資訊，請參閱[這裡](../../linux/configure-raid.md)
- 有關設定 LVM 來建立等量磁碟區以達到最大輸送量的詳細資訊，請參閱[這裡](../../linux/configure-lvm.md)

根據大小需求，有不同的選項可達到 VM 的最大輸送量。 以下是可能的資料磁碟區磁碟組態，可讓每個 DT 2.0 VM 類型達到 VM 輸送量上限。 E32sv3 VM 應視為較小工作負載的入門層級。 萬一後來變得不夠快，則可能需要將 VM 大小調整為 M64-32ms。
M64-32ms VM 有很多記憶體，因此 IO 負載可能無法達到特別為讀取密集工作負載所設定的限制。 因此，視客戶特定工作負載而定，等量集合中少量磁碟可能就足夠。 不過，爲了以防萬一而選擇了下列磁碟組態，以保證最大輸送量：


| VM SKU | 磁碟組態 1 | 磁碟組態 2 | 磁碟組態 3 | 磁碟組態 4 | 磁碟組態 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3.5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2.5 TB | 6 x P15 -> 1.5 TB | 


特別是在工作負載為密集讀取的情況下，可以提升 IO 效能，進而為資料庫軟體的資料磁碟區開啟 Azure 主機快取「唯讀」 (建議做法)。 而對於交易記錄，Azure 主機磁碟快取必須是「無」。 

就記錄磁碟區的大小而論，建議的起始點為 15% 資料大小的啟發學習法。 根據成本和輸送量需求，使用不同的 Azure 磁碟類型，即可完成記錄磁碟區建立。 針對記錄磁碟區，需要高 I/O 輸送量。  如果使用 VM 類型 M64-32ms，就會強制啟用[寫入加速器](../../how-to-enable-write-accelerator.md) \(部分機器翻譯\)。 Azure 寫入加速器可為交易記錄提供最佳的磁碟寫入延遲 (僅適用於 M 系列)。 此外，還有一些需要考量的事項，像是每個 VM 類型的磁碟數目上限。 在[這裡](../../how-to-enable-write-accelerator.md)可以找到寫入加速器的詳細資訊


以下是一些關於調整記錄磁碟區大小的範例：

| 資料磁碟區大小和磁碟類型 | 記錄磁碟區和磁碟類型組態 1 | 記錄磁碟區和磁碟類型組態 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2.5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1.5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


如同 SAP HANA 相應放大一樣，/hana/shared 目錄必須在 SAP HANA VM 與 DT 2.0 VM 之間共用。 對於使用專用 VM 作為高可用性 NFS 伺服器的 SAP HANA 相應放大，建議使用相同的架構。 若要提供共用備份磁碟區，可以使用相同的設計。 但是，由客戶決定 HA 是否必要，或者只使用具有足夠儲存體容量的專用 VM 是否足以作為備份伺服器。



### <a name="links-to-dt-20-documentation"></a>DT 2.0 文件的連結 

- [SAP HANA Dynamic Tiering installation and update guide](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA Dynamic Tiering tutorials and resources](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA Dynamic Tiering PoC](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2.0 SPS 02 dynamic tiering enhancements](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>在 Azure VM 上部署 SAP HANA 的作業
下列各節說明關於在 Azure VM 上部署 SAP HANA 系統的一些作業。

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Azure VM 上的備份和還原作業
下列文件說明如何備份及還原您的 SAP HANA 部署：

- [SAP HANA 備份概觀](./sap-hana-backup-guide.md)
- [SAP HANA 檔案層級備份](./sap-hana-backup-file-level.md)
- [SAP Hana 儲存體快照集基準](./sap-hana-backup-guide.md)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>啟動及重新啟動包含 SAP HANA 的 VM
Azure 公用雲端的重要功能是您僅需支付運算的分鐘數。 例如，當您關閉執行 SAP HANA 的 VM 時，您只需要支付這段時間的儲存體成本。 當您在初始部署時指定 VM 的靜態 IP 位址，就可使用另一項功能。 當您重新啟動具有 SAP HANA 的 VM 時，VM 會使用其先前的 IP 位址來重新啟動。 


### <a name="use-saprouter-for-sap-remote-support"></a>使用 SAPRouter 以取得 SAP 遠端支援
如果您有內部部署位置與 Azure 之間的站對站連線，且您是執行 SAP 元件，則您可能已在執行 SAProuter。 在此情況下，請完成下列項目以取得遠端支援：

- 維護在 SAProuter 組態中裝載 SAP HANA 之 VM 的私用和靜態 IP 位址。
- 設定裝載 HANA VM 的子網路之 NSG，以允許透過 TCP/IP 通訊埠 3299 的流量。

如果您要透過網際網路連線到 Azure，且您沒有 SAP 路由器可供包含 SAP HANA 的 VM 使用，就需要安裝此元件。 在管理子網路中，將 SAProuter 安裝在不同的 VM 中。 下圖顯示不需要透過站對站連線、而需要 SAProuter 部署 SAP HANA 的概略結構描述：

![不具站台對站台連線能力和 SAProuter 的 SAP Hana 粗略部署結構描述](media/hana-vm-operations/hana-simple-networking-saprouter.png)

請務必在不同的 VM 上安裝 SAPRouter，而非安裝在 Jumpbox VM 上。 不同的 VM 必須有靜態 IP 位址。 若要將您的 SAProuter 連線至 SAP 所裝載的 SAProuter，請連絡 SAP 以索取 IP 位址。 (由 SAP 裝載的 SAProuter 是您在 VM 上安裝之 SAProuter 執行個體的對應項目)。使用 SAP 提供的 IP 位址來設定 SAProuter 執行個體。 在組態集中，唯一必要的連接埠是 TCP 通訊埠 3299。

如需有關如何設定和維護透過 SAProuter 的遠端連線詳細資訊，請參閱 [SAP 文件](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)。

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>在 Azure 原生 VM 上具有 SAP Hana 的高可用性
如果您是執行 SUSE Linux Enterprise Server 或 Red Hat，可以使用 STONITH 裝置建立 Pacemaker 叢集。 您可以使用裝置設定 SAP HANA 組態，該組態是使用包含 HANA 系統複寫以及自動容錯移轉的同步複寫。 如需「後續步驟」一節中所列的詳細資訊。

## <a name="next-steps"></a>後續步驟
熟悉列出的文章
- [SAP HANA Azure 虛擬機器儲存體設定](./hana-vm-operations-storage.md)
- [在 SUSE Linux Enterprise Server 上使用 Azure NetApp Files 於 Azure VM 上部署 SAP HANA 擴增系統與待命節點](./sap-hana-scale-out-standby-netapp-files-suse.md) \(部分機器翻譯\)
- [在 Red Hat Enterprise Linux 上使用 Azure NetApp Files 於 Azure VM 上部署 SAP HANA 擴增系統與待命節點](./sap-hana-scale-out-standby-netapp-files-rhel.md) \(部分機器翻譯\)
- [SUSE Linux Enterprise Server 上 Azure VM 中 SAP HANA 的高可用性](./sap-hana-high-availability.md) \(部分機器翻譯\)
- [Red Hat Enterprise Linux 上 Azure VM 中 SAP HANA 的高可用性](./sap-hana-high-availability-rhel.md) \(部分機器翻譯\)

