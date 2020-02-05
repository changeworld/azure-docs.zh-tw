---
title: Azure VMware 解決方案（AVS）-使用 VMware Site Recovery Manager 將 AVS 私用雲端設定為損毀修復網站
description: 說明如何將您的 AVS 私人雲端設定為內部部署 VMware 工作負載的嚴重損壞修復網站
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0bbd8a12820432ce2f131dda29af6740a2f04e18
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024716"
---
# <a name="set-up-avs-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>使用 VMware Site Recovery Manager 將 AVS 私用雲端設定為嚴重損壞修復目標

您可以使用您的 AVS 私人雲端作為內部部署 VMware 工作負載的嚴重損壞修復（DR）網站。

DR 解決方案是以 vSphere Replication 和 VMware Site Recovery Manager （SRM）為基礎。 您可以遵循類似的方法，讓您的 AVS 私人雲端成為內部部署復原網站所保護的主要網站。

AVS 解決方案：

* 不需要特別針對 DR 設定資料中心。
* 可讓您利用部署了 AVS 以進行全球地理彈性的 Azure 位置。
* 提供選項來降低部署成本，以及建立 DR 的擁有權總成本。

AVS 解決方案會要求您執行下列動作：

* 在您的 AVS 私人雲端中安裝、設定及管理 vSphere 複寫和 SRM。
* 當 AVS 私用雲端是受保護的網站時，為您的 SRM 提供您自己的授權。 當您使用 AVS 網站作為復原網站時，您不需要任何額外的「SRM」授權。

透過此解決方案，您可以完全掌控 vSphere 複寫和 SRM。 熟悉的 UI、API 和 CLI 介面可讓您使用現有的腳本和工具。

![Site Recovery Manager 部署](media/srm-deployment.png)

您可以使用任何與您的 AVS 私用雲端和內部部署環境相容的 vRA 和 SRM 版本。 本指南中的範例使用 vRA 6.5 和 SRM 6.5。 這些版本與 AVS 支援的 vSphere 6.5 相容。

## <a name="deploy-the-solution"></a>部署解決方案

下列各節說明如何在您的 AVS 私人雲端中使用「SRM」部署 DR 解決方案。

1. [確認 VMware 產品版本相容](#verify-that-vmware-product-versions-are-compatible)
2. [估計 DR 環境的大小](#estimate-the-size-of-your-dr-environment)
3. [為您的環境建立一個 AVS 私用雲端](#create-an-avs-private-cloud-for-your-environment)
4. [設定適用于 SRM 解決方案的 AVS 私用雲端網路](#set-up-avs-private-cloud-networking-for-the-srm-solution)
5. [在您的內部部署網路與 AVS 私人雲端之間設定站對站 VPN 連線，並開啟所需的埠](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports)
6. [在您的 AVS 私人雲端中設定基礎結構服務](#set-up-infrastructure-services-in-your-avs-private-cloud)
7. [在您的內部部署環境中安裝 vSphere 複寫應用裝置](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [在您的 AVS 私用雲端環境中安裝 vSphere 複寫應用裝置](#install-vsphere-replication-appliance-in-your-avs-private-cloud-environment)
9. [在您的內部部署環境中安裝 SRM 伺服器](#install-srm-server-in-your-on-premises-environment)
10. [在您的 AVS 私人雲端中安裝 SRM 伺服器](#install-srm-server-in-your-avs-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>確認 VMware 產品版本相容

本指南中的設定受限於下列相容性需求：

* 相同版本的 SRM 必須部署在您的 AVS 私人雲端和您的內部部署環境中。
* 您必須在您的 AVS 私人雲端和內部部署環境中部署相同版本的 vSphere 複寫。
* 您的 AVS 私用雲端和內部部署環境中的平臺服務控制站（PSC）版本必須相容。
* 您的 AVS 私人雲端和內部部署環境中的 vCenter 版本必須相容。
* SRM 和 vSphere 複寫的版本必須與其他版本和 PSC 和 vCenter 相容。

如需相關 VMware 檔和相容性資訊的連結，請移至[vmware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)檔。

若要瞭解您的 AVS 私用雲端中的 vCenter 和 PSC 版本，請開啟 AVS 入口網站。 移至 [**資源**]，選取您的 AVS 私人雲端，然後按一下 [ **vSphere 管理網路**] 索引標籤。

![在 AVS 私人雲端中的 vCenter & PSC 版本](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>估計 DR 環境的大小

1. 請確認您所識別的內部部署設定是否在支援的限制範圍內。 針對 SRM 6.5，這些限制記載于 VMware 知識庫文章中[Site Recovery Manager 6.5 的操作限制](https://kb.vmware.com/s/article/2147110)。
2. 請確定您有足夠的網路頻寬，以符合您的工作負載大小和 RPO 需求。 有關[計算 vSphere 複寫的頻寬需求](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)的 VMware 知識庫文章提供頻寬限制的指引。
3. 使用 AVS sizer 工具來估計 DR 網站中所需的資源，以保護您的內部部署環境。

### <a name="create-an-avs-private-cloud-for-your-environment"></a>為您的環境建立一個 AVS 私用雲端

遵循[建立 Avs 私用雲端](create-private-cloud.md)中的指示和大小建議，從 AVS 入口網站建立 avs 私用雲端。

### <a name="set-up-avs-private-cloud-networking-for-the-srm-solution"></a>設定適用于 SRM 解決方案的 AVS 私用雲端網路

存取 AVS 入口網站，為 SRM 解決方案設定 AVS 私用雲端網路。

建立適用于 SRM 解決方案網路的 VLAN，並為其指派子網 CIDR。 如需指示，請參閱[建立和管理 vlan/子網](create-vlan-subnet.md)。

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports"></a>在您的內部部署網路與 AVS 私人雲端之間設定站對站 VPN 連線，並開啟所需的埠

設定內部部署網路與您的 AVS 私人雲端之間的站對站連線能力。 如需指示，請參閱[設定您的 AVS 私人雲端的 VPN](set-up-vpn.md)連線。

### <a name="set-up-infrastructure-services-in-your-avs-private-cloud"></a>在您的 AVS 私人雲端中設定基礎結構服務

在 AVS 私人雲端中設定基礎結構服務，以輕鬆管理您的工作負載和工具。

如果您想要執行下列任何一項動作，您可以新增外部身分識別提供者，如在[AVS 私人雲端上作為 vCenter 的識別提供者使用 Azure AD](azure-ad.md)中所述：

* 從您的 AVS 私用雲端中的內部部署 Active Directory （AD）識別使用者。
* 在您的 AVS 私人雲端中為所有使用者設定 AD。
* 使用 Azure AD。

若要在您的 AVS 私人雲端中為您的工作負載提供 IP 位址查閱、IP 位址管理和名稱解析服務，請依照在[您的 Avs 私人雲端中設定 DNS 和 DHCP 應用程式和工作負載](dns-dhcp-setup.md)中所述，設定 DHCP 和 DNS 伺服器。

您的 AVS 私人雲端中的管理 Vm 和主機會使用 *. avs.io 網域。 若要解決此網域的要求，請在 DNS 伺服器上設定 DNS 轉送，如[建立條件](on-premises-dns-setup.md#create-a-conditional-forwarder)轉寄站中所述。

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>在您的內部部署環境中安裝 vSphere 複寫應用裝置

遵循 VMware 檔，在您的內部部署環境中安裝 vSphere 複寫應用裝置（vRA）。 安裝包含下列高階步驟：

1. 準備您的內部部署環境以進行 vRA 安裝。

2. 使用 vmware.com 的 VR ISO 中的 OVF，在您的內部部署環境中部署 vRA。 針對 vRA 6.5，[此 VMware blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices)包含相關資訊。

3. 在內部部署網站上，向 vCenter 單一登入註冊內部部署 vRA。 如需 vSphere 複寫6.5 的詳細指示，請參閱 VMware 檔[VMware vSphere 複寫6.5 安裝和](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)設定。

## <a name="install-vsphere-replication-appliance-in-your-avs-private-cloud-environment"></a>在您的 AVS 私用雲端環境中安裝 vSphere 複寫應用裝置

開始之前，請先確認您具有下列各項：

* 從內部部署環境中的子網到您的 AVS 私用雲端的管理子網的 IP 連線能力
* 從內部部署 vSphere 環境中的複寫子網到您的 AVS 私人雲端的 SRM 解決方案子網的 IP 連線能力

如需指示，請參閱[設定您的 AVS 私人雲端的 VPN](set-up-vpn.md)連線。 這些步驟類似于內部部署安裝。

在 vRA 和 SRM 安裝期間，AVS 建議使用 Fqdn 而不是 IP 位址。 若要在您的 AVS 私用雲端中找出 vCenter 和 PSC 的 FQDN，請開啟 AVS 入口網站。 移至 [**資源**]，選取您的 AVS 私人雲端，然後按一下 [ **vSphere 管理網路**] 索引標籤。

![在 AVS 私人雲端中尋找 vCenter/PSC 的 FQDN](media/srm-resources.png)

AVS 要求您不要使用預設的 ' cloudowner ' 使用者來安裝 vRA 和 SRM，而是改為建立新的使用者。 這麼做是為了協助確保您的 AVS 私用雲端 vCenter 環境具有高執行時間和可用性。 不過，AVS 私用雲端 vCenter 中的預設 cloudowner 使用者沒有足夠的許可權可建立具有系統管理許可權的新使用者。

在安裝 vRA 和 SRM 之前，您必須先呈報 cloudowner 使用者的 vCenter 許可權，然後在 vCenter SSO 網域中建立具有系統管理許可權的使用者。 如需預設的 AVS 私用雲端使用者和許可權模型的詳細資訊，請參閱[瞭解 AVS 私用雲端許可權模型](learn-private-cloud-permissions.md)。

安裝包含下列高階步驟：

1. [提升許可權](escalate-private-cloud-privileges.md)。
2. 在您的 AVS 私人雲端中建立使用者，以進行 vSphere 複寫和 SRM 安裝。 以下在[VCENTER UI 中說明：在適用于 vRA & SRM 安裝的 AVS 私人雲端中建立使用者](#vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation)。
3. 準備您的 AVS 私用雲端環境，以進行 vRA 安裝。
4. 使用 vmware.com 的 VR ISO 中的 OVF，在您的 AVS 私人雲端中部署 vRA。 針對 vRA 6.5，[此 VMware blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices)包含相關資訊。
5. 設定 vRA 的防火牆規則。 以下在[AVS 入口網站中說明：設定 vRA 的防火牆規則](#avs-portal-configure-firewall-rules-for-vra)。
6. 在 AVS 私用雲端網站上註冊具有 vCenter 單一登入的 AVS 私用雲端 vRA。
7. 設定兩個設備之間的 vSphere 複寫連接。 請確定已在防火牆上開啟所需的埠。 如需 vSphere 複寫6.5 必須開啟的埠號碼清單，請參閱[此 VMware 知識庫文章](https://kb.vmware.com/s/article/2087769)。

如需 vSphere 複寫6.5 的詳細安裝指示，請參閱 VMware 檔[VMware vSphere 複寫6.5 安裝和](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)設定。

#### <a name="vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation"></a>vCenter UI：在適用于 vRA 和 SRM 安裝的 AVS 私人雲端中建立使用者

從 AVS 入口網站升級許可權後，使用 cloudowner 使用者認證登入 vCenter。

在 vCenter 中建立新的使用者 `srm-soln-admin`，並將它新增至 vCenter 中的 administrators 群組。
以 cloudowner 使用者身分登出 vCenter，並以「 *srm-soln-管理員*」使用者身分登入。

#### <a name="avs-portal-configure-firewall-rules-for-vra"></a>AVS 入口網站：設定 vRA 的防火牆規則

設定防火牆規則，如[設定防火牆資料表和規則](firewall.md)中所述，以開啟埠以啟用下列各項之間的通訊：

* 在管理網路中的 SRM 解決方案網路和 vCenter 和 ESXi 主機中 vRA。
* 在兩個網站上 vRA 設備。

如需 vSphere 複寫6.5 必須開啟的埠號碼清單，請參閱此[VMware 知識庫文章](https://kb.vmware.com/s/article/2087769)。

### <a name="install-srm-server-in-your-on-premises-environment"></a>在您的內部部署環境中安裝 SRM 伺服器

開始之前，請確認下列事項：

* vSphere 複寫設備會安裝在您的內部部署和 AVS 私人雲端環境中。
* 這兩個網站上的 vSphere 複寫設備會彼此連接。
* 您已複習有關必要條件和最佳作法的 VMware 資訊。 針對 SRM 6.5，您可以參考 VMware 檔的[必要條件和 srm 6.5 的最佳作法](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)。

遵循 VMware 檔，在部署模型「具有每個平臺服務的一個 vCenter 實例的雙網站拓撲」中執行「SRM 伺服器安裝」，如這份[VMware 檔](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)中所述。 如需 SRM 6.5 的安裝指示，請查看[安裝 Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)的 VMware 檔。

### <a name="install-srm-server-in-your-avs-private-cloud"></a>在您的 AVS 私人雲端中安裝 SRM 伺服器

開始之前，請確認下列事項：

* vSphere 複寫設備會安裝在您的內部部署和 AVS 私人雲端環境中。
* 這兩個網站上的 vSphere 複寫設備會彼此連接。
* 您已複習有關必要條件和最佳作法的 VMware 資訊。 針對 SRM 6.5，您可以參考[Site Recovery Manager 6.5 伺服器安裝的必要條件和最佳作法](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)。

下列步驟說明 AVS 私用雲端 SRM 安裝。

1. [vCenter UI：安裝 SRM](#vcenter-ui-install-srm)
2. [AVS 入口網站：設定 SRM 的防火牆規則](#avs-portal-configure-firewall-rules-for-srm)
3. [vCenter UI：設定 SRM](#vcenter-ui-configure-srm)
4. [AVS 入口網站：取消提升許可權](#avs-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter UI：安裝 SRM

使用「srm-soln-系統管理員」認證登入 vCenter 之後，請遵循 VMware 檔，在部署模型「具有每個平臺服務的一個 vCenter 實例的雙網站拓撲」中執行「SRM 伺服器安裝」，如此[VMware 檔](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)中所述。 如需 SRM 6.5 的安裝指示，請查看[安裝 Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)的 VMware 檔。

#### <a name="avs-portal-configure-firewall-rules-for-srm"></a>AVS 入口網站：設定 SRM 的防火牆規則

依照[設定防火牆資料表和規則](firewall.md)中所述的方式設定防火牆規則，以允許下列各項之間的通訊：

在 AVS 私用雲端中的 SRM 伺服器和 vCenter/PSC。
兩個網站上的 SRM 伺服器

如需 vSphere 複寫6.5 必須開啟的埠號碼清單，請參閱[此 VMware 知識庫文章](https://kb.vmware.com/s/article/2087769)。

#### <a name="vcenter-ui-configure-srm"></a>vCenter UI：設定 SRM

在 AVS 私人雲端中安裝了「SRM」之後，請依照 VMware Site Recovery 管理員安裝和設定指南的各節中所述，執行下列工作。 針對 SRM 6.5，[安裝 Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)的 VMware 檔中提供指示。

1. 連接受保護和復原網站上的 Site Recovery Manager 伺服器實例。
2. 建立與遠端 Site Recovery 管理員伺服器實例的用戶端連接。
3. 安裝 Site Recovery Manager 授權金鑰。

#### <a name="avs-portal-de-escalate-privileges"></a>AVS 入口網站：取消提升許可權

若要取消提升許可權，請參閱[取消提升許可權](escalate-private-cloud-privileges.md#de-escalate-privileges)。

## <a name="ongoing-management-of-your-srm-solution"></a>持續管理您的 SRM 解決方案

您可以完整控制您的 AVS 私用雲端環境中的 vSphere 複寫和 SRM 軟體，而且預期會執行必要的軟體生命週期管理。 更新或升級 vSphere 複寫或 SRM 之前，請確定任何新版本的軟體都與 AVS 私用雲端 vCenter 和 PSC 相容。

> [!NOTE]
> AVS 目前正在探索提供受控 DR 服務的選項。 

## <a name="multiple-replication-configuration"></a>多重複寫設定

 以陣列為基礎的複寫[和 vSphere 複寫技術可以同時與 SRM 搭配使用](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)。 不過，它們必須套用至個別的 Vm 集（指定的 VM 可以透過以陣列為基礎的複寫或 vSphere 複寫來保護，但不能同時受到這兩者的保護）。 此外，您也可以將 AVS 網站設定為多個受保護網站的復原網站。 如需多網站設定的詳細資訊，請參閱[SRM 多網站選項](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/)。

## <a name="references"></a>參考

* [VMware Site Recovery Manager 檔](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Site Recovery Manager 6.5 的操作限制](https://kb.vmware.com/s/article/2147110)
* [計算 vSphere 複寫的頻寬需求](https://kb.vmware.com/s/article/2037268)
* [部署 vSphere 複寫6.5 時的 OVF 選項](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere 複寫6.5 安裝和設定](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [SRM 6.5 的必要條件和最佳作法](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [具有一個 vCenter Server 實例每個平臺服務控制器的兩個網站拓撲中的 Site Recovery 管理員](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware Site Recovery Manager 6.5 安裝和設定指南](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [以陣列為基礎的複寫與 vSphere 複寫的 SRM 上的 VMware Blog](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [SRM 多網站選項的 VMware Blog](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [必須針對 vSphere 複寫 6.x. x、6.x 和8開啟的埠號碼](https://kb.vmware.com/s/article/2147112)
