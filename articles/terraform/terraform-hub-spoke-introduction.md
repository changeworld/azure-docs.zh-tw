---
title: 教學課程 - 使用 Terraform 在 Azure 中建立中樞和輪輻混合網路拓撲
description: 教學課程說明如何在 Azure 中使用 Terraform 建立完整的混合式網路參考架構
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 6f156dd90b83ceaf5749c8c2acebae35bcb54a92
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472174"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-in-azure-using-terraform"></a>教學課程：使用 Terraform 在 Azure 中建立中樞和輪輻混合網路拓撲

本系列教學課程顯示如何使用 Terraform 在 Azure 中實作[中樞和輪輻網路拓撲](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)。 

中樞和輪輻拓撲是一種在共用一般服務時隔離工作負載的方式。 這些服務包括身分識別與安全性。 中樞是一個虛擬網路 (VNet)，可當作與內部部署網路的連線中心點。 「輪輻」是與中樞對等的 VNet。 共用的服務會部署在中樞中，個別工作負載則會部署在輪輻網路中。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 使用 HCL (HashiCorp 語言) 配置中樞和輪輻混合網路參考架構資源
> * 使用 Terraform 建立中樞網路設備資源
> * 使用 Terraform 在 Azure 中建立中樞網路作為所有資源的一般點
> * 使用 Terraform 在 Azure 中建立個別工作負載作為輪輻 VNet
> * 使用 Terraform 建立內部部署和 Azure 網路之間的閘道和連線
> * 使用 Terraform 建立與輪輻網路的 VNet 對等互連

## <a name="prerequisites"></a>Prerequisites

- **Azure 訂用帳戶**：如果您還沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

- **安裝及設定 Terraform**：若要在 Azure 中佈建 VM 和其他基礎架構，請[安裝及設定 Terraform](terraform-install-configure.md)

## <a name="hub-and-spoke-topology-architecture"></a>中樞與輪輻拓撲架構

中樞與輪輻拓撲中的中樞為 VNet。 VNet 可作為與您內部部署網路的連線中心點。 輪輻是與中樞對等的 VNet，可用於隔離工作負載。 流量會透過 ExpressRoute 或 VPN 閘道連線，在內部部署資料中心和中樞之間流動。 下圖示範中樞和輪輻拓撲中的元件：

![Azure 中的中樞與輪輻拓撲架構](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>中樞與輪輻拓撲的優點

中樞和輪輻網路拓撲是一種在共用一般服務時隔離工作負載的方式。 這些服務包括身分識別與安全性。 中樞是一個 VNet，可當作與內部部署網路的連線中心點。 「輪輻」是與中樞對等的 VNet。 共用的服務會部署在中樞中，個別工作負載則會部署在輪輻網路中。 以下是中樞與輪輻網路拓撲的幾個優點：

- **節省成本**：將可由多個工作負載共用的服務集中在單一位置。 這些工作負載包括網路虛擬設備和 DNS 伺服器。
- **克服訂用帳戶限制**：將不同訂用帳戶中的 VNet 對等互連到中心中樞。
- **關注點分離**：中央 IT (SecOps、InfraOps) 和工作負載 (DevOps)。

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>中樞和輪輻架構的一般用途

中樞和輪輻架構的部分一般用途包括：

- 許多客戶會在不同的環境中部署工作負載。 這些環境包括開發、測試與生產。 這些工作負載常常需要共用像是 DNS、IDS、NTP 或 AD DS 這樣的服務。 這些共用的服務可以放在中樞 VNet 中。 這會將每個環境部署至輪輻以維持隔離。
- 不需要彼此連線，但需要存取共用服務的工作負載。
- 需要集中控制安全性層面的企業。
- 需要個別管理每個輪輻中工作負載的企業。

## <a name="preview-the-demo-components"></a>預覽示範元件

您會在完成本系列中每個教學課程的過程中，使用不同的 Terraform 指令碼定義各種元件。 建立及部署的示範架構中包含以下元件：

- **內部部署網路**。 配合組織執行的私人區域網路。 對於中樞和輪輻參考架構，會使用 Azure 中的 VNet 模擬內部部署網路。

- **VPN 裝置**。 可對內部部署網路提供外部連線的 VPN 裝置或服務。 VPN 裝置可以是硬體設備或軟體解決方案。 

- **中樞 VNet**。 中樞是與您內部部署網路的連線中心點，以及裝載服務的位置。 這些服務可供裝載在輪輻 VNet 中的不同工作負載取用。

- **閘道子網路**。 VNet 閘道會保留在相同的子網路中。

- **輪輻 VNet**。 輪輻可在自己的 VNet 中，用來隔離從其他輪輻分開管理的工作負載。 每個工作負載在透過 Azure 負載平衡器連線多個子網路的情況下，都可能包括多層。 

- **VNet 對等互連**。 使用對等連線可以連線兩個 VNet。 對等連線是 VNet 之間不可轉移的低延遲連線。 一旦對等互連，VNet 就會使用 Azure 骨幹交換流量，而不需要使用路由器。 在中樞和輪輻網路拓撲中，中樞連線至每個輪輻都是使用 VNet 對等互連。 您可以將相同區域或不同區域中的 VNet 對等互連。

## <a name="create-the-directory-structure"></a>建立目錄結構

建立目錄，用來存放供示範使用的 Terraform 組態檔。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。

1. 開啟 [Azure Cloud Shell](/azure/cloud-shell/overview)。 如果您先前未選取環境，請選取 **Bash** 作為您的環境。

    ![Cloud Shell 提示](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. 切換至 `clouddrive` 目錄。

    ```bash
    cd clouddrive
    ```

1. 建立名為 `hub-spoke` 的目錄。

    ```bash
    mkdir hub-spoke
    ```

1. 將目錄變更為新的目錄：

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-azure-provider"></a>宣告 Azure 提供者

建立宣告 Azure 提供者的 Terraform 組態檔。

1. 在 Cloud Shell 中開啟名為 `main.tf` 的新檔案。

    ```bash
    code main.tf
    ```

1. 將下列程式碼貼到編輯器中：

    ```hcl
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. 儲存檔案並結束編輯器。

## <a name="create-the-variables-file"></a>建立變數檔案

建立跨不同指令碼所使用通用變數的 Terraform 組態檔。

1. 在 Cloud Shell 中開啟名為 `variables.tf` 的新檔案。

    ```bash
    code variables.tf
    ```

1. 將下列程式碼貼到編輯器中：

    ```hcl
    variable "location" {
      description = "Location of the network"
      default     = "centralus"
    }
    
    variable "username" {
      description = "Username for Virtual Machines"
      default     = "testadmin"
    }
    
    variable "password" {
      description = "Password for Virtual Machines"
      default     = "Password1234!"
    }
    
    variable "vmsize" {
      description = "Size of the VMs"
      default     = "Standard_DS1_v2"
    }
    ```

1. 儲存檔案並結束編輯器。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [使用 Azure 中的 Terraform 建立內部部署虛擬網路](./terraform-hub-spoke-on-prem.md)