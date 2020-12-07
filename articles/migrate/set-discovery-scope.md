---
title: 使用 Azure Migrate 設定 VMware VM 探索的範圍
description: 說明如何使用 Azure Migrate 設定 VMware VM 評量和遷移的探索領域。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: dc5bd178c837deea7a22fb3be5ba438085c0e748
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753547"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>設定 VMware Vm 的探索範圍

本文說明如何限制您在下列情況下探索的 VMware Vm 範圍：

- 當您使用「Azure Migrate：伺服器評估」工具時，使用 [Azure Migrate 設備](migrate-appliance-architecture.md) 探索 vm。
- 當您使用 Azure Migrate：伺服器遷移工具時探索具有 [Azure Migrate 設備](migrate-appliance-architecture.md) 的 vm，以無代理程式將 VMware vm 遷移至 Azure。

當您設定設備時，它會連接到 vCenter Server 並開始探索。 將設備連線到 vCenter Server 之前，您可以將探索限制為 vCenter Server 的資料中心、叢集、叢集的資料夾、主機、主機的資料夾或個別 Vm。 若要設定範圍，請在設備用來存取 vCenter Server 的帳戶上指派許可權。

## <a name="before-you-start"></a>開始之前

如果您尚未設定 Azure Migrate 用於探索的 vCenter 使用者帳戶，請立即進行 [評定](./tutorial-discover-vmware.md#prepare-vmware) 或 [無代理程式遷移](./migrate-support-matrix-vmware-migration.md#agentless-migration)。


## <a name="assign-permissions-and-roles"></a>指派許可權和角色

您可以使用下列兩種方法的其中一種，指派 VMware 清查物件的許可權：

- 在設備所使用的帳戶上，指派具有您想要範圍之物件的必要許可權的角色。
- 或者，將角色指派給資料中心層級的帳戶，並傳播到子物件。 然後針對您不想要在範圍中的每個物件，授與帳戶「 **無」存取** 角色。 我們不建議使用這種方法，因為它很麻煩，而且可能會公開存取控制，因為每個新的子物件都會自動授與繼承自父系的存取權。

您無法在 vCenter VM 資料夾層級進行清查探索的範圍。 如果您需要將探索範圍設為 VM 資料夾中的 Vm，請建立使用者並個別授與每個必要 VM 的存取權。 支援主機和叢集資料夾。


### <a name="assign-a-role-for-assessment"></a>指派用於評量的角色

1. 在您要用來進行探索的設備 vCenter 帳戶上，針對裝載您要探索的 Vm 的所有父代物件套用 **唯讀** 角色，並在 (主機、叢集、主機資料夾、叢集資料夾、最多資料中心) 上進行評估。
2. 將這些許可權傳播到階層中的子物件。

    ![指派權限](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>指派無代理程式遷移的角色

1. 在您要用來進行遷移的設備 vCenter 帳戶上，將具有 [所需許可權](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)的使用者定義角色套用至所有裝載您要探索和遷移之 vm 的父物件。
2. 您可以使用更容易識別的內容來命名角色。 例如， <em>Azure_Migrate</em>。

## <a name="work-around-vm-folder-restriction"></a>解決 VM 資料夾限制

目前，如果在 vCenter VM 資料夾層級授與存取權，則伺服器評定工具無法探索 Vm。 如果您想要依 VM 資料夾界定探索和評量的範圍，請使用此因應措施。

1. 針對您想要進行探索和評量的 VM 資料夾中的所有 Vm，指派唯讀許可權。
2. 授與所有裝載 Vm 主機、叢集、主機資料夾、叢集資料夾、最多資料中心) 之父物件的唯讀存取權。 您不需要將權限傳播給所有子物件。
3. 若要使用認證進行探索，請選取 [資料中心] 作為 [ **收集領域**]。


以角色為基礎的存取控制設定可確保對應的 vCenter 使用者帳戶只能存取租使用者特定的 Vm。


## <a name="next-steps"></a>後續步驟

[設定設備](how-to-set-up-appliance-vmware.md)