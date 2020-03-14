---
title: 監視 Azure 備份受保護的工作負載
description: 在本文中，您將瞭解使用 Azure 入口網站 Azure 備份工作負載的監視和通知功能。
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: ea5102a95a9bef17f25219e00dec4654bf7f06d6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273367"
---
# <a name="monitoring-azure-backup-workloads"></a>監視 Azure 備份工作負載

Azure 備份根據備份需求和基礎結構拓撲（內部部署與 Azure）提供多個備份解決方案。 所有的備份使用者或系統管理員應該都會看到所有解決方案的情況，而且預期會在重要案例中收到通知。 本文詳細說明 Azure 備份服務所提供的監視和通知功能。

## <a name="backup-jobs-in-recovery-services-vault"></a>復原服務保存庫中的備份作業

Azure 備份針對受 Azure 備份保護的工作負載，提供內建的監視和警示功能。 在復原服務保存庫設定中，[**監視**] 區段會提供內建的作業和警示。

![RS 保存庫內建監視](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

當執行諸如設定備份、備份、還原、刪除備份等作業時，就會產生工作。

下列 Azure 備份解決方案的作業如下所示：

- Azure VM 備份
- Azure 檔案備份
- Azure 工作負載備份，例如 SQL
- Azure 備份代理程式 (MAB)

系統不會顯示來自 System Center Data Protection Manager （SC-DPM）、Microsoft Azure 備份 Server （MABS）的作業。

> [!NOTE]
> Azure 工作負載（例如 Azure Vm 中的 SQL 備份）有大量的備份作業。 例如，記錄備份可以每隔15分鐘執行一次。 因此，針對這類資料庫工作負載，只會顯示使用者觸發的作業。 不會顯示已排程的備份作業。

## <a name="backup-alerts-in-recovery-services-vault"></a>復原服務保存庫中的備份警示

警示主要是通知使用者，讓他們可以採取相關動作的情況。 [**備份警示**] 區段會顯示 Azure 備份服務所產生的警示。 這些警示是由服務所定義，使用者無法自訂建立任何警示。

### <a name="alert-scenarios"></a>警示案例

下列案例是由服務定義為打斷案例。

- 備份/還原失敗
- 備份成功但有 Azure 備份代理程式 (MAB) 的警告
- 使用 [刪除資料] 停止保護 [保留資料]/[停止保護]

### <a name="exceptions-when-an-alert-is-not-raised"></a>未引發警示時的例外狀況

當警示不會在失敗時引發時，有幾個例外狀況，它們是：

- 使用者已明確取消正在執行的作業
- 作業失敗，因為另一個備份作業正在進行中（在此不會採取任何動作，因為我們只需要等待前一個作業完成）
- VM 備份作業失敗，因為備份的 Azure VM 已不存在

上述例外狀況的設計來自于瞭解這些作業的結果（主要由使用者觸發）會立即顯示在入口網站/PS/CLI 用戶端上。 因此，使用者會立即察覺並不需要通知。

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>下列 Azure 備份解決方案的警示如下所示

- Azure VM 備份
- Azure 檔案備份
- SQL 之類的 Azure 工作負載備份
- Azure 備份代理程式 (MAB)

> [!NOTE]
> 這裡不會顯示來自 System Center Data Protection Manager （SC-DPM）、Microsoft Azure 備份 Server （MABS）的警示。

### <a name="alert-types"></a>警示類型

根據警示嚴重性，警示可以定義為下列三種類型：

- **重大**：原則是，任何備份或復原失敗（已排程或使用者觸發）都會導致產生警示，並顯示為重大警示，以及破壞性作業，例如刪除備份。
- **警告**：如果備份作業成功，但有幾個警告，則會列為警告警示。
- **資訊**：從今天起，Azure 備份服務不會產生任何資訊警示。

## <a name="notification-for-backup-alerts"></a>備份警示的通知

> [!NOTE]
> 通知設定只能透過 Azure 入口網站來完成。 不支援 PS/CLI/REST API/Azure Resource Manager 範本支援。

一旦產生警示，就會通知使用者。 Azure 備份透過電子郵件提供內建通知機制。 其中一個可以指定個別的電子郵件地址或通訊群組清單，以便在產生警示時收到通知。 您也可以選擇是否要取得每個個別警示的通知，或以每小時摘要將其分組，然後收到通知。

![RS 保存庫內建電子郵件通知](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

> [!NOTE]
> SQL 備份的警示將會合並，而且只會在第一次出現時傳送電子郵件。 但是，如果使用者已停用警示，則下一次發生時將會觸發另一封電子郵件。

當設定通知時，您會收到歡迎或簡介電子郵件。 這會確認當警示引發時，Azure 備份可以傳送電子郵件給這些位址。<br>

如果 [頻率] 設定為 [每小時摘要]，而且在一小時內引發警示並加以解決，則不會成為即將到來的每小時摘要的一部分。

> [!NOTE]
>
> - 如果執行**具有刪除資料的停止保護**之類的破壞性作業，則會產生警示，並將電子郵件傳送給訂用帳戶擁有者、系統管理員和共同管理員，即使未針對復原服務保存庫設定通知也一樣。
> - 若要設定成功作業的通知，請使用[Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)。

## <a name="inactivating-alerts"></a>將警示

若要停用/解決作用中警示，您可以按一下對應至您想要停用之警示的清單專案。 這會開啟一個畫面，顯示有關警示的詳細資訊，並在頂端出現 [停用] 按鈕。 按一下此按鈕會將警示的狀態變更為「非作用中」。 您也可以在與該警示對應的清單專案上按一下滑鼠右鍵，然後選取 [停用]，以停用警示。

![RS Vault 警示](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>後續步驟

[使用 Azure 監視器監視 Azure 備份工作負載](backup-azure-monitoring-use-azuremonitor.md)
