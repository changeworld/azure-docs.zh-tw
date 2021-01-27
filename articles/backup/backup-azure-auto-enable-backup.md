---
title: 使用 Azure 原則自動啟用 VM 建立的備份
description: 本文說明如何使用 Azure 原則來針對在指定範圍內建立的所有 Vm 自動啟用備份
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 7e8195d22f54f29b36549b966322623ed0987d72
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896862"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>使用 Azure 原則自動啟用 VM 建立的備份

組織中的備份或合規性管理員的主要責任之一，就是確保所有業務關鍵的機器都能以適當的保留期進行備份。

目前，Azure 備份提供各種內建原則 (使用 [Azure 原則](https://docs.microsoft.com/azure/governance/policy/overview)) ，以協助您自動確保您的 Azure 虛擬機器已設定備份。 您可以使用下列任何一個原則，根據備份小組和資源的組織方式：

## <a name="policy-1---configure-backup-on-vms-without-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>原則 1-在沒有指定標記的 Vm 上設定備份到相同位置中的現有復原服務保存庫

如果您的組織具有管理跨應用程式小組之備份的中央備份小組，您可以使用此原則來設定備份到與受管理的 Vm 位於相同訂用帳戶和位置的現有中央復原服務保存庫。 您可以從這個原則的範圍中，選擇 **排除** 包含特定標記的 vm。

## <a name="policy-2---preview-configure-backup-on-vms-with-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>原則 2-[預覽] 使用指定標記將 Vm 上的備份設定至相同位置中的現有復原服務保存庫
此原則的運作方式與上述原則1相同，唯一的差別在於您可以使用此原則，在此原則的範圍內 **包含** 包含特定標記的 vm。 

## <a name="policy-3---preview-configure-backup-on-vms-without-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>原則 3-[預覽] 使用預設原則將沒有指定標記的 Vm 上的備份設定到新的復原服務保存庫
如果您將應用程式組織在專用資源群組中，而且想要讓相同的保存庫備份它們，此原則可讓您自動管理此動作。 您可以從這個原則的範圍中，選擇 **排除** 包含特定標記的 vm。

## <a name="policy-4---preview-configure-backup-on-vms-with-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>原則 4-[預覽] 使用預設原則將具有指定標記的 Vm 上的備份設定至新的復原服務保存庫
此原則的運作方式與上述原則3相同，唯一的差別在於，您可以使用此原則，在此原則的範圍內 **包含** 包含特定標記的 vm。 

除了上述以外，Azure 備份也提供 [僅限審核](https://docs.microsoft.com/azure/governance/policy/concepts/effects#audit) 原則- **Azure 備份應針對虛擬機器啟用**。 此原則會識別未啟用備份的虛擬機器，但不會自動設定這些 Vm 的備份。 當您只想要評估 Vm 的整體合規性，但不想立即採取動作時，這會很有用。

## <a name="supported-scenarios"></a>支援的案例

* 目前只有 Azure Vm 支援內建原則。 使用者必須小心確保指派期間指定的保留原則是 VM 保留原則。 請參閱 [這](./backup-azure-policy-supported-skus.md) 份檔，以查看此原則支援的所有 VM sku。

* 原則1和2可以一次指派給單一位置和訂用帳戶。 若要啟用跨位置和訂用帳戶的 Vm 備份，必須建立多個原則指派實例，每個位置和訂用帳戶的組合各一個。

* 針對原則1和2，目前不支援管理群組範圍。

* 針對原則1和2，指定的保存庫和針對備份所設定的 Vm 可以位於不同的資源群組底下。

* 美國國家雲端目前無法使用原則1、2、3和4。

* 原則3和4可以一次指派給單一訂用帳戶， (或訂用帳戶) 內的資源群組。

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policies"></a>使用內建原則

下列步驟說明指派原則1的端對端程式：在 **不含指定標籤的 vm 上設定備份至指定範圍的相同位置中的現有復原服務保存庫** 。 其他原則會套用類似的指示。 一旦指派之後，會自動設定在範圍中建立的所有新 VM 進行備份。

1. 登入 Azure 入口網站，然後流覽至 **原則** 儀表板。
2. 選取左側功能表中的 [ **定義** ]，以取得跨 Azure 資源的所有內建原則清單。
3. 篩選 [ **類別目錄 = 備份** ] 的清單，然後選取名為 [在相同位置的現有中央保存庫中的 Vm 上設定備份] 的原則。
![原則儀表板](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. 選取原則的名稱。 系統會將您重新導向至此原則的詳細定義。
![原則定義窗格](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. 選取窗格頂端的 [ **指派** ] 按鈕。 這會將您重新導向至 [ **指派原則** ] 窗格。
6. 在 [ **基本**] 底下，選取 [ **領域** ] 欄位旁的三個點。 這會開啟一個右邊的內容窗格，您可以在其中選取要套用原則的訂用帳戶。 您也可以選擇性地選取資源群組，讓原則僅適用于特定資源群組中的 Vm。
![原則指派基本概念](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. 在 [ **參數** ] 索引標籤中，從下拉式清單中選擇一個位置，然後選取必須與範圍中的 vm 相關聯的保存庫和備份原則。 您也可以選擇指定標記名稱和標記值的陣列。 包含指定標記之任何指定值的 VM，將會從原則指派的範圍中排除。
![原則指派參數](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. 確定 [ **效果** ] 設定為 [deployIfNotExists]。
9. 流覽至 [ **審核] + [建立** ]，然後選取 [ **建立**]。

> [!NOTE]
>
> Azure 原則也可以在現有的 Vm 上使用 [補救](../governance/policy/how-to/remediate-resources.md)。

> [!NOTE]
>
> 建議您一次不將此原則指派給超過200個 Vm。 如果將原則指派給超過200的 Vm，可能會導致備份所觸發的時間比排程所指定的時間還要多。

## <a name="next-steps"></a>後續步驟

[深入瞭解 Azure 原則](../governance/policy/overview.md)
