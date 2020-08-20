---
title: 使用 Azure 原則自動啟用 VM 建立的備份
description: 本文說明如何使用 Azure 原則來針對在指定範圍內建立的所有 Vm 自動啟用備份
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 19985ebc51fe713ee0392800e2791ea1891ff3cd
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612668"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>使用 Azure 原則自動啟用 VM 建立的備份

組織中的備份或合規性管理員的主要責任之一，就是確保所有業務關鍵的機器都能以適當的保留期進行備份。

目前，Azure 備份提供內建原則 (使用 Azure 原則) ，可指派給訂用帳戶 **或資源群組內指定位置中的所有 Azure vm**。 將此原則指派給指定的範圍時，在該範圍中建立的所有新 Vm 都會自動設定為備份至 **相同位置和訂用帳戶中現有**的保存庫。 使用者可以指定要與備份的 Vm 相關聯的保存庫和保留原則。

## <a name="supported-scenarios"></a>支援的案例

* 目前只有 Azure Vm 支援內建原則。 使用者必須小心確保指派期間指定的保留原則是 VM 保留原則。 請參閱 [這](./backup-azure-policy-supported-skus.md) 份檔，以查看此原則支援的所有 VM sku。

* 原則可以一次指派給單一位置和訂用帳戶。 若要啟用跨位置和訂用帳戶的 Vm 備份，必須建立多個原則指派實例，每個位置和訂用帳戶的組合各一個。

* 指定的保存庫和針對備份設定的 Vm 可以位於不同的資源群組底下。

* 目前不支援管理群組範圍。

* 內建原則目前無法在國家雲端中使用。

## <a name="using-the-built-in-policy"></a>使用內建原則

若要將原則指派給所需的範圍，請遵循下列步驟：

1. 登入 Azure 入口網站，然後流覽至 **原則** 儀表板。
1. 選取左側功能表中的 [ **定義** ]，以取得跨 Azure 資源的所有內建原則清單。
1. 篩選 **分類 = 備份**的清單。 您會看到清單已篩選至名為「在相同位置的現有中央保存庫中的 Vm 上設定備份」的單一原則。
![原則儀表板](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
1. 選取原則的名稱。 系統會將您重新導向至此原則的詳細定義。
![原則定義窗格](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
1. 選取窗格頂端的 [ **指派** ] 按鈕。 這會將您重新導向至 [ **指派原則** ] 窗格。
1. 在 [ **基本**] 底下，選取 [ **領域** ] 欄位旁的三個點。 這會開啟一個右邊的內容窗格，您可以在其中選取要套用原則的訂用帳戶。 您也可以選擇性地選取資源群組，讓原則僅適用于特定資源群組中的 Vm。
![原則指派基本概念](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
1. 在 [ **參數** ] 索引標籤中，從下拉式清單中選擇一個位置，然後選取必須與範圍中的 vm 相關聯的保存庫和備份原則。
![原則指派參數](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
1. 確定 [ **效果** ] 設定為 [deployIfNotExists]。
1. 流覽至 [ **審核] + [建立** ]，然後選取 [ **建立**]。

> [!NOTE]
>
> Azure 原則也可以在現有的 Vm 上使用 [補救](../governance/policy/how-to/remediate-resources.md)。

> [!NOTE]
>
> 建議您一次未將此原則指派給超過200個 Vm。 如果將原則指派給超過200的 Vm，它可能會導致在幾個小時之後觸發備份，而不是排程所指定的時間。

## <a name="next-steps"></a>後續步驟

[深入瞭解 Azure 原則](../governance/policy/overview.md)
