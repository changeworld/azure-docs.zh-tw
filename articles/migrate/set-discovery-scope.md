---
title: 使用 Azure Migrate 設定 VMware VM 探索的範圍
description: 說明如何使用 Azure Migrate 設定 VMware VM 評估和遷移的探索領域。
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: e53eb0d01df2152aeced2901335f75879885fd22
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84770385"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>設定 VMware Vm 的探索範圍

本文說明如何限制您在下列情況下探索到的 VMware Vm 範圍：

- 當您使用 Azure Migrate： Server 評估工具時，使用[Azure Migrate 設備](migrate-appliance-architecture.md)探索 vm。
- 當您使用 Azure Migrate：伺服器遷移工具時，利用[Azure Migrate 設備](migrate-appliance-architecture.md)探索 vm，以進行從 VMware Vm 至 Azure 的無代理程式遷移。

當您設定設備時，它會連線至 vCenter Server 並啟動探索。 將設備連線到 vCenter Server 之前，您可以將探索限制為 vCenter Server 資料中心、叢集、叢集的資料夾、主機、主機的資料夾或個別的 Vm。 若要設定範圍，請在設備用來存取 vCenter Server 的帳戶上指派許可權。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

如果您尚未設定 Azure Migrate 用於探索的 vCenter 使用者帳戶，請立即執行[評估](tutorial-prepare-vmware.md#set-up-permissions-for-assessment)或[無代理程式遷移](tutorial-prepare-vmware.md#assign-permissions-to-an-account)。


## <a name="assign-permissions-and-roles"></a>指派許可權和角色

您可以使用下列兩種方法的其中一種來指派 VMware 清查物件的許可權：

- 在設備所使用的帳戶上，針對您想要界定範圍的物件，指派具有必要許可權的角色。
- 或者，將角色指派給資料中心層級的帳戶，並傳播至子物件。 然後，針對您不想要在範圍內的每個物件，授與帳戶 a 「**無」存取**角色。 我們不建議使用這種方法，因為這很麻煩，而且可能會公開存取控制，因為每個新的子物件都會自動授與從父系繼承的存取權。

您無法在 vCenter VM 資料夾層級界定清查探索的範圍。 如果您需要將探索範圍限定在 VM 資料夾中的 Vm，請建立使用者，並將存取權個別授與每個必要的 VM。 支援主機和叢集資料夾。


### <a name="assign-a-role-for-assessment"></a>指派評估角色

1. 在您用於探索的應用裝置 vCenter 帳戶上，針對裝載您要探索和評估之 Vm （主機、叢集、主機資料夾、叢集資料夾，最多 datacenter）的所有父物件套用**唯讀**角色。
2. 將這些許可權傳播至階層中的子物件。

    ![指派權限](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>指派無代理程式遷移的角色

1. 在您用來進行遷移的應用裝置 vCenter 帳戶上，將具有[所需許可權](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)的使用者定義角色套用至裝載您要探索和遷移之 vm 的所有父物件。
2. 您可以使用更容易識別的名稱來命名角色。 例如， <em>Azure_Migrate</em>。

## <a name="work-around-vm-folder-restriction"></a>解決 VM 資料夾限制

目前，如果已授與 vCenter VM 資料夾層級的存取權，伺服器評估工具就無法探索 Vm。 如果您想要依 VM 資料夾界定探索和評量的範圍，請使用此因應措施。

1. 在您想要界定探索和評定範圍的 VM 資料夾中的所有 Vm 上，指派唯讀許可權。
2. 授與所有主控 Vm 主機、叢集、主機資料夾、叢集資料夾（最多 datacenter）之父物件的唯讀存取權。 您不需要將權限傳播給所有子物件。
3. 若要使用認證進行探索，請選取 [資料中心] 做為**集合範圍**。


以角色為基礎的存取控制設定可確保對應的 vCenter 使用者帳戶只能存取租使用者特定的 Vm。


## <a name="next-steps"></a>後續步驟

[設定設備](how-to-set-up-appliance-vmware.md)，並[啟動連續探索](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential)。
