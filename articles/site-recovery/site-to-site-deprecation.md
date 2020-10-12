---
title: 在客戶管理的網站 (與 VMM) 使用 Azure Site Recovery 的災難復原淘汰Microsoft Docs
description: 在客戶擁有的網站之間使用 Hyper-v 以及由 SCVMM 管理的網站與其他選項之間的 DR 即將淘汰的詳細資料
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: sharrai
ms.openlocfilehash: 9ffe7a3158b1de6828350947dcf81ef41d08708d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87421836"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>在客戶管理的網站 (使用 Azure Site Recovery 的 VMM) 淘汰災難修復

本文描述即將推出的取代計畫、對應的含意，以及適用于下列案例的客戶可用的替代選項：

由 System Center Virtual Machine Manager (SCVMM) 管理之客戶擁有的網站之間的 DR 使用 Site Recovery

> [!IMPORTANT]
> 建議客戶儘早採取補救步驟，以避免任何環境中斷。 

## <a name="what-changes-should-you-expect"></a>您預期會有哪些變更？

- 從2020年3月開始，您將會收到 Azure 入口網站通知，& 與即將淘汰的 Hyper-v Vm 站對站複寫的電子郵件通訊。 已于2023年3月規劃淘汰。

- 如果您有現有的設定，將不會對設定產生任何影響。

- 一旦已淘汰案例，除非客戶遵循替代方法，否則現有的複製可能會中斷。 客戶將無法透過 Azure 入口網站的 Azure Site Recovery 體驗來查看、管理或執行任何 DR 相關的作業。
 
## <a name="alternatives"></a>替代方案 

以下是客戶可從中選擇的替代方案，以確保當案例淘汰時，DR 策略不會受到影響。 

- 選項 1 (建議的) ：選擇 [開始使用 Azure 做為 DR 目標](hyper-v-vmm-azure-tutorial.md)。


- 選項2：選擇使用基礎 [Hyper-v 複本解決方案](/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)繼續進行站對站複寫，但您將無法使用 Azure 入口網站中的 Azure Site Recovery 來管理 DR 設定。 


## <a name="remediation-steps"></a>補救步驟

如果您選擇使用選項1，請執行下列步驟：

1. [停用所有與 VMMs 相關聯之虛擬機器的保護](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario)。 使用 [ **停** 用複寫] 和 [移除] 選項，或執行提及的腳本，以確保會清除內部部署的複寫設定。 

2. 從站對站複寫設定中[取消註冊所有的 VMM 伺服器](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)。

3. [準備 Azure 資源](tutorial-prepare-azure-for-hyperv.md) 以啟用 vm 的複寫。
4. [準備內部部署 Hyper-V 伺服器](hyper-v-prepare-on-premises-tutorial.md)
5. [為 VMM 雲端中的 Vm 設定複寫](hyper-v-vmm-azure-tutorial.md)
6. 選用，但建議使用： [執行 DR 演練](tutorial-dr-drill-azure.md)

如果您選擇使用 Hyper-v 複本的選項2，請執行下列步驟：

1. 在 [**受保護的專案**] 複寫  >  **專案**中，以滑鼠右鍵按一下電腦，>**停**用複寫]。
2. 在 [ **停**用複寫] 中選取 [ **移除**]。

    這會將複寫的項目從 Azure Site Recovery 移除 (停止計費)。 內部部署虛擬機器上的複寫設定**將不會**遭到清除。 

## <a name="next-steps"></a>接下來的步驟
規劃淘汰，然後選擇最適合您的基礎結構和企業的替代選項。 如果您有任何關於此情況的查詢，請聯繫 Microsoft 支援服務

