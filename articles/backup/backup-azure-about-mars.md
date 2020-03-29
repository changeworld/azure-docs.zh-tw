---
title: 關於 MARS 代理
description: 瞭解 MARS 代理如何支援備份方案
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2cc8e32152f6930c9c250e2811668cc2c924616
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673284"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>關於微軟 Azure 恢復服務 （MARS） 代理

本文介紹 Azure 備份服務如何使用 Microsoft Azure 恢復服務 （MARS） 代理從本地電腦備份和還原檔、資料夾以及卷或系統狀態到 Azure。

MARS 代理支援以下備份方案：

![MARS 備份方案](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **檔和資料夾**：有選擇地保護 Windows 檔和資料夾。
- **卷級別**：保護電腦的整個 Windows 卷。
- **系統級別**：保護整個 Windows 系統狀態。

MARS 代理支援以下還原方案：

![MARS 恢復方案](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **同一伺服器**：最初創建備份的伺服器。
  - **檔和資料夾**：選擇要還原的單個檔和資料夾。
  - **音量級別**：選擇要還原的卷和復原點，然後將其還原到同一台電腦上的同一位置或備用位置。  創建現有檔的副本，覆蓋現有檔，或跳過恢復現有檔。
  - **系統級別**：選擇系統狀態和復原點以在指定位置還原到同一台電腦。

- **待命伺服器**：執行備份的伺服器以外的伺服器。
  - **檔和資料夾**：選擇要還原到目的電腦的復原點的各個檔和資料夾。
  - **卷級別**：選擇要還原到另一個位置的卷和復原點。 創建現有檔的副本，覆蓋現有檔，或跳過恢復現有檔。
  - **系統級別**：選擇系統狀態和復原點以還原為系統狀態檔到備用電腦。

## <a name="backup-process"></a>備份程序

1. 從 Azure 門戶創建[恢復服務保存庫](install-mars-agent.md#create-a-recovery-services-vault)，並從備份目標中選擇檔、資料夾和系統狀態。
2. [將恢復服務保存庫憑據和代理安裝程式下載](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent)到本地電腦。

    要通過選擇"備份"選項保護本地電腦，請選擇檔、資料夾和系統狀態，然後下載 MARS 代理。

3. 準備基礎結構：

    a. 運行安裝程式以[安裝代理](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent)。

    b. 使用下載的保存庫憑據將電腦註冊到恢復服務保存庫。
4. 從用戶端上的代理主控台配置[備份](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy)。 指定備份資料的保留原則以開始保護它。

![Azure 備份代理圖](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-scenarios"></a>其他案例

- **備份 Azure 虛擬機器中的特定檔和資料夾**：備份 Azure 虛擬機器 （VM） 的主要方法是在 VM 上使用 Azure 備份副檔名。 擴展備份整個 VM。 如果要在 VM 中備份特定檔和資料夾，可以在 Azure VM 中安裝 MARS 代理。 有關詳細資訊，請參閱[體系結構：內置 Azure VM 備份](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup)。

- **離線種子設定**：將資料的初始完整備份到 Azure 通常傳輸大量資料，並需要更多的網路頻寬。 後續備份僅傳輸增量或增量資料量。 Azure 備份會壓縮初始備份。 通過*離線種子設定*過程，Azure 備份可以使用磁片將壓縮的初始備份資料離線上載到 Azure。 有關詳細資訊，請參閱使用[Azure 資料框 進行 Azure 備份離線備份](offline-backup-azure-data-box.md)。

## <a name="next-steps"></a>後續步驟

[MARS 代理程式支援矩陣](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[MARS 代理常見問題解答](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
