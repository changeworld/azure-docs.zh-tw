---
title: 設定 Azure SSIS 整合執行時間以加入虛擬網路的教學課程
description: 瞭解如何加入 Azure SSIS 整合執行時間以加入 Azure 虛擬網路。
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: ef2bd2fa9badc7c299099b647e1f67c50e997024
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91292297"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>設定 Azure SQL Server Integration Services (SSIS) 整合執行時間 (IR) 以加入虛擬網路

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本教學課程提供使用 Azure 入口網站設定 Azure SQL Server Integration Services (SSIS) 整合執行時間 (IR) 加入虛擬網路的基本步驟。

步驟包括：

- 設定虛擬網路。
- 從 Azure Data Factory 入口網站將 Azure-SSIS IR 加入虛擬網路。

## <a name="prerequisites"></a>Prerequisites

- **AZURE SSIS 整合運行**時間。 如果您沒有 Azure SSIS 整合執行時間，請在開始之前， [在 Azure Data Factory 中布建 AZURE ssis 整合運行](tutorial-deploy-ssis-packages-azure.md) 時間。

- **使用者權限**。 建立 Azure SSIS IR 的使用者必須使用下列其中一個選項，至少在 Azure Data Factory 資源上具有[角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope)：

    - 使用內建的「網路參與者」角色。 此角色隨附 _microsoft.network /\*_ 權限，它的權限範圍大於必要的範圍。
    - 建立包含只有必要 _Microsoft.Network/virtualNetworks/\*/join/action_ 權限的自訂角色。 如果您也想要將自己 Azure-SSIS IR 的公用 IP 位址加入 Azure Resource Manager 的虛擬網路，並將其加入至的虛擬網路，請也在角色中加入 _publicIPAddresses/*/join/action_ 許可權。

- **虛擬網路**。

    - 如果您沒有虛擬網路，請 [使用 Azure 入口網站建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)。

    - 請確認虛擬網路的資源群組可建立及刪除特定 Azure 網路資源。
    
        Azure-SSIS IR 需要在與虛擬網路相同的資源群組下，建立特定的網路資源。 這些資源包括：
        - Azure 負載平衡器，名稱為* \<Guid> -azurebatch-cloudserviceloadbalancer*
        - 具有名稱 * \<Guid> -azurebatch-cloudservicenetworksecuritygroup 的網路安全性群組
        - Azure 公用 IP 位址，名稱為 -azurebatch-cloudservicepublicip
    
        當 Azure SSIS IR 啟動時，將會建立這些資源。 當 Azure-SSIS IR 停止時，就會予以刪除。 為了避免 Azure-SSIS IR 無法停止，請勿在其他資源中重複使用這些網路資源。

    - 請確認在虛擬網路所屬的資源群組/訂用帳戶上，沒有任何[鎖定的資源](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)。 如果您設定唯讀/刪除鎖定，則啟動和停止 Azure-SSIS IR 將會失敗，或停止回應。

    - 請確定您沒有 Azure 原則指派可防止在虛擬網路所屬的資源群組/訂用帳戶下建立下列資源：
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- 本教學課程未涵蓋以下 **網路** 設定案例：
    - 如果您為 Azure-SSIS IR 帶入自己的公用 IP 位址。
    - 如果您使用自己的網域名稱系統 (DNS) server。
    - 如果您使用網路安全性群組 (在子網上) NSG。
    - 如果您使用 Azure ExpressRoute 或使用者定義的路由 (UDR) 。
    - 如果您使用自訂的 Azure-SSIS IR。
    
    如需詳細資訊，請檢查 [虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)設定。

## <a name="configure-a-virtual-network"></a>設定虛擬網路

您可以使用 Azure 入口網站來設定虛擬網路，然後再嘗試將 Azure-SSIS IR 加入其中。

1. 啟動 Microsoft Edge 或 Google Chrome。 目前只有這些網頁瀏覽器支援 Data Factory 的 UI。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [ **更多服務**]。 篩選並選取 [虛擬網路]****。

1. 篩選並選取清單中的 [虛擬網路]。

1. 在 [虛擬網路]**** 頁面上，選取 [屬性]****。

1. 選取 [資源識別碼]**** 的 [複製] 按鈕，將虛擬網路的資源識別碼複製到剪貼簿。 將剪貼簿中的識別碼儲存至 OneNote 或檔案中。

1. 在左側功能表上選取 [ **子網**]。

    - 確定您選取的子網具有足夠的可用位址空間，以供 Azure-SSIS IR 使用。 將可用的 IP 位址保留至少為 IR 節點編號的兩倍。 Azure 會在每個子網路中保留一些 IP 位址。 無法使用這些位址。 子網的第一個和最後一個 IP 位址會保留給通訊協定一致性，而另外還有三個位址用於 Azure 服務。 如需詳細資訊，請參閱 [在這些子網路內使用 IP 位址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - 請勿選取要部署 Azure-SSIS IR 的 GatewaySubnet。 專用於虛擬網路閘道。
    - 請勿使用由其他 Azure 服務所獨佔的子網 (例如，SQL Database SQL 受控執行個體、App Service 等) 。

1. 確認已在具有虛擬網路的 Azure 訂用帳戶中註冊 Azure Batch 提供者。 或註冊 Azure Batch 提供者。 如果您的訂用帳戶中已經有 Azure Batch 帳戶，您的訂用帳戶會註冊 Azure Batch。 (若在 Data Factory 入口網站中建立 Azure-SSIS IR，便會自動註冊 Azure Batch 提供者)。

   1. 在 [Azure 入口網站中，選取左側功能表上的 [ **訂閱**]。

   1. 選取您的訂用帳戶。

   1. 選取左側的 [ **資源提供者**]，然後確認 **Microsoft.Batch** 是已註冊的提供者。

   ![確認「已註冊」狀態](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果您在清單中未看到 **Microsoft.Batch**，若要進行註冊，請在訂用帳戶中[建立空白 Azure Batch 帳戶](../batch/batch-account-create-portal.md)。 您可以稍後刪除它。

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>將 Azure-SSIS IR 加入虛擬網路

設定 Azure Resource Manager 虛擬網路或傳統虛擬網路之後，您可以將 Azure-SSIS IR 加入虛擬網路：

1. 啟動 Microsoft Edge 或 Google Chrome。 目前只有這些網頁瀏覽器支援 Data Factory 的 UI。

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，選取左側功能表上的 [ **Data**factory]。 如果您在功能表上沒有看到 [ **Data** factory]，請選取 [ **更多服務**]，然後在 [ **智慧 + 分析** ] 區段中，選取 [ **data**factory]。

   ![資料處理站清單](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 使用清單中的 Azure-SSIS IR 選取您的 data factory。 您會看到資料處理站的首頁。 選取 [建立者和監視] 圖格。 您會在個別的索引標籤上看到 Data Factory UI。

   ![Data Factory 首頁](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. 在 Data Factory UI 中，切換至 [編輯]**** 索引標籤，選取 [連線]****，然後切換至 [整合執行階段]**** 索引標籤。

   ![[整合執行階段] 索引標籤](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. 如果您的 Azure-SSIS IR 正在執行，請在 [ **整合運行** 時間] 清單的 [ **動作** ] 欄中，為您的 Azure-SSIS IR 選取 [ **停止** ] 按鈕。 您無法編輯 Azure-SSIS IR，除非您將它停止。

   ![停止 IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. 在 [ **整合運行** 時間] 清單的 [ **動作** ] 欄中，選取您 Azure-SSIS IR 的 [ **編輯** ] 按鈕。

   ![編輯整合執行階段](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. 在 [整合執行時間設定] 面板上，選取 [**下一步]** 按鈕，以前進到 [**一般設定**] 和 [ **SQL 設定**] 區段。

1. 在 [ **Advanced Settings** ] 區段上：
   1. 選取 [ **選取要加入 Azure-SSIS Integration Runtime 的 VNet，讓 ADF 建立特定的網路資源，並選擇性地攜帶您自己的靜態公用 IP 位址** ] 核取方塊。

   1. 針對 [訂用帳戶]****，選取具有您的虛擬網路的 Azure 訂用帳戶。

   1. 針對 [位置]****，選取整合執行階段的相同位置。

   1. 在 [ **類型**] 中，選取虛擬網路的類型： [傳統] 或 [Azure Resource Manager]。 建議您選取 Azure Resource Manager 的虛擬網路，因為傳統虛擬網路很快就會淘汰。

   1. 針對 **[VNet 名稱]**，選取您虛擬網路的名稱。 它應該與虛擬網路服務端點所使用的相同，或是搭配私人端點來裝載 SSISDB 的 SQL 受控執行個體 SQL Database。 也應該與您的內部部署網路相同。 否則，它可以是任何虛擬網路，以將您自己的靜態公用 IP 位址用於 Azure-SSIS IR。

   1. 針對 [子網路名稱]****，選取虛擬網路的子網路名稱。 它應該與用於具有虛擬網路服務端點的 SQL 資料庫相同，以裝載 SSISDB。 或者，它應該是與您的 SQL 受控執行個體搭配私人端點來裝載 SSISDB 的不同子網。 否則，它可以是任何子網，以將您自己的靜態公用 IP 位址用於 Azure-SSIS IR。

   1. 選取 [ **VNet 驗證**]。 如果驗證成功，請選取 [ **繼續**]。

   ![虛擬網路進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. 在 [ **摘要** ] 區段上，檢查您 Azure-SSIS IR 的所有設定。 然後選取 [ **更新**]。

1. 在 Azure-SSIS IR 的 [**動作**] 欄中選取 [**開始**] 按鈕，以啟動您的 Azure-SSIS IR。 需要大約20到30分鐘的時間，才能啟動聯結虛擬網路的 Azure-SSIS IR。

## <a name="next-steps"></a>後續步驟

深入瞭解 [如何將 Azure-SSIS IR 加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)。
