---
title: 配置 Azure-SSIS 集成運行時以加入虛擬網路的教程
description: 瞭解如何將 Azure-SSIS 集成運行時加入 Azure 虛擬網路。
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
ms.openlocfilehash: 065610a9de4898d012cef8a16849c09a81f0774c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841092"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>配置 Azure-SQL 伺服器整合服務 （SSIS） 集成運行時 （IR） 以加入虛擬網路

本教程提供了使用 Azure 門戶配置 Azure SQL 伺服器整合服務 （SSIS） 集成運行時 （IR） 以加入虛擬網路的基本步驟。

步驟包括：

- 配置虛擬網路。
- 從 Azure 資料工廠門戶將 Azure-SSIS IR 加入虛擬網路。

## <a name="prerequisites"></a>Prerequisites

- **Azure-SSIS 集成運行時**。 如果沒有 Azure-SSIS 集成運行時，則在開始之前[在 Azure 資料工廠預配 Azure-SSIS 集成運行時](tutorial-deploy-ssis-packages-azure.md)。

- **使用者許可權**。 創建 Azure-SSIS IR 的使用者必須在 Azure 資料工廠資源上至少具有[角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope)，並具有以下選項之一：

    - 使用內建的「網路參與者」角色。 此角色隨附 _microsoft.network /\*_ 權限，它的權限範圍大於必要的範圍。
    - 建立包含只有必要 _Microsoft.Network/virtualNetworks/\*/join/action_ 權限的自訂角色。 如果還需要在將其加入 Azure 資源管理器虛擬網路時為 Azure-SSIS IR 引入自己的公共 IP 位址，請在角色中包括_Microsoft.Network/publicIP 位址/*/聯接/操作_許可權。

- **虛擬網路**。

    - 如果沒有虛擬網路，[請使用 Azure 門戶創建虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)。

    - 確保虛擬網路的資源組可以創建和刪除某些 Azure 網路資源。
    
        Azure-SSIS IR 需要在與虛擬網路相同的資源群組下，建立特定的網路資源。 這些資源包括：
        - Azure 負載等化器，名稱為*\<Guid>-azure 批次處理-雲服務負載等化器*
        - 一個網路安全性群組，名稱為 guid\<>-azure 批次處理-雲服務網路安全性群組
        - Azure 公共 IP 位址，名稱為 -azurebatch-雲服務公共
    
        這些資源將在 Azure-SIS IR 啟動時創建。 當 Azure-SSIS IR 停止時，它們將被刪除。 為了避免阻止 Azure-SSIS IR 停止，請不要在其他資源中重用這些網路資源。

    - 確保虛擬網路所屬的資源組/訂閱沒有[資源鎖定](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)。 如果配置唯讀/刪除鎖，則啟動和停止 Azure-SSIS IR 將失敗，否則將停止回應。

    - 確保沒有 Azure 策略阻止在虛擬網路所屬的資源組/訂閱下創建以下資源：
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- 本教程中未介紹以下**網路設定**方案：
    - 如果為 Azure-SSIS IR 自帶公共 IP 位址。
    - 如果您使用自己的網域名稱系統 （DNS） 伺服器。
    - 如果在子網上使用網路安全性群組 （NSG）。
    - 如果使用 Azure 快速路由或使用者定義的路由 （UDR）。
    - 如果使用自訂的 Azure SSIS IR。
    
    有關詳細資訊，請查看[虛擬網路配置](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)。

## <a name="configure-a-virtual-network"></a>設定虛擬網路

在嘗試將 Azure-SSIS IR 加入虛擬網路之前，請使用 Azure 門戶配置虛擬網路。

1. 啟動 Microsoft Edge 或 Google Chrome。 目前，只有這些 Web 瀏覽器支援資料工廠 UI。

1. 登錄到 Azure[門戶](https://portal.azure.com)。

1. 選擇**更多服務**。 篩選並選取 [虛擬網路]****。

1. 篩選並選取清單中的 [虛擬網路]。

1. 在 [虛擬網路]**** 頁面上，選取 [屬性]****。

1. 選取 [資源識別碼]**** 的 [複製] 按鈕，將虛擬網路的資源識別碼複製到剪貼簿。 將剪貼簿中的識別碼儲存至 OneNote 或檔案中。

1. 在左側功能表上，選擇**子網**。

    - 確保您選擇的子網有足夠的可用位址空間供 Azure-SSIS IR 使用。 將可用的 IP 位址保留至少兩倍于 IR 節點編號。 Azure 會在每個子網路中保留一些 IP 位址。 不能使用這些位址。 子網的第一個和最後一個 IP 位址保留用於協定一致性，另外三個位址用於 Azure 服務。 如需詳細資訊，請參閱 [在這些子網路內使用 IP 位址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - 不要選擇閘道子網來部署 Azure-SSIS IR。 它專用於虛擬網路閘道。
    - 不要使用由其他 Azure 服務（例如，SQL 資料庫託管實例、應用服務等）獨佔的子網。

1. 確認已在具有虛擬網路的 Azure 訂用帳戶中註冊 Azure Batch 提供者。 或註冊 Azure 批次處理提供程式。 如果訂閱中已有 Azure 批次處理帳戶，則訂閱將註冊為 Azure 批次處理。 (若在 Data Factory 入口網站中建立 Azure-SSIS IR，便會自動註冊 Azure Batch 提供者)。

   1. 在 Azure 門戶中，在左側功能表中，選擇 **"訂閱**"。

   1. 選取您的訂用帳戶。

   1. 在左側，選擇**資來源提供者**，並確認**Microsoft.Batch**是註冊提供程式。

   ![確認「已註冊」狀態](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果您在清單中未看到 **Microsoft.Batch**，若要進行註冊，請在訂用帳戶中[建立空白 Azure Batch 帳戶](../batch/batch-account-create-portal.md)。 您可以稍後刪除它。

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>將 Azure-SSIS IR 加入虛擬網路

配置 Azure 資源管理器虛擬網路或經典虛擬網路後，可以將 Azure-SSIS IR 加入虛擬網路：

1. 啟動 Microsoft Edge 或 Google Chrome。 目前，只有這些 Web 瀏覽器支援資料工廠 UI。

1. 在[Azure 門戶](https://portal.azure.com)中，在左側功能表中，選擇 **"資料工廠**"。 如果在功能表上看不到**資料工廠**，請選擇 **"更多服務**"，然後在 **"智慧 + 分析**"部分中選擇 **"資料工廠**"。

   ![資料處理站清單](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 在清單中使用 Azure-SSIS IR 選擇資料工廠。 您會看到資料處理站的首頁。 選擇 **"作者&監視器磁貼**。 您會在個別的索引標籤上看到 Data Factory UI。

   ![Data Factory 首頁](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. 在 Data Factory UI 中，切換至 [編輯]**** 索引標籤，選取 [連線]****，然後切換至 [整合執行階段]**** 索引標籤。

   ![[整合執行階段] 索引標籤](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. 如果 Azure-SSIS IR 正在運行，請在"**操作"** 列中的 **"集成運行時**"清單中運行，選擇 Azure-SSIS IR 的 **"停止"** 按鈕。 在停止 Azure-SSIS IR 之前，無法對其進行編輯。

   ![停止 IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. 在 **"集成運行時"** 清單中，在 **"操作"** 列中，為 Azure-SSIS IR 選擇 **"編輯"** 按鈕。

   ![編輯整合執行階段](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. 在集成運行時設置面板上，通過選擇"**下一步**"按鈕，提前完成 **"常規設置**"和 **"SQL 設置"** 部分。

1. 在 **"高級設置"** 部分：
   1. 選擇**Azure-SSIS 集成運行時的 VNet 以加入，允許 ADF 創建某些網路資源，並選擇攜帶自己的靜態公共 IP 位址**核取方塊。

   1. 針對 [訂用帳戶]****，選取具有您的虛擬網路的 Azure 訂用帳戶。

   1. 針對 [位置]****，選取整合執行階段的相同位置。

   1. 對於**類型**，請選擇虛擬網路的類型：經典或 Azure 資源管理器。 我們建議您選擇 Azure 資源管理器虛擬網路，因為經典虛擬網路將很快被棄用。

   1. 針對 **[VNet 名稱]**，選取您虛擬網路的名稱。 它應該與 Azure SQL 資料庫伺服器使用的相同，該伺服器具有虛擬網路服務終結點，或者使用專用終結點託管 SSISDB 的託管實例。 或者它應該是連接到本地網路的同一個。 否則，任何虛擬網路都可以為 Azure-SSIS IR 帶來自己的靜態公共 IP 位址。

   1. 針對 [子網路名稱]****，選取虛擬網路的子網路名稱。 它應該與 Azure SQL 資料庫伺服器所用的相同，該伺服器具有虛擬網路服務終結點來承載 SSISDB。 或者，它應該是一個不同的子網，與用於託管實例的子網，具有專用終結點來承載 SSISDB。 否則，它可以是任何子網，為 Azure-SSIS IR 自帶靜態公共 IP 位址。

   1. 選擇**VNet 驗證**。 如果驗證成功，請選擇"**繼續**"。

   ![虛擬網路進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. 在 **"摘要"** 部分，查看 Azure-SSIS IR 的所有設置。 然後選擇 **"更新**"。

1. 通過選擇 Azure-SSIS IR 的"**操作"** 列中的 **"開始"** 按鈕來啟動 Azure-SSIS IR。 啟動加入虛擬網路的 Azure-SSIS IR 大約需要 20 到 30 分鐘。

## <a name="next-steps"></a>後續步驟

瞭解有關將[Azure-SSIS IR 加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)的更多資訊。
