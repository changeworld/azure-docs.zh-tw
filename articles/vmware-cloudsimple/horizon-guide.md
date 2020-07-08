---
title: Azure VMware Solution by CloudSimple-使用私用雲端網站來裝載使用 VMware 範圍的虛擬桌面基礎結構
description: 說明如何使用您的 CloudSimple 私用雲端網站，透過 VMware 的水準裝載虛擬桌面基礎結構
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 723821a78ecae308443c93567402e3b232c036f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77025243"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>使用 CloudSimple 私用雲端網站來裝載使用 VMware 範圍的虛擬桌面基礎結構

您可以使用您的 CloudSimple 私用雲端網站來裝載使用 VMware 範圍 7. x 的虛擬桌面基礎結構（VDI）。 下圖顯示 VDI 的邏輯解決方案架構。

![水準部署](media/horizon-deployment.png)

透過此解決方案，您可以完全掌控您的「水準視圖管理員」和「應用程式」磁片區。 熟悉的 UI、API 和 CLI 介面可讓您使用現有的腳本和工具。

CloudSimple 解決方案會要求您執行下列動作：

* 在您的私人雲端中安裝、設定及管理 VMware 範圍 7. x。
* 提供您自己的水準授權。

## <a name="deploy-the-solution"></a>部署解決方案

下列各節說明如何在您的私用雲端中使用水準部署 VDI 解決方案。

1. [確認 VMware 產品版本相容](#verify-that-vmware-product-versions-are-compatible)
2. [估計桌面環境的大小](#estimate-the-size-of-your-desktop-environment)
3. [為您的環境建立私人雲端](#create-a-private-cloud-for-your-environment)
4. [在您的私用雲端中安裝 VMware 水準](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>確認 VMware 產品版本相容

* 請確認您目前和規劃的範圍、應用程式磁片區、統一存取閘道，以及使用者環境管理員彼此相容，而且與私人雲端中的 vCenter 和 PSC 相同。 如需相容性資訊，請參閱[水準7.5 的 VMware 相容性矩陣](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=)。
* 若要在您的私人雲端中找出 vCenter 和 PSC 的目前版本，請移至[CloudSimple 入口網站](access-cloudsimple-portal.md)中的 [**資源**]，選取您的私人雲端，然後按一下 [ **vSphere 管理網路**] 索引標籤。

![vCenter 和 PSC 版本](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>估計桌面環境的大小

* 確認您已識別的設定是否在 VMware 操作限制內。
* 估計所有桌上型電腦和您的水準管理元件所需的資源。

### <a name="create-a-private-cloud-for-your-environment"></a>為您的環境建立私人雲端

1. 遵循[設定私人雲端環境](quickstart-create-private-cloud.md)中的指示，從 CloudSimple 入口網站建立私人雲端。  CloudSimple 會在每個新建立的私用雲端中，建立名為 ' cloudowner ' 的預設 vCenter 使用者。 如需預設私用雲端使用者和許可權模型的詳細資訊，請參閱[瞭解私用雲端許可權模型](learn-private-cloud-permissions.md)。
2. 在您的私人雲端中為水準管理平面建立 VLAN，並為其指派子網 CIDR。 如需指示，請參閱[建立和管理 vlan/子網](create-vlan-subnet.md)。 這是將會安裝所有解決方案元件（整合存取閘道、連線伺服器、應用程式磁片區伺服器和使用者環境管理員伺服器）的網路。
3. 決定您是否想要使用外部身分識別提供者來搭配您的私用雲端 vCenter。 如果是，請選擇下列其中一個選項：
    * 使用內部部署 Active Directory 作為外部身分識別提供者。 如需指示，請參閱[vCenter 身分識別來源](set-vcenter-identity.md)。
    * 在水準管理平面 VLAN 中設定私人雲端中的 Active Directory 伺服器，以做為外部身分識別提供者使用。 如需指示，請參閱[vCenter 身分識別來源](set-vcenter-identity.md)。
    * 在私人雲端中設定水準管理平面 VLAN 中的 DHCP 和 DNS 伺服器。 如需指示，請參閱[在您的 CloudSimple 私人雲端中設定 DNS 和 DHCP 應用程式和工作負載](dns-dhcp-setup.md)。
4. 在安裝于私人雲端中的 DNS 伺服器上設定 DNS 轉送。 如需指示，請參閱[建立條件](on-premises-dns-setup.md#create-a-conditional-forwarder)轉寄站。

### <a name="install-vmware-horizon-in-your-private-cloud"></a>在您的私用雲端中安裝 VMware 水準

下列部署圖表描述在私人雲端中部署的水準解決方案。 整合存取閘道、AD/DC、View 和應用程式磁片區伺服器會安裝在使用者建立的 VLAN 234 中。 整合存取閘道具有指派的公用 IP 位址，可從網際網路連線。 水準桌面集區 Vm 會部署在 VLAN 235 中，以提供額外的隔離和安全性。

![私用雲端中的水準部署](media/horizon-private-cloud.png)

下列各節概述設定類似圖所示之部署的指示。 開始之前，請先確認您具有下列各項：

* 使用 CloudSimple 入口網站建立的私用雲端，具有足夠的容量來執行您的桌面集區。
* 您的內部部署環境與私人雲端環境之間有足夠的頻寬，可支援您桌上型電腦的網路流量。
* 在您的內部部署資料中心與私人雲端之間設定的站對站 VPN 通道。
* 從內部部署環境中的使用者子網到 CloudSimple 私用雲端子網的 IP 連線能力。
* 已針對您的私人雲端安裝 AD/DHCP/DNS。

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>CloudSimple 入口網站：建立桌面集區的專用 VLAN/子網

建立適用于水準桌面集區的 VLAN，並為其指派子網 CIDR。 如需指示，請參閱[建立和管理 vlan/子網](create-vlan-subnet.md)。 這是所有桌面虛擬機器將會在其中執行的網路。

遵循標準的安全性最佳作法來保護您的水準部署：

* 僅允許桌上型電腦 RDP 流量/SSH 流量傳送至您的桌面 Vm。
* 僅允許水準管理平面 VLAN 和桌面集區 VLAN 之間的管理流量。
* 僅允許來自內部部署網路的管理流量。

您可以從 CloudSimple 入口網站設定[防火牆規則](firewall.md)，以強制執行這些最佳作法。

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>CloudSimple 入口網站：設定防火牆規則以保護水準管理平面

在 CloudSimple 入口網站中設定下列規則。 如需指示，請參閱[設定防火牆資料表和規則](firewall.md)。

1. 在 CloudSimple N-S 防火牆中設定防火牆規則，以允許在內部部署子網與水準管理 VLAN 之間進行通訊，以便只允許 VMware 檔[水準埠清單](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html)中所列的網路埠。

2. 在私人雲端中的水準管理 VLAN 和桌面集區 VLAN 之間建立 E-W 防火牆規則。

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>CloudSimple 入口網站：建立統一存取閘道的公用 IP 位址

建立統一存取閘道設備的公用 IP 位址，以啟用來自網際網路的桌面用戶端連線。 如需指示，請參閱[配置公用 IP 位址](public-ips.md)。

當安裝程式完成時，公用 IP 位址會被指派並列在 [公用 ip] 頁面上。

#### <a name="cloudsimple-portal-escalate-privileges"></a>CloudSimple 入口網站：提升許可權

預設的 ' cloudowner ' 使用者在私人雲端 vCenter 中沒有足夠的許可權可以安裝，因此必須提升使用者的 vCenter 許可權。 如需詳細資訊，請參閱[提升許可權](escalate-private-cloud-privileges.md)。

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>vCenter UI：在私人雲端中建立用於水準安裝的使用者

1. 使用 ' cloudowner ' 使用者認證登入 vCenter。
2. 在 vCenter 中建立新的使用者 [soln-admin]，並將使用者新增至 vCenter 中的 [系統管理員] 群組。
3. 以 ' cloudowner ' 使用者身分登出 vCenter，並以「soln-系統管理員」使用者身分登入。

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter UI：安裝 VMware 水準

如先前的邏輯架構一節中所述，水準解決方案具有下列元件：

* VMware 範圍視圖
* VMware 整合存取閘道
* VMware 應用程式磁片區管理員
* VMware 使用者環境管理員

安裝元件，如下所示：

1. 遵循 VMware 檔[部署和設定 Vmware 整合存取閘道](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html)中所提供的指示，安裝並設定整合存取閘道。

2. 依照[觀看安裝指南](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html)中的指示，在私用雲端中安裝水準視圖。

3. 遵循[安裝和設定 VMware 應用程式磁片](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html)區中的指示，安裝應用程式磁片區管理員。

4. 遵循[關於安裝和設定 VMware 使用者環境管理員](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html)中的指示，安裝和設定使用者環境管理員。

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>提出支援要求，以上傳 VMware 範圍預先封裝的應用程式磁片區

在安裝過程中，應用程式磁片區管理員會使用預先封裝的磁片區來布建應用程式堆疊和可寫入的磁片區。 這些磁片區會作為應用程式堆疊和可寫入磁片區的範本。

將磁片區上傳至私人雲端資料存放區需要 ESXi 的根密碼。 如需協助，請提交[支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。 附加 AppVolumes 安裝程式配套，讓 CloudSimple 支援人員可以將範本上傳至您的私用雲端環境。

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple 入口網站：取消提升許可權

您現在可以[取消升級](escalate-private-cloud-privileges.md#de-escalate-privileges)' cloudowner ' 使用者的許可權。

## <a name="ongoing-management-of-your-horizon-solution"></a>持續管理您的水準解決方案

您可以完全掌控私人雲端環境中的水準和應用程式磁片區管理員軟體，而且預期會執行必要的軟體生命週期管理。 更新或升級水準或應用程式磁片區之前，請確定任何新版本的軟體都與私用雲端 vCenter 和 PSC 相容。
