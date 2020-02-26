---
title: 使用 Azure Site Recovery 在客戶管理的網站（含 VMM）之間淘汰嚴重損壞修復Microsoft Docs
description: 有關使用 Hyper-v 在客戶擁有的網站與從 SCVMM 管理的網站之間的 DR 即將淘汰的詳細資料，以及其他選項
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: rajanaki
ms.openlocfilehash: 29a939452d9b90bd8afda7db4e115d10956ee5e5
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77606629"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>使用 Azure Site Recovery 在客戶管理的網站（與 VMM）之間淘汰嚴重損壞修復

本文說明即將推出的淘汰計畫、對應的含意，以及適用于下列案例之客戶的替代選項：

使用 Site Recovery System Center Virtual Machine Manager （SCVMM）管理客戶擁有的網站之間的 DR

> [!IMPORTANT]
> 建議客戶儘早採取補救步驟，以避免對其環境造成任何中斷。 

## <a name="what-changes-should-you-expect"></a>您預期會有哪些變更？

- 從2019年11月開始，這些案例不會允許 boardings 的新使用者。 **現有的複寫和管理**作業，包括容錯移轉、測試容錯移轉、監視等，**將不會受到影響**。

- 如果您有現有的設定，您將無法註冊新的 VMMs。

- 一旦淘汰案例之後，除非客戶遵循其他方法，否則現有的複寫可能會中斷。 客戶無法透過 Azure 入口網站中的 Azure Site Recovery 體驗，來查看、管理或執行任何 DR 相關的作業。
 
## <a name="alternatives"></a>替代方案 

以下是客戶可以選擇的替代方案，以確保在案例淘汰之後，其 DR 策略不會受到影響。 

- 選項1（建議）：選擇[開始使用 Azure 做為 hyper-v 主機上 vm 的 DR 目標](hyper-v-azure-tutorial.md)。

    > [!IMPORTANT]
    > 請注意，您的內部部署環境仍然可以有 SCVMMM，但您會將僅限 Hyper-v 主機的參考設定為 ASR。

- 選項2：選擇使用基礎[Hyper-v 複本解決方案](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)繼續進行站對站複寫，但是您將無法使用 Azure 入口網站中的 Azure Site Recovery 來管理 DR 設定。 


## <a name="remediation-steps"></a>補救步驟

如果您選擇使用選項1，請執行下列步驟：

1. [停用所有與 VMMs 相關聯之虛擬機器的保護](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario)。 使用 [**停**用複寫] 和 [移除] 選項，或執行所述的腳本，以確保會清除內部部署的複寫設定。 

2. [取消註冊所有 VMM 伺服器](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)

3. [準備 Azure 資源](tutorial-prepare-azure-for-hyperv.md)以啟用 vm 的複寫。
4. [準備內部部署 Hyper-v 伺服器](hyper-v-prepare-on-premises-tutorial.md)

> [!IMPORTANT]
> 請注意，您不需要執行 [準備 VMM] 底下的步驟。

5. [設定 Vm 的複寫](hyper-v-azure-tutorial.md)
6. 選擇性但建議使用：[執行 DR 演練](tutorial-dr-drill-azure.md)

如果您選擇使用 [Hyper-v 複本] 的選項2，請執行下列步驟：

1. 在 [受保護的項目] > [已複寫的項目] 中，以滑鼠右鍵按一下機器 > [停用複寫]。
2. 在 [**停**用複寫] 中，選取 [**移除**]。

    這會將複寫的項目從 Azure Site Recovery 移除 (停止計費)。 內部部署虛擬機器上的複寫設定**將不會**遭到清除。 

## <a name="next-steps"></a>後續步驟
規劃淘汰，並選擇最適合您的基礎結構和業務的替代選項。 如果您有任何關於此情況的查詢，請與 Microsoft 支援服務

