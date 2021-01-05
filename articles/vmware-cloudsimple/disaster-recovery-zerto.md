---
title: Azure VMware Solution by CloudSimple-使用私用雲端作為內部部署工作負載的災難網站
description: 說明如何將 CloudSimple 私人雲端設定為內部部署 VMware 工作負載的災難復原網站
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5dd1f157984cdb300cc3b375d71654e5da54d1ae
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898125"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>將 CloudSimple 私人雲端設定為內部部署 VMware 工作負載的災難復原網站

您的 CloudSimple 私人雲端可以設定為內部部署應用程式的復原網站，以在發生嚴重損壞時供應商務持續性。 復原解決方案是以 Zerto 虛擬複寫為基礎，作為複寫和協調流程平臺。 重要的基礎結構和應用程式虛擬機器可從內部部署 vCenter 持續複寫至私人雲端。 您可以使用私人雲端進行容錯移轉測試，並確保您的應用程式在發生嚴重損壞時的可用性。 您可以遵循類似的方法，將私人雲端設定為在不同位置受到復原網站保護的主要網站。

> [!NOTE]
> 如需調整嚴重損壞修復環境大小的指導方針，請參閱 Zerto 虛擬複寫的 Zerto 檔 [大小考慮](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) 。

CloudSimple 解決方案：

* 不需要特別針對嚴重損壞修復設定資料中心 (DR) 。
* 可讓您運用 CloudSimple 部署所在的 Azure 位置，以進行全球地理復原。
* 提供選項來降低部署成本，以及 DR 的擁有權總成本。

解決方案需要您：

* 在您的私人雲端中安裝、設定及管理 Zerto。
* 當私人雲端是受保護的網站時，請提供您自己的 Zerto 授權。 您可以將 CloudSimple 網站上執行的 Zerto 與您的內部部署網站配對，以獲得授權。

下圖顯示 Zerto 解決方案的架構。

![架構](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>如何部署解決方案

下列各節說明如何在您的私人雲端中使用 Zerto 虛擬複寫來部署 DR 解決方案。

1. [先決條件](#prerequisites)
2. [CloudSimple 私人雲端上的選擇性設定](#optional-configuration-on-your-private-cloud)
3. [在 CloudSimple 私用雲端上設定 ZVM 和 VRA](#set-up-zvm-and-vra-on-your-private-cloud)
4. [設定 Zerto 虛擬保護群組](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>必要條件

若要從您的內部部署環境啟用 Zerto 虛擬複寫至私人雲端，請完成下列必要條件。

1. [設定內部部署網路與 CloudSimple 私人雲端之間的站對站 VPN](set-up-vpn.md)連線。
2. [設定 DNS 查閱，以便將私人雲端管理元件轉送至私人雲端 DNS 伺服器](on-premises-dns-setup.md)。  若要啟用 DNS 查閱的轉送，請在您的內部部署 DNS 伺服器中建立轉送區域專案，以便 `*.cloudsimple.io` CLOUDSIMPLE DNS 伺服器。
3. 設定 DNS 查閱，以便將內部部署 vCenter 元件轉送至內部部署的 DNS 伺服器。  DNS 伺服器必須可透過站對站 VPN 從您的 CloudSimple 私人雲端連線。 如需協助，請提交 [支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，並提供下列資訊。  

    * 內部部署 DNS 功能變數名稱
    * 內部部署 DNS 伺服器 IP 位址

4. 在您的私人雲端上安裝 Windows server。 伺服器是用來安裝 Zerto Virtual Manager。
5. [提升您的 CloudSimple 許可權](escalate-private-cloud-privileges.md)。
6. 使用系統管理角色，在私人雲端 vCenter 上建立新的使用者，以作為 Zerto Virtual Manager 的服務帳戶。

### <a name="optional-configuration-on-your-private-cloud"></a>私人雲端上的選擇性設定

1. 在私用雲端 vCenter 上建立一或多個資源集區，以作為內部部署環境中 Vm 的目標資源集區。
2. 在您的私人雲端 vCenter 上建立一或多個資料夾，以作為內部部署環境中 Vm 的目的檔案夾。
3. 建立容錯移轉網路的 Vlan 並設定防火牆規則。 開啟 [支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 以取得協助。
4. 建立用於容錯移轉網路和測試網路的分散式埠群組，以測試 Vm 的容錯移轉。
5. 安裝 [DHCP 和 DNS 伺服器，](dns-dhcp-setup.md) 或在您的私人雲端環境中使用 Active Directory 網域控制站。

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>在您的私人雲端上設定 ZVM 和 VRA

1. 在您的私人雲端中，于 Windows 伺服器上安裝 Zerto Virtual Manager (ZVM) 。
2. 使用在先前步驟中建立的服務帳戶登入 ZVM。
3. 設定 Zerto Virtual Manager 的授權。
4. 在您私人雲端的 ESXi 主機上安裝 Zerto 虛擬複寫設備 (VRA) 。
5. 將您的私用雲端 ZVM 與您的內部部署 ZVM 配對。

### <a name="set-up-zerto-virtual-protection-group"></a>設定 Zerto 虛擬保護群組

1. 建立新的虛擬保護群組 (VPG) 並指定 VPG 的優先順序。
2. 選取需要保護商務持續性的虛擬機器，並視需要自訂開機順序。
3. 選取復原網站作為私人雲端，並選取預設的復原伺服器作為私用雲端叢集或您建立的資源群組。 針對私人雲端上的復原資料存放區選取 **vsanDatastore** 。

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > 您可以在 VM 設定選項下自訂個別 Vm 的主機選項。

4. 視需要自訂儲存體選項。
5. 指定要用於容錯移轉網路和容錯移轉測試網路的復原網路，作為先前建立的分散式埠群組，並視需要自訂修復腳本。
6. 視需要自訂個別 Vm 的網路設定，並建立 VPG。
7. 複寫完成之後，測試容錯移轉。

## <a name="reference"></a>參考

[Zerto 檔](https://www.zerto.com/myzerto/technical-documentation/)
