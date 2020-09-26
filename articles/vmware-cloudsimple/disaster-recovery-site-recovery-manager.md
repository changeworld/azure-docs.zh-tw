---
title: Azure VMware Solution by CloudSimple-使用 VMware Site Recovery Manager 將私人雲端設定為災難復原網站
description: 說明如何使用 VMware Site Recovery Manager 將 CloudSimple 私人雲端設定為災難復原網站。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6714b2c5d87141fd94d0f96d9cf07913442d18d6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267052"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>使用 VMware Site Recovery Manager 將私人雲端設定為災難復原目標

您可以使用 CloudSimple 私用雲端做為內部部署 VMware 工作負載 (DR) 網站的嚴重損壞修復。

DR 解決方案是以 vSphere 複寫和 VMware Site Recovery Manager (SRM) 為基礎。 您可以遵循類似的方法，讓私人雲端成為內部部署復原網站所保護的主要網站。

CloudSimple 解決方案：

* 不需要特別為 DR 設定資料中心。
* 可讓您運用 CloudSimple 部署所在的 Azure 位置，以進行全球地理復原。
* 提供選項來降低部署成本，以及建立 DR 的擁有權總成本。

CloudSimple 解決方案會要求您執行下列作業：

* 在您的私人雲端中安裝、設定及管理 vSphere 複寫和 SRM。
* 當私用雲端是受保護的網站時，請提供您自己的「SRM 授權」。 當您使用 CloudSimple 網站作為復原網站時，不需要任何額外的「SRM」授權。

透過此解決方案，您可以完全掌控 vSphere 複寫和 SRM。 熟悉的 UI、API 和 CLI 介面可讓您使用現有的腳本和工具。

![Site Recovery Manager 部署](media/srm-deployment.png)

您可以使用任何與您的私人雲端和內部部署環境相容的 vRA 和 SRM 版本。 本指南中的範例會使用 vRA 6.5 和 SRM 6.5。 這些版本與 CloudSimple 支援的 vSphere 6.5 相容。

## <a name="deploy-the-solution"></a>部署解決方案

下列各節說明如何在您的私人雲端中使用 SRM 部署 DR 解決方案。

1. [確認 VMware 產品版本相容](#verify-that-vmware-product-versions-are-compatible)
2. [估計 DR 環境的大小](#estimate-the-size-of-your-dr-environment)
3. [為您的環境建立私用雲端](#create-a-private-cloud-for-your-environment)
4. [設定適用于 SRM 解決方案的私人雲端網路](#set-up-private-cloud-networking-for-the-srm-solution)
5. [設定內部部署網路與私人雲端之間的站對站 VPN 連線，並開啟必要的埠](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [設定私人雲端中的基礎結構服務](#set-up-infrastructure-services-in-your-private-cloud)
7. [在您的內部部署環境中安裝 vSphere 複寫設備](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [在您的私人雲端環境中安裝 vSphere 複寫設備](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [在您的內部部署環境中安裝 SRM 伺服器](#install-srm-server-in-your-on-premises-environment)
10. [在您的私人雲端中安裝 SRM 伺服器](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>確認 VMware 產品版本相容

本指南中的設定受限於下列相容性需求：

* 相同版本的 SRM 必須部署在私人雲端和內部部署環境中。
* 您必須在私人雲端和內部部署環境中部署相同版本的 vSphere 複寫。
* 您私用雲端和內部部署環境中 Platform Services Controller (PSC) 的版本必須相容。
* 私人雲端和內部部署環境中的 vCenter 版本必須相容。
* SRM 和 vSphere 複寫的版本必須彼此相容，以及與 PSC 和 vCenter 的版本相容。

如需相關 VMware 檔和相容性資訊的連結，請移至 [vmware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) 檔。

若要瞭解您私用雲端中的 vCenter 和 PSC 版本，請開啟 CloudSimple 入口網站。 移至 [ **資源**]，選取您的私人雲端，然後按一下 [ **vSphere 管理網路** ] 索引標籤。

![私人雲端中的 vCenter & PSC 版本](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>估計 DR 環境的大小

1. 確認您識別的內部部署設定是否在支援的限制內。 針對 SRM 6.5，這些限制記載于 VMware 知識庫文章中有關 [Site Recovery Manager 6.5 的操作限制](https://kb.vmware.com/s/article/2147110)。
2. 確定您有足夠的網路頻寬，以符合您的工作負載大小和 RPO 需求。 有關 [計算 vSphere 複寫頻寬需求](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) 的 VMware 知識庫文章，提供頻寬限制的指引。
3. 使用 CloudSimple sizer 工具來預估 DR 網站中所需的資源，以保護您的內部部署環境。

### <a name="create-a-private-cloud-for-your-environment"></a>為您的環境建立私用雲端

遵循 [建立私人雲端](create-private-cloud.md)中的指示和大小建議，從 CloudSimple 入口網站建立私人雲端。

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>設定適用于 SRM 解決方案的私人雲端網路

存取 CloudSimple 入口網站，以設定適用于 SRM 解決方案的私用雲端網路功能。

建立適用于 SRM 解決方案網路的 VLAN，並為其指派子網 CIDR。 如需相關指示，請參閱 [建立和管理 vlan/子網](create-vlan-subnet.md)。

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>設定內部部署網路與私人雲端之間的站對站 VPN 連線，並開啟必要的埠

設定內部部署網路與私人雲端之間的站對站連線能力。 如需相關指示，請參閱 [設定 CloudSimple 私人雲端的 VPN](set-up-vpn.md)連線。

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>設定私人雲端中的基礎結構服務

在私用雲端中設定基礎結構服務，讓您輕鬆地管理您的工作負載和工具。

如果您想要執行下列其中一項動作，您可以將外部識別提供者新增 [Azure AD 為 CloudSimple 私人雲端上的 vCenter 作為身分識別提供者的身分識別提供者](azure-ad.md) ：

* 識別您的內部部署 Active Directory 私人雲端中 (AD) 的使用者。
* 在私用雲端中為所有使用者設定 AD。
* 使用 Azure AD。

若要針對私人雲端中的工作負載提供 IP 位址查閱、IP 位址管理和名稱解析服務，請依照在 [CloudSimple 私人雲端中設定 DNS 和 DHCP 應用程式和工作負載中](dns-dhcp-setup.md)所述，設定 DHCP 和 DNS 伺服器。

您私人雲端中的管理 Vm 和主機會使用 *. cloudsimple.io 網域。 若要解決此網域的要求，請依照 [建立條件](on-premises-dns-setup.md#create-a-conditional-forwarder)轉寄站中的說明，在 dns 伺服器上設定 dns 轉送。

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>在您的內部部署環境中安裝 vSphere 複寫設備

遵循 VMware 檔，在您的內部部署環境中安裝 vSphere Replication 設備 (vRA) 。 此安裝包含下列高階步驟：

1. 準備您的內部部署環境以進行 vRA 安裝。

2. 在您的內部部署環境中，使用 vmware.com 的 VR ISO 中的 OVF 來部署 vRA。 針對 vRA 6.5， [此 VMware blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) 有相關的資訊。

3. 在內部部署網站上，使用 vCenter 單一登入註冊您的內部部署 vRA。 如需 vSphere 複寫6.5 的詳細指示，請參閱 VMware 檔 [VMware vSphere replication 6.5 安裝和](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)設定。

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>在您的私人雲端環境中安裝 vSphere 複寫設備

開始之前，請確認您有下列各項：

* 從內部部署環境中子網到私人雲端的管理子網的 IP 連線能力
* 從內部部署 vSphere 環境中的複寫子網到私人雲端的 SRM 解決方案子網的 IP 連線能力

如需相關指示，請參閱 [設定 CloudSimple 私人雲端的 VPN](set-up-vpn.md)連線。 這些步驟與內部部署安裝的步驟類似。

CloudSimple 建議在 vRA 和 SRM 安裝期間使用 Fqdn，而不是 IP 位址。 若要瞭解私人雲端中 vCenter 和 PSC 的 FQDN，請開啟 CloudSimple 入口網站。 移至 [ **資源**]，選取您的私人雲端，然後按一下 [ **vSphere 管理網路** ] 索引標籤。

![在私人雲端中尋找 vCenter/PSC 的 FQDN](media/srm-resources.png)

CloudSimple 要求您不要使用預設的 ' cloudowner ' 使用者來安裝 vRA 和 SRM，而是改為建立新的使用者。 這樣做的目的是為了協助確保您私用雲端 vCenter 環境的高執行時間和可用性。 不過，私用雲端 vCenter 不中的預設 cloudowner 使用者有足夠的許可權，可建立具有系統管理許可權的新使用者。

在安裝 vRA 和 SRM 之前，您必須先呈報 cloudowner 使用者的 vCenter 許可權，然後在 vCenter SSO 網域中建立具有系統管理許可權的使用者。 如需預設私用雲端使用者和許可權模型的詳細資訊，請參閱 [瞭解私用雲端許可權模型](learn-private-cloud-permissions.md)。

此安裝包含下列高階步驟：

1. [提升許可權](escalate-private-cloud-privileges.md)。
2. 在您的私人雲端中建立使用者，以進行 vSphere 複寫和 SRM 安裝。 以下是 VCenter UI 中的說明 [：在私人雲端中建立使用者以 vRA & SRM 安裝](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation)。
3. 準備私人雲端環境以進行 vRA 安裝。
4. 在您的私人雲端中使用 OVF，以 vmware.com 中的 VR ISO 部署 vRA。 針對 vRA 6.5， [此 VMware blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) 有相關的資訊。
5. 設定 vRA 的防火牆規則。 以下說明 [CloudSimple 入口網站：設定 vRA 的防火牆規則](#cloudsimple-portal-configure-firewall-rules-for-vra)。
6. 在私用雲端網站上使用 vCenter 單一登入註冊私用雲端 vRA。
7. 設定兩個設備之間的 vSphere 複寫連接。 確定已在防火牆中開啟所需的埠。 請參閱 [這份 VMware 知識庫文章](https://kb.vmware.com/s/article/2087769) ，以取得必須針對 vSphere Replication 6.5 開啟的埠號碼清單。

如需 vSphere 複寫6.5 的詳細安裝指示，請參閱 VMware 檔 [VMware vSphere replication 6.5 安裝和](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)設定。

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter UI：在私人雲端中建立用於 vRA 和 SRM 安裝的使用者

從 CloudSimple 入口網站提高許可權之後，使用 cloudowner 使用者認證登入 vCenter。

在 vCenter 中建立新的使用者， `srm-soln-admin` 並將其新增至 vcenter 中的 administrators 群組。
以 cloudowner 使用者身分登出 vCenter，然後以 *srm soln-管理* 使用者身分登入。

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>CloudSimple 入口網站：設定 vRA 的防火牆規則

依照 [設定防火牆資料表和規則](firewall.md) 以開啟埠來啟用通訊埠，如下所述設定防火牆規則：

* vRA 在管理網路的「SRM 解決方案網路」和 vCenter 和 ESXi 主機中。
* 在兩個網站上 vRA 設備。

請參閱這份 [VMware 知識庫文章](https://kb.vmware.com/s/article/2087769) ，以取得必須針對 vSphere Replication 6.5 開啟的埠號碼清單。

### <a name="install-srm-server-in-your-on-premises-environment"></a>在您的內部部署環境中安裝 SRM 伺服器

開始之前，請確認下列事項：

* vSphere 複寫設備是安裝在您的內部部署和私用雲端環境中。
* 這兩個網站上的 vSphere 複寫設備彼此連線。
* 您已複習 VMware 有關必要條件和最佳作法的資訊。 針對 SRM 6.5，您可以參考 VMware 檔 [必要條件和 srm 6.5 的最佳作法](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)。

遵循 VMware 檔，在部署模型的雙網站拓撲中，執行每個 Platform Services Controller 的單一 vCenter 實例，如這 [份 VMware 檔](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)所述。 您可以在 [安裝 Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)的 VMware 檔中取得 SRM 6.5 的安裝指示。

### <a name="install-srm-server-in-your-private-cloud"></a>在您的私人雲端中安裝 SRM 伺服器

開始之前，請確認下列事項：

* vSphere 複寫設備是安裝在您的內部部署和私用雲端環境中。
* 這兩個網站上的 vSphere 複寫設備彼此連線。
* 您已複習 VMware 有關必要條件和最佳作法的資訊。 針對 SRM 6.5，您可以參考 [Site Recovery Manager 6.5 伺服器安裝的必要條件和最佳作法](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)。

下列步驟說明私用雲端 SRM 安裝。

1. [vCenter UI：安裝 SRM](#vcenter-ui-install-srm)
2. [CloudSimple 入口網站：設定適用于 SRM 的防火牆規則](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter UI：設定 SRM](#vcenter-ui-configure-srm)
4. [CloudSimple 入口網站：取消提升許可權](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter UI：安裝 SRM

使用「srm-soln-管理員」認證來登入 vCenter 之後，請遵循 VMware 檔集，在部署模型的「具有一個 vCenter 實例的雙網站拓撲（每個 Platform Services Controller」）中執行 SRM 伺服器安裝，如這份 [VMware 檔](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)所述。 您可以在 [安裝 Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)的 VMware 檔中取得 SRM 6.5 的安裝指示。

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>CloudSimple 入口網站：設定適用于 SRM 的防火牆規則

依照 [設定防火牆資料表和規則](firewall.md) 中所述，設定防火牆規則，以允許下列各項之間的通訊：

在私用雲端中的「SRM 伺服器」和「vCenter/PSC」。
這兩個網站上的 SRM 伺服器

請參閱 [這份 VMware 知識庫文章](https://kb.vmware.com/s/article/2087769) ，以取得必須針對 vSphere Replication 6.5 開啟的埠號碼清單。

#### <a name="vcenter-ui-configure-srm"></a>vCenter UI：設定 SRM

將「SRM」安裝在私用雲端之後，請執行下列工作，如《 VMware Site Recovery 管理員安裝和設定指南》章節中所述。 針對 SRM 6.5， [安裝 Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)的 VMware 檔中有提供指示。

1. 連接受保護和復原網站上的 Site Recovery Manager 伺服器實例。
2. 建立遠端 Site Recovery Manager 伺服器實例的用戶端連接。
3. 安裝 Site Recovery Manager 授權金鑰。

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple 入口網站：取消提升許可權

若要取消提升許可權，請參閱 [取消提升許可權](escalate-private-cloud-privileges.md#de-escalate-privileges)。

## <a name="ongoing-management-of-your-srm-solution"></a>持續管理您的 SRM 解決方案

您可以完全掌控私用雲端環境中的 vSphere 複寫和 SRM 軟體，而且預期會執行必要的軟體生命週期管理。 更新或升級 vSphere 複寫或 SRM 之前，請先確定任何新版本的軟體都與私人雲端 vCenter 和 PSC 相容。

> [!NOTE]
> CloudSimple 目前正在探索提供受控 DR 服務的選項。 

## <a name="multiple-replication-configuration"></a>多個複寫設定

 以陣列為基礎的複寫[和 vSphere 複寫技術都可以同時與 SRM 一起使用](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)。 不過，它們必須套用至個別的 Vm 集合 (指定的 VM 可以透過以陣列為基礎的複寫或 vSphere 複寫來進行保護，但不能同時為這兩個) 。 此外，您可以將 CloudSimple 網站設定為多個受保護網站的復原網站。 如需多網站設定的詳細資訊，請參閱 [SRM 多網站選項](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) 。

## <a name="references"></a>參考資料

* [VMware Site Recovery Manager 檔](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Site Recovery Manager 6.5 的操作限制](https://kb.vmware.com/s/article/2147110)
* [計算 vSphere 複寫的頻寬需求](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [部署 vSphere Replication 6.5 時的 OVF 選項](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere Replication 6.5 安裝和設定](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [SRM 6.5 的必要條件和最佳作法](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [在兩個網站拓撲中 Site Recovery 管理員，每個 Platform Services Controller 都有一個 vCenter Server 實例](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware Site Recovery Manager 6.5 安裝和設定指南](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [使用以陣列為基礎的複寫與 vSphere 複寫之 SRM 上的 VMware Blog](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [SRM 多網站選項的 VMware Blog](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [必須針對 vSphere 複寫5.8、6.x 和8開啟的埠號碼](https://kb.vmware.com/s/article/2147112)
