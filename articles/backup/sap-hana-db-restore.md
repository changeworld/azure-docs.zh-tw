---
title: 在 Azure VM 上還原 SAP HANA 資料庫
description: 在本文中，瞭解如何還原在 Azure 虛擬機器上運行的 SAP HANA 資料庫。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287914"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>在 Azure VM 上還原 SAP HANA 資料庫

本文介紹如何還原 Azure 備份服務已備份到 Azure 備份恢復服務保存庫的 Azure 虛擬機器 （VM） 上運行的 SAP HANA 資料庫。 還原可用於為開發/測試方案創建資料副本或返回到以前的狀態。

有關如何備份 SAP HANA 資料庫的詳細資訊，請參閱[在 Azure VM 上備份 SAP HANA 資料庫](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>恢復到某個時間點或復原點

Azure 備份可以還原在 Azure VM 上執行的 SAP HANA 資料庫，如下所示：

* 使用記錄備份還原至特定日期或時間 (可精確到秒)。 Azure 備份會根據選取的時間，自動決定還原所需的適當完整差異備份和記錄備份鏈結。

* 還原至特定的完整或差異備份以還原至特定復原點。

## <a name="prerequisites"></a>Prerequisites

在還原資料庫之前，請注意以下事項：

* 您只能將資料庫還原到相同區域中的 SAP HANA 執行個體

* 目標執行個體必須使用相同的來源保存庫進行註冊

* Azure 備份無法標識同一 VM 上的兩個不同的 SAP HANA 實例。 因此，無法在同一 VM 上將資料從一個實例還原到另一個實例

* 為確保目標 SAP HANA 實例已準備好進行還原，請檢查其**備份就緒**狀態：

  * 打開註冊目標 SAP HANA 實例的保存庫

  * 在保存庫儀表板上，在 **"入門"下**，選擇 **"備份"**

![在保存庫儀表板中備份](media/sap-hana-db-restore/getting-started-backup.png)

* 在 **"備份**"中，在 **"要備份什麼"下？** **在 Azure VM 中選擇 SAP HANA**

![在 Azure VM 中選擇 SAP HANA](media/sap-hana-db-restore/sap-hana-backup.png)

* 在**VM 中的"發現 QB"** 下按一下"**查看詳細資訊**"

![檢視詳細資料](media/sap-hana-db-restore/view-details.png)

* 查看目標 VM 的**備份就緒性**

![受保護的伺服器](media/sap-hana-db-restore/protected-servers.png)

* 要瞭解有關 SAP HANA 支援的還原類型的詳細資訊，請參閱 SAP HANA 注釋[1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>還原資料庫

* 打開要還原 SAP HANA 資料庫的保存庫

* 在保存庫儀表板上，在 **"受保護專案"** 下，選擇 **"備份專案**"

![備份項目](media/sap-hana-db-restore/backup-items.png)

* 在**備份項**中，在**備份管理類型**下，**在 Azure VM 中選擇 SAP HANA**

![備份管理類型](media/sap-hana-db-restore/backup-management-type.png)

* 選擇要還原的資料庫

 ![要還原的資料庫](media/sap-hana-db-restore/database-to-restore.png)

* 檢閱資料庫功能表。 它提供有關資料庫備份的資訊，包括：

  * 最古老和最新的還原點

  * 資料庫過去 24 和 72 小時的記錄備份狀態

![資料庫功能表](media/sap-hana-db-restore/database-menu.png)

* 選擇 **"還原資料庫"**

* 在 **"還原配置**"下，指定還原資料的位置（或方式）：

  * **備用位置**：將資料庫還原到備用位置並保留原始源資料庫。

  * **覆蓋資料庫**：將資料還原到與原始源相同的 SAP HANA 實例。 此選項會覆寫原始資料庫。

![還原配置](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>還原至替代位置

* 在 **"還原配置"** 功能表中，在 **"還原位置"** 下，選擇 **"備用位置**"。

![還原至替代位置](media/sap-hana-db-restore/restore-alternate-location.png)

* 選擇要還原資料庫的 SAP HANA 主機名稱和實例名稱。
* 通過確保目標 SAP HANA 實例的**備份就緒**性，檢查目標 SAP HANA 實例是否已準備好進行還原。 有關詳細資訊，請參閱[先決條件部分](#prerequisites)。
* 在 [還原的 DB 名稱]**** 方塊中，輸入目標資料庫的名稱。

> [!NOTE]
> 單個資料庫容器 （SDC） 還原必須遵循這些[檢查](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)。

* 如果適用，**請選擇"如果所選 HANA 實例上已存在具有相同名稱的 DB"，則選擇"覆蓋"。**
* 選取 [確定]****。

![恢復配置 - 最終螢幕](media/sap-hana-db-restore/restore-configuration-last.png)

* 在 **"選擇還原點**"**中，選擇"日誌"（時間點）** 以[還原到特定時間點](#restore-to-a-specific-point-in-time)。 或者選擇 **"完全&差"** 以[還原到特定復原點](#restore-to-a-specific-recovery-point)。

### <a name="restore-and-overwrite"></a>還原並覆寫資料庫

* 在 **"還原配置"** 功能表中，在 **"還原位置"** 下，選擇 **"覆蓋 DB** > **確定**"。

![覆寫 DB](media/sap-hana-db-restore/overwrite-db.png)

* 在 **"選擇還原點**"**中，選擇"日誌"（時間點）** 以[還原到特定時間點](#restore-to-a-specific-point-in-time)。 或者選擇 **"完全&差"** 以[還原到特定復原點](#restore-to-a-specific-recovery-point)。

### <a name="restore-to-a-specific-point-in-time"></a>還原至特定時間點

如果您已選取 [記錄 (時間點)]**** 作為還原類型，請執行下列動作：

* 從日誌圖中選擇復原點，然後選擇 **"確定"** 以選擇還原點。

![還原點](media/sap-hana-db-restore/restore-point.png)

* 在 [還原]**** 功能表上，選取 [還原]**** 以啟動還原作業。

![選擇還原](media/sap-hana-db-restore/restore-restore.png)

* 跟蹤 **"通知"** 區域中的還原進度，或通過在資料庫功能表上選擇 **"還原作業**"來跟蹤它。

![已成功觸發還原](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>還原到特定復原點

如果您已選取 [完整和差異]**** 作為還原類型，請執行下列動作：

* 從清單中選擇復原點，然後選擇 **"確定"** 以選擇還原點。

![還原特定復原點](media/sap-hana-db-restore/specific-recovery-point.png)

* 在 [還原]**** 功能表上，選取 [還原]**** 以啟動還原作業。

![開始還原作業](media/sap-hana-db-restore/restore-specific.png)

* 跟蹤 **"通知"** 區域中的還原進度，或通過在資料庫功能表上選擇 **"還原作業**"來跟蹤它。

![還原進度](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> 在系統 DB 還原到目標實例後還原的多個資料庫容器 （MDC） 中，需要再次運行預註冊腳本。 只有這樣，後續租戶資料庫還原才會成功。 要瞭解更多資訊，請參閱[故障排除 – MDC 還原](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore)。

## <a name="next-steps"></a>後續步驟

* [瞭解如何](sap-hana-db-manage.md)管理使用 Azure 備份備份的 SAP HANA 資料庫
