---
title: 備份中心的支援矩陣
description: 本文摘要說明備份中心針對每個工作負載類型所支援的案例
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 8effc2514abf1cac55abc28b625b869810536baf
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994573"
---
# <a name="support-matrix-for-backup-center"></a>備份中心的支援矩陣

備份中心提供單一的單一窗格，讓企業 [大規模管理、監視、操作及分析備份](backup-center-overview.md)。 本文摘要說明備份中心針對每個工作負載類型所支援的案例。

## <a name="supported-scenarios"></a>支援的案例

| **類別** | **案例**  | **支援的工作負載**  | **限制** |
| -------------| ------------- | ----------------------- |------------|
| 監視   | 查看所有作業 | <li> Azure 虛擬機器 <br><br> <li> 適用於 PostgreSQL 的 Azure 資料庫伺服器 | <li> 現成可用的7天工作。 <br> <li> 每個篩選器/下拉式清單最多可支援1000個專案。 因此，您可以使用備份中心來監視跨租使用者的最多1000訂用帳戶和1000保存庫。 |
| 監視 | 查看所有備份實例 | <li> Azure 虛擬機器 <br><br> <li> 適用於 PostgreSQL 的 Azure 資料庫伺服器 | 同上 |
| 監視 | 查看所有備份原則 | <li> Azure 虛擬機器 <br><br> <li> 適用於 PostgreSQL 的 Azure 資料庫伺服器 | 同上 |
| 監視 | 查看所有保存庫 | <li> Azure 虛擬機器 <br><br> <li> 適用於 PostgreSQL 的 Azure 資料庫伺服器 | 同上 |
| 動作 | 設定備份 | <li> Azure 虛擬機器 <br><br> <li> 適用於 PostgreSQL 的 Azure 資料庫伺服器 | 請參閱[AZURE VM 備份](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas)和[適用於 PostgreSQL 的 Azure 資料庫 Server 備份](backup-azure-database-postgresql.md#support-matrix)的支援矩陣 |
| 動作 | 還原備份實例 | <li> Azure 虛擬機器 <br><br> <li> 適用於 PostgreSQL 的 Azure 資料庫伺服器 | 請參閱[AZURE VM 備份](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas)和[適用於 PostgreSQL 的 Azure 資料庫 Server 備份](backup-azure-database-postgresql.md#support-matrix)的支援矩陣 |
| 動作 | 建立保存庫 | <li> Azure 虛擬機器 <br><br> <li> 適用於 PostgreSQL 的 Azure 資料庫伺服器 | 請參閱復原服務保存[庫](https://docs.microsoft.com/azure/backup/backup-support-matrix#vault-support)的支援矩陣 |
| 動作 | 建立備份原則 | <li> Azure 虛擬機器 <br><br> <li> 適用於 PostgreSQL 的 Azure 資料庫伺服器 | 請參閱復原服務保存[庫](https://docs.microsoft.com/azure/backup/backup-support-matrix#vault-support)的支援矩陣 |
| 動作 | 針對備份實例執行隨選備份 | <li> Azure 虛擬機器 <br><br> <li> 適用於 PostgreSQL 的 Azure 資料庫伺服器 | 請參閱[AZURE VM 備份](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas)和[適用於 PostgreSQL 的 Azure 資料庫 Server 備份](backup-azure-database-postgresql.md#support-matrix)的支援矩陣 |
| 動作 | 停止備份實例的備份 | <li> Azure 虛擬機器 <br><br> <li> 適用於 PostgreSQL 的 Azure 資料庫伺服器 | 請參閱[AZURE VM 備份](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas)和[適用於 PostgreSQL 的 Azure 資料庫 Server 備份](backup-azure-database-postgresql.md#support-matrix)的支援矩陣 |
| 深入解析 | 查看備份報表 | <li> Azure 虛擬機器 <br><br> <li> Azure 虛擬機器中的 SQL <br><br> <li> Azure 虛擬機器中的 SAP Hana <br><br> <li> Azure 檔案 <br><br> <li> System Center Data Protection Manager <br><br> <li> Azure 備份代理程式 (MARS)  <br><br> <li> Azure 備份伺服器 (MABS) | 請參閱 [備份報告的支援案例](https://docs.microsoft.com/azure/backup/configure-reports#supported-scenarios) |
| 控管 | 在 [備份] 類別下，查看並指派內建和自訂 Azure 原則 | N/A | N/A |
| 控管 | 視圖未設定進行備份的資料來源 | <li> Azure 虛擬機器 <br><br> <li> 適用於 PostgreSQL 的 Azure 資料庫伺服器 | N/A |

## <a name="unsupported-scenarios"></a>不支援的案例

| **類別** | **案例**  |
|--------------|---------------|
| 監視 | 大規模查看警示 |
| 動作 | 大規模設定保存庫設定 |
| 動作 | 從備份中心執行跨區域還原作業 |

## <a name="next-steps"></a>下一步

* [複習 Azure 備份的支援矩陣](https://docs.microsoft.com/azure/backup/backup-support-matrix)
* [查看 Azure VM 備份的支援矩陣](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas)
* [複習適用於 PostgreSQL 的 Azure 資料庫 Server 備份的支援矩陣](backup-azure-database-postgresql.md#support-matrix)
