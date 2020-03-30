---
title: 關於 Azure VM 中的 SAP HANA 資料庫備份
description: 在本文中，瞭解備份在 Azure 虛擬機器上運行的 SAP HANA 資料庫。
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 52c235c95cea73a0c51c62fcb55f7f711d2eff21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476452"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>關於 Azure VM 中的 SAP HANA 資料庫備份

SAP HANA 資料庫是任務關鍵型工作負載，需要低復原點目標 （RPO） 和快速恢復時間目標 （RTO）。 現在可以使用[Azure 備份](https://docs.microsoft.com/azure/backup/backup-overview)[備份備份在 Azure VM 上運行的 SAP HANA 資料庫](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db)。

Azure 備份由 SAP[認證](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5)，通過利用 SAP HANA 的本機 API 提供本機備份支援。 Azure 備份的此產品與 Azure 備份的**零基礎結構**備份口號一致，無需部署和管理備份基礎結構。 現在，您可以無縫備份和恢復在 Azure VM 上運行的 SAP HANA 資料庫（現在也支援[M 系列 VM！），](../virtual-machines/m-series.md)並利用 Azure 備份提供的企業管理功能。

## <a name="added-value"></a>附加值

使用 Azure 備份來備份和恢復 SAP HANA 資料庫，具有以下優點：

* **15 分鐘復原點目標 （RPO）：** 現在有可能恢復長達 15 分鐘的關鍵資料。
* **一鍵式，時間點還原**：將生產資料還原到備用 HANA 伺服器變得容易。 備份和目錄的鏈條執行還原都由 Azure 在後臺管理。
* **長期保留**：滿足嚴格的合規性和審計需求。 根據保留期保留備份數年，超過此時間，復原點將由內置生命週期管理功能自動壓縮。
* **Azure 的備份管理**：使用 Azure 備份的管理和監視功能來改進管理體驗。 Azure CLI 也受支援。

要查看我們今天支援的備份和還原方案，請參閱[SAP HANA 方案支援矩陣](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)。

## <a name="backup-architecture"></a>備份架構

![備份體系結構圖](./media/sap-hana-db-about/backup-architecture.png)

* 備份過程首先在 Azure 中[創建恢復服務保存庫](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault)。 此保存庫將用於存儲隨時間創建的備份和復原點。
* 運行 SAP HANA 伺服器的 Azure VM 已註冊到保存庫，並[發現](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases)要備份的資料庫。 要使 Azure 備份服務能夠發現資料庫，必須在 HANA 伺服器上作為根使用者運行[預註冊腳本](https://aka.ms/scriptforpermsonhana)。
* 此腳本在**hdbuser 存儲**中創建**AZUREWLBACKUPHANAUSER** DB 使用者和同名的相應金鑰。 請參閱[預註冊腳本的作用](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)部分，以瞭解有關腳本的作用的詳細資訊。
* Azure 備份服務現在在已註冊的 SAP HANA 伺服器上安裝用於 HANA 的**Azure 備份外掛程式**。
* 由預註冊腳本創建的**AZUREWLBACKUPHANAUSER** DB 使用者由用於**HANA 的 Azure 備份外掛程式**用於執行所有備份和還原操作。 如果您嘗試在不運行此腳本的情況下為 SAP HANA DB 配置備份，您可能會收到以下錯誤 **：UserErrorHanaScriptNotRun**。
* 要[配置](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup)發現的資料庫上的備份，請選擇所需的備份策略並啟用備份。

* 配置備份後，Azure 備份服務在受保護的 SAP HANA 伺服器上的 DATABASE 級別設置以下 Backint 參數：
  * [catalog_backup_using_backint：真]
  * [enable_accumulated_catalog_backup：假]
  * [parallel_data_backup_backint_channels：1]
  * [log_backup_timeout_s：900）]
  * [backint_response_timeout：7200]

>[!NOTE]
>確保這些*參數不在*HOST 級別存在。 主機級參數將覆蓋這些參數，並可能導致意外行為。
>

* **HANA 的 Azure 備份外掛程式**維護所有備份計畫以及策略詳細資訊。 它觸發計畫備份，並通過 Backint API 與**HANA 備份引擎**通信。
* **HANA 備份引擎**返回具有要備份資料的 Backint 流。
* 所有計劃備份和按需備份（從 Azure 門戶觸發）都是完整備份或差異備份，由**HANA 的 Azure 備份外掛程式**啟動。 但是，記錄備份由**HANA 備份引擎**本身管理和觸發。
* SAP HANA 的 Azure 備份是 BackInt 認證的解決方案，不依賴于基礎磁片或 VM 類型。 備份由 HANA 生成的流執行。

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>將 Azure VM 備份與 Azure SAP HANA 備份一起使用

除了在 Azure 中使用提供資料庫級備份和恢復的 SAP HANA 備份外，還可以使用 Azure VM 備份解決方案備份作業系統和非資料庫磁片。

[經過 Backint 認證的 Azure SAP HANA 備份解決方案](#backup-architecture)可用於資料庫備份和恢復。

[Azure VM 備份](backup-azure-vms-introduction.md)可用於備份作業系統和其他非資料庫磁片。 VM 備份每天執行一次，並備份所有磁片（**寫入加速器 （WA） 磁片**和**UltraDisk**除外）。 由於正在使用 Azure SAP HANA 備份解決方案備份資料庫，因此可以使用當前處於預覽狀態的排除磁片功能僅對作業系統和非資料庫磁片進行檔一致的備份。

>[!NOTE]
> 將預發佈腳本與 Azure VM 備份一起使用將允許對資料庫的資料捲進行應用一致的備份。 但是，如果日誌區域駐留在 WA 磁片上，則拍攝這些磁片的快照可能不能保證日誌區域的一致性。 HANA 具有生成記錄備份的明確方法，原因正是如此。 在 SAP HANA 中啟用相同的功能，可以使用 Azure SAP HANA 備份來備份它們。

要還原運行 SAP HANA 的 VM，請按照以下步驟操作：

* [從 Azure VM 備份從最新復原點還原新 VM。](backup-azure-arm-restore-vms.md) 或者創建新的空 VM 並從最新的復原點連接磁片。
* 由於未備份 WA 磁片，因此不會還原它們。 創建空 WA 磁片和日誌區域。
* 設置所有其他配置（如 IP、系統名稱等）後，VM 將設置為從 Azure 備份接收資料庫資料。
* 現在，將資料庫從[Azure SAP HANA 資料庫備份](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point)還原到 VM 到所需的時間點。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[還原在 Azure VM 上運行的 SAP HANA 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* 了解如何[管理使用 Azure 備份進行備份的 SAP HANA 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
