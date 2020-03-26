---
title: 教學課程 - 使用 Terraform 在 Azure 中驗證中樞和輪輻網路
description: 驗證將所有虛擬網路連線至另一個網路的中樞和輪輻網路拓撲的教學課程。
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 9ba4780c59e5e9da4999573abbc08ecd2738a2cd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74159198"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-in-azure-using-terraform"></a>教學課程：使用 Terraform 在 Azure 中驗證中樞和輪輻網路

您會在本文中，執行於本系列之前文章中所建立的 Terraform 檔案。 結果是驗證示範虛擬網路之間的連線能力。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 使用 HCL (HashiCorp 語言) 以在中樞輪輻拓撲中實作中樞 VNet
> * 使用 Terraform 方案驗證要部署的資源
> * 使用 Terraform 套用在 Azure 中建立資源
> * 驗證不同網路之間的連線能力
> * 使用 Terraform 終結所有資源

## <a name="prerequisites"></a>Prerequisites

1. [使用 Azure 中的 Terraform 建立中樞和輪輻混合網路拓撲](./terraform-hub-spoke-introduction.md)。
1. [使用 Azure 中的 Terraform 建立內部部署虛擬網路](./terraform-hub-spoke-on-prem.md)。
1. [使用 Azure 中的 Terraform 建立中樞虛擬網路](./terraform-hub-spoke-hub-network.md)。
1. [使用 Azure 中的 Terraform 建立中樞虛擬網路設備](./terraform-hub-spoke-hub-nva.md)。
1. [使用 Azure 中的 Terraform 建立輪輻虛擬網路](./terraform-hub-spoke-spoke-network.md)。

## <a name="verify-your-configuration"></a>驗證您的設定

完成[必要條件](#prerequisites)之後，確認是否顯示適當的組態檔。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。

1. 開啟 [Azure Cloud Shell](/azure/cloud-shell/overview)。 如果您先前未選取環境，請選取 **Bash** 作為您的環境。

    ![Cloud Shell 提示](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. 切換至 `clouddrive` 目錄。

    ```bash
    cd clouddrive
    ```

1. 將目錄變更為新的目錄：

    ```bash
    cd hub-spoke
    ```

1. 執行 `ls` 命令，確認是否列出在先前教學課程中建立的組態檔 `.tf`：

    ![Terraform 示範組態檔](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>部署資源

1. 初始化 Terraform 提供者：
    
    ```bash
    terraform init
    ```
    
    !["terraform init" 命令的結果範例](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. 執行 `terraform plan` 命令，在執行之前先查看部署效果：

    ```bash
    terraform plan
    ```
    
    !["terraform plan" 命令的結果範例](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. 部署解決方案：

    ```bash
    terraform apply
    ```
    
    當系統提示您確認部署時，請輸入 `yes`。

    !["terraform apply" 命令的結果範例](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>測試中樞 Vnet 與輪輻 VNet

本節顯示如何測試從模擬的內部部署環境到中樞 VNet 的連線能力。

1. 在 Azure 入口網站中，瀏覽至 **onprem-vnet-rg** 資源群組的清單。

1. 在 **onprem-vnet-rg** 索引標籤上，選取名為 **onprem-vm** 的 VM。

1. 選取 [連接]  。

1. 將 [使用 VM 本機帳戶登入]  文字旁邊的 **ssh** 命令複製到剪貼簿。

1. 在 Linux 提示字元中執行 `ssh`，以連線至模擬的內部部署環境。 請使用 `on-prem.tf` 參數檔案中指定的密碼。

1. 執行 `ping` 命令測試與中樞 VNet 中 jumpbox VM 的連線能力：

   ```bash
   ping 10.0.0.68
   ```

1. 執行 `ping` 命令測試與每個輪輻中 jumpbox VM 的連線能力：

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. 若要結束 **onprem-vm** 虛擬機器中的 ssh 工作階段，請輸入 `exit` 並按 &lt; Enter>。

## <a name="troubleshoot-vpn-issues"></a>針對 VPN 問題進行疑難排解

如需解決 VPN 錯誤的相關資訊，請參閱[針對混合 VPN 連線進行疑難](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn)一文。

## <a name="clean-up-resources"></a>清除資源

如果不再需要本教學課程系列中建立的資源，請將它們刪除。

1. 移除此方案中宣告的資源：

    ```bash
    terraform destroy
    ```

    當系統提示您確認移除資源時，請輸入 `yes`。

1. 將目錄變更至父系目錄：

    ```bash
    cd ..
    ```

1. 刪除 `hub-scope` 目錄 (包括其所有檔案)：

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [深入了解如何使用 Azure 中的 Terraform](/azure/terraform)