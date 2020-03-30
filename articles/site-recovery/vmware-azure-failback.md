---
title: 通過 Azure 網站恢復，從 Azure 故障恢復 VMware VM/物理伺服器
description: 了解如何在從 VMware VM 和實體伺服器至 Azure 的災害復原期間，在容錯移轉至 Azure 後容錯回復至內部部署網站。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: cd4cc90fb102d517a47ba458619e22b8921dd498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495319"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>將 VMware VM 故障退到本地網站

本文介紹如何在本地 VM[容錯移轉](site-recovery-failover.md)到 Azure 後，使用[Azure 網站恢復](site-recovery-overview.md)將 Azure 容錯移轉，將 Azure VM 故障退回本地網站。 故障回退到本地後，可以啟用複製，以便本地 VM 開始複製到 Azure。

## <a name="before-you-start"></a>開始之前

1. 瞭解[VMware 故障恢復](failover-failback-overview.md#vmwarephysical-reprotectionfailback)。 
2. 請確保您已查看並完成了[準備故障倒退](vmware-azure-prepare-failback.md)的步驟，並部署了所有必需的元件。 元件包括 Azure 中的進程伺服器、本地主目標伺服器以及用於故障倒機的 VPN 網站到網站連接（或 ExpressRoute 專用對等互連）。
3. 請確保已完成重新保護和故障恢復[的要求](vmware-azure-reprotect.md#before-you-begin)，並且已啟用 Azure VM 的[重新保護](vmware-azure-reprotect.md#enable-reprotection)，以便它們從 Azure 複製到本地網站。 VM 必須處於複製狀態才能失敗。




## <a name="run-a-failover-to-fail-back"></a>運行容錯移轉以故障恢復

1. 確保重新保護 Azure VM 並將其複製到本地網站。 
    - VM 至少需要一個復原點才能故障。
    - 如果恢復計畫失敗，則計畫中的所有電腦應至少具有一個復原點。
2. 在保存庫>**複製的專案**中，選擇 VM。 按右鍵 vm >**計畫外容錯移轉**。
3. 在 [確認容錯移轉]**** 中，確認容錯移轉方向 (以 Azure 為來源)。
4. 選取您要用於容錯移轉的復原點。
    - 我們建議您使用**最新的**復原點。 應用一致性點落後于最新時間點，並導致一些資料丟失。
    - **最新**是一個崩潰一致的復原點。
    - 使用 **"最新"** 時，VM 將容錯移轉到其最新的可用時間點。 如果恢復計畫中具有多 VM 一致性的複製組，則組中的每個 VM 都失敗到其獨立的最新時間點。
    - 如果使用應用一致的復原點，則每個 VM 都失敗回其最新可用點。 如果恢復計畫具有複製組，則每個組將恢復到其公共可用復原點。
5. 容錯移轉開始。 網站恢復將關閉 Azure VM。
6. 容錯移轉完成後，檢查一切按預期工作。 檢查 Azure VM 已關閉。 
7. 驗證所有內容後，按右鍵 VM >**提交**，以完成容錯移轉過程。 提交將刪除失敗的溢出 Azure VM。 

> [!NOTE]
> 對於 Windows VM，網站恢復在容錯移轉期間禁用 VMware 工具。 在 Windows VM 的故障退退期間，VMware 工具將再次啟用。 




## <a name="reprotect-from-on-premises-to-azure"></a>從內部部署移至 Azure 來重新保護

提交故障回退後，Azure VM 將被刪除。 VM 已返回本地網站，但不受保護。 要再次將 VM 複製到 Azure，如下所示：

1. 在保存庫>**複製的專案**中，選擇失敗的回 VM，然後選擇 **"重新保護**"。
2. 指定用於將資料發送回 Azure 的進程伺服器。
3. 選取 [確定]**** 以開始重新保護作業。

> [!NOTE]
> 本地 VM 啟動後，代理最多需要 15 分鐘才能註冊回佈建服務器。 在這段時間，重新保護會失敗，並傳回錯誤訊息以指出未安裝代理程式。 如果發生這種情況，請等待幾分鐘，然後重新保護。

## <a name="next-steps"></a>後續步驟

重新保護作業完成後，本地 VM 將複製到 Azure。 根據需要，可以[運行另一個容錯移轉](site-recovery-failover.md)到 Azure。

