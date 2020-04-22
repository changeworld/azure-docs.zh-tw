---
title: Microsoft Azure 備份伺服器 v3 版本資訊
description: 本文提供有關 Microsoft Azure 備份伺服器 (MABS) v3 的已知問題和解決方法的資訊。
ms.topic: conceptual
ms.date: 11/22/2018
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: 03863f7598da06bb36cbb7497d7c773f811a004e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685619"
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

1. [備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) 您的 SQL 資料庫，並解除安裝 MABS V2 (解除安裝期間，選擇此選項以保留受保護的資料)。
2. 升級到 SQL 2017 (Enterprise).，並解除安裝報告，作為升級的一部分。
3. [安裝](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS)。
4. [安裝](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS)。
5. 使用 [SQL 2017 的 SSRS 設定](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs)中記錄的參數來設定報告。
6. [安裝](backup-azure-microsoft-azure-backup.md) MABS V3。
7. 使用 SSMS 以[還原](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL，並執行 DPM 同步工具，如[此處](https://docs.microsoft.com/system-center/dpm/back-up-the-dpm-server?view=sc-dpm-2019#using-dpmsync)所述。
8. 使用下列命令，更新 dbo.tbl_DLS_GlobalSetting 表格中的 ‘DataBaseVersion’ 屬性：

```sql
        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'
```

9. 啟動 MSDPM 服務。

## <a name="next-steps"></a>後續步驟

[MABS V3 中新增功能](backup-mabs-whats-new-mabs.md)
