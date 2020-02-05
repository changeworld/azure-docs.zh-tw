---
title: 關於 MARS 代理程式
description: 瞭解 MARS 代理程式如何支援備份案例
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 348980b840b814c09ce46627f286489d4caa9f8f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023951"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>關於 Microsoft Azure 復原服務（MARS）代理程式

本文說明 Azure 備份服務如何使用 Microsoft Azure 復原服務（MARS）代理程式，將檔案、資料夾和磁片區或系統狀態從內部部署電腦備份和還原至 Azure。

MARS 代理程式支援下列備份案例：

![MARS 備份案例](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- 檔案**和資料夾**：選擇性地保護 Windows 檔案和資料夾。
- **磁片區層級**：保護電腦的整個 Windows 磁片區。
- **系統層級**：保護整個 Windows 系統狀態。

MARS 代理程式支援下列還原案例：

![MARS 復原案例](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

-   **相同的伺服器**：原先用來建立備份的伺服器。
    -    檔案**和資料夾**：選擇您想要還原的個別檔案和資料夾。
    -    **磁片區層級**：選擇您想要還原的磁片區和復原點，然後將它還原到相同電腦上的相同位置或替代位置。  建立現有檔案的複本、覆寫現有的檔案，或略過復原現有的檔案。
    -    **系統層級**：選擇 [系統狀態] 和 [復原點]，在指定的位置還原至同一部電腦。


-   **替代伺服器**：執行備份之伺服器以外的伺服器。
    -    檔案**和資料夾**：選擇您想要還原至目的電腦之復原點的個別檔案和資料夾。
    -    **磁片區層級**：選擇您想要還原到其他位置的磁片區和復原點。 建立現有檔案的複本、覆寫現有的檔案，或略過復原現有的檔案。
    -    **系統層級**：選擇 [系統狀態] 和 [復原點]，將系統狀態檔案還原至其他電腦。

## <a name="backup-process"></a>備份程序

1. 從 Azure 入口網站建立復原服務保存[庫](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-recovery-services-vault)，並從備份目標選擇檔案、資料夾和系統狀態。
2. 將復原[服務保存庫認證和代理程式安裝程式下載](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent)到內部部署機器。 

    若要保護內部部署機器，請選取 [備份] 選項，選擇 [檔案]、[資料夾] 和 [系統狀態]，然後下載 MARS 代理程式。

3. 準備基礎結構：

    a. 執行安裝程式以[安裝代理程式](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent)。

    b. 使用您已下載的保存庫認證，將機器註冊到復原服務保存庫。
4. 從用戶端上的代理程式主控台，[設定備份](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-backup-policy)。 指定備份資料的保留原則，以開始保護它。

![Azure 備份代理程式圖表](./media/backup-try-azure-backup-in-10-mins/backup-process.png)


### <a name="additional-scenarios"></a>其他案例
-   **備份 azure 虛擬機器中的特定檔案和資料夾**：備份 azure 虛擬機器（vm）的主要方法是使用 VM 上的 Azure 備份延伸模組。 延伸模組會備份整個 VM。 如果您想要備份 VM 中的特定檔案和資料夾，您可以在 Azure Vm 中安裝 MARS 代理程式。 如需詳細資訊，請參閱[架構：內建的 AZURE VM 備份](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup)。

-   **離線植**入：資料至 Azure 的初始完整備份通常會傳輸大量資料，而且需要更多的網路頻寬。 後續備份只會傳輸差異或累加的資料量。 Azure 備份會壓縮初始備份。 透過*離線植*入的程式，Azure 備份可以使用磁片，將壓縮的初始備份資料離線上傳至 Azure。 如需詳細資訊，請參閱[使用 Azure 資料箱 Azure 備份離線備份](offline-backup-azure-data-box.md)。

## <a name="next-steps"></a>後續步驟
[MARS 代理程式支援矩陣](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[MARS 代理程式常見問題](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
