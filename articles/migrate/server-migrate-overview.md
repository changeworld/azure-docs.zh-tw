---
title: 使用 Azure Migrate Server 遷移來選取 VMware 遷移選項 |Microsoft Docs
description: 提供使用 Azure Migrate Server 遷移將 VMware Vm 遷移至 Azure 的選項總覽
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 52e7103ea3ebcd83369a866cc3f75b0bf0e889a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76028725"
---
# <a name="select-a-vmware-migration-option"></a>選取 VMware 遷移選項

您可以使用 Azure Migrate 伺服器移轉工具將 VMware VM 遷移至 Azure。 此工具為 VMware VM 移轉提供幾個選項：

- 使用無代理程式複寫進行移轉。 無須在 VM 上安裝任何項目即可遷移 VM。
- 使用代理程式複寫來進行移轉。 在 VM 上安裝代理程式以進行複寫。




## <a name="compare-migration-methods"></a>比較遷移方法

使用這些選取的比較，協助您決定要使用的方法。 您也可以查看[無代理](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)[程式和代理](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers)程式為基礎之遷移的完整支援需求。

**設定** | **無代理程式** | **以代理程式為基礎**
--- | --- | ---
**Azure 權限** | 您需要建立 Azure Migrate 專案的許可權，並註冊在部署 Azure Migrate 應用裝置時所建立 Azure AD 應用程式。 | 您需要 Azure 訂用帳戶的參與者許可權。 
**同時複寫** | 最多可同時從 vCenter Server 複寫100部 Vm。<br/> 如果您有超過50個 Vm 可供遷移，請建立多個 Vm 批次。<br/> 一次複寫更多會影響效能。 | NA
**設備部署** | [Azure Migrate 設備](migrate-appliance.md)已部署在內部部署環境中。 | [Azure Migrate 複寫應用裝置](migrate-replication-appliance.md)會部署在內部部署環境中。
**Site Recovery 相容性** | 性. | 如果您已使用 Site Recovery 設定機器的複寫，則無法使用 Azure Migrate Server 遷移進行複寫。
**目標磁片** | 受控磁碟 | 受控磁碟
**磁片限制** | OS 磁片： 2 TB<br/><br/> 資料磁片： 4 TB<br/><br/> 磁片上限：60 | OS 磁片： 2 TB<br/><br/> 資料磁片： 8 TB<br/><br/> 磁片上限：63
**傳遞磁片** | 不支援 | 支援
**UEFI 開機** | 不支援 | Azure 中已遷移的 VM 會自動轉換為 BIOS 開機 VM。<br/><br/> OS 磁片應該有最多四個磁碟分割，而且磁片區應該使用 NTFS 格式化。


## <a name="deployment-steps-comparison"></a>部署步驟比較

在檢查限制之後，瞭解部署每個解決方案的步驟可能有助於您決定要選擇哪一個選項。

**Task** | **詳細資料** |**無代理程式** | **以代理程式為基礎**
--- | --- | --- | ---
**評量** | 在遷移前評估伺服器。  評量是選擇性的。 我們建議您先評估機器，再進行遷移，但您不需要這麼做。 <br/><br/> 針對評估，Azure Migrate 設定輕量設備來探索及評估 Vm。 | 如果您在評量之後執行無代理程式遷移，則會使用為評估設定的相同 Azure Migrate 設備來進行無代理程式遷移。  |  如果您在評量之後執行以代理程式為基礎的遷移，則不會在無代理程式遷移期間使用設定進行評估的設備。 如果您不想執行進一步的探索和評估，可以將設備保留在原處，或將它移除。
**準備 VMware 伺服器和 Vm 以進行遷移** | 在 VMware 伺服器和 Vm 上設定一些設定。 | 必要 | 必要
**新增伺服器移轉工具** | 在 Azure Migrate 專案中加入 Azure Migrate 伺服器遷移工具。 | 必要 | 必要
**部署 Azure Migrate 設備** | 在 VMware VM 上設定輕量設備，以進行 VM 探索和評估。 | 必要 | 不需要。
**在 Vm 上安裝行動服務** | 在您要複寫的每個 VM 上安裝行動服務 | 不需要 | 必要
**部署 Azure Migrate Server 遷移複寫設備** | 在 VMware VM 上設定設備以探索 Vm，並在 Vm 上執行的行動服務與 Azure Migrate 伺服器遷移之間橋接 | 不需要 | 必要
複寫**vm**。 啟用 VM 複寫。 | 設定複寫設定並選取要複寫的 Vm | 必要 | 必要
**執行測試移轉** | 執行測試移轉，確定一切都沒問題。 | 必要 | 必要
**執行完整的遷移** | 遷移 Vm。 | 必要 | 必要




## <a name="next-steps"></a>後續步驟

遷移具有無代理程式遷移的[VMware vm](tutorial-migrate-vmware.md) 。



