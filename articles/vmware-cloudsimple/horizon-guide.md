---
title: Azure VMware 解決方案（按雲簡單 - 使用私有雲網站使用 VMware Horizon 託管虛擬桌面基礎結構）
description: 描述如何使用雲簡單私有雲網站使用 VMware Horizon 託管虛擬桌面基礎架構
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 723821a78ecae308443c93567402e3b232c036f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025243"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>使用雲簡單私有雲網站使用 VMware Horizon 託管虛擬桌面基礎架構

您可以使用雲簡單私有雲網站使用 VMware Horizon 7.x 託管虛擬桌面基礎設施 （VDI）。 下圖顯示了 VDI 的邏輯解決方案體系結構。

![地平線部署](media/horizon-deployment.png)

使用此解決方案，您可以完全控制地平線視圖管理器和應用卷。 熟悉的 UI、API 和 CLI 介面支援使用現有腳本和工具。

CloudSimple 解決方案要求您執行以下操作：

* 在私有雲中安裝、配置和管理 VMware Horizon 7.x。
* 提供您自己的 Horizon 許可證。

## <a name="deploy-the-solution"></a>部署解決方案

以下各節介紹如何在私有雲中使用 Horizon 部署 VDI 解決方案。

1. [驗證 VMware 產品版本是否相容](#verify-that-vmware-product-versions-are-compatible)
2. [估計桌面環境的大小](#estimate-the-size-of-your-desktop-environment)
3. [為您的環境創建私有雲](#create-a-private-cloud-for-your-environment)
4. [在私有雲中安裝 VMware 地平線](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>驗證 VMware 產品版本是否相容

* 驗證您當前和計畫版本的 Horizon、應用卷、統一訪問閘道和使用者環境管理器是否相互相容，並與私有雲中的 vCenter 和 PSC 相容。 有關相容性資訊，請參閱[Horizon 7.5 的 VMware 相容性矩陣](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=)。
* 要查找私有雲中的 vCenter 和 PSC 的當前版本，請轉到[雲簡單門戶](access-cloudsimple-portal.md)中的 **"資源"，** 選擇您的私有雲，然後按一下**vSphere 管理網路**選項卡。

![vCenter 和 PSC 版本](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>估計桌面環境的大小

* 驗證您標識的配置是否在 VMware 操作限制範圍內。
* 估計所有桌面和 Horizon 管理元件所需的資源。

### <a name="create-a-private-cloud-for-your-environment"></a>為您的環境創建私有雲

1. 按照[配置私有雲環境](quickstart-create-private-cloud.md)中的說明，從雲簡單門戶創建私有雲。  CloudSimple 在每個新創建的私有雲中創建名為"雲擁有者"的預設 vCenter 使用者。 有關預設私有雲使用者和許可權模型的詳細資訊，請參閱[瞭解私有雲許可權模型](learn-private-cloud-permissions.md)。
2. 在私有雲中為 Horizon 管理平面創建 VLAN，並為其分配子網 CIDR。 有關說明，請參閱[創建和管理 VLAN/子網](create-vlan-subnet.md)。 這是將安裝所有解決方案元件（統一訪問閘道、連接伺服器、應用卷伺服器和使用者環境管理器伺服器）的網路。
3. 決定是否要將外部標識供應商與私有雲 vCenter 一起使用。 如果是，請選擇以下選項之一：
    * 使用本地活動目錄作為外部標識提供程式。 有關說明，請參閱[vCenter 標識源](set-vcenter-identity.md)。
    * 在 Horizon 管理平面 VLAN 中的私有雲中設置活動目錄伺服器，以用作外部標識提供程式。 有關說明，請參閱[vCenter 標識源](set-vcenter-identity.md)。
    * 在私有雲中的 Horizon 管理平面 VLAN 中設置 DHCP 和 DNS 伺服器。 有關說明，請參閱[在雲簡單私有雲中設置 DNS 和 DHCP 應用程式和工作負載](dns-dhcp-setup.md)。
4. 在私有雲中安裝的 DNS 伺服器上配置 DNS 轉發。 有關說明，請參閱[創建條件轉寄站](on-premises-dns-setup.md#create-a-conditional-forwarder)。

### <a name="install-vmware-horizon-in-your-private-cloud"></a>在私有雲中安裝 VMware 地平線

以下部署圖描述了部署在私有雲中的 Horizon 解決方案。 統一訪問閘道、AD/DC、視圖和應用卷伺服器安裝在使用者創建的 VLAN 234 中。 統一訪問閘道具有可從 Internet 訪問的指定公共 IP 位址。 Horizon 桌面池 VM 部署在 VLAN 235 中，以提供額外的隔離和安全性。

![私有雲中的地平線部署](media/horizon-private-cloud.png)

以下各節概述了設置類似于圖中所示的部署的說明。 開始之前，請驗證您是否具有以下內容：

* 使用 CloudSimple 門戶創建的私有雲，具有運行桌面池的足夠容量。
* 本地環境和私有雲環境之間有足夠的頻寬，以支援桌面的網路流量。
* 在本地資料中心和私有雲之間設置的網站到網站 VPN 隧道。
* 從本地環境中的最終使用者子網到雲簡單私有雲子網的 IP 可訪問性。
* 為您的私有雲安裝 AD/DHCP/DNS。

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>雲簡單門戶：為桌面池創建專用 VLAN/子網

為 Horizon 桌面池創建 VLAN 並為其分配子網 CIDR。 有關說明，請參閱[創建和管理 VLAN/子網](create-vlan-subnet.md)。 這是所有桌面虛擬機器都將運行的網路。

遵循標準安全最佳實踐來保護 Horizon 部署：

* 僅允許桌面 RDP 流量/SSH 流量到桌面 VM。
* 只允許 Horizon 管理平面 VLAN 和桌面池 VLAN 之間的管理流量。
* 只允許來自本地網路的管理流量。

您可以通過從 CloudSimple 門戶配置[防火牆規則](firewall.md)來強制實施這些最佳實踐。

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>雲簡單門戶：配置防火牆規則以保護 Horizon 管理平面

在 CloudSimple 門戶中設置以下規則。 有關說明，請參閱[設置防火牆表和規則](firewall.md)。

1. 在 CloudSimple N-S 防火牆中配置防火牆規則，以允許本地子網和 Horizon 管理 VLAN 之間的通信，以便僅允許 VMware 文檔[Horizon 埠清單中](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html)列出的網路埠。

2. 在私有雲中的 Horizon 管理 VLAN 和桌面池 VLAN 之間創建 E-W 防火牆規則。

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>雲簡單門戶：為統一訪問閘道創建公共 IP 位址

為統一接入閘道設備創建公共 IP 位址，以啟用來自 Internet 的桌面用戶端連接。 有關說明，請參閱[分配公共 IP 位址](public-ips.md)。

設置完成後，公共 IP 位址將分配並在公共 IP 頁上列出。

#### <a name="cloudsimple-portal-escalate-privileges"></a>雲簡單門戶：升級許可權

預設的"雲擁有者"使用者在私有雲 vCenter 中沒有足夠的許可權來安裝 Horizon，因此必須升級使用者的 vCenter 許可權。 有關詳細資訊，請參閱[升級許可權](escalate-private-cloud-privileges.md)。

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>vCenter UI：在私有雲中創建使用者，用於地平線安裝

1. 使用"雲擁有者"使用者憑據登錄到 vCenter。
2. 在 vCenter 中創建新使用者"horizon-soln-admin"，並將該使用者添加到 vCenter 中的管理員組。
3. 登出 vCenter 作為"雲擁有者"使用者，並登錄為"horizon-soln-admin"使用者。

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter UI：安裝 VMware 地平線

如前面邏輯體系結構部分所述，Horizon 解決方案具有以下元件：

* VMware 地平線視圖
* VMware 統一接入閘道
* VMware 應用程式卷管理器
* VMware 使用者環境管理器

安裝元件如下：

1. 安裝和配置統一訪問閘道，按照 VMware 文檔中提供的說明[部署和配置 VMware 統一訪問閘道](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html)。

2. 按照[視圖安裝指南](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html)中的說明在私有雲中安裝地平線視圖。

3. 按照[安裝和配置 VMware 應用卷](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html)中的說明安裝應用卷管理器。

4. 按照[有關安裝和配置 VMware 使用者環境管理器中的說明來安裝和配置使用者環境管理器](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html)。

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>提交支援請求以上傳 VMware Horizon 預打包的應用程式卷

作為安裝過程的一部分，應用卷管理器使用預打包的卷來預配應用堆疊和可寫卷。 這些卷用作應用堆疊和可寫卷的範本。

將卷上載到私有雲資料存儲需要 ESXi 根密碼。 為提供説明，請提交[支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。 附加 App卷安裝程式捆綁包，以便 CloudSimple 支援人員可以將範本上載到您的私有雲環境。

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>雲簡單門戶：降級許可權

您現在可以[取消"](escalate-private-cloud-privileges.md#de-escalate-privileges)雲擁有者"使用者的許可權。

## <a name="ongoing-management-of-your-horizon-solution"></a>持續管理您的 Horizon 解決方案

您可以完全控制私有雲環境中的 Horizon 和 App 卷管理器軟體，並應執行必要的軟體生命週期管理。 在更新或升級 Horizon 或應用卷之前，請確保任何新版本的軟體都與私有雲 vCenter 和 PSC 相容。
