---
title: Microsoft Azure 備份伺服器 v3 版本資訊
description: 本文提供 Microsoft Azure 備份 Server （MABS） v3 的已知問題和因應措施的相關資訊。
ms.topic: conceptual
ms.date: 06/03/2020
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: 1f4900bb129ee67cd75d2b793f4179e3135569a6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032556"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Microsoft Azure 備份伺服器版本資訊

本文提供 Microsoft Azure 備份伺服器 (MABS) V3 的已知問題和因應措施。

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>叢集工作負載的備份及復原失敗

**描述：** 將 MABS V2 升級到 MABS V3 後，對於叢集化的資料來源 (例如 Hyper-V 叢集或 SQL 叢集 (SQL Always On) 或資料庫可用性群組 (DAG) 中的 Exchange)，備份/還原失敗。

**因應措施：** 若要避免此問題，請開啟 SQL Server Management Studio (SSMS)，並在 DPM DB 上執行下列 SQL 指令碼：

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>在俄文地區設定升級至 MABS V3 失敗

**描述：** 在俄文地區設定中，從 MABS V2 升級至 MABS V3 失敗，出現錯誤代碼 **4387**。

**因應措施：** 使用俄文安裝套件，執行下列步驟以升級至 MABS V3：

1. [備份](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server#SSMSProcedure) 您的 SQL 資料庫，並解除安裝 MABS V2 (解除安裝期間，選擇此選項以保留受保護的資料)。
2. 升級到 SQL 2017 (Enterprise).，並解除安裝報告，作為升級的一部分。
3. [安裝](/sql/reporting-services/install-windows/install-reporting-services#install-your-report-server)SQL Server Reporting Services （SSRS）。
4. [安裝](/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS)。
5. 使用 [SQL 2017 的 SSRS 設定](./backup-azure-microsoft-azure-backup.md#upgrade-mabs)中記錄的參數來設定報告。
6. [安裝](backup-azure-microsoft-azure-backup.md) MABS V3。
7. 使用 SSMS 以[還原](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms) SQL，並執行 DPM 同步工具，如[此處](/system-center/dpm/back-up-the-dpm-server?view=sc-dpm-2019#using-dpmsync)所述。
8. 使用下列命令，更新 dbo.tbl_DLS_GlobalSetting 表格中的 ‘DataBaseVersion’ 屬性：

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. 啟動 MSDPM 服務。

## <a name="after-installing-ur1-the-mabs-reports-arent-updated-with-new-rdl-files"></a>安裝 UR1 之後，MABS 報表不會以新的 RDL 檔案更新

**描述**：使用 UR1 時，會修正已更新的 RDL 檔案的 MABS 報告格式問題。 新的 RDL 檔案不會自動取代為現有的檔案。

因應**措施：若**要取代 RDL 檔案，請遵循下列步驟：

1. 在 MABS 電腦上，開啟 SQL Reporting Services Web 入口網站 URL]。
1. 在入口網站 URL 上，DPMReports 資料夾的格式會是**`DPMReports_<GUID>`**

    >[!NOTE]
    >一律只有一個資料夾具有此命名慣例。 如果 MABS 是從舊版升級，可能也會有另一個較舊的資料夾，但您將無法開啟它。

    ![DPMReports 資料夾](./media/backup-mabs-release-notes-v3/dpm-reports-folder.png)

1. 選取並開啟 **`DPMReports_<GUID>`** 資料夾。 個別的報告檔案將會列出，如下所示。

    ![個別報表檔案的清單](./media/backup-mabs-release-notes-v3/individual-report-files.png)

1. 選取不是以**報表**結尾的報表檔案，並以滑鼠右鍵按一下 [**選項**]，然後選取 [**管理**]。

    ![針對報表檔案選取 [管理]](./media/backup-mabs-release-notes-v3/manage-files.png)

1. 在 [新增] 頁面中，選取 [**取代**] 選項，以最新的報表檔案取代檔案。

    您可以在路徑中找到最新的報告檔案`<MABS Installation Directory>\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    例如：`C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    ![以最新的報表檔案取代檔案](./media/backup-mabs-release-notes-v3/replace-files.png)

    取代檔案之後，請確定**名稱**和**描述**都是完整的，而且不是空的。

1. 取代檔案之後，請重新開機 MABS 服務，並使用報告檔案。

## <a name="next-steps"></a>接下來的步驟

[MABS 的新功能](backup-mabs-whats-new-mabs.md)
