---
title: 將 SSIS 套件重新部署到 SQL 單一資料庫
titleSuffix: Azure Database Migration Service
description: 瞭解如何使用 Azure 資料庫移轉服務和 Data Migration Assistant，將 SQL Server Integration Services 封裝和專案遷移或重新部署至 Azure SQL Database 單一資料庫。
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
ms.openlocfilehash: 483f60138dcaa6252999b9d15e846fbd1c68e9a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84021512"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>使用 Azure 資料庫移轉服務將 SSIS 套件重新部署到 Azure SQL Database

如果您使用 SQL Server Integration Services （SSIS），而且想要將 SSIS 專案/套件從 SQL Server 裝載的來源 SSISDB 遷移到 Azure SQL Database 所裝載的目的地 SSISDB，您可以使用 Integration Services 部署嚮導來重新部署它們。 您可以從 SQL Server Management Studio (SSMS) 內啟動該精靈。

如果您使用 2012 以前的 SSIS 版本，則在將 SSIS 專案/套件重新部署至專案部署模型之前，您必須先使用 Integration Services 專案轉換精靈 (亦可從 SSMS 啟動) 進行轉換。 如需詳細資訊，請參閱[將專案轉換為專案部署模型](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)一文。

> [!NOTE]
> Azure 資料庫移轉服務（DMS）目前不支援將來源 SSISDB 遷移至 Azure SQL Database，但您可以使用下列程式重新部署您的 SSIS 專案/套件。

在本文中，您將學會如何：
> [!div class="checklist"]
>
> * 評估來源 SSIS 專案/套件。
> * 將 SSIS 專案/套件遷移至 Azure。

## <a name="prerequisites"></a>必要條件

若要完成這些步驟，您需要：

* SSMS 17.2 版或更新版本。
* 目標資料庫伺服器的執行個體，以便裝載 SSISDB。 如果您還沒有，請流覽至 [SQL Server （僅限邏輯伺服器）][表單](https://ms.portal.azure.com/#create/Microsoft.SQLServer)，以使用 Azure 入口網站建立[邏輯 SQL 伺服器](../azure-sql/database/logical-servers.md)（不含資料庫）。
* SSIS 必須在包含 Azure SSIS Integration Runtime （IR）的 Azure Data Factory （ADF）中布建，且其目的地 SSISDB 是由 SQL Database 所裝載（如在[Azure Data Factory 中布建 AZURE SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)一文所述）。

## <a name="assess-source-ssis-projectspackages"></a>評估來源 SSIS 專案/套件

雖然來源 SSISDB 的評量尚未整合到資料庫移轉小幫手（DMA）或 Azure 資料庫移轉服務（DMS），但您的 SSIS 專案/套件將會進行評估/驗證，因為它們會重新部署到由 Azure SQL Database 所裝載的目的地 SSISDB。

## <a name="migrate-ssis-projectspackages"></a>遷移 SSIS 專案/套件

若要將 SSIS 專案/套件遷移至 Azure SQL Database，請執行下列步驟。

1. 開啟 SSMS，然後選取 [選項]**** 來顯示 [連線至伺服器]**** 對話方塊。

2. 在 [**登**入] 索引標籤上，指定連接到將裝載目的地 SSISDB 之伺服器所需的資訊。

    ![SSIS 的 [登入] 索引標籤](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. 在 [連線屬性]**** 索引標籤上的 [連線至資料庫]**** 文字方塊中，選取或輸入 [SSISDB]****，然後選取 [連線]****。

    ![SSIS 的 [連線屬性] 索引標籤](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. 在 [SSMS 物件總管] 中，依序展開 [Integration Services 目錄]**** 節點和 [SSISDB]****，如果其中沒有現有的資料夾，則在 [SSISDB]**** 上按一下滑鼠右鍵並建立新的資料夾。

5. 展開 [SSISDB]**** 底下的任何資料夾，以滑鼠右鍵按一下 [專案]****，然後選取 [部署專案]****。

    ![SSIS 已展開 SSISDB 節點](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. 在 Integration Services 部署精靈中的 [簡介]**** 頁面上，於檢閱資訊後選取 [下一步]****。

    ![部署精靈的 [簡介] 頁面](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. 在 [選取來源]**** 頁面上，指定您想要部署的現有 SSIS 專案。

    如果 SSMS 也連線至裝載了來源 SSISDB 的 SQL Server，請選取 [Integration Services 目錄]****，然後在目錄中輸入伺服器名稱和專案路徑以直接部署專案。

    或者，請選取 [專案部署檔案]****，然後指定現有專案部署檔案 (.ispac) 的路徑來部署專案。

    ![部署精靈的 [選取來源] 頁面](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. 選取 [下一步]。
9. 在 [選取目的地]**** 頁面上，指定專案的目的端。

    a. 在 [伺服器名稱] 文字方塊中，輸入完整的伺服器名稱（<server_name>. database.windows.net）。

    b. 提供驗證資訊，然後選取 [連線]****。

    ![部署精靈的 [選取目的地] 頁面](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. 選取 **[流覽]** 以指定 SSISDB 中的目的地資料夾，然後選取 **[下一步]**。

    > [!NOTE]
    > 在選取 [連線]**** 之後，才會啟用 [下一步]**** 按鈕。

10. 檢視 [驗證]**** 頁面上的任何錯誤/警告，然後視需要據此修改套件。

    ![部署精靈的 [驗證] 頁面](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. 選取 [下一步]。

12. 在 [檢閱]**** 頁面上，檢閱您的部署設定。

    > [!NOTE]
    > 您可以選取 [**上一**步]，或選取左窗格中的任何步驟連結來變更您的設定。

13. 選取 [部署]**** 來啟動部署程序。

14. 部署程序完成後，您可以檢視 [結果] 頁面，裡面會顯示每個部署動作是成功還是失敗。
    a. 如果有任何動作失敗，請在 [結果]**** 資料行中選取 [失敗]****，以顯示錯誤的說明。
    b. (選擇性) 選取 [儲存報告]**** 將結果儲存至 XML 檔案。

15. 選取 [關閉]**** 來結束 Integration Services 部署精靈。

如果專案部署成功且沒有任何失敗，您便可以選取其內含的任何套件以在 Azure-SSIS IR 上執行。

## <a name="next-steps"></a>後續步驟

* 在 Microsoft [資料庫移轉指南](https://datamigration.microsoft.com/) \(英文\) 中檢閱移轉指引。
