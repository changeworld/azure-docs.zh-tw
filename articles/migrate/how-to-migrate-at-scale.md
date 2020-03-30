---
title: 在 Azure 遷移中自動遷移電腦遷移
description: 介紹如何使用腳本遷移 Azure 遷移中的大量電腦
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: 317b6e8aa799b7982e9897c6a504d6092491c7ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196354"
---
# <a name="scale-migration-of-vms"></a>擴展 VM 的遷移 

本文可説明您瞭解如何使用腳本遷移大量虛擬機器 （VM）。 要縮放遷移，請使用[Azure 網站恢復](../site-recovery/site-recovery-overview.md)。 

網站恢復腳本可在 GitHub 上的[Azure PowerShell 示例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery)存儲庫下載。 這些腳本可用於將 VMware、AWS、GCP VM 和物理伺服器遷移到 Azure 中的託管磁片。 如果您以實體伺服器的形式來遷移 VM，您也可以使用這些程式碼來遷移 Hyper-V VM。 [此處](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell)記錄了利用 Azure 網站恢復電源外殼的腳本。

## <a name="current-limitations"></a>目前的限制
- 支援指定靜態 IP 位址僅適用於目標 VM 的主要 NIC
- 指令碼不會採用 Azure Hybrid Benefit 相關輸入，您需要在入口網站中手動更新複寫 VM 的屬性

## <a name="how-does-it-work"></a>運作方式

### <a name="prerequisites"></a>Prerequisites
開始之前，您需要執行下列步驟：
- 確定 Site Recovery 保存庫是在 Azure 訂用帳戶中建立
- 確定組態伺服器和處理序伺服器安裝在來源環境，且保存庫可以探索環境
- 確定複寫原則已建立且與組態伺服器建立關聯
- 確保將 VM 管理員帳戶添加到佈建服務器（將用於複製本地 VM）
- 確保創建 Azure 中的目標專案
    - 目標資源群組
    - 目標存儲帳戶（及其資源組） - 如果您計畫遷移到高級託管磁片，請創建高級存儲帳戶
    - 緩存存儲帳戶（及其資源組） - 在同一區域中創建與保存庫的標準存儲帳戶
    - 用於容錯移轉的虛擬網路 (和其資源群組)
    - 目標子網路
    - 用於測試容錯移轉的虛擬網路 (和其資源群組)
    - 可用性設定組 (若需要)
    - 目標網路安全性群組和其資源群組
- 確定您已決定目標 VM 的內容
    - 目標 VM 名稱
    - Azure 中的目標 VM 大小 (可以使用 Azure Migrate 評量來決定)
    - VM 中主要 NIC 的私人 IP 位址
- 從 GitHub 上的 [Azure PowerShell 範例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery)存放庫下載指令碼

### <a name="csv-input-file"></a>CSV 輸入檔案
完成所有先決條件後，需要創建一個 CSV 檔，該檔包含要遷移的每個源電腦的資料。 輸入 CSV 必須有標頭行 (具有輸入詳細資料)，以及資料列 (具有必須遷移的每一部機器的詳細資料)。 所有指令碼是設計成在相同的 CSV 檔案上運作。 範例 CSV 範本可以在指令碼資料夾中取得，供您參考。

### <a name="script-execution"></a>指令碼執行
CSV 準備就緒之後，您可以執行下列步驟來執行內部部署 VM 的移轉：

**步驟 #** | **指令碼名稱** | **描述**
--- | --- | ---
1 | asr_startmigration.ps1 | 針對 CSV 中列出的所有 VM 啟用複寫，指令碼會建立 CSV 輸出，其中具有每部 VM 的作業詳細資料
2 | asr_replicationstatus.ps1 | 檢查複寫的狀態，指令碼會建立 CSV，其中具有每部 VM 的狀態
3 | asr_updateproperties.ps1 | VM 複寫/受保護之後，使用此指令碼來更新 VM 的目標屬性 (Compute 和 Network 屬性)
4 | asr_propertiescheck.ps1 | 確認屬性是否已適當更新
5 | asr_testmigration.ps1 |  對 CSV 中列出的 VM 啟動測試容錯移轉，指令碼會建立 CSV 輸出，其中具有每部 VM 的作業詳細資料
6 | asr_cleanuptestmigration.ps1 | 一旦您手動驗證 VM 已進行測試容錯移轉，您可以使用此指令碼來清除測試容錯移轉 VM
7 | asr_migration.ps1 | 對 CSV 中列出的 VM 執行非計劃性容錯移轉，指令碼會建立 CSV 輸出，其中具有每部 VM 的作業詳細資料。 對於應用程式一致性，腳本在觸發容錯移轉之前不會關閉內部部署 VM，建議您在執行腳本之前手動關閉 VM。
8 | asr_completemigration.ps1 | 對 VM 執行提交操作並刪除 Azure 網站恢復實體
9 | asr_postmigration.ps1 | 如果您打算將網路安全性群組指派給 NIC 容錯移轉後，您可以使用此指令碼來完成這項操作。 它將 NSG 分配給目標 VM 中的任何一個 NIC。

## <a name="how-to-migrate-to-managed-disks"></a>如何遷移到託管磁片？
預設情況下，該腳本將 VM 遷移到 Azure 中的託管磁片。 如果提供的目標存儲帳戶是高級存儲帳戶，則在遷移後將創建高級管理磁片。 緩存存儲帳戶仍可以是標準帳戶。 如果目標存儲帳戶是標準存儲帳戶，則在遷移後創建標準磁片。 

## <a name="next-steps"></a>後續步驟

[深入了解](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure)使用 Azure Site Recovery 將伺服器遷移至 Azure
