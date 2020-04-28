---
title: 在 Azure Vm 上還原 SAP Hana 資料庫
description: 在本文中，您將瞭解如何還原在 Azure 虛擬機器上執行 SAP Hana 資料庫。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74287914"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>在 Azure Vm 上還原 SAP Hana 資料庫

本文說明如何還原 Azure 備份服務已備份至 Azure 備份復原服務保存庫的 Azure 虛擬機器（VM）上執行的 SAP Hana 資料庫。 還原可用於建立開發/測試案例的資料複本，或回到先前的狀態。

如需有關如何備份 SAP Hana 資料庫的詳細資訊，請參閱在[Azure vm 上備份 SAP Hana 資料庫](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>還原到某個時間點或復原點

Azure 備份可以還原在 Azure VM 上執行的 SAP HANA 資料庫，如下所示：

* 使用記錄備份還原至特定日期或時間 (可精確到秒)。 Azure 備份會根據選取的時間，自動決定還原所需的適當完整差異備份和記錄備份鏈結。

* 還原至特定的完整或差異備份以還原至特定復原點。

## <a name="prerequisites"></a>Prerequisites

在還原資料庫之前，請注意下列事項：

* 您只能將資料庫還原到相同區域中的 SAP HANA 執行個體

* 目標執行個體必須使用相同的來源保存庫進行註冊

* Azure 備份無法在相同的 VM 上識別兩個不同的 SAP Hana 實例。 因此，無法在相同的 VM 上將某個實例的資料還原至另一個實例

* 若要確定目標 SAP Hana 實例已準備好進行還原，請檢查其**備份就緒**狀態：

  * 開啟用來註冊目標 SAP Hana 實例的保存庫

  * 在保存庫儀表板的 [**使用者入門] 底下，選擇**[**備份**]

![保存庫中的備份儀表板](media/sap-hana-db-restore/getting-started-backup.png)

* 在 [**備份**] 中，于 [**您要備份什麼？** ] 下選擇 [**在 Azure VM 中 SAP Hana** ]

![選擇 Azure VM 中的 SAP Hana](media/sap-hana-db-restore/sap-hana-backup.png)

* 在 [**探索 vm 中的 db** ] 底下，按一下 [**查看詳細資料**]

![檢視詳細資料](media/sap-hana-db-restore/view-details.png)

* 審查目標 VM 的**備份就緒**程度

![受保護的伺服器](media/sap-hana-db-restore/protected-servers.png)

* 若要深入瞭解 SAP Hana 支援的還原類型，請參閱 SAP Hana 附注[1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>還原資料庫

* 開啟要用來還原 SAP Hana 資料庫的保存庫

* 在保存庫儀表板的 [**受保護的專案**] 底下，選擇 [**備份專案**]

![備份項目](media/sap-hana-db-restore/backup-items.png)

* 在 [備份**專案**] 的 [**備份管理類型**] 底下，選取 [ **Azure VM 中的 SAP Hana** ]

![備份管理類型](media/sap-hana-db-restore/backup-management-type.png)

* 選取要還原的資料庫

 ![要還原的資料庫](media/sap-hana-db-restore/database-to-restore.png)

* 檢閱資料庫功能表。 它提供資料庫備份的相關資訊，包括：

  * 最舊和最新的還原點

  * 資料庫過去24和72小時的記錄備份狀態

![資料庫功能表](media/sap-hana-db-restore/database-menu.png)

* 選取**還原資料庫**

* 在 [**還原**設定] 底下，指定還原資料的位置（或如何）：

  * **替代位置**：將資料庫還原至替代位置，並保留原始源資料庫。

  * **覆寫 DB**：將資料還原至與原始來源相同的 SAP Hana 實例。 此選項會覆寫原始資料庫。

![還原設定](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>還原至替代位置

* 在 [**還原**設定] 功能表的 [**要還原的位置**] 底下，選取 [**替代位置**]。

![還原至替代位置](media/sap-hana-db-restore/restore-alternate-location.png)

* 選取您要還原資料庫的 SAP Hana 主機名稱和實例名稱。
* 確認目標 SAP Hana 實例是否已準備好進行還原，方法是確保其**備份準備就緒。** 如需詳細資訊，請參閱[必要條件一節](#prerequisites)。
* 在 [還原的 DB 名稱]**** 方塊中，輸入目標資料庫的名稱。

> [!NOTE]
> 單一資料庫容器（SDC）還原必須遵循這些[檢查](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)。

* 如果適用的話，請選取 [**覆寫] （如果已有相同名稱的資料庫存在於所選的 HANA 實例上**）。
* 選取 [確定]  。

![還原設定-最終畫面](media/sap-hana-db-restore/restore-configuration-last.png)

* 在 [**選取還原點**] 中，選取 **[記錄（時間點）** ] 以[還原至特定時間點](#restore-to-a-specific-point-in-time)。 或選取 [**完整 & 差異**]，以[還原至特定復原點](#restore-to-a-specific-recovery-point)。

### <a name="restore-and-overwrite"></a>還原並覆寫資料庫

* 在 [**還原**設定] 功能表的 [**要還原的位置**] 底下，選取 [**覆寫 DB** > **正常]**。

![覆寫 DB](media/sap-hana-db-restore/overwrite-db.png)

* 在 [**選取還原點**] 中，選取 **[記錄（時間點）** ] 以[還原至特定時間點](#restore-to-a-specific-point-in-time)。 或選取 [**完整 & 差異**]，以[還原至特定復原點](#restore-to-a-specific-recovery-point)。

### <a name="restore-to-a-specific-point-in-time"></a>還原至特定時間點

如果您已選取 [記錄 (時間點)]**** 作為還原類型，請執行下列動作：

* 從記錄圖形中選取復原點，然後選取 **[確定]** 以選擇還原點。

![還原點](media/sap-hana-db-restore/restore-point.png)

* 在 [還原]**** 功能表上，選取 [還原]**** 以啟動還原作業。

![選取還原](media/sap-hana-db-restore/restore-restore.png)

* 在 [**通知**] 區域中追蹤還原進度，或選取 [資料庫] 功能表上的 [**還原作業**] 加以追蹤。

![已成功觸發還原](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>還原至特定復原點

如果您已選取 [完整和差異]**** 作為還原類型，請執行下列動作：

* 從清單中選取復原點，然後選取 **[確定]** 以選擇還原點。

![還原特定復原點](media/sap-hana-db-restore/specific-recovery-point.png)

* 在 [還原]**** 功能表上，選取 [還原]**** 以啟動還原作業。

![啟動還原作業](media/sap-hana-db-restore/restore-specific.png)

* 在 [**通知**] 區域中追蹤還原進度，或選取 [資料庫] 功能表上的 [**還原作業**] 加以追蹤。

![還原進度](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> 在將系統資料庫還原至目標實例之後，在多個資料庫容器（MDC）還原中，需要再次執行預先註冊腳本。 只有之後，後續的租使用者資料庫還原才會成功。 若要深入瞭解，請參閱[疑難排解-MDC 還原](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore)。

## <a name="next-steps"></a>後續步驟

* [瞭解如何](sap-hana-db-manage.md)管理使用 Azure 備份備份的 SAP Hana 資料庫
