---
title: 關於 Azure Vm 中的 SAP Hana 資料庫備份
description: 在本文中，您將瞭解如何備份在 Azure 虛擬機器上執行的 SAP Hana 資料庫。
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: e30507e433ff9a828266c88ca79e576c508edc31
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757535"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>關於 Azure Vm 中的 SAP Hana 資料庫備份

SAP Hana 資料庫是任務關鍵性工作負載，需要低復原點目標 (RPO) 以及 (RTO) 的快速復原時間目標。 您現在可以使用[Azure 備份](./backup-overview.md)來[備份在 Azure vm 上執行的 SAP Hana 資料庫](./tutorial-backup-sap-hana-db.md)。

Azure 備份是 [經過 SAP 認證的 Backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) ，可利用 SAP Hana 的原生 api 來提供原生備份支援。 Azure 備份的這項供應專案符合 Azure 備份的 **零基礎結構** 備份口號，無須部署及管理備份基礎結構。 您現在可以順暢地備份和還原在 Azure Vm 上執行的 SAP Hana 資料庫 ([M 系列 vm](../virtual-machines/m-series.md) 現在也受到支援！ ) 並利用 Azure 備份提供的企業管理功能。

## <a name="added-value"></a>已新增值

使用 Azure 備份來備份和還原 SAP Hana 資料庫可提供下列優點：

* **15 分鐘復原點目標 (RPO) **：現在可以復原最多15分鐘的重要資料。
* 單鍵還原**時間點**：讓生產資料還原至替代的 HANA 伺服器變得很簡單。 備份和目錄以執行還原的連結，全都由 Azure 在幕後管理。
* **長期保留**：適用于嚴格的合規性和審核需求。 保留您的備份數年，以保留持續時間為准，而內建的生命週期管理功能將自動剪除復原點。
* **Azure 的備份管理**：使用 Azure 備份的管理和監視功能，以改善管理體驗。 也支援 Azure CLI。

若要查看目前支援的備份和還原案例，請參閱 [SAP Hana 案例支援矩陣](./sap-hana-backup-support-matrix.md#scenario-support)。

## <a name="backup-architecture"></a>備份架構

![備份架構圖表](./media/sap-hana-db-about/backup-architecture.png)

* 備份程式一開始會先在 Azure 中 [建立復原服務保存庫](./tutorial-backup-sap-hana-db.md#create-a-recovery-services-vault) 。 此保存庫將用來儲存一段時間內建立的備份和復原點。
* 執行 SAP Hana server 的 Azure VM 會向保存庫註冊，而且會 [探索](./tutorial-backup-sap-hana-db.md#discover-the-databases)要備份的資料庫。 若要啟用 Azure 備份服務以探索資料庫，必須在 HANA 伺服器上以根使用者的形式執行 [preregistration 腳本](https://aka.ms/scriptforpermsonhana) 。
* 此腳本會使用**hdbuserstore**中的相同名稱來建立**AZUREWLBACKUPHANAUSER** DB 使用者和對應的金鑰。 請參閱  [預先註冊腳本的功能](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) 一節，以深入瞭解腳本的用途。
* Azure 備份服務現在會在已註冊的 SAP Hana 伺服器上安裝 **HANA 的 Azure 備份外掛程式** 。
* **適用于 HANA 的 Azure 備份外掛程式**會使用 preregistration 腳本所建立的**AZUREWLBACKUPHANAUSER** DB 使用者來執行所有備份和還原作業。 如果您嘗試在不執行此腳本的情況下設定 SAP Hana Db 的備份，您可能會收到下列錯誤： **UserErrorHanaScriptNotRun**。
* 若要在探索到的資料庫上 [設定備份](./tutorial-backup-sap-hana-db.md#configure-backup) ，請選擇所需的備份原則，並啟用備份。

* 一旦設定了備份，Azure 備份服務就會在受保護的 SAP Hana 伺服器上的資料庫層級設定下列 Backint 參數：
  *  [catalog_backup_using_backint:true]
  *  [enable_accumulated_catalog_backup:false]
  *  [parallel_data_backup_backint_channels:1]
  *  [log_backup_timeout_s:900)]
  *  [backint_response_timeout:7200]

>[!NOTE]
>請確定這些參數 *不* 存在於主機層級上。 主機層級參數將會覆寫這些參數，而且可能會導致非預期的行為。
>

* **適用于 HANA 的 Azure 備份外掛程式**會維護所有備份排程和原則詳細資料。 它會觸發排程的備份，並透過 Backint Api 與 **HANA 備份引擎** 進行通訊。
* **HANA 備份引擎**會傳回包含要備份之資料的 Backint 資料流程。
* 所有已排程的備份和隨選備份 (由 **HANA 的 Azure 備份外掛程式**起始的 Azure 入口網站) 所觸發。 但是，記錄備份是由 **HANA 備份引擎** 本身管理及觸發。
* 適用于 SAP Hana 的 Azure 備份，是一種以 BackInt 認證的解決方案，並不依存于基礎磁片或 VM 類型。 備份是由 HANA 產生的資料流程執行。

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>使用 azure VM 備份搭配 Azure SAP Hana 備份

除了在 Azure 中使用可提供資料庫層級備份和復原的 SAP Hana 備份之外，您還可以使用 Azure VM 備份解決方案來備份作業系統和非資料庫磁片。

已 [認證的 Azure SAP Hana 備份解決方案](#backup-architecture) 可用於備份和復原資料庫。

您可以使用[AZURE VM 備份](backup-azure-vms-introduction.md)來備份 OS 和其他非資料庫磁片。 VM 備份會每天執行一次，而且它會備份所有磁片 (但 **寫入加速器 (WA) 磁片** 和 **ultra 磁片**) 除外。 因為正在使用 Azure SAP Hana 備份解決方案來備份資料庫，所以您可以使用適用于 [Azure vm 的選擇性磁片備份和還原](selective-disk-backup-restore.md) 功能，只對 OS 和非資料庫磁片進行檔案一致備份。

>[!NOTE]
> 使用預先張貼腳本搭配 Azure VM 備份，可允許應用程式一致備份資料庫的資料磁片區。 但是，如果記錄區域位於 WA 磁片上，則取得這些磁片的快照集可能無法保證記錄區的一致性。 HANA 有明確的方式可針對此確切原因產生記錄備份。 在您的 SAP Hana 中啟用相同的功能，並可使用 Azure SAP Hana 備份進行備份。

若要還原執行 SAP Hana 的 VM，請遵循下列步驟：

* 從最新的復原點，[從 AZURE VM 備份還原新的 vm](backup-azure-arm-restore-vms.md) 。 或建立新的空白 VM，並從最新的復原點連接磁片。
* 因為不會備份 WA 磁片，所以不會還原它們。 建立空白的 WA 磁片和記錄區域。
* 在設定所有其他設定之後 (例如 IP、系統名稱等) 設定好之後，VM 便會設定為接收來自 Azure 備份的 DB 資料。
* 現在將資料庫從 [Azure SAP HANA db 備份](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) 還原至 VM，以進行所需的時間點。

## <a name="next-steps"></a>後續步驟

* 瞭解如何 [還原在 AZURE VM 上執行的 SAP Hana 資料庫](./sap-hana-db-restore.md)
* 了解如何[管理使用 Azure 備份進行備份的 SAP HANA 資料庫](./sap-hana-db-manage.md)
