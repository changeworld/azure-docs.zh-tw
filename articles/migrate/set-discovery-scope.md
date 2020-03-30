---
title: 使用 Azure 遷移設置 VMware VM 發現的範圍
description: 介紹如何使用 Azure 遷移為 VMware VM 評估和遷移設置發現範圍。
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337632"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>為 VMware VM 設置發現範圍

本文介紹如何限制[Azure 遷移設備](migrate-appliance-architecture.md)發現的 VMware VM 的發現範圍。

當您： 

- 使用[Azure 遷移：伺服器評估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具評估 VMware VM 以遷移到 Azure。
- 使用[Azure 遷移：伺服器遷移](migrate-services-overview.md#azure-migrate-server-migration-tool)工具將 VMware VM[無代理遷移到](server-migrate-overview.md)Azure。

## <a name="set-discovery-scope"></a>設置發現範圍


為 VMware VM 評估或遷移設置 Azure 遷移設備後，設備將開始發現 VM，並將 VM 中繼資料發送到 Azure。 在將設備連接到 vCenter Server 進行發現之前，可以將發現範圍設置為將發現限制為 vCenter Server 資料中心、群集、群集資料夾、主機、主機資料夾或單個 VM。

要設置範圍，請對 Azure 遷移用於訪問 vCenter 伺服器的帳戶分配許可權。

## <a name="create-a-vcenter-user-account"></a>建立 vCenter 使用者帳戶

如果尚未設置 Azure 遷移設備用於發現、評估和遷移 VMware VM 的 vCenter 使用者帳戶，請先執行此操作。

1.    以 vCenter Server 系統管理員身分登入 vSphere Web 用戶端。
2.    選擇 **"管理** > **SSO 使用者和組**"，然後按一下"**使用者**"選項卡。
3.    選取 [新增使用者]**** 圖示。
4.    填寫新的使用者資訊>**確定**。

帳戶許可權取決於要部署的內容。

**特徵** | **帳戶權限**
--- | ---
[評定](tutorial-assess-vmware.md)| 該帳戶需要唯讀訪問。
[探索應用程式/角色/功能](how-to-discover-applications.md) | 該帳戶需要唯讀訪問，為虛擬機器啟用了特權>來賓操作。
[分析依賴項（無代理）](how-to-create-group-machine-dependencies-agentless.md) | 該帳戶需要唯讀訪問，為虛擬機器啟用了特權>來賓操作。
[遷移（無代理）](tutorial-migrate-vmware.md) | 您需要分配正確許可權的角色。<br/><br/> 要創建角色，請以 vCenter 伺服器管理員的身份登錄到 vSphere Web 用戶端。 選擇 vCenter 伺服器實例>**創建角色**。 指定角色名稱（例如<em>Azure_Migrate</em>，並將[所需的許可權](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)分配給角色。


## <a name="assign-permissions-on-vcenter-objects"></a>指派 vCenter 物件的權限

您可以使用以下兩種方法之一對清單物件分配許可權：

- 為要發現/遷移的所有父物件分配具有所需許可權的角色、要使用的帳戶。
- 或者，您可以在資料中心級別分配角色和使用者帳戶，並將它們傳播到子物件。 然後，為帳戶提供 **"不訪問**"角色，用於您不想發現/遷移的每個物件。 我們不建議使用此方法，因為它很麻煩，並且可能會公開訪問控制項，因為每個新的子物件都會自動授予從父物件繼承的存取權限。

要將角色指派給用於所有相關物件的帳戶，請執行以下操作：

- **對於評估**：對於 VM 評估，將**唯讀**角色應用於要發現的所有承載 VM 的父物件 vCenter 使用者帳戶。 父物件包括：主機、主機資料夾、群集和層次結構中群集的資料夾，到資料中心。 將這些許可權傳播到層次結構中的子物件。

    ![指派權限](./media/tutorial-assess-vmware/assign-perms.png)

- **對於無代理遷移**：對於無代理遷移，對要發現的所有承載 VM 的父物件應用具有[所需許可權](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)的使用者定義角色。 您可以Azure_Migrate<em>命名角色。</em>

### <a name="scope-support"></a>範圍支援

目前，如果 vCenter 帳戶在 vCenter VM 資料夾級別授予存取權限，則伺服器評估工具無法發現 VM。 支援主機和群集的資料夾。

如果要按 VM 資料夾對發現進行限定，請完成下一個過程，以確保 vCenter 帳戶具有在 VM 級別分配的唯讀存取權限。

1. 對要作用域進行發現的 VM 資料夾中的所有 VM 分配唯讀許可權。
2. 授予對承載 VM 的所有父物件的唯讀存取權限。
    - 包括層次結構中所有父物件（主機、主機資料夾、群集資料夾、群集資料夾）。
    - 您不需要將權限傳播給所有子物件。
3. 藉由選取資料中心作為 [集合範圍]****，來使用用於探索的認證。 基於角色的存取控制設置可確保相應的 vCenter 使用者帳戶僅有權訪問特定于租戶的 VM。


## <a name="next-steps"></a>後續步驟

[設置設備](how-to-set-up-appliance-vmware.md)並開始[連續發現](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential)。
