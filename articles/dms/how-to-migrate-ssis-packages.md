---
title: 將 SSIS 包重新部署到 SQL 單個資料庫
titleSuffix: Azure Database Migration Service
description: 瞭解如何使用 Azure 資料庫移轉服務和資料移轉助手將 SQL 伺服器整合服務包和專案遷移到 Azure SQL 資料庫單個資料庫。
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
ms.openlocfilehash: 90a39b8fe3604a05f1d35a875ae4e34491b47d72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648524"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>使用 Azure 資料庫移轉服務將 SSIS 包重新部署到 Azure SQL 資料庫

如果使用 SQL Server 整合服務 （SSIS）， 並希望將 SSIS 專案/包從 SQL Server 託管的源 SSISDB 遷移到 Azure SQL 資料庫託管的目標 SSISDB，則可以使用整合服務部署嚮導重新部署它們。 您可以從 SQL Server Management Studio (SSMS) 內啟動該精靈。

如果您使用 2012 以前的 SSIS 版本，則在將 SSIS 專案/套件重新部署至專案部署模型之前，您必須先使用 Integration Services 專案轉換精靈 (亦可從 SSMS 啟動) 進行轉換。 如需詳細資訊，請參閱[將專案轉換為專案部署模型](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)一文。

> [!NOTE]
> Azure 資料庫移轉服務 （DMS） 當前不支援將源 SSISDB 遷移到 Azure SQL 資料庫伺服器，但可以使用以下過程重新部署 SSIS 專案/包。

在本文中，您將學會如何：
> [!div class="checklist"]
>
> * 評估來源 SSIS 專案/套件。
> * 將 SSIS 專案/套件遷移至 Azure。

## <a name="prerequisites"></a>Prerequisites

若要完成這些步驟，您需要：

* SSMS 17.2 版或更新版本。
* 目標資料庫伺服器的執行個體，以便裝載 SSISDB。 如果還沒有，請使用 Azure 門戶創建 Azure SQL 資料庫伺服器（沒有資料庫），通過導航到 SQL Server（僅限邏輯伺服器）[表單](https://ms.portal.azure.com/#create/Microsoft.SQLServer)。
* SSIS 必須在 Azure 資料工廠 （ADF） 中預配，其中包含 Azure-SSIS 集成運行時 （IR），目標 SSISDB 由 Azure SQL 資料庫伺服器的實例託管（如文章在[Azure 資料工廠中預配 Azure-SSIS 集成運行時](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)）。

## <a name="assess-source-ssis-projectspackages"></a>評估來源 SSIS 專案/套件

雖然源 SSISDB 的評估尚未集成到資料庫移轉助理 （DMA） 或 Azure 資料庫移轉服務 （DMS） 中，但 SSIS 專案/包在重新部署到 Azure SQL 資料庫伺服器上託管的目標 SSISDB 時，將進行評估/驗證。

## <a name="migrate-ssis-projectspackages"></a>遷移 SSIS 專案/套件

要將 SSIS 專案/包遷移到 Azure SQL 資料庫伺服器，執行以下步驟。

1. 開啟 SSMS，然後選取 [選項]**** 來顯示 [連線至伺服器]**** 對話方塊。

2. 在 **"登錄"** 選項卡上，指定連接到承載目標 SSISDB 的 Azure SQL 資料庫伺服器所需的資訊。

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
 
8. 選取 [下一步]****。
9. 在 [選取目的地]**** 頁面上，指定專案的目的端。

    a. 在"伺服器名稱文本"框中，輸入完全限定的 Azure SQL 資料庫伺服器名稱（<server_name>.database.windows.net）。

    b. 提供驗證資訊，然後選取 [連線]****。

    ![部署精靈的 [選取目的地] 頁面](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. 選擇 **"流覽"** 以在 SSISDB 中指定目的檔案夾，然後選擇 **"下一步**"。

    > [!NOTE]
    > 在選取 [連線]**** 之後，才會啟用 [下一步]**** 按鈕。

10. 檢視 [驗證]**** 頁面上的任何錯誤/警告，然後視需要據此修改套件。

    ![部署精靈的 [驗證] 頁面](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. 選取 [下一步]****。

12. 在 [檢閱]**** 頁面上，檢閱您的部署設定。

    > [!NOTE]
    > 您可以通過選擇 **"上一步"** 或選擇左側窗格中的任何步驟連結來更改設置。

13. 選取 [部署]**** 來啟動部署程序。

14. 部署程序完成後，您可以檢視 [結果] 頁面，裡面會顯示每個部署動作是成功還是失敗。
    a. 如果有任何動作失敗，請在 [結果]**** 資料行中選取 [失敗]****，以顯示錯誤的說明。
    b. (選擇性) 選取 [儲存報告]**** 將結果儲存至 XML 檔案。

15. 選取 [關閉]**** 來結束 Integration Services 部署精靈。

如果專案部署成功且沒有任何失敗，您便可以選取其內含的任何套件以在 Azure-SSIS IR 上執行。

## <a name="next-steps"></a>後續步驟

* 在 Microsoft [資料庫移轉指南](https://datamigration.microsoft.com/) \(英文\) 中檢閱移轉指引。
