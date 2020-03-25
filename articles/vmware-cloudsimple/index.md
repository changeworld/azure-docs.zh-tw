---
title: 由 CloudSimple 提供的 Azure VMware 解決方案
description: Azure VMware Solution by CloudSimple 的文件入口網站。
author: sharaths-cs
ms.author: b-mashar
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 936a9eb4d9b33b45800fab213fc72a306a04146e
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2020
ms.locfileid: "77025209"
---
# <a name="azure-vmware-solution-by-cloudsimple"></a>由 CloudSimple 提供的 Azure VMware 解決方案

歡迎使用一站式 入口網站，以取得 Azure VMware Solution by CloudSimple 的協助。
在文件網站中，您可以了解下列主題：

## <a name="overview"></a>概觀

深入了解 Azure VMware Solution by CloudSimple

* 在[什麼是 Azure VMware Solution by CloudSimple](cloudsimple-vmware-solutions-overview.md) 中了解其功能、優點和使用案例
* 檢閱[系統管理的重要概念](key-concepts.md)

## <a name="quickstart"></a>快速入門

了解如何開始使用解決方案

* 了解如何[初始化服務和購買容量](quickstart-create-cloudsimple-service.md)
* 在[設定私人雲端環境](quickstart-create-private-cloud.md)中了解如何建立新的 VMware 環境
* 檢閱[在 Azure 上使用 VMware VM](quickstart-create-vmware-virtual-machine.md) 一文，了解如何整合 VMware 與 Azure 的管理

## <a name="concepts"></a>概念

了解下列概念

* [CloudSimple 服務](cloudsimple-service.md) (也稱為「Azure VMware Solution by CloudSimple - 服務」)。 每個區域都必須建立一次此資源。
* 藉由建立一或多個 [CloudSimple 節點](cloudsimple-node.md)資源來購買環境的容量。 這些資源也稱為「Azure VMware Solution by CloudSimple - 節點」。
* 使用[私人雲端](cloudsimple-private-cloud.md)初始化和設定您的 VMware 環境。
* 使用 [CloudSimple 虛擬機器](cloudsimple-virtual-machines.md) (也稱為「Azure VMware Solution by CloudSimple - 虛擬機器」) 整合管理。
* 使用 [VLAN/子網路](cloudsimple-vlans-subnets.md)來設計基礎網路。
* 使用[防火牆資料表](cloudsimple-firewall-tables.md)資源來分割和保護您的基礎網路。
* 使用 [VPN 閘道](cloudsimple-vpn-gateways.md)，透過 WAN 取得 VMware 環境的安全存取。
* 針對使用[公用 IP](cloudsimple-public-ip-address.md) 的工作負載啟用公用存取。
* 使用 [Azure 網路連線](cloudsimple-azure-network-connection.md)建立與 Azure 虛擬網路和內部部署網路的連線能力。
* 使用[帳戶管理](cloudsimple-account.md)設定警示電子郵件目標。
* 使用[活動管理](cloudsimple-activity.md)畫面來檢視使用者和系統活動的記錄。
* 了解各種 [VMware 元件](vmware-components.md)。

## <a name="tutorials"></a>教學課程

了解如何執行一般工作，例如：

* 在您要部署 VMware 環境的每個區域中，[建立 CloudSimple 服務](create-cloudsimple-service.md)。
* 在 [CloudSimple 入口網站](access-cloudsimple-portal.md)中管理核心服務功能。
* 藉由[購買 CloudSimple 節點](create-nodes.md)，為您的基礎結構啟用容量和最佳化計費。
* 使用私人雲端管理 VMware 環境設定。 您可以[建立](create-private-cloud.md)、[管理](manage-private-cloud.md)、[擴充](expand-private-cloud.md)或[縮小](shrink-private-cloud.md)私人雲端。
* 藉由[對應 Azure 訂用帳戶](azure-subscription-mapping.md)來啟用整合管理。
* 使用[活動頁面](monitor-activity.md)來監視使用者和系統活動。
* 藉由[建立和管理子網路](create-vlan-subnet.md)，為您的環境設定網路功能。
* 使用[防火牆資料表和規則](firewall.md)來分割和保護您的環境。
* 藉由[配置公用 IP](public-ips.md)，為工作負載啟用輸入網際網路存取。
* 藉由[設定 VPN](vpn-gateway.md)，從內部網路或用戶端工作站啟用連線能力。
* 從您的[內部部署環境](on-premises-connection.md)和 [Azure 虛擬網路](virtual-network-connection.md)啟用通訊。
* 在[帳戶摘要](account.md)中設定警示目標並檢視總購買容量
* 檢視已存取 CloudSimple 入口網站的[使用者](users.md)。
* 從 Azure 入口網站管理 VMware 虛擬機器：
    * 在 Azure 入口網站中[建立虛擬機器](azure-create-vm.md)。
    * [管理虛擬機器](azure-manage-vm.md) (您已建立的虛擬機器)。

## <a name="how-to-guides"></a>使用說明指南

這些指南會描述目標的解決方案，例如：

* [保護您的環境](private-cloud-secure.md)
* 使用[權限提升](escalate-privileges.md)，安裝第三方工具，在 vSphere 中啟用其他使用者和外部驗證來源。
* 藉由[設定內部部署 DNS](on-premises-dns-setup.md)，設定對各種 VMware 服務的存取權。
* 藉由[設定工作負載 DNS 和 DHCP](dns-dhcp-setup.md)，為您的工作負載啟用名稱和位址配置。
* 了解服務如何透過服務[更新和升級](vmware-components.md#updates-and-upgrades)來確保您平台中的安全性和功能。
* 藉由使用[第三方備份軟體 (例如 Veeam)](backup-workloads-veeam.md) 來建立範例備份架構，以節省備份的 TCO。
* 藉由使用[第三方 KMS 加密軟體](vsan-encryption.md)來啟用待用加密，以建立安全的環境。
* 藉由設定 [Azure AD 身分識別來源](azure-ad.md)，將 Azure Active Directory (Azure AD) 管理延伸至 VMware。
