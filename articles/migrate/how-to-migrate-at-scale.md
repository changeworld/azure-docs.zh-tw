---
title: 在 Azure Migrate 中自動化遷移機器遷移
description: 說明如何使用腳本，在 Azure Migrate 中遷移大量機器
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: c354c1c9dfacfcb6bf84f1140b58deca60c1874e
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109836"
---
# <a name="scale-migration-of-vms"></a>Vm 的規模遷移 

本文可協助您瞭解如何使用腳本來遷移大量的虛擬機器（Vm）。 若要調整遷移，請使用[Azure Site Recovery](../site-recovery/site-recovery-overview.md)。 

您可以在 GitHub 上的[Azure PowerShell 範例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery)存放庫下載 Site Recovery 腳本。 這些腳本可以用來將 VMware、AWS、GCP Vm 和實體伺服器遷移至 Azure 中的受控磁片。 如果您以實體伺服器的形式來遷移 VM，您也可以使用這些程式碼來遷移 Hyper-V VM。 利用 Azure Site Recovery PowerShell 的腳本記載于[此處](../site-recovery/vmware-azure-disaster-recovery-powershell.md)。

## <a name="current-limitations"></a>目前的限制
- 支援指定靜態 IP 位址僅適用於目標 VM 的主要 NIC
- 指令碼不會採用 Azure Hybrid Benefit 相關輸入，您需要在入口網站中手動更新複寫 VM 的屬性

## <a name="how-does-it-work"></a>運作方式

### <a name="prerequisites"></a>必要條件
開始之前，您需要執行下列步驟：
- 確定 Site Recovery 保存庫是在 Azure 訂用帳戶中建立
- 確定組態伺服器和處理序伺服器安裝在來源環境，且保存庫可以探索環境
- 確定複寫原則已建立且與組態伺服器建立關聯
- 請確定您已將 VM 系統管理員帳戶新增至 config 伺服器（這將用來複寫內部部署 Vm）
- 確定已建立 Azure 中的目標成品
    - 目標資源群組
    - 目標儲存體帳戶（和其資源群組）-如果您打算遷移至高階受控磁片，請建立 premium 儲存體帳戶
    - 快取儲存體帳戶（和其資源群組）-在與保存庫相同的區域中建立標準儲存體帳戶
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
完成所有必要條件之後，您必須建立 CSV 檔案，其中包含您想要遷移之每部來源電腦的資料。 輸入 CSV 必須有標頭行 (具有輸入詳細資料)，以及資料列 (具有必須遷移的每一部機器的詳細資料)。 所有指令碼是設計成在相同的 CSV 檔案上運作。 範例 CSV 範本可以在指令碼資料夾中取得，供您參考。

### <a name="script-execution"></a>指令碼執行
CSV 準備就緒之後，您可以執行下列步驟來執行內部部署 VM 的移轉：

**步驟 #** | **腳本名稱** | **描述**
--- | --- | ---
1 | asr_startmigration.ps1 | 針對 CSV 中列出的所有 VM 啟用複寫，指令碼會建立 CSV 輸出，其中具有每部 VM 的作業詳細資料
2 | asr_replicationstatus.ps1 | 檢查複寫的狀態，指令碼會建立 CSV，其中具有每部 VM 的狀態
3 | asr_updateproperties.ps1 | VM 複寫/受保護之後，使用此指令碼來更新 VM 的目標屬性 (Compute 和 Network 屬性)
4 | asr_propertiescheck.ps1 | 確認屬性是否已適當更新
5 | asr_testmigration.ps1 |  對 CSV 中列出的 VM 啟動測試容錯移轉，指令碼會建立 CSV 輸出，其中具有每部 VM 的作業詳細資料
6 | asr_cleanuptestmigration.ps1 | 一旦您手動驗證 VM 已進行測試容錯移轉，您可以使用此指令碼來清除測試容錯移轉 VM
7 | asr_migration.ps1 | 對 CSV 中列出的 VM 執行非計劃性容錯移轉，指令碼會建立 CSV 輸出，其中具有每部 VM 的作業詳細資料。 在觸發容錯移轉之前，腳本不會關閉內部部署 Vm，針對應用程式一致性，建議您先手動關閉 Vm，然後再執行腳本。
8 | asr_completemigration.ps1 | 在 Vm 上執行認可作業，並刪除 Azure Site Recovery 的實體
9 | asr_postmigration.ps1 | 如果您打算將網路安全性群組指派給 NIC 容錯移轉後，您可以使用此指令碼來完成這項操作。 它會將 NSG 指派給目標 VM 中的任何一個 NIC。

## <a name="how-to-migrate-to-managed-disks"></a>如何遷移至受控磁片？
根據預設，此腳本會將 Vm 遷移至 Azure 中的受控磁片。 如果提供的目標儲存體帳戶是 premium 儲存體帳戶，則會在遷移後建立高階受控磁片。 快取儲存體帳戶仍然可以是標準帳戶。 如果目標儲存體帳戶是標準儲存體帳戶，則會在遷移後建立標準磁片。 

## <a name="next-steps"></a>後續步驟

[深入了解](../site-recovery/migrate-tutorial-on-premises-azure.md)使用 Azure Site Recovery 將伺服器遷移至 Azure
