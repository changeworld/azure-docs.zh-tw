---
title: 使用 Azure 原則自動啟用 VM 建立的備份
description: 描述如何使用 Azure 策略自動啟用給定作用域中創建的所有 VM 的備份的文章
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1d423371788f87155328e8f5c9334e47349a68e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584263"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>使用 Azure 原則自動啟用 VM 建立的備份

組織中備份或合規性管理員的主要職責之一是確保所有業務關鍵型電腦都具有適當的保留性。

如今，Azure 備份提供了一個內置策略（使用 Azure 策略），可以分配給**訂閱或資源組中指定位置中的所有 Azure VM。** 當此策略分配給給定作用域時，該作用域中創建的所有新 VM 將自動設定為備份到**同一位置和訂閱中的現有保存庫**。 使用者可以指定備份 VM 應關聯的保存庫和保留原則。

## <a name="supported-scenarios"></a>支援的案例

* 內置策略當前僅支援 Azure VM。 使用者必須注意確保分配期間指定的保留原則是 VM 保留原則。 請參閱[本文檔](https://docs.microsoft.com/azure/backup/backup-azure-policy-supported-skus)以查看此策略支援的所有 VM SKU。

* 策略可以一次分配給單個位置和訂閱。 為了跨位置和訂閱為 VM 啟用備份，需要創建策略分配的多個實例，每個位置和訂閱組合一個實例。

* 為備份配置的指定保存庫和 VM 可以屬於不同的資源組。

* 管理組作用域當前不受支援。

* 內置策略目前不在國家雲中。

## <a name="using-the-built-in-policy"></a>使用內置策略

要將策略分配給所需的範圍，請按照以下步驟操作：

1. 登錄到 Azure 門戶並導航到**策略**儀表板。
2. 選擇左側功能表中**的定義**，以獲取有關 Azure 資源的所有內置策略的清單。
3. 篩選**類別=備份**的清單。 您將看到清單被篩選為名為"將位置的 VM 上的備份配置為同一位置的現有中央保存庫"的單個策略。
![策略儀表板](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. 按一下策略的名稱。 您將被重定向到此策略的詳細定義。
![策略定義邊欄選項卡](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. 按一下邊欄選項卡頂部的 **"分配"** 按鈕。 這將將您重定向到 **"分配策略"** 邊欄選項卡。
6. 在 **"基礎知識"** 下，按一下 **"範圍"** 欄位旁邊的三個點。 這將打開一個正確的上下文邊欄選項卡，您可以在其中選擇要應用的策略的訂閱。 您還可以選擇資源組，以便策略僅應用於特定資源組中的 VM。
![策略分配基礎知識](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. 在 **"參數"** 選項卡中，從下拉清單中選擇一個位置，然後選擇作用域中的 VM 必須與之關聯的保存庫和備份策略。
![策略分配參數](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. 確保**效果**設置為部署"不存在"。
9. 導航到 **"審閱+創建**"，然後按一下"**創建**"。

> [!NOTE]
>
> Azure 策略也可以用於現有 VM，使用[修正](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources)。

> [!NOTE]
>
> 建議一次不要將此策略分配給超過 200 個 VM。 如果策略分配給 200 多個 VM，則可能導致備份比計畫指定的備份晚幾個小時觸發。

## <a name="next-steps"></a>後續步驟

[瞭解有關 Azure 策略的更多](https://docs.microsoft.com/azure/governance/policy/overview)
