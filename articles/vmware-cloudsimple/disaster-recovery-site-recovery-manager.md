---
title: Azure VMware 解決方案（按雲簡單 - 使用 VMware 網站恢復管理器將私有雲設置為災害復原網站）
description: 描述如何將雲簡單私有雲設置為本地 VMware 工作負載的災害復原網站
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa8b2da683d68a337df38e13726f22c5af43540a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565922"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>使用 VMware 網站恢復管理器將私有雲設置為災害復原目標

您可以將雲簡單私有雲用作本地 VMware 工作負載的災害復原 （DR） 網站。

DR 解決方案基於 vSphere 複製和 VMware 網站恢復管理器 （SRM）。 可以採用類似方法，使私有雲成為受本地恢復網站保護的主網站。

雲簡單解決方案：

* 無需專門為 DR 設置資料中心。
* 允許您利用部署 CloudSimple 的 Azure 位置，實現全球地理恢復能力。
* 為您提供了降低部署成本和建立 DR 的擁有權總成本的選項。

CloudSimple 解決方案要求您執行以下操作：

* 在私有雲中安裝、配置和管理 vSphere 複製和 SRM。
* 當私有雲是受保護的網站時，為 SRM 提供您自己的許可證。 當 CloudSimple 網站用作恢復網站時，不需要任何其他 SRM 許可證。

使用此解決方案，您可以完全控制 vSphere 複製和 SRM。 熟悉的 UI、API 和 CLI 介面支援使用現有腳本和工具。

![網站恢復管理器部署](media/srm-deployment.png)

您可以使用與您的私有雲和本地環境相容的任何版本的 vRA 和 SRM。 本指南中的示例使用 vRA 6.5 和 SRM 6.5。 這些版本與 vSphere 6.5 相容，雲簡單支援 vSphere 6.5。

## <a name="deploy-the-solution"></a>部署解決方案

以下各節介紹如何在私有雲中使用 SRM 部署 DR 解決方案。

1. [驗證 VMware 產品版本是否相容](#verify-that-vmware-product-versions-are-compatible)
2. [估計 DR 環境的大小](#estimate-the-size-of-your-dr-environment)
3. [為您的環境創建私有雲](#create-a-private-cloud-for-your-environment)
4. [為 SRM 解決方案設置私有雲網路](#set-up-private-cloud-networking-for-the-srm-solution)
5. [在本地網路和私有雲之間佈建網站到網站 VPN 連接，並打開所需的埠](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [在私有雲中設置基礎設施服務](#set-up-infrastructure-services-in-your-private-cloud)
7. [在本地環境中安裝 vSphere 複製設備](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [在私有雲環境中安裝 vSphere 複製設備](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [在本地環境中安裝 SRM 伺服器](#install-srm-server-in-your-on-premises-environment)
10. [在私有雲中安裝 SRM 伺服器](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>驗證 VMware 產品版本是否相容

本指南中的配置受以下相容性要求的約束：

* 必須在私有雲和本地環境中部署同一版本的 SRM。
* 必須在私有雲和本地環境中部署相同版本的 vSphere 複製。
* 私有雲中的平臺服務控制器 （PSC） 版本和本地環境必須相容。
* 私有雲中的 vCenter 版本和本地環境中必須相容。
* SRM 和 vSphere 複製的版本必須彼此相容，並且與 PSC 和 vCenter 的版本相容。

有關相關 VMware 文檔和相容性資訊的連結，請訪問[VMware 網站恢復管理器](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)文檔。

要瞭解私有雲中的 vCenter 和 PSC 版本，請打開雲簡單門戶。 轉到 **"資源**"，選擇您的私有雲，然後按一下**vSphere 管理網路**選項卡。

![私有雲中的 vCenter & PSC 版本](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>估計 DR 環境的大小

1. 驗證已識別的本地配置是否在受支援的限制範圍內。 對於 SRM 6.5，限制記錄在 VMware 知識庫文章中，該文章介紹了[網站恢復管理器 6.5 的操作限制](https://kb.vmware.com/s/article/2147110)。
2. 確保您有足夠的網路頻寬來滿足工作負載大小和 RPO 要求。 VMware 知識庫文章，介紹[計算 vSphere 複製的頻寬要求](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)，提供有關頻寬限制的指導。
3. 使用 CloudSimple Sizer 工具估計 DR 網站中保護本地環境所需的資源。

### <a name="create-a-private-cloud-for-your-environment"></a>為您的環境創建私有雲

通過按照["創建私有雲](create-private-cloud.md)"中的說明和大小調整建議，從 CloudSimple 門戶創建私有雲。

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>為 SRM 解決方案設置私有雲網路

訪問 CloudSimple 門戶，為 SRM 解決方案設置私有雲網路。

為 SRM 解決方案網路創建 VLAN 並為其分配子網 CIDR。 有關說明，請參閱[創建和管理 VLAN/子網](create-vlan-subnet.md)。

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>在本地網路和私有雲之間佈建網站到網站 VPN 連接，並打開所需的埠

在本地網路和私有雲之間建立網站到網站的連接。 有關說明，請參閱[配置到雲簡單私有雲的 VPN 連接](set-up-vpn.md)。

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>在私有雲中設置基礎設施服務

在私有雲中配置基礎設施服務，以便輕鬆管理工作負載和工具。

如果要執行以下任一操作，可以添加外部標識提供程式，如[使用 Azure AD 作為雲簡單私有雲上 vCenter 的標識提供程式](azure-ad.md)：

* 從私有雲中的本地活動目錄 （AD） 中標識使用者。
* 在私有雲中為所有使用者設置 AD。
* 使用 Azure AD。

要為私有雲中的工作負載提供 IP 位址查找、IP 位址管理和名稱解析服務，請設置 DHCP 和 DNS 伺服器，如[在雲簡單私有雲中設置 DNS 和 DHCP 應用程式和工作負載中](dns-dhcp-setup.md)所述。

*.cloudsimple.io域由私有雲中的管理 VM 和主機使用。 要解析對此域的請求，請按照[創建條件轉寄站](on-premises-dns-setup.md#create-a-conditional-forwarder)中所述，在 DNS 伺服器上配置 DNS 轉發。

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>在本地環境中安裝 vSphere 複製設備

通過遵循 VMware 文檔，在本地環境中安裝 vSphere 複製設備 （vRA）。 安裝由以下高級步驟組成：

1. 為 vRA 安裝準備本地環境。

2. 使用 VR ISO 中的 OVF 從 vmware.com 在本地環境中部署 vRA。 對於 vRA 6.5，[此 VMware 博客](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices)具有相關資訊。

3. 在本地網站使用 vCenter 單點登錄註冊本地 vRA。 有關 vSphere 複製 6.5 的詳細說明，請參閱 VMware 文檔[VMware vSphere 複製 6.5 安裝和配置](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)。

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>在私有雲環境中安裝 vSphere 複製設備

開始之前，請驗證您是否具有以下內容：

* 從本地環境中的子網到私有雲的管理子網的 IP 可訪問性
* 從本地 vSphere 環境中的複製子網到私有雲的 SRM 解決方案子網的 IP 可訪問性

有關說明，請參閱[配置到雲簡單私有雲的 VPN 連接](set-up-vpn.md)。 這些步驟與本地安裝的步驟類似。

CloudSimple 建議在 vRA 和 SRM 安裝期間使用 FQDN 而不是 IP 位址。 要瞭解私有雲中的 vCenter 和 PSC 的 FQDN，請打開雲簡單門戶。 轉到 **"資源**"，選擇您的私有雲，然後按一下**vSphere 管理網路**選項卡。

![在私有雲中查找 vCenter/PSC 的 FQDN](media/srm-resources.png)

CloudSimple 要求您不使用預設的"雲擁有者"使用者安裝 vRA 和 SRM，而是創建新使用者。 這樣做是為了説明確保私有雲 vCenter 環境的高停機時間和可用性。 但是，私有雲 vCenter 中的預設雲擁有者使用者沒有足夠的許可權來創建具有管理許可權的新使用者。

在安裝 vRA 和 SRM 之前，必須升級雲擁有者使用者的 vCenter 許可權，然後在 vCenter SSO 域中創建具有管理許可權的使用者。 有關預設私有雲使用者和許可權模型的詳細資訊，請參閱[瞭解私有雲許可權模型](learn-private-cloud-permissions.md)。

安裝由以下高級步驟組成：

1. [升級許可權](escalate-private-cloud-privileges.md)。
2. 在私有雲中創建使用者，用於 vSphere 複製和 SRM 安裝。 下面在[vCenter UI 中解釋：在私有雲中創建使用者，用於 vRA & SRM 安裝](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation)。
3. 為 vRA 安裝準備私有雲環境。
4. 使用 vr ISO 中的 OVF 從 vmware.com 在私有雲中部署 vRA。 對於 vRA 6.5，[此 VMware 博客](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices)具有相關資訊。
5. 為 vRA 配置防火牆規則。 下面在[雲簡單門戶中解釋：為 vRA 配置防火牆規則](#cloudsimple-portal-configure-firewall-rules-for-vra)。
6. 在私有雲網站使用 vCenter 單一登入註冊私有雲 vRA。
7. 配置兩個設備之間的 vSphere 複製連接。 確保在整個防火牆上打開所需的埠。 有關 vSphere 複製 6.5 必須打開的埠號清單，請參閱[此 VMware 知識庫文章](https://kb.vmware.com/s/article/2087769)。

有關 vSphere 複製 6.5 的詳細安裝說明，請參閱 VMware 文檔[VMware vSphere 複製 6.5 安裝和配置](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)。

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter UI：在私有雲中創建使用者，用於 vRA 和 SRM 安裝

在從 CloudSimple 門戶升級許可權後，使用雲擁有者使用者憑據登錄到 vCenter。

在 vCenter`srm-soln-admin`中創建新使用者 ，並將其添加到 vCenter 中的管理員組。
登出 vCenter 作為雲擁有者使用者，並作為*srm-soln 管理員*使用者登錄。

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>雲簡單門戶：為 vRA 配置防火牆規則

將防火牆規則配置為打開端口[Set up firewall tables and rules](firewall.md)，以便在以下之間通信：

* vRA 在 SRM 解決方案網路中，vCenter 和 ESXi 託管在管理網路中。
* 兩個地點的 vRA 設備。

有關 vSphere 複製 6.5 必須打開的埠號清單，請參閱此[VMware 知識庫文章](https://kb.vmware.com/s/article/2087769)。

### <a name="install-srm-server-in-your-on-premises-environment"></a>在本地環境中安裝 SRM 伺服器

開始之前，請驗證以下內容：

* vSphere 複製設備安裝在本地和私有雲環境中。
* 兩個網站的 vSphere 複製設備相互連接。
* 您已經查看了有關先決條件和最佳實踐的 VMware 資訊。 對於 SRM 6.5，您可以參考[SRM 6.5 的](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)VMware 文檔先決條件和最佳實踐。

按照 VMware 文檔執行 SRM 伺服器安裝的部署模型"每個平臺服務控制器具有一個 vCenter 實例的雙網站拓撲"，如本[VMWare 文檔中](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)所述。 SRM 6.5 的安裝說明在 VMware 文檔[安裝網站恢復管理器](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)中提供。

### <a name="install-srm-server-in-your-private-cloud"></a>在私有雲中安裝 SRM 伺服器

開始之前，請驗證以下內容：

* vSphere 複製設備安裝在本地和私有雲環境中。
* 兩個網站的 vSphere 複製設備相互連接。
* 您已經查看了有關先決條件和最佳實踐的 VMware 資訊。 對於 SRM 6.5，您可以參考[網站恢復管理器 6.5 伺服器安裝的先決條件和最佳實踐](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)。

以下步驟描述了私有雲 SRM 安裝。

1. [vCenter UI：安裝 SRM](#vcenter-ui-install-srm)
2. [雲簡單門戶：為 SRM 配置防火牆規則](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter UI：配置 SRM](#vcenter-ui-configure-srm)
4. [雲簡單門戶：降級許可權](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter UI：安裝 SRM

使用 srm-soln-admin 憑據登錄到 vCenter 後，請按照 VMware 文檔執行部署模型中的 SRM 伺服器安裝"每個平臺服務控制器具有一個 vCenter 實例的雙網站拓撲"，如本[VMWare 文檔中](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)所述。 SRM 6.5 的安裝說明在 VMware 文檔[安裝網站恢復管理器](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)中提供。

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>雲簡單門戶：為 SRM 配置防火牆規則

按照[設置防火牆表和規則](firewall.md)中所述配置防火牆規則，以允許在以下之間通信：

私有雲中的 SRM 伺服器和 vCenter / PSC。
兩個網站的 SRM 伺服器

有關 vSphere 複製 6.5 必須打開的埠號清單，請參閱[此 VMware 知識庫文章](https://kb.vmware.com/s/article/2087769)。

#### <a name="vcenter-ui-configure-srm"></a>vCenter UI：配置 SRM

在私有雲中安裝 SRM 後，請執行 VMware 網站恢復管理器安裝和配置指南部分中所述的以下任務。 對於 SRM 6.5，這些說明可在 VMware 文檔[安裝網站恢復管理器](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)中提供。

1. 在受保護和恢復網站上連接網站恢復管理器伺服器實例。
2. 建立與遠端站台恢復管理器伺服器實例的用戶端連接。
3. 安裝網站恢復管理器許可證金鑰。

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>雲簡單門戶：降級許可權

要取消許可權，請參閱[降級特權](escalate-private-cloud-privileges.md#de-escalate-privileges)。

## <a name="ongoing-management-of-your-srm-solution"></a>持續管理您的 SRM 解決方案

您可以完全控制私有雲環境中的 vSphere 複製和 SRM 軟體，並應執行必要的軟體生命週期管理。 在更新或升級 vSphere 複製或 SRM 之前，請確保任何新版本的軟體都與私有雲 vCenter 和 PSC 相容。

> [!NOTE]
> CloudSimple 目前正在探索提供託管 DR 服務的選項。 

## <a name="multiple-replication-configuration"></a>多重複制配置

 [基於陣列的複製和 vSphere 複製技術都可以與 SRM](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)同時使用。 但是，它們必須應用於單獨的 VM 集（給定 VM 可以通過基於陣列的複製或 vSphere 複製進行保護，但不能同時保護這兩者）。 此外，CloudSimple 網站可以配置為多個受保護網站的恢復網站。 有關多網站配置的資訊，請參閱[SRM 多網站選項](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/)。

## <a name="references"></a>參考

* [VMware 網站恢復管理器文檔](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [網站恢復管理器 6.5 的操作限制](https://kb.vmware.com/s/article/2147110)
* [計算 vSphere 複製的頻寬要求](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [部署 vSphere 複製 6.5 時的 OVF 選擇](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere 複製 6.5 安裝和配置](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [SRM 6.5 的先決條件和最佳實踐](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [雙網站拓撲中的網站恢復管理器，每個平臺服務控制器具有一個 vCenter 伺服器實例](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware 網站恢復管理器 6.5 安裝和配置指南](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [VMware 博客上基於陣列的複製與 vSphere 複製](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [VMware 博客關於 SRM 多網站選項](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [vSphere 複製 5.8.x、6.x 和 8 必須打開的埠號](https://kb.vmware.com/s/article/2147112)
