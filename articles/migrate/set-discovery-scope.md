---
title: 使用 Azure Migrate 設定 VMware VM 探索的範圍
description: 說明如何使用 Azure Migrate 設定 VMware VM 評估和遷移的探索領域。
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337632"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>設定 VMware Vm 的探索範圍

本文說明如何針對由[Azure Migrate 設備](migrate-appliance-architecture.md)探索到的 VMware vm，限制探索的範圍。

當您執行下列動作時，Azure Migrate 設備會探索內部部署 VMware Vm： 

- 使用[Azure Migrate：伺服器評估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具來評估要遷移至 Azure 的 VMware vm。
- 使用[Azure Migrate：伺服器遷移](migrate-services-overview.md#azure-migrate-server-migration-tool)工具，將 VMware vm 的[無代理程式遷移](server-migrate-overview.md)至 Azure。

## <a name="set-discovery-scope"></a>設定探索範圍


在您設定適用于 VMware Vm 評估或遷移的 Azure Migrate 設備之後，設備會開始探索 Vm，並將 VM 中繼資料傳送至 Azure。 將設備連線到 vCenter Server 進行探索之前，您可以設定探索領域來限制探索，以 vCenter Server 資料中心、叢集、叢集的資料夾、主機的資料夾或個別的 Vm。

若要設定範圍，請在 Azure Migrate 用來存取 vCenter Server 的帳戶上指派許可權。

## <a name="create-a-vcenter-user-account"></a>建立 vCenter 使用者帳戶

如果您尚未設定 Azure Migrate 設備用來探索、評估和遷移 VMware Vm 的 vCenter 使用者帳戶，請先執行此動作。

1.    以 vCenter Server 系統管理員身分登入 vSphere Web 用戶端。
2.    選取 [系統**管理** > ] [**SSO 使用者和群組**]，然後按一下 [**使用者**] 索引標籤。
3.    選取 [新增使用者]**** 圖示。
4.    填入新的使用者資訊 > **[確定]**。

帳戶許可權取決於您要部署的內容。

**功能** | **帳戶權限**
--- | ---
[評定](tutorial-assess-vmware.md)| 此帳戶需要唯讀存取權。
[探索應用程式/角色/功能](how-to-discover-applications.md) | 此帳戶需要唯讀存取權，且已啟用虛擬機器 > 來賓作業的許可權。
[分析相依性（無代理程式）](how-to-create-group-machine-dependencies-agentless.md) | 此帳戶需要唯讀存取權，且已啟用虛擬機器 > 來賓作業的許可權。
[遷移（無代理程式）](tutorial-migrate-vmware.md) | 您需要已獲指派正確許可權的角色。<br/><br/> 若要建立角色，請以 vCenter Server 系統管理員身分登入 vSphere Web 用戶端。 選取 vCenter Server 實例 >**建立角色**]。 指定角色名稱，例如<em>Azure_Migrate</em>，並將[所需的許可權](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)指派給角色。


## <a name="assign-permissions-on-vcenter-objects"></a>指派 vCenter 物件的權限

您可以使用下列兩種方法的其中一種來指派清查物件的許可權：

- 針對裝載您要探索/遷移之 Vm 的所有父物件，將具有必要許可權的角色指派給您所使用的帳戶。
- 或者，您可以在資料中心層級指派角色和使用者帳戶，並將它們傳播至子物件。 然後，針對您不想要探索/遷移的每個物件，授與帳戶 a 「**無」存取**角色。 我們不建議使用這種方法，因為這很麻煩，而且可能會公開存取控制，因為每個新的子物件都會自動授與從父系繼承的存取權。

若要將角色指派給您要用於所有相關物件的帳戶，請執行下列動作：

- **針對評估**：若為 VM 評估，請將**唯讀**角色套用至裝載您要探索之 vm 的所有父物件的 vCenter 使用者帳戶。 包含的父物件：主機、主機的資料夾、叢集的叢集，以及階層中叢集的資料夾，最多到資料中心。 將這些許可權傳播至階層中的子物件。

    ![指派權限](./media/tutorial-assess-vmware/assign-perms.png)

- **針對無代理程式遷移**：若要進行無代理程式遷移，請針對裝載您要探索之 vm 的所有父物件，將具有[必要許可權](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)的使用者定義角色套用至使用者帳戶。 您可以將角色命名為<em>Azure_Migrate</em>。

### <a name="scope-support"></a>範圍支援

目前，如果 vCenter 帳戶具有在 vCenter VM 資料夾層級上授與的存取權，伺服器評估工具就無法探索 Vm。 支援主機和叢集的資料夾。

如果您想要依 VM 資料夾界定探索範圍，請完成下一個程式，以確保 vCenter 帳戶具有在 VM 層級上指派的唯讀存取權。

1. 在您想要界定探索範圍的 VM 資料夾中，指派所有 Vm 的唯讀許可權。
2. 授與裝載 Vm 之所有父物件的唯讀存取權。
    - 階層中的所有父物件（主機、主機、叢集、叢集的資料夾）都會包含在資料中心的階層中。
    - 您不需要將權限傳播給所有子物件。
3. 藉由選取資料中心作為 [集合範圍]****，來使用用於探索的認證。 以角色為基礎的存取控制設定可確保對應的 vCenter 使用者帳戶只能存取租使用者特定的 Vm。


## <a name="next-steps"></a>後續步驟

[設定設備](how-to-set-up-appliance-vmware.md)並[啟動連續探索](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential)。
