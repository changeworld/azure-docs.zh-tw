---
title: 教學課程：將 SQL Server 離線遷移至 SQL 單一資料庫
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure 資料庫移轉服務，在離線狀態下從 SQL Server 遷移至 Azure SQL Database。
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 1027b4f37160281bcf298e57e890b73b472526a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91308753"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-offline-using-dms"></a>教學課程：使用 DMS 在離線狀態下將 SQL Server 移轉至 Azure SQL Database

您可以使用 Azure 資料庫移轉服務，將資料庫從 SQL Server 執行個體遷移至 [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/)。 在本教學課程中，您會使用 Azure 資料庫移轉服務，將已還原至 SQL Server 2016 (或更新版本) 內部部署執行個體的 **Adventureworks2012** 資料庫遷移至 Azure SQL Database 中的單一資料庫或集區資料庫。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
>
> - 使用 Data Migration Assistant 評估您的內部部署資料庫。
> - 使用 Data Migration Assistant 移轉範例結構描述。
> - 建立 Azure 資料庫移轉服務的執行個體。
> - 使用 Azure 資料庫移轉服務來建立移轉專案。
> - 執行移轉。
> - 監視移轉。
> - 下載移轉報告。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文將說明如何在離線狀態下從 SQL Server 移轉至 Azure SQL Database 中的資料庫。 如需有關線上移轉的資訊，請參閱[使用 DMS 在線上將 SQL Server 移轉至 Azure SQL Database](tutorial-sql-server-azure-sql-online.md)。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

- 下載並安裝 [SQL Server 2016 或更新版本](https://www.microsoft.com/sql-server/sql-server-downloads)。
- 啟用 TCP/IP 通訊協定，在 SQL Server Express 安裝期間預設會停用，方法是遵循[啟用或停用伺服器網路通訊協定](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文中的指示。
- 依照[使用 Azure 入口網站在 Azure SQL Database 中建立資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started)一文中的詳細資料，在 Azure SQL Database 中建立資料庫。

    > [!NOTE]
    > 如果您使用 SQL Server Integration Services (SSIS)，而且想要將 SSIS 專案/套件 (SSISDB) 的目錄資料庫從 SQL Server 遷移到 Azure SQL Database，當您在 Azure Data Factory (ADF) 中佈建 SSIS 時，系統會自動代替您建立及管理目的地 SSISDB。 如需有關遷移 SSIS 套件的詳細資訊，請參閱[將 SQL Server Integration Services 套件遷移到 Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) 一文。
  
- 下載及安裝[資料移轉小幫手](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 或更新版本。
- 使用 Azure Resource Manager 部署模型建立 Azure 資料庫移轉服務的 Microsoft Azure 虛擬網路，以使用 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 為您的內部部署來源伺服器提供站對站連線能力。 如需建立虛擬網路的詳細資訊，請參閱[虛擬網路文件](https://docs.microsoft.com/azure/virtual-network/)，特別是快速入門文章，裡面會提供逐步操作詳細資料。

    > [!NOTE]
    > 在虛擬網路設定期間，如果您使用 ExpressRoute 搭配與 Microsoft 對等互連的網路，請將下列服務[端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)新增至將佈建服務的子網路：
    >
    > - 目標資料庫端點 (例如，SQL 端點、Cosmos DB 端點等)
    > - 儲存體端點
    > - 服務匯流排端點
    >
    > 此為必要設定，因為 Azure 資料庫移轉服務沒有網際網路連線。
    >
    >如果您沒有內部部署網路與 Azure 之間的站對站連線，或是您的站對站連線頻寬有限，請考慮在混合模式 (預覽) 中使用 Azure 資料庫移轉服務。 混合模式會搭配使用內部部署移轉背景工作角色與雲端中執行的 Azure 資料庫移轉服務執行個體。 若要在混合模式中建立 Azure 資料庫移轉服務的執行個體，請參閱[使用 Azure 入口網站在混合模式中建立 Azure 資料庫移轉服務執行個體](https://aka.ms/dms-hybrid-create)一文。

- 確定您的虛擬網路網路安全性群組規則不會對 Azure 資料庫移轉服務封鎖下列輸入通訊埠：443、53、9354、445、12000。 如需 Azure 虛擬網路 NSG 流量篩選的詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)。
- 設定[用於 Database Engine 存取的 Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
- 開啟您的 Windows 防火牆以允許 Azure 資料庫移轉服務存取來源 SQL Server (依預設會使用 TCP 連接埠 1433)。
- 如果您使用動態連接埠執行多個具名 SQL Server 執行個體，您可以啟用 SQL Browser 服務並允許通過防火牆存取 UDP 連接埠 1434，讓 Azure 資料庫移轉服務連線來源伺服器上的具名執行個體。
- 使用來源資料庫前面的防火牆應用裝置時，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取來源資料庫，以進行移轉。
- 為 Azure SQL Database 建立伺服器層級 IP [防火牆規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)，以允許 Azure 資料庫移轉服務存取目標資料庫。 提供用於 Azure 資料庫移轉服務之虛擬網路的子網路範圍。
- 確定用來連線至來源 SQL Server 執行個體的認證具有 [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) 權限。
- 確定用來連線至目標 Azure SQL DB 執行個體的認證，在目標資料庫上具有 CONTROL DATABASE 權限。

## <a name="assess-your-on-premises-database"></a>評估您的內部部署資料庫

將資料從 SQL Server 執行個體遷移到 Azure SQL Database 中的單一資料庫或集區資料庫之前，您必須評估 SQL Server 資料庫是否有任何可能會阻礙移轉的問題。 使用資料移轉小幫手 v3.3 或更新版本，依照[執行 SQL Server 移轉評估](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)一文中所描述的步驟，完成內部部署資料庫評估。 所需的步驟摘要如下：

1. 在 Data Migration Assistant 中，選取 [New] \(新增\) (+) 圖示，然後選取 [Assessment] \(評估\)**** 專案類型。
2. 在 [Source server type] \(來源伺服器類型\)**** 文字方塊中指定專案名稱，選取 [SQL Server]****，並在 [Target server type] \(目標伺服器類型\)**** 文字方塊中，選取 [Azure SQL Database]****，然後選取 [建立]**** 以建立專案。

    當您評估來源 SQL Server 資料庫遷移到 Azure SQL Database 中的單一資料庫或集區資料庫時，可以選擇下列其中一種或兩種評估報告類型：

   - 檢查資料庫相容性
   - 檢查功能同位

    預設會選取這兩種報告類型。

3. 在 Data Migration Assistant 的 [Options] \(選項\)**** 畫面上，選取 [Next] \(下一步\)****。
4. 在 [Select sources] \(選取來源\)**** 畫面的 [Connect to a server] \(連線到伺服器\)**** 對話方塊中，提供您 SQL Server 的連線詳細資料，然後選取 [Connect] \(連線\)****。
5. 在 [新增資源]**** 對話方塊中，依序選取 [AdventureWorks2012]****、[新增]****，然後選取 [開始評估]****。

    > [!NOTE]
    > 如果您使用 SSIS，DMA 目前不支援來源 SSISDB 的評估。 不過會評估/驗證 SSIS 專案/套件，因為它們會重新部署到 Azure SQL Database 所裝載的目的地 SSISDB。 如需有關遷移 SSIS 套件的詳細資訊，請參閱[將 SQL Server Integration Services 套件遷移到 Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) 一文。

    評估完成時，結果會如下圖所示：

    ![評估資料移轉](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    對於 Azure SQL Database 中的資料庫，評估會識別部署至單一資料庫或集區資料庫時的功能同位問題和移轉阻礙問題。

    - **SQL Server 功能同位**類別提供一組完整的建議、Azure 中可使用的替代方法以及補救步驟，協助您規劃移轉專案所需的時間和精力。
    - **相容性問題**類別則識別反映出相容性問題的部分支援或不支援功能，這些相容性問題可能會阻礙將 SQL Server 資料庫移轉至 Azure SQL Database。 同時也提供協助您解決這些問題的建議。

6. 選取特定的選項，檢閱評估結果是否有阻礙移轉的問題和功能同位問題。

## <a name="migrate-the-sample-schema"></a>移轉範例結構描述

當您滿意評估結果，且認為選取的資料庫也適合遷移至 Azure SQL Database 中的單一資料庫或集區資料庫之後，請使用 DMA 將結構描述遷移至 Azure SQL Database。

> [!NOTE]
> 在 Data Migration Assistant 中建立移轉專案之前，請務必先確認您已經如必要條件中所述在 Azure 中佈建資料庫。 基於本教學課程的目的，Azure SQL Database 的名稱會假設為 **AdventureWorksAzure**，但您可以命名為不同的名稱。

> [!IMPORTANT]
> 如果您使用 SSIS，DMA 目前不支援來源 SSISDB 的移轉，但您可以將 SSIS 專案/套件重新部署到 Azure SQL Database 所裝載的目的地 SSISDB。 如需有關遷移 SSIS 套件的詳細資訊，請參閱[將 SQL Server Integration Services 套件遷移到 Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) 一文。

若要將 **AdventureWorks2012** 結構描述移轉到 Azure SQL Database 中的單一資料庫或集區資料庫，請執行下列步驟：

1. 在資料移轉小幫手中，選取新增 (+) 圖示，然後在 [專案類型]**** 底下選取 [移轉]****。
2. 在 [Source server type] \(來源伺服器類型\)**** 文字方塊中指定專案名稱，選取 [SQL Server]****，然後在 [Target server type] \(目標伺服器類型\)**** 文字方塊中，選取 [Azure SQL Database]****。
3. 在 [Migration Scope] \(移轉範圍\)**** 下，選取 [Schema only] \(僅結構描述\)****。

    執行先前的步驟之後，Data Migration Assistant 介面應該如下圖所示：

    ![建立 Data Migration Assistant 專案](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. 選取 [Create] \(建立\)  以建立專案。
5. 在 Data Migration Assistant 中，為您的 SQL Server 指定來源連線詳細資料，選取 [Connect] \(連線\)****，然後選取 [AdventureWorks2012]**** 資料庫。

    ![Data Migration Assistant 來源連線詳細資料](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. 在 [連線到目標伺服器] 下方選取 [下一步]，指定 Azure SQL 資料庫的目標連線詳細資料、選取 [連線]，然後選取您已在 Azure SQL Database 中預先佈建的 [AdventureWorksAzure] 資料庫。

    ![Data Migration Assistant 目標連線詳細資料](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. 選取 [Next] \(下一步\)**** 前進到 [Select objects] \(選取物件\)**** 畫面，您可以指定 **AdventureWorks2012** 資料庫中需要部署至 Azure SQL Database 的結構描述物件。

    預設會選取所有物件。

    ![產生 SQL 指令碼](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. 選取 [Generate SQL script] \(產生 SQL 指令碼\)**** 來建立 SQL 指令碼，然後檢閱指令碼是否有任何錯誤。

    ![結構描述指令碼](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. 選取 [Deploy schema] \(部署結構描述\)**** 以將結構描述部署至 Azure SQL Database，並在結構描述部署好之後，檢查目標伺服器是否有任何異常狀況。

    ![部署結構描述](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者

1. 登入 Azure 入口網站。 搜尋並選取 [訂用帳戶]****。

   ![顯示入口網站訂用帳戶](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]。

    ![顯示資源提供者](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. 搜尋移轉，然後針對 [Microsoft.DataMigration] 選取 [註冊]。

    ![註冊資源提供者](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>建立執行個體

1. 從 Azure 入口網站功能表或 [首頁] 頁面上，選取 [建立資源]。 搜尋並選取 [Azure 資料庫移轉服務]。

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. 在 [Azure 資料庫移轉服務]**** 畫面上，選取 [建立]****。

    ![建立 Azure 資料庫移轉服務執行個體](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. 在 [建立移轉服務]**** 畫面上，指定服務的名稱、訂用帳戶，以及新的或現有的資源群組。

4. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的位置。

5. 選取現有的虛擬網路或建立新的虛擬網路。

    虛擬網路會為 Azure 資料庫移轉服務提供來源 SQL Server 和目標 Azure SQL Database 執行個體的存取權。

    如需如何在 Azure 入口網站中建立虛擬網路的詳細資訊，請參閱[使用 Azure 入口網站建立虛擬網路](https://aka.ms/DMSVnet)一文。

6. 選取定價層。

    如需成本和定價層的詳細資訊，請參閱[定價分頁](https://aka.ms/dms-pricing)。

    ![設定 Azure 資料庫移轉服務執行個體設定](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7. 選取 [建立]**** 以建立服務。

## <a name="create-a-migration-project"></a>建立移轉專案

建立服務之後，請在 Azure 入口網站中找出該服務，然後建立新的移轉專案。

1. 在 Azure 入口網站功能表中，選取 [所有服務]。 搜尋並選取 [Azure 資料庫移轉服務]。

     ![找出 Azure 資料庫移轉服務的所有執行個體](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. 在 [Azure 資料庫移轉服務] 畫面上，選取您建立的 Azure 資料庫移轉服務執行個體。

3. 選取 [新增移轉專案]。

     ![找出 Azure 資料庫移轉服務的執行個體](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. 在 [新增移轉專案]**** 畫面上指定專案名稱、在 [來源伺服器類型]**** 文字方塊中選取 [SQL Server]****、在 [目標伺服器類型]**** 文字方塊中選取 [Azure SQL Database]****，然後針對 [選擇活動類型]****，選取 [離線資料移轉]****。

    ![建立資料庫移轉服務專案](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. 選取 [建立及執行活動]****，以建立專案並執行移轉活動。

## <a name="specify-source-details"></a>指定來源詳細資料

1. 在 [移轉來源詳細資料]**** 畫面上，指定來源 SQL Server 執行個體的連線詳細資料。

    請務必使用來源 SQL Server 執行個體名稱的完整網域名稱 (FQDN)。 如果無法解析 DNS 名稱，您也可以使用 IP 位址。

2. 如果您尚未在來源伺服器上安裝信任的憑證，選取 [信任伺服器憑證]**** 核取方塊。

    未安裝信任的憑證時，SQL Server 會在執行個體啟動時，產生自我簽署憑證。 此憑證用來加密用戶端連線的認證。

    > [!CAUTION]
    > 使用自我簽署憑證加密的 TLS 連線不會提供增強式安全性。 這種連線容易受到攔截式攻擊。 在生產環境或連線到網際網路的伺服器上，您不應該仰賴使用自我簽署憑證的 TLS。

   ![來源詳細資料](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

    > [!IMPORTANT]
    > 如果您使用 SSIS，DMS 目前不支援來源 SSISDB 的移轉，但您可以將 SSIS 專案/套件重新部署到 Azure SQL Database 所裝載的目的地 SSISDB。 如需有關遷移 SSIS 套件的詳細資訊，請參閱[將 SQL Server Integration Services 套件遷移到 Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) 一文。

## <a name="specify-target-details"></a>指定目標詳細資料

1. 選取 [儲存]，然後在 [移轉目標詳細資料] 畫面上指定目標 Azure SQL Database 的連線詳細資料，此伺服器是使用 Data Migration Assistant 將 **AdventureWorks2012** 結構描述部署到的預先佈建 Azure SQL Database。

    ![選取目標](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. 選取 [儲存]****，然後在 [對應到目標資料庫]**** 畫面上，對應要進行移轉的來源和目標資料庫。

    如果目標資料庫包含與來源資料庫相同的資料庫名稱，Azure 資料庫移轉服務依預設會選取目標資料庫。

    ![對應到目標資料庫](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. 在 [選取資料表]**** 畫面上選取 [儲存]****，展開資料表清單，然後檢閱受影響欄位的清單。

    Azure 資料庫移轉服務會自動選取存在於目標 Azure SQL Database 執行個體上的所有空來源資料表。 如果您想要重新移轉已包含資料的資料表，就必須在此刀鋒視窗上明確地選取資料表。

    ![選取資料表](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4. 在 [Migration summary] \(移轉摘要\)**** 畫面上選取 [儲存]****，在 [Activity name] \(活動名稱\)**** 文字方塊中，指定移轉活動的名稱。

5. 展開 [驗證選項]**** 區段以顯示 [選擇驗證選項]**** 畫面，然後指定是否要對已移轉的資料庫驗證**結構描述比較**、**資料一致性**和**查詢正確性**。

    ![選擇驗證選項](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

6. 選取 [儲存]****，檢閱摘要以確定來源和目標詳細資料符合您先前指定的內容。

    ![移轉摘要](media/tutorial-sql-server-to-azure-sql/dms-run-migration2.png)

## <a name="run-the-migration"></a>執行移轉

- 選取 [執行移轉]****。

    [移轉活動] 視窗隨即出現，而且活動的 [狀態] 為 [擱置]。

    ![活動狀態](media/tutorial-sql-server-to-azure-sql/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>監視移轉

1. 在移轉活動畫面上，選取 [重新整理]**** 以更新顯示，直到移轉的**狀態**顯示為 [已完成]**** 為止。

    ![活動狀態已完成](media/tutorial-sql-server-to-azure-sql/dms-completed-activity1.png)

2. 移轉完成之後，請選取 [下載報告]**** 以取得報告，其中會列出與移轉程序相關聯的詳細資料。

3. 驗證目標 Azure SQL 資料庫上的目標資料庫。

### <a name="additional-resources"></a>其他資源

- [使用 Azure 資料移轉服務 (DMS) 移轉 SQL](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) 實作教室。
- 如需執行線上移轉至 Azure SQL Database 時的已知問題和限制，請參閱 [Azure SQL Database 線上移轉的已知問題和因應措施](known-issues-azure-sql-online.md)一文。
- 如需 Azure 資料庫移轉服務的相關資訊，請參閱[什麼是 Azure 資料庫移轉服務？](https://docs.microsoft.com/azure/dms/dms-overview)一文。
- 如需 Azure SQL Database 的相關資訊，請參閱[什麼是 Azure SQL Database 服務？](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)。
