---
title: Azure VMware 解決方案（按雲簡單 - 使用私有雲作為本地工作負載的災難網站）
description: 描述如何將雲簡單私有雲設置為本地 VMware 工作負載的災害復原網站
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e019a9229b671be2fb73e758bd39f33657bc2d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083143"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>將雲簡單私有雲設置為本地 VMware 工作負載的災害復原網站

雲簡單私有雲可以設置為本地應用程式的恢復網站，在發生災難時提供業務連續性。 恢復解決方案基於 Zerto 虛擬複製作為複製和業務流程平臺。 關鍵基礎設施和應用程式虛擬機器可以從本地 vCenter 連續複製到私有雲。 您可以使用私有雲進行容錯移轉測試，並確保應用程式在災難期間的可用性。 也可以採用類似方法將私有雲設置為主網站，該網站受其他位置的恢復網站保護。

> [!NOTE]
> 有關調整災害復原環境大小的指南，請參閱[Zerto 文檔"Zerto 虛擬複製大小調整注意事項](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf)"。

雲簡單解決方案：

* 無需設置專門用於災害復原 （DR） 的資料中心。
* 允許您利用部署 CloudSimple 的 Azure 位置，實現全球地理恢復能力。
* 為您提供了降低 DR 的部署成本和擁有權總成本的選項。

該解決方案要求您：

* 在私有雲中安裝、配置和管理 Zerto。
* 當私有雲是受保護的網站時，為 Zerto 提供您自己的許可證。 您可以將在 CloudSimple 網站上運行的 Zerto 與本地網站配對以進行許可。

下圖顯示了 Zerto 解決方案的體系結構。

![架構](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>如何部署解決方案

以下各節介紹如何在私有雲中使用 Zerto 虛擬複製部署 DR 解決方案。

1. [必要條件](#prerequisites)
2. [雲簡單私有雲上的可選配置](#optional-configuration-on-your-private-cloud)
3. [在雲簡單私有雲上設置 ZVM 和 VRA](#set-up-zvm-and-vra-on-your-private-cloud)
4. [設置澤托虛擬保護組](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Prerequisites

要啟用從本地環境到私有雲的 Zerto 虛擬複製，請完成以下先決條件。

1. [在本地網路和雲簡單私有雲之間建立網站到網站 VPN 連接](set-up-vpn.md)。
2. [設置 DNS 查找，以便將私有雲管理元件轉發到私有雲 DNS 伺服器](on-premises-dns-setup.md)。  要啟用 DNS 查找的轉發，請在本地 DNS 伺服器`*.cloudsimple.io`中創建用於 CloudSimple DNS 伺服器的轉發區域條目。
3. 設置 DNS 查找，以便將本地 vCenter 元件轉發到本地 DNS 伺服器。  DNS 伺服器必須通過網站到網站 VPN 從雲簡單私有雲訪問。 為尋求説明，請提交[支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，並提供以下資訊。  

    * 本地 DNS 功能變數名稱
    * 本地 DNS 伺服器 IP 位址

4. 在私有雲上安裝 Windows 伺服器。 伺服器用於安裝 Zerto 視覺管理員。
5. [升級雲簡單許可權](escalate-private-cloud-privileges.md)。
6. 在私有雲 vCenter 上創建一個新使用者，其管理角色用作 Zerto 視覺管理員的服務帳戶。

### <a name="optional-configuration-on-your-private-cloud"></a>私有雲上的可選配置

1. 在私有雲 vCenter 上創建一個或多個資源池，用作本地環境中 VM 的目標資源池。
2. 在私有雲 vCenter 上創建一個或多個資料夾，用作本地環境中 VM 的目的檔案夾。
3. 為容錯移轉網路創建 VLAN 並設置防火牆規則。 打開[支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)請求以尋求説明。
4. 為容錯移轉網路和測試網路創建分散式埠組，以測試 VM 的容錯移轉。
5. 在私有雲環境中安裝[DHCP 和 DNS 伺服器](dns-dhcp-setup.md)或使用活動目錄網域控制站。

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>在私有雲上設置 ZVM 和 VRA

1. 在私有雲中的 Windows 伺服器上安裝 Zerto 視覺管理員 （ZVM）。
2. 使用在前面的步驟中創建的服務帳戶登錄到 ZVM。
3. 為 Zerto 視覺管理員設置許可。
4. 在私有雲的 ESXi 主機上安裝 Zerto 虛擬複製設備 （VRA）。
5. 將私有雲 ZVM 與本地 ZVM 配對。

### <a name="set-up-zerto-virtual-protection-group"></a>設置澤托虛擬保護組

1. 創建新的虛擬保護組 （VPG） 並指定 VPG 的優先順序。
2. 選擇需要保護業務連續性的虛擬機器，並根據需要自訂啟動順序。
3. 選擇恢復網站作為私有雲，將預設恢復伺服器作為私有雲群集或您創建的資源組。 為私有雲上的恢復資料存儲選擇**vsan DataStore。**

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > 您可以在"VM 設置"選項下為各個 VM 自訂主機選項。

4. 根據需要自訂存儲選項。
5. 指定用於容錯移轉網路和容錯移轉測試網路的恢復網路作為較早創建的分散式埠組，並根據需要自訂恢復腳本。
6. 如有必要，自訂各個 VM 的網路設置並創建 VPG。
7. 複製完成後測試容錯移轉。

## <a name="reference"></a>參考資料

[澤托文檔](https://www.zerto.com/myzerto/technical-documentation/)
