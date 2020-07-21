---
title: 關於 Azure Vm 中的 SAP Hana 資料庫備份
description: 在本文中，您將瞭解如何備份在 Azure 虛擬機器上執行的 SAP Hana 資料庫。
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 980278b3cdb9c97a5a483354a004a8278a745b3b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86503501"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>關於 Azure Vm 中的 SAP Hana 資料庫備份

SAP Hana 資料庫是需要低復原點目標（RPO）和快速復原時間目標（RTO）的任務關鍵性工作負載。 您現在可以使用[Azure 備份](./backup-overview.md)來[備份在 Azure vm 上執行的 SAP Hana 資料庫](./tutorial-backup-sap-hana-db.md)。

Azure 備份是由 SAP[認證的 Backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) ，藉由利用 SAP Hana 的原生 api 來提供原生備份支援。 這項來自 Azure 備份的供應專案與 Azure 備份的**零基礎結構**備份口號一致，因此不需要部署和管理備份基礎結構。 您現在可以順暢地備份和還原在 Azure Vm 上執行的 SAP Hana 資料庫（現在也支援[M 系列 vm](../virtual-machines/m-series.md) ！），並利用 Azure 備份提供的企業管理功能。

## <a name="added-value"></a>已新增值

使用 Azure 備份來備份和還原 SAP Hana 資料庫，可提供下列優點：

* **15 分鐘復原點目標（RPO）**：現在可以復原最多15分鐘的重要資料。
* 單鍵**時間點還原**：將生產資料還原至替代 HANA 伺服器變得很容易。 備份與目錄的連結以執行還原，全都由 Azure 在幕後進行管理。
* **長期保留**：適用于嚴格的合規性和審查需求。 根據保留期間，保留您的備份數年後，內建生命週期管理功能將自動剪除復原點。
* **Azure 的備份管理**：使用 Azure 備份的管理和監視功能來改善管理體驗。 也支援 Azure CLI。

若要查看目前支援的備份和還原案例，請參閱[SAP Hana 案例支援矩陣](./sap-hana-backup-support-matrix.md#scenario-support)。

## <a name="backup-architecture"></a>備份架構

![備份架構圖表](./media/sap-hana-db-about/backup-architecture.png)

* 備份程式一開始會先在 Azure 中[建立復原服務保存庫](./tutorial-backup-sap-hana-db.md#create-a-recovery-service-vault)。 此保存庫將用來儲存一段時間內建立的備份和復原點。
* 執行 SAP Hana server 的 Azure VM 會向保存庫註冊，並會[探索](./tutorial-backup-sap-hana-db.md#discover-the-databases)要備份的資料庫。 若要讓 Azure 備份服務探索資料庫，您必須在 HANA 伺服器上以根使用者身分執行[preregistration 腳本](https://aka.ms/scriptforpermsonhana)。
* 此腳本會在**hdbuserstore**中建立**AZUREWLBACKUPHANAUSER** DB 使用者，以及具有相同名稱的對應索引鍵。 請參閱[預先註冊腳本](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)的作用一節，以深入瞭解腳本的作用。
* Azure 備份服務現在會在已註冊的 SAP Hana 伺服器上安裝**HANA 的 Azure 備份外掛程式**。
* **適用于 HANA 的 Azure 備份外掛程式**會使用 preregistration 腳本所建立的**AZUREWLBACKUPHANAUSER** DB 使用者來執行所有的備份和還原作業。 如果您嘗試設定 SAP Hana Db 的備份，但未執行此腳本，您可能會收到下列錯誤： **UserErrorHanaScriptNotRun**。
* 若要在探索到的資料庫上[設定備份](./tutorial-backup-sap-hana-db.md#configure-backup)，請選擇所需的備份原則，並啟用備份。

* 一旦設定備份，Azure 備份服務就會在受保護 SAP Hana 伺服器上的資料庫層級設定下列 Backint 參數：
  *  [catalog_backup_using_backint:true]
  *  [enable_accumulated_catalog_backup:false]
  *  [parallel_data_backup_backint_channels:1]
  *  [log_backup_timeout_s:900)]
  *  [backint_response_timeout:7200]

>[!NOTE]
>請確定這些參數*不*存在於主機層級。 主機層級的參數將會覆寫這些參數，而且可能會導致非預期的行為。
>

* **適用于 HANA 的 Azure 備份外掛程式**會維護所有備份排程和原則詳細資料。 它會觸發排程備份，並透過 Backint Api 與**HANA 備份引擎**進行通訊。
* **HANA 備份引擎**會傳回具有要備份之資料的 Backint 資料流程。
* **適用于 HANA 的 Azure 備份外掛程式**會起始完整或差異的所有排程備份和隨選備份（從 Azure 入口網站觸發）。 不過，記錄備份是由**HANA 備份引擎**本身管理和觸發。
* 適用于 SAP Hana 的 Azure 備份是一種 BackInt 認證的解決方案，並不依存于基礎磁片或 VM 類型。 備份是由 HANA 產生的資料流程執行。

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>使用 azure VM 備份搭配 Azure SAP Hana 備份

除了在 Azure 中使用可提供資料庫層級備份和復原的 SAP Hana 備份之外，您還可以使用 Azure VM 備份解決方案來備份 OS 和非資料庫磁片。

已[認證的 Backint Azure SAP Hana 備份解決方案](#backup-architecture)可用於資料庫備份和復原。

您可以使用[AZURE VM 備份](backup-azure-vms-introduction.md)來備份 OS 和其他非資料庫磁片。 每天會執行一次 VM 備份，並備份所有磁片（**寫入加速器（WA）磁片**和**UltraDisks**除外）。 由於使用 Azure SAP Hana 備份解決方案來備份資料庫，因此您可以使用 [排除磁片] 功能（目前處於預覽狀態），只針對 OS 和非資料庫磁片進行檔案一致備份。

>[!NOTE]
> 使用預先張貼的腳本搭配 Azure VM 備份，可讓資料庫的資料磁片區進行應用程式一致備份。 不過，如果記錄區域位於 WA 磁片上，取得這些磁片的快照集可能無法保證記錄區域的一致性。 HANA 有明確的方式可產生記錄備份，因為這是確切的原因。 在您的 SAP Hana 中啟用相同的功能，並可使用 Azure SAP Hana 備份進行備份。

若要還原執行 SAP Hana 的 VM，請遵循下列步驟：

* 從最新的復原點，[從 AZURE VM 備份還原新的 VM](backup-azure-arm-restore-vms.md) 。 或者，建立新的空 VM，並從最新的復原點連接磁片。
* 由於 WA 磁片不會備份，因此不會還原。 建立空的 WA 磁片和記錄區域。
* 設定所有其他設定（例如 IP、系統名稱等等）之後，VM 會設定為從 Azure 備份接收資料庫資料。
* 現在將資料庫從[Azure SAP HANA db 備份](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point)還原到所需的時間點。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[還原在 AZURE VM 上執行的 SAP Hana 資料庫](./sap-hana-db-restore.md)
* 了解如何[管理使用 Azure 備份進行備份的 SAP HANA 資料庫](./sap-hana-db-manage.md)
