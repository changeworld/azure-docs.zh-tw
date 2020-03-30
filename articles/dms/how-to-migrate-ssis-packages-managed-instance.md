---
title: 將 SSIS 包遷移到 SQL 託管實例
titleSuffix: Azure Database Migration Service
description: 瞭解如何使用 Azure 資料庫移轉服務或資料移轉助手將 SQL 伺服器整合服務 （SSIS） 包和專案遷移到 Azure SQL 資料庫託管實例。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 97a466ab033a42016c0d82465d1f98e2dcae8080
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297188"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>將 SQL Server Integration Services 套件遷移至 Azure SQL Database 受控執行個體
如果使用 SQL Server 整合服務 （SSIS）， 並希望將 SSIS 專案/包從 SQL Server 託管的源 SSISDB 遷移到由 Azure SQL 資料庫託管實例託管的目標 SSISDB，則可以使用 Azure 資料庫移轉服務。

如果您使用的 SSIS 版本早于 2012 年，或者您在遷移 SSIS 專案/包之前使用非 SSISDB 包存儲類型，則需要使用整合服務專案轉換向導進行轉換，該嚮導也可以從 SSMS 啟動。 如需詳細資訊，請參閱[將專案轉換為專案部署模型](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)一文。

> [!NOTE]
> Azure 資料庫移轉服務 （DMS） 當前不支援 Azure SQL 資料庫作為目標遷移目標。 要將 SSIS 專案/包重新部署到 Azure SQL 資料庫，請參閱將[SQL 伺服器整合服務包重新部署到 Azure SQL 資料庫](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)。

在本文中，您將學會如何：
> [!div class="checklist"]
>
> * 評估來源 SSIS 專案/套件。
> * 將 SSIS 專案/套件遷移至 Azure。

## <a name="prerequisites"></a>Prerequisites

若要完成這些步驟，您需要：

* 通過使用 Azure 資源管理器部署模型為 Azure 資料庫移轉服務創建 Microsoft Azure 虛擬網路，該模型通過使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)提供到本地源伺服器的網站到網站的連接。 有關詳細資訊，請參閱使用 Azure[資料庫移轉服務 進行 Azure SQL 資料庫託管實例遷移的文章"]( https://aka.ms/dmsnetworkformi) 有關創建虛擬網路的詳細資訊，請參閱[虛擬網路文檔](https://docs.microsoft.com/azure/virtual-network/)，尤其是包含分步詳細資訊的快速入門文章。
* 為確保虛擬網路網路安全性群組規則不會阻止以下到 Azure 資料庫移轉服務的入站通訊連接埠：443、53、9354、445、12000。 有關虛擬網路 NSG 流量篩選的更多詳細資訊，請參閱文章["使用網路安全性群組篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)"。
* 為[源資料庫引擎訪問](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017)配置 Windows 防火牆。
* 打開 Windows 防火牆以允許 Azure 資料庫移轉服務訪問源 SQL 伺服器，預設情況下，該伺服器是 TCP 埠 1433。
* 如果您使用動態連接埠執行多個具名 SQL Server 執行個體，您可以啟用 SQL Browser 服務並允許通過防火牆存取 UDP 連接埠 1434，讓 Azure 資料庫移轉服務連線來源伺服器上的具名執行個體。
* 如果您是在來源資料庫前面使用防火牆設備，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取用於移轉的來源資料庫，以及透過 SMB 連接埠 445 存取檔案。
* 要承載 SSISDB 的 Azure SQL 資料庫託管實例。 如果需要創建一個，請按照文章"創建 Azure [SQL 資料庫託管實例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)"中的詳細資訊進行操作。
* 確保用於連接源 SQL Server 和目標託管實例的登錄名是 sysadmin 伺服器角色的成員。
* 要驗證 SSIS 是否預配在 Azure 資料工廠 （ADF） 中，其中包含 Azure-SSIS 集成運行時 （IR），目標 SSISDB 由 Azure SQL 資料庫託管實例託管（如文章在[Azure 資料工廠中創建 Azure-SSIS 集成運行時](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)中所述）。

## <a name="assess-source-ssis-projectspackages"></a>評估來源 SSIS 專案/套件

雖然源 SSISDB 的評估尚未集成到資料庫移轉助理 （DMA） 中，但 SSIS 專案/包將重新部署到 Azure SQL 資料庫託管實例上託管的目標 SSISDB 時進行評估/驗證。

## <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者

1. 登入 Azure 入口網站，選取 [所有服務]****，然後選取 [訂用帳戶]****。

    ![顯示入口網站訂用帳戶](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]****。

    ![顯示資源提供者](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. 搜索遷移，然後搜索 Microsoft 的右側 **。** **Register**

    ![註冊資源提供者](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>建立 Azure 資料庫移轉服務執行個體

1. 在 Azure 門戶中，選擇 "**創建資源**"，搜索**Azure 資料庫移轉服務**，然後從下拉清單中選擇**Azure 資料庫移轉服務**。

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. 在 [Azure 資料庫移轉服務]**** 畫面上，選取 [建立]****。

    ![建立 Azure 資料庫移轉服務執行個體](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. 在 [建立移轉服務]**** 畫面上，指定服務的名稱、訂用帳戶，以及新的或現有的資源群組。

4. 選取您要建立 DMS 執行個體的位置。

5. 選擇現有虛擬網路或創建虛擬網路。

    虛擬網路為 Azure 資料庫移轉服務提供對源 SQL Server 和目標 Azure SQL 資料庫託管實例的訪問。

    有關如何在 Azure 門戶中創建虛擬網路的詳細資訊，請參閱使用 Azure[門戶創建虛擬網路](https://aka.ms/DMSVnet)的文章。

    如需其他詳細資訊，請參閱[了解使用 Azure 資料庫移轉服務進行 Azure SQL DB 受控執行個體移轉的網路拓樸](https://aka.ms/dmsnetworkformi)一文。

6. 選取定價層。

    有關成本和定價層的詳細資訊，請參閱[定價頁](https://aka.ms/dms-pricing)。

    ![建立 DMS 服務](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. 選取 [建立]**** 以建立服務。

## <a name="create-a-migration-project"></a>建立移轉專案

建立服務執行個體之後，請在 Azure 入口網站中找出該服務，然後建立新的移轉專案。

1. 在 Azure 入口網站中，選取 [所有服務]****，搜尋 Azure 資料庫移轉服務，然後選取 [Azure 資料庫移轉服務]****。

    ![找出 Azure 資料庫移轉服務的所有執行個體](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. 在**Azure 資料庫移轉服務**螢幕上，搜索所創建的實例的名稱，然後選擇實例。

3. 選取 [+ 新增移轉專案]****。

4. 在 **"新建遷移專案"** 螢幕上，指定專案的名稱，在 **"源伺服器類型**文本"框中，在 **"目標伺服器類型**文本"框中選擇**SQL Server，** 選擇**Azure SQL 資料庫託管實例**，然後**選擇活動類型**，選擇**SSIS 包遷移**。

   ![建立 DMS 專案](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. 選取 [Create] \(建立\)**** 以建立專案。

## <a name="specify-source-details"></a>指定來源詳細資料

1. 在 [移轉來源詳細資料]**** 畫面上，指定來源 SQL Server 的連線詳細資料。

2. 如果您尚未在伺服器上安裝信任的憑證，請選取 [信任伺服器憑證]**** 核取方塊。

    未安裝信任的憑證時，SQL Server 會在執行個體啟動時產生自我簽署憑證。 此憑證用來加密用戶端連線的認證。

    > [!CAUTION]
    > 使用自簽章憑證加密的 TLS 連接不提供強大的安全性。 這種連線容易受到攔截式攻擊。 不應依賴 TLS 在生產環境中或連接到 Internet 的伺服器上使用自簽章憑證。

   ![來源詳細資料](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. 選取 [儲存]****。

## <a name="specify-target-details"></a>指定目標詳細資料

1. 在 **"遷移目標詳細資訊**"螢幕上，指定目標的連接詳細資訊。

     ![目標詳細資訊](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. 選取 [儲存]****。

## <a name="review-the-migration-summary"></a>檢閱移轉摘要

1. 在 [移轉摘要]**** 畫面上的 [活動名稱]**** 文字方塊中，指定移轉活動的名稱。

2. 對於**SSIS 專案和環境覆蓋選項**，指定是覆蓋還是忽略現有的 SSIS 專案和環境。

    ![移轉專案摘要](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. 檢閱並確認與移轉專案相關聯的詳細資料。

## <a name="run-the-migration"></a>執行移轉

* 選取 [執行移轉]****。

## <a name="next-steps"></a>後續步驟

* 在 Microsoft [資料庫移轉指南](https://datamigration.microsoft.com/) \(英文\) 中檢閱移轉指引。
