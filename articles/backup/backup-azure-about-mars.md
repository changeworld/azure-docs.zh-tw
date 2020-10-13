---
title: 關於 MARS 代理程式
description: 瞭解 MARS 代理程式如何支援備份案例
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 8e4ace0c17dbe75e989981db56583ed9477b3716
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87562594"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>關於 Microsoft Azure 復原服務 (MARS) 代理程式

本文說明 Azure 備份服務如何使用 Microsoft Azure 復原服務 (MARS) 代理程式來備份和還原檔案、資料夾，以及從內部部署電腦將磁片區或系統狀態備份至 Azure。

## <a name="backup-scenarios"></a>備份案例

MARS 代理程式支援下列備份案例：

![MARS 備份案例](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- 檔案**和資料夾**：選擇性地保護 Windows 檔案和資料夾。
- **磁片區層級**：保護電腦的整個 Windows 磁片區。
- **系統層級**：保護整個 Windows 系統狀態。

### <a name="additional-scenarios"></a>其他案例

- **備份 azure 虛擬機器中的特定檔案和資料夾**： (Vm 備份 Azure 虛擬機器的主要方法) 是在 vm 上使用 Azure 備份擴充功能。 延伸模組會備份整個 VM。 如果您想要備份 VM 內的特定檔案和資料夾，您可以在 Azure Vm 中安裝 MARS 代理程式。 如需詳細資訊，請參閱 [架構：內建的 AZURE VM 備份](./backup-architecture.md#architecture-built-in-azure-vm-backup)。

- **離線植**入：將資料備份至 Azure 的初始完整備份通常會傳輸大量資料，而且需要更多的網路頻寬。 後續的備份只會傳送差異或增量資料量。 Azure 備份會壓縮初始備份。 透過 *離線植*入程式，Azure 備份可以使用磁片，將壓縮的初始備份資料從離線上傳至 Azure。 如需詳細資訊，請參閱 [使用 Azure 資料箱 Azure 備份離線備份](offline-backup-azure-data-box.md)。

## <a name="restore-scenarios"></a>還原案例

MARS 代理程式支援下列還原案例：

![MARS 復原案例](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **相同伺服器**：原先建立備份的伺服器。
  - 檔案**和資料夾**：選擇您要還原的個別檔案和資料夾。
  - **磁片區層級**：選擇您要還原的磁片區和復原點。 然後將它還原到相同的位置或同一部電腦上的替代位置。  建立現有檔案的複本、覆寫現有的檔案，或略過復原現有的檔案。
  - **系統層級**：選擇要在指定位置還原到同一部電腦的系統狀態和復原點。

- **替代伺服器**：執行備份的伺服器以外的伺服器。
  - 檔案**和資料夾**：選擇您想要還原到目的電腦之復原點的個別檔案和資料夾。
  - **磁片區層級**：選擇您要還原到另一個位置的磁片區和復原點。 建立現有檔案的複本、覆寫現有的檔案，或略過復原現有的檔案。
  - **系統層級**：選擇要以系統狀態檔案的形式還原至替代電腦的系統狀態和復原點。

## <a name="backup-process"></a>備份程序

1. 從 Azure 入口網站建立復原服務保存 [庫](install-mars-agent.md#create-a-recovery-services-vault)，並從 **備份目標**選擇檔案、資料夾和系統狀態。
2. 將復原[服務保存庫認證和代理程式安裝程式下載](./install-mars-agent.md#download-the-mars-agent)到內部部署機器。

3. [安裝代理程式](./install-mars-agent.md#install-and-register-the-agent) ，並使用已下載的保存庫認證向復原服務保存庫註冊電腦。
4. 從用戶端上的代理程式主控台， [將備份](./backup-windows-with-mars-agent.md#create-a-backup-policy) 設定為指定要備份的內容、何時要備份 (排程) 、備份應保留在 Azure 中的時間長度 (保留原則) 並開始保護。

![Azure 備份代理程式圖表](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-information"></a>其他資訊

- **初始備份** (第一個備份) 會根據您的備份設定來執行。  MARS 代理程式會使用 VSS 來取得選取要備份之磁片區的時間點快照集。 代理程式只會使用 Windows 系統寫入器操作來捕捉快照集。 它不會使用任何應用程式 VSS 寫入器，且不會捕捉應用程式一致的快照集。 使用 VSS 建立快照集之後，MARS 代理程式會在您設定備份時所指定的快取資料夾中，建立 (VHD) 的虛擬硬碟。 代理程式也會儲存每個資料區塊的總和檢查碼。

- **增量備份** (後續的備份) 根據您指定的排程執行。 在增量備份期間，會識別已變更的檔案並建立新的 VHD。 VHD 會經過壓縮和加密，然後傳送至保存庫。 增量備份完成後，新的 VHD 會與初始複寫之後建立的 VHD 合併。 這個合併的 VHD 會提供最新的狀態，以用於比較進行中的備份。

- MARS 代理程式可以透過掃描整個磁片區來檢查目錄或 **檔案中的** 變更，以使用 USN (更新序號) 變更日誌或未 **優化模式** 執行備份作業。 未優化模式的速度較慢，因為代理程式必須掃描磁片區上的每個檔案，並將它與中繼資料進行比較，以判斷變更的檔案。  **初始備份**一律會以未優化的模式執行。 如果先前的備份失敗，則下一個排定的備份工作將會以未優化的模式執行。 若要深入瞭解這些模式以及如何進行驗證，請參閱 [這篇文章](backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-job-running-in-unoptimized-mode)。

## <a name="next-steps"></a>後續步驟

[MARS 代理程式支援矩陣](./backup-support-matrix-mars-agent.md)

[MARS 代理程式常見問題](./backup-azure-file-folder-backup-faq.md)
