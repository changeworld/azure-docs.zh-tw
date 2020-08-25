---
title: 監視 Azure 備份受保護的工作負載
description: 在本文中，您將瞭解使用 Azure 入口網站 Azure 備份工作負載的監視和通知功能。
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 2fae7cfdb8b316341e01d15b43811d3f0e7638ef
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827183"
---
# <a name="monitoring-azure-backup-workloads"></a>監視 Azure 備份工作負載

Azure 備份會根據 (內部部署與 Azure) 的備份需求和基礎結構拓撲，提供多個備份解決方案。 任何備份使用者或系統管理員應該會看到所有解決方案的內容，而且可能會在重要案例中收到通知。 本文詳細說明 Azure 備份服務所提供的監視和通知功能。

## <a name="backup-jobs-in-recovery-services-vault"></a>復原服務保存庫中的備份作業

Azure 備份針對受 Azure 備份保護的工作負載，提供內建的監視和警示功能。 在 [復原服務保存庫] 設定中，[ **監視** ] 區段會提供內建的工作和警示。

![RS 保存庫內建監視](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

執行像是設定備份、備份、還原、刪除備份等的作業時，就會產生工作。

下列 Azure 備份解決方案中的工作如下所示：

- Azure VM 備份
- Azure 檔案備份
- Azure 工作負載備份，例如 SQL 和 SAP Hana
- Microsoft Azure 復原服務 (MARS) 代理程式

系統不會顯示 System Center Data Protection Manager (SC-DPM) 、Microsoft Azure 備份 Server (MABS) 中的作業。

> [!NOTE]
> Azure Vm 中的 azure 工作負載（例如 SQL 和 SAP Hana 備份）具有大量的備份作業。 例如，記錄備份可每隔15分鐘執行一次。 因此針對這類資料庫工作負載，只會顯示使用者觸發的作業。 未顯示已排程的備份作業。

## <a name="backup-alerts-in-recovery-services-vault"></a>復原服務保存庫中的備份警示

警示的主要案例是通知使用者，讓他們可以採取相關動作。 [ **備份警示** ] 區段會顯示 Azure 備份服務產生的警示。 這些警示是由服務所定義，而使用者無法自訂建立任何警示。

### <a name="alert-scenarios"></a>警示案例

下列案例是由服務在可提供警示案例中定義。

- 備份/還原失敗
- 備份成功，但 Microsoft Azure 復原服務 (MARS) 代理程式的警告
- 使用刪除資料停止保護保留資料/停止保護

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>下列 Azure 備份解決方案中的警示如下所示

- Azure VM 備份
- Azure 檔案備份
- Azure 工作負載備份，例如 SQL、SAP Hana
- Microsoft Azure 復原服務 (MARS) 代理程式

> [!NOTE]
> System Center Data Protection Manager (SC-DPM) 、Microsoft Azure 備份 Server (MABS) 中的警示不會顯示在此處。

### <a name="consolidated-alerts"></a>合併的警示

針對 Azure 工作負載備份解決方案（例如 SQL 和 SAP Hana），記錄備份可能會根據原則) 產生非常頻繁的 (（最多每隔15分鐘）。 因此，記錄檔備份失敗也可能很頻繁 (每隔15分鐘) 。 在這種情況下，如果每次發生失敗，就會產生一則終端使用者。 因此，系統會在第一次發生時傳送警示，如果較新的失敗原因是因為根本原因相同，則不會產生進一步的警示。 第一個警示會以失敗計數更新。 但是，如果使用者已停用警示，下一次發生的情況將會觸發另一個警示，而這會被視為該發生的第一個警示。 這是 Azure 備份針對 SQL 和 SAP Hana 備份執行警示匯總的方式。

### <a name="exceptions-when-an-alert-is-not-raised"></a>未引發警示時的例外狀況

在失敗時不會引發警示的例外狀況。 分別是：

- 使用者已明確取消正在執行的作業
- 作業失敗，因為另一個備份作業正在進行中 (沒有作用，因為我們只需要等候先前的作業完成) 
- VM 備份作業失敗，因為備份的 Azure VM 已不存在
- [合併的警示](#consolidated-alerts)

上述例外狀況的設計是為了瞭解這些作業的結果 (主要是在入口網站/PS/CLI 用戶端上立即顯示的使用者觸發) 。 如此一來，使用者就可以立即察覺，不需要通知。

### <a name="alert-types"></a>警示類型

根據警示嚴重性，可以在三種類型中定義警示：

- **關鍵**：在主體中，任何備份或復原失敗 (已排程或使用者觸發的) 會導致產生警示，並顯示為重大警示，以及刪除備份之類的破壞性作業。
- **警告**：如果備份作業成功但有幾個警告，則會列為 [警告警示]。 警告警示目前僅適用于 Azure 備份代理程式備份。
- **資訊**：目前 Azure 備份服務未產生任何資訊警示。

## <a name="notification-for-backup-alerts"></a>備份警示的通知

> [!NOTE]
> 通知的設定只能透過 Azure 入口網站完成。 不支援 PS/CLI/REST API/Azure Resource Manager 範本支援。

一旦引發警示，系統就會通知使用者。 Azure 備份透過電子郵件提供內建通知機制。 您可以指定要在產生警示時收到通知的個別電子郵件地址或通訊群組清單。 您也可以選擇是否要收到每個個別警示的通知，或以每小時摘要將其分組，然後獲得通知。

![RS 保存庫內建電子郵件通知](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

當通知設定完成時，您會收到歡迎或簡介電子郵件。 這可確認 Azure 備份可以在產生警示時，將電子郵件傳送至這些位址。<br>

如果頻率設定為每小時的摘要，並在一小時內引發警示並加以解決，它就不會成為即將推出的每小時摘要的一部分。

> [!NOTE]
>
> - 如果執行了破壞性作業（例如 **停止保護與刪除資料** ），則會引發警示，並將電子郵件傳送給訂用帳戶擁有者、系統管理員和共同管理員，即使沒有針對復原服務保存庫設定通知也是一樣。
> - 若要設定成功作業的通知，請使用 [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)。

## <a name="inactivating-alerts"></a>將警示

若要停用/解決作用中的警示，您可以選取對應至您想要停用之警示的清單專案。 這會開啟畫面，顯示警示的詳細資訊，並在頂端顯示 [ **停** 用] 按鈕。 按一下這個按鈕會將警示的狀態變更為「 **非**作用中」。 您也可以用滑鼠右鍵按一下對應至該警示的清單專案，然後選取 [ **停**用]，以停用警示。

![RS 保存庫警示 {0}](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>後續步驟

[使用 Azure 監視器監視 Azure 備份工作負載](backup-azure-monitoring-use-azuremonitor.md)
