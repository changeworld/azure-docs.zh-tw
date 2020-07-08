---
title: Azure VMware Solution by CloudSimple-針對內部部署工作負載使用私用雲端作為災難網站
description: 說明如何將 CloudSimple 私用雲端設定為內部部署 VMware 工作負載的嚴重損壞修復網站
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e019a9229b671be2fb73e758bd39f33657bc2d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77083143"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>將 CloudSimple 私用雲端設定為內部部署 VMware 工作負載的損毀修復網站

您的 CloudSimple 私人雲端可以設定為內部部署應用程式的復原網站，以在發生嚴重損壞時供應商務持續性。 復原解決方案是以 Zerto 虛擬複寫為基礎，做為複寫和協調流程平臺。 重要的基礎結構和應用程式虛擬機器可以從內部部署 vCenter 持續複寫到私人雲端。 您可以使用私人雲端進行容錯移轉測試，並確保應用程式在嚴重損壞期間的可用性。 您可以遵循類似的方法，將私人雲端設定為在不同位置受到復原網站保護的主要網站。

> [!NOTE]
> 如需調整嚴重損壞修復環境大小的指導方針，請參閱 Zerto 虛擬複寫的 Zerto 檔[大小考慮](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf)。

CloudSimple 解決方案：

* 不需要特別針對嚴重損壞修復（DR）設定資料中心。
* 可讓您利用部署 CloudSimple 以取得全球地理彈性的 Azure 位置。
* 可讓您選擇降低部署成本，以及 DR 的擁有權總成本。

解決方案需要您：

* 在您的私人雲端中安裝、設定及管理 Zerto。
* 當私人雲端是受保護的網站時，請提供您自己的 Zerto 授權。 您可以將 CloudSimple 網站上執行的 Zerto 與您的內部部署網站配對，以進行授權。

下圖顯示 Zerto 解決方案的架構。

![架構](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>如何部署解決方案

下列各節說明如何在您的私人雲端中使用 Zerto 虛擬複寫來部署 DR 解決方案。

1. [先決條件](#prerequisites)
2. [CloudSimple 私用雲端上的選擇性設定](#optional-configuration-on-your-private-cloud)
3. [在 CloudSimple 私用雲端上設定 ZVM 和 VRA](#set-up-zvm-and-vra-on-your-private-cloud)
4. [設定 Zerto 虛擬保護群組](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>必要條件

若要啟用從內部部署環境到私人雲端的 Zerto 虛擬複寫，請完成下列必要條件。

1. [在您的內部部署網路與 CloudSimple 私人雲端之間設定站對站 VPN](set-up-vpn.md)連線。
2. [設定 DNS 查閱，以便將私人雲端管理元件轉送到私人雲端 DNS 伺服器](on-premises-dns-setup.md)。  若要啟用 DNS 查閱的轉送，請在您的內部部署 DNS 伺服器中建立轉送區域專案， `*.cloudsimple.io` 以 CLOUDSIMPLE DNS 伺服器。
3. 設定 DNS 查閱，以便將內部部署 vCenter 元件轉送至內部部署 DNS 伺服器。  您必須透過站對站 VPN，從您的 CloudSimple 私用雲端連線到 DNS 伺服器。 如需協助，請提交[支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，並提供下列資訊。  

    * 內部部署 DNS 功能變數名稱
    * 內部部署 DNS 伺服器 IP 位址

4. 在您的私人雲端上安裝 Windows server。 伺服器是用來安裝 Zerto Virtual Manager。
5. [提升您的 CloudSimple 許可權](escalate-private-cloud-privileges.md)。
6. 在您的私用雲端 vCenter 上，使用系統管理角色來建立新的使用者，以作為 Zerto Virtual Manager 的服務帳戶。

### <a name="optional-configuration-on-your-private-cloud"></a>私人雲端上的選擇性設定

1. 在您的私用雲端 vCenter 上建立一或多個資源集區，以作為內部部署環境中 Vm 的目標資源集區。
2. 在您的私用雲端 vCenter 上建立一或多個資料夾，以作為內部部署環境中 Vm 的目的檔案夾。
3. 建立用於容錯移轉網路的 Vlan 並設定防火牆規則。 開啟[支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)以取得協助。
4. 為容錯移轉網路和測試網路建立分散式通訊埠群組，以測試 Vm 的容錯移轉。
5. 在您的私人雲端環境中安裝[DHCP 和 DNS 伺服器，](dns-dhcp-setup.md)或使用 Active Directory 網域控制站。

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>在您的私人雲端上設定 ZVM 和 VRA

1. 在私人雲端的 Windows server 上安裝 Zerto Virtual Manager （ZVM）。
2. 使用在先前步驟中建立的服務帳戶登入 ZVM。
3. 設定 Zerto Virtual Manager 的授權。
4. 在私人雲端的 ESXi 主機上安裝 Zerto 虛擬複寫設備（VRA）。
5. 將您的私用雲端 ZVM 與您的內部部署 ZVM 配對。

### <a name="set-up-zerto-virtual-protection-group"></a>設定 Zerto 虛擬保護群組

1. 建立新的虛擬保護群組（VPG），並指定 VPG 的優先順序。
2. 選取需要保護商務持續性的虛擬機器，並視需要自訂開機順序。
3. 選取復原網站作為您的私人雲端，以及預設復原伺服器做為私人雲端叢集或您建立的資源群組。 針對私人雲端上的復原資料存放區選取 [ **vsanDatastore** ]。

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > 您可以在 [VM 設定] 選項下，自訂個別 Vm 的 [主機] 選項。

4. 視需要自訂儲存體選項。
5. 指定要用於容錯移轉網路和容錯移轉測試網路的復原網路，作為稍早建立的分散式通訊埠群組，並視需要自訂修復腳本。
6. 視需要自訂個別 Vm 的網路設定，並建立 VPG。
7. 複寫完成之後，測試容錯移轉。

## <a name="reference"></a>參考

[Zerto 檔](https://www.zerto.com/myzerto/technical-documentation/)
