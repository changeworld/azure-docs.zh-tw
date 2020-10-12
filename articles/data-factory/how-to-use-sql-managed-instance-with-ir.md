---
title: '在 Azure Data Factory 中使用 Azure SQL 受控執行個體搭配 Azure SQL Server Integration Services (SSIS) '
description: 瞭解如何搭配使用 Azure SQL 受控執行個體與 Azure Data Factory 中的 SQL Server Integration Services (SSIS) 。
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/15/2020
ms.openlocfilehash: 2bdfdd31e2cc9bc964abc040d0631c4760fca283
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984867"
---
# <a name="use-azure-sql-managed-instance-with-sql-server-integration-services-ssis-in-azure-data-factory"></a>在 Azure Data Factory 中搭配使用 Azure SQL 受控執行個體與 SQL Server Integration Services (SSIS) 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

您現在可以將 SQL Server Integration Services (SSIS) 專案、套件和工作負載移至 Azure 雲端。 使用 SQL Server Management Studio (SSMS) 等熟悉的工具，在 Azure SQL Database 或 SQL 受控執行個體上部署、執行及管理 SSIS 專案和套件。 本文著重在使用 azure SQL 受控執行個體搭配 Azure SSIS 整合執行時間 (IR) 時的下列特定區域：

- [使用 SSIS 目錄將 Azure-SSIS IR 布建 (由 Azure SQL 託管的 SSISDB) 受控執行個體](#provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance)
- [透過 Azure SQL 受控執行個體代理程式作業執行 SSIS 套件](how-to-invoke-ssis-package-managed-instance-agent.md)
- [透過 Azure SQL 受控執行個體代理程式作業清除 SSISDB 記錄](#clean-up-ssisdb-logs)
- [使用 Azure SQL 受控執行個體 Azure-SSIS IR 容錯移轉](configure-bcdr-azure-ssis-integration-runtime.md#azure-ssis-ir-failover-with-a-sql-managed-instance)
- [使用 Azure SQL 受控執行個體將內部部署 SSIS 工作負載遷移至 ADF 中的 SSIS 作為資料庫工作負載目的地](scenario-ssis-migration-overview.md#azure-sql-managed-instance-as-database-workload-destination)

## <a name="provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance"></a>使用 Azure SQL 受控執行個體所裝載的 SSISDB 布建 Azure-SSIS IR

### <a name="prerequisites"></a>Prerequisites

1. 選擇受控執行個體驗證時，請[啟用 AZURE SQL Azure Active Directory 上的 Azure Active Directory (Azure AD) ](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance)。

1. 選擇如何透過私人端點或透過公用端點連接 SQL 受控執行個體：

    - 透過私人端點 (偏好選項)

        1. 選擇 Azure-SSIS IR 要加入的虛擬網路：
            - 在與受控實例相同的虛擬網路內，具有 **不同的子網**。
            - 在與受控實例不同的虛擬網路中，透過虛擬網路對等互連 (，因為全域 VNet 對等互連限制) 或從虛擬網路連線到虛擬網路，所以受限於相同區域。

            如需 SQL 受控執行個體連接的詳細資訊，請參閱 [將您的應用程式連線到 AZURE SQL 受控執行個體](https://review.docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connect-app)。

        1. [設定虛擬網路](#configure-virtual-network)。

    - 透過公用端點

        Azure SQL 受控實例可透過 [公用端點](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)提供連線能力。 輸入和輸出需求必須符合，才能允許 SQL 受控執行個體和 Azure-SSIS IR 之間的流量：

        - 當 Azure-SSIS IR 不在虛擬網路內時 (偏好選項)

            **SQL 受控執行個體的輸入需求**，以允許來自 Azure-SSIS IR 的輸入流量。

            | 傳輸通訊協定 | 來源 | 來源連接埠範圍 | Destination | 目的地連接埠範圍 |
            |---|---|---|---|---|
            |TCP|Azure 雲端服務標記|*|VirtualNetwork|3342|

            如需詳細資訊，請參閱[允許網路安全性群組上的公用端點流量](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure#allow-public-endpoint-traffic-on-the-network-security-group) (英文)。

        - 當 Azure-SSIS IR 在虛擬網路內時

            當 SQL 受控執行個體位於 Azure-SSIS IR 不支援的區域中時，有一個特殊情況，因為全域 VNet 對等互連限制，所以 Azure-SSIS IR 位於沒有 VNet 對等互連的虛擬網路內。 在此案例中， **虛擬網路內的 Azure-SSIS IR** 會透過 **公用端點**連接 SQL 受控執行個體。 使用以下網路安全性群組 (NSG) 規則，以允許 SQL 受控執行個體和 Azure-SSIS IR 之間的流量：

            1. **SQL 受控執行個體的輸入需求**，以允許來自 Azure-SSIS IR 的輸入流量。

                | 傳輸通訊協定 | 來源 | 來源連接埠範圍 | Destination |目的地連接埠範圍 |
                |---|---|---|---|---|
                |TCP|Azure-SSIS IR 的靜態 IP 位址 <br> 如需詳細資料，請參閱[針對 Azure-SSIS IR 使用自己的公用 IP](join-azure-ssis-integration-runtime-virtual-network.md#publicIP)。|*|VirtualNetwork|3342|

             1. **Azure-SSIS IR 的輸出需求**，以允許對 SQL 受控執行個體的輸出流量。

                | 傳輸通訊協定 | 來源 | 來源連接埠範圍 | Destination |目的地連接埠範圍 |
                |---|---|---|---|---|
                |TCP|VirtualNetwork|*|[SQL 受控執行個體公用端點 IP 位址](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-find-management-endpoint-ip-address)|3342|

### <a name="configure-virtual-network"></a>設定虛擬網路

1. **使用者權限**。 建立 Azure SSIS IR 的使用者必須使用下列其中一個選項，至少在 Azure Data Factory 資源上具有[角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope)：

    - 使用內建的「網路參與者」角色。 此角色隨附 _microsoft.network /\*_ 權限，它的權限範圍大於必要的範圍。
    - 建立包含只有必要 _Microsoft.Network/virtualNetworks/\*/join/action_ 權限的自訂角色。 如果您也想要針對 Azure-SSIS IR 使用自己的公用 IP，並同時將其加入 Azure Resource Manager 虛擬網路，也請在角色中包含 _Microsoft.Network/publicIPAddresses/*/join/action_ 權限。

1. **虛擬網路**。

    1. 請確認虛擬網路的資源群組可建立及刪除特定 Azure 網路資源。

        Azure-SSIS IR 需要在與虛擬網路相同的資源群組下，建立特定的網路資源。 這些資源包括：
        - Azure 負載平衡器，名稱為* \<Guid> -azurebatch-cloudserviceloadbalancer*
        - 具有名稱 * \<Guid> -azurebatch-cloudservicenetworksecuritygroup 的網路安全性群組
        - Azure 公用 IP 位址，名稱為 -azurebatch-cloudservicepublicip

        當 Azure SSIS IR 啟動時，將會建立這些資源。 當 Azure-SSIS IR 停止時，就會予以刪除。 為了避免 Azure-SSIS IR 無法停止，請勿在其他資源中重複使用這些網路資源。

    1. 請確認在虛擬網路所屬的資源群組/訂用帳戶上，沒有任何[鎖定的資源](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)。 如果您設定唯讀/刪除鎖定，則啟動和停止 Azure-SSIS IR 將會失敗，或停止回應。

    1. 請確認沒有任何 Azure 原則會禁止在虛擬網路所屬資源群組/訂用帳戶之下建立下列資源：
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

    1. 允許網路安全性群組上的流量 (NSG) 規則，以允許 SQL 受控執行個體和 Azure-SSIS IR 之間的流量，以及 Azure-SSIS IR 所需的流量。
        1. **SQL 受控執行個體的輸入需求**，以允許來自 Azure-SSIS IR 的輸入流量。

            | 傳輸通訊協定 | 來源 | 來源連接埠範圍 | Destination | 目的地連接埠範圍 | 註解 |
            |---|---|---|---|---|---|
            |TCP|VirtualNetwork|*|VirtualNetwork|1433、11000-11999|如果 SQL Database 伺服器連線原則設定為 [Proxy] 而不是 [重新導向]，則只需要連接埠 1433。|

        1. **Azure-SSIS IR 的輸出需求**，以允許對 SQL 受控執行個體的輸出流量，以及 Azure-SSIS IR 所需的其他流量。

        | 傳輸通訊協定 | 來源 | 來源連接埠範圍 | Destination | 目的地連接埠範圍 | 註解 |
        |---|---|---|---|---|---|
        | TCP | VirtualNetwork | * | VirtualNetwork | 1433、11000-11999 |允許對 SQL 受控執行個體的輸出流量。 如果連線原則設定為 [Proxy] 而不是 [重新導向]，則只需要連接埠 1433。 |
        | TCP | VirtualNetwork | * | AzureCloud | 443 | 虛擬網路中的 Azure-SSIS IR 節點會使用此連接埠來存取 Azure 服務，例如 Azure 儲存體和 Azure 事件中樞。 |
        | TCP | VirtualNetwork | * | Internet | 80 | (選擇性) 虛擬網路中的 Azure-SSIS IR 節點會使用此連接埠從網際網路下載憑證撤銷清單。 如果您封鎖此流量，則可能會在啟動 IR 時遇到效能降低的情況，且無法檢查憑證撤銷清單中是否有憑證使用情況。 如果您想要進一步將目的地縮小到特定 FQDN，請參閱[使用 Azure ExpressRoute 或使用者定義的路由 (UDR)](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network#route) (機器翻譯)。|
        | TCP | VirtualNetwork | * | 儲存體 | 445 | (選擇性) 只有當想要執行儲存在 Azure 檔案儲存體中的 SSIS 套件時，才需要此規則。 |
        |||||||

        1. **Azure-SSIS IR 的輸入需求**，以允許 Azure-SSIS IR 所需的流量。

        | 傳輸通訊協定 | 來源 | 來源連接埠範圍 | Destination | 目的地連接埠範圍 | 註解 |
        |---|---|---|---|---|---|
        | TCP | BatchNodeManagement | * | VirtualNetwork | 29876、29877 (如果您將 IR 加入 Resource Manager 虛擬網路) <br/><br/>10100、20100、30100 (如果您將 IR 加入傳統虛擬網路)| Data Factory 服務會使用這些連接埠與虛擬網路中的 Azure-SSIS IR 節點進行通訊。 <br/><br/> 無論您是否建立子網路層級的 NSG，Data Factory 一律會在連結至 Azure-SSIS IR 主控虛擬機器的網路介面卡 (NIC) 層級上設定 NSG。 該 NIC 層級的 NSG 僅允許來自指定連接埠上 Data Factory IP 位址的輸入流量。 即使您在子網路層級上對網際網路流量開啟這些連接埠，只要流量不是來自 Data Factory 的 IP 位址，就會在 NIC 層級上遭到封鎖。 |
        | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | (選擇性) 只有在 Microsoft 支援人員要求客戶開啟以進行進階疑難排解時才需要此規則，並可在疑難排解之後立即關閉。 **CorpNetSaw** 服務標記只允許 Microsoft 公司網路上的安全存取工作站使用遠端桌面。 而且此服務標記無法從入口網站選取，只能透過 Azure PowerShell 或 Azure CLI 使用。 <br/><br/> 在 NIC 層級的 NSG 預設會開啟連接埠 3389，且可供控制子網層級 NSG 的連接埠 3389，同時 Azure-SSIS IR 預設會在每個 IR 節點上使用 Windows 防火牆規則來禁止連接埠 3389 輸出，以提供保護。 |
        |||||||

    1. 如需下列情況的詳細資訊，請參閱[虛擬網路設定](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration) (機器翻譯)：
        - 如果針對 Azure-SSIS IR 使用自己的公用 IP 位址
        - 如果使用自己的網域名稱系統 (DNS) 伺服器
        - 如果使用 Azure ExpressRoute 或使用者定義的路由 (UDR)
        - 如果使用自訂的 Azure-SSIS IR

### <a name="provision-azure-ssis-integration-runtime"></a>佈建 Azure-SSIS Integration Runtime

1. 選取 SQL 受控執行個體私人端點或公用端點。

    在 Azure 入口網站/ADF 應用程式中布建 [Azure-SSIS IR](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime) 時，請在 [sql 設定] 頁面上，使用 sql 受控執行個體 **私人端點** 或 **公用端點** 建立 SSIS 目錄 (SSISDB) 。

    公用端點主機名稱的格式為 <MI 名稱>.public.<DNZ 區域>.database.windows.net，而用於連線的連接埠為 3342。  

    ![螢幕擷取畫面顯示已選取 [建立我的目錄] 和輸入目錄資料庫伺服器端點的整合執行時間設定。](./media/how-to-use-sql-managed-instance-with-ir/catalog-public-endpoint.png)

1. 選取 Azure AD 驗證 (如果適用的話)。

    ![catalog-public-endpoint](./media/how-to-use-sql-managed-instance-with-ir/catalog-aad.png)

    如需如何啟用 Azure AD authentication 的詳細資訊，請參閱 [在 AZURE SQL 受控執行個體上啟用 Azure AD](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance)。

1. 將 Azure-SSIS IR 加入虛擬網路 (如果適用的話)。

    在進階設定頁面上，選取要加入的虛擬網路和子網路。
    
    當與 SQL 受控執行個體位於相同的虛擬網路內時，請選擇與 SQL 受控執行個體 **不同的子網** 。 

    如需如何將 Azure-SSIS IR 加入虛擬網路的詳細資訊，請參閱[將 Azure-SSIS Integration Runtime 加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)。

    ![螢幕擷取畫面顯示整合執行時間安裝程式的 [Advanced] 設定，您可以在其中選取要加入執行時間的虛擬網路。](./media/how-to-use-sql-managed-instance-with-ir/join-virtual-network.png)

如需關於如何建立 Azure-SSIS IR 的詳細資訊，請參閱[在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime)。

## <a name="clean-up-ssisdb-logs"></a>清除 SSISDB 記錄

SSISDB 記錄保留原則是由 [catalog.catalog_properties](https://docs.microsoft.com/sql/integration-services/system-views/catalog-catalog-properties-ssisdb-database?view=sql-server-ver15) 中的下列屬性所定義：

- OPERATION_CLEANUP_ENABLED

    當值為 TRUE 時，會從目錄中刪除早於 RETENTION_WINDOW (天) 的作業詳細資料和作業訊息。 當值為 FALSE 時，所有作業詳細資料和作業訊息都會儲存在目錄中。 注意：SQL Server 作業 (Job) 會執行作業 (Operation) 清除。

- RETENTION_WINDOW

    作業詳細資訊和作業訊息儲存在目錄中的天數。 當值為 -1 時，保留週期為無限。 注意:如果不想要清除，請將 OPERATION_CLEANUP_ENABLED 設定為 FALSE。

若要將保留週期 (由系統管理員設定) 以外的 SSISDB 記錄移除，您可觸發預存程序 `[internal].[cleanup_server_retention_window_exclusive]`。 （選擇性）您可以排程 SQL 受控執行個體 agent 作業執行，以觸發預存程式。

## <a name="next-steps"></a>後續步驟

- [透過 Azure SQL 受控執行個體代理程式作業執行 SSIS 套件](how-to-invoke-ssis-package-managed-instance-agent.md)
- [設定商務持續性和災害復原 (BCDR)](configure-bcdr-azure-ssis-integration-runtime.md)
- [將內部部署 SSIS 工作負載移轉到 ADF 中的 SSIS](scenario-ssis-migration-overview.md)
