---
title: 使用 Azure 遷移伺服器遷移選擇 VMware 遷移選項 |微軟文檔
description: 提供使用 Azure 遷移伺服器遷移將 VMware VM 遷移到 Azure 的選項概述
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 52e7103ea3ebcd83369a866cc3f75b0bf0e889a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028725"
---
# <a name="select-a-vmware-migration-option"></a>選擇 VMware 遷移選項

您可以使用 Azure Migrate 伺服器移轉工具將 VMware VM 遷移至 Azure。 此工具為 VMware VM 移轉提供幾個選項：

- 使用無代理程式複寫進行移轉。 無須在 VM 上安裝任何項目即可遷移 VM。
- 使用代理程式複寫來進行移轉。 在 VM 上安裝代理程式以進行複寫。




## <a name="compare-migration-methods"></a>比較遷移方法

使用這些選定的比較，以説明您決定使用哪種方法。 您還可以查看[無代理](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)和[基於代理的](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers)遷移的完整支援要求。

**設定** | **無代理程式** | **基於代理**
--- | --- | ---
**Azure 權限** | 您需要許可權才能創建 Azure 遷移專案，並註冊在部署 Azure 遷移應用時創建的 Azure AD 應用。 | 需要 Azure 訂閱上的參與者許可權。 
**同時複製** | 最多可以從 vCenter 伺服器複製 100 個 VM。<br/> 如果您有 50 多個 VM 進行遷移，請創建多批 VM。<br/> 一次複製更多將影響性能。 | NA
**設備部署** | [Azure 遷移設備](migrate-appliance.md)部署在本地。 | [Azure 遷移複製設備](migrate-replication-appliance.md)部署在本地。
**網站恢復相容性** | 相容。 | 如果已使用網站恢復為電腦設置複製設置複製，則無法使用 Azure 遷移伺服器遷移進行複製。
**目標磁片** | 受控磁碟 | 受控磁碟
**磁片限制** | OS 磁片： 2 TB<br/><br/> 資料磁片： 4 TB<br/><br/> 最大磁片數： 60 | OS 磁片： 2 TB<br/><br/> 資料磁片： 8 TB<br/><br/> 最大磁片數： 63
**直通磁片** | 不支援 | 支援
**UEFI 啟動** | 不支援 | Azure 中遷移的 VM 將自動轉換為 BIOS 引導 VM。<br/><br/> 作業系統磁片應最多具有四個分區，並且卷應使用 NTFS 進行格式化。


## <a name="deployment-steps-comparison"></a>部署步驟比較

在查看限制後，瞭解部署每個解決方案所涉及的步驟可以説明您決定選擇哪個選項。

**任務** | **詳細資料** |**無代理程式** | **基於代理**
--- | --- | --- | ---
**評量** | 在遷移之前評估伺服器。  評估是可選的。 我們建議您在遷移機器之前對其進行評估，但不必評估它們。 <br/><br/> 為了進行評估，Azure 遷移設置一個羽量級設備來發現和評估 VM。 | 如果在評估後運行無代理遷移，則為評估設置的相同 Azure 遷移設備將用於無代理遷移。  |  如果在評估後運行基於代理的遷移，則在無代理遷移期間不使用為評估設置的設備。 如果不想執行進一步的發現和評估，您可以將產品保留到位，或者將其卸下。
**準備 VMware 伺服器和 VM 進行遷移** | 在 VMware 伺服器和 VM 上配置多個設置。 | 必要 | 必要
**添加伺服器遷移工具** | 在 Azure 遷移專案中添加 Azure 遷移伺服器遷移工具。 | 必要 | 必要
**部署 Azure 遷移設備** | 在 VMware VM 上設置羽量級設備，用於 VM 發現和評估。 | 必要 | 不需要。
**在 VM 上安裝移動服務** | 在要複製的每個 VM 上安裝移動服務 | 不需要 | 必要
**部署 Azure 遷移伺服器遷移複製設備** | 在 VMware VM 上設置設備以發現 VM，並在 VM 上運行的移動服務與 Azure 遷移伺服器遷移之間架橋 | 不需要 | 必要
**複製 VM**。 啟用 VM 複寫。 | 配置複製設置並選擇要複製的 VM | 必要 | 必要
**執行測試移轉** | 執行測試移轉，確定一切都沒問題。 | 必要 | 必要
**運行完整遷移** | 遷移 VM。 | 必要 | 必要




## <a name="next-steps"></a>後續步驟

使用無代理遷移[遷移 VMware VM。](tutorial-migrate-vmware.md)



