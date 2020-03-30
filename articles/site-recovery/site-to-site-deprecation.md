---
title: 使用 Azure 網站恢復在客戶託管網站（使用 VMM）之間棄用災害復原 |微軟文檔
description: 有關使用 Hyper-V 的客戶擁有的網站之間以及 SCVMM 管理的網站與 Azure 和備用選項之間即將棄用 DR 的詳細資訊
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: rajanaki
ms.openlocfilehash: 208177d10e9002fafe2495710da229541a11a43e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661665"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>使用 Azure 網站恢復在客戶託管網站（使用 VMM）之間棄用災害復原

本文介紹了即將推出的棄用計畫、相應的影響以及適用于以下方案的客戶可用的替代選項：

使用網站恢復的系統中心虛擬機器管理器 （SCVMM） 管理的客戶擁有的網站之間的 DR

> [!IMPORTANT]
> 建議客戶儘早採取補救措施，以避免對其環境造成任何破壞。 

## <a name="what-changes-should-you-expect"></a>您應該期待哪些更改？

- 從 2020 年 3 月開始，您將收到 Azure 門戶通知&電子郵件通信，以及即將棄用的 Hyper-V VM 網站複製到。 棄用計畫于 2023 年 3 月進行。

- 如果您有現有配置，則對設置沒有影響。

- 一旦方案被棄用，除非客戶遵循替代方法，否則現有複製可能會中斷。 客戶將無法通過 Azure 門戶中的 Azure 網站恢復體驗查看、管理或執行任何與 DR 相關的操作。
 
## <a name="alternatives"></a>替代方案 

以下是客戶可以選擇的備選方案，以確保一旦方案被棄用，他們的 DR 策略不會受到影響。 

- 選項 1（建議）：選擇[開始使用 Azure 作為 DR 目標](hyper-v-vmm-azure-tutorial.md)。


- 選項 2：選擇使用基礎[Hyper-V 副本解決方案](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)繼續進行網站到網站複製，但您將無法使用 Azure 門戶中的 Azure 網站恢復管理 DR 配置。 


## <a name="remediation-steps"></a>補救步驟

如果您選擇使用選項 1，請執行以下步驟：

1. [禁用與 VM 關聯的所有虛擬機器的保護](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario)。 使用**禁用複製和刪除**選項或運行上述腳本，以確保清理本地複製設置。 

2. 從網站到網站複製配置[中取消註冊所有 VMM 伺服器](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)。

3. [準備 Azure 資源](tutorial-prepare-azure-for-hyperv.md)以啟用 VM 的複製。
4. [準備內部部署 Hyper-V 伺服器](hyper-v-prepare-on-premises-tutorial.md)
5. [為 VMM 雲中的 VM 設置複製](hyper-v-vmm-azure-tutorial.md)
6. 可選但建議：[運行 DR 鑽](tutorial-dr-drill-azure.md)

如果您選擇使用使用 Hyper-V 副本的選項 2，請執行以下步驟：

1. 在**受保護專案** > **複製項**中，按右鍵電腦>**禁用複製**。
2. 在**禁用複製**中，選擇 **"刪除**"。

    這會將複寫的項目從 Azure Site Recovery 移除 (停止計費)。 內部部署虛擬機器上的複寫設定**將不會**遭到清除。 

## <a name="next-steps"></a>後續步驟
規劃棄用，並選擇最適合您的基礎架構和業務的備用選項。 如果您對此有任何疑問，則聯繫 Microsoft 支援部門

