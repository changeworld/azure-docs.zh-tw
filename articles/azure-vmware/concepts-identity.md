---
title: 概念-身分識別和存取
description: 深入瞭解 Azure VMware 解決方案的身分識別和存取概念
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: e9c0d62968d94e2b018186f67072b6ae7078db02
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536093"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Azure VMware 解決方案身分識別概念

Azure VMware 解決方案私用雲端是使用 vCenter server 和 NSX-T 管理員所布建。 您可以使用 vCenter 來管理虛擬機器 (VM) 工作負載。 您可以使用 NSX-T 管理員來擴充私人雲端。

存取和身分識別管理使用適用于 vCenter 的 CloudAdmin 群組許可權，以及適用于 NSX-T 管理員的受限制系統管理員許可權。 它可確保您的私用雲端平臺會自動升級為最新的功能和修補程式。  如需詳細資訊，請參閱 [私用雲端升級概念文章][concepts-upgrades]。

## <a name="vcenter-access-and-identity"></a>vCenter 存取和身分識別

CloudAdmin 群組會提供 vCenter 中的許可權。 您可以在 vCenter 本機管理群組。 另一個選項是透過與 Azure Active Directory 整合 vCenter LDAP 單一登入。 部署私人雲端之後，您可以啟用該整合。 

此表格顯示 **CloudAdmin** 和 **CloudGlobalAdmin** 許可權。

|  許可權集合           | CloudAdmin | CloudGlobalAdmin | 註解 |
| :---                     |    :---:   |       :---:      |   :--:  |
|  警示                  | CloudAdmin 使用者對於 Compute-ResourcePool 和 Vm 中的鬧鐘具有所有警示許可權。     |          --        |  -- |
|  自動部署             |  --  |        --        |  Microsoft 進行主機管理。  |
|  憑證            |  --  |        --       |  Microsoft 會進行憑證管理。  |
|  內容庫         | CloudAdmin 使用者具有在內容庫中建立及使用檔案的許可權。    |         啟用 SSO。         |  Microsoft 會將內容庫中的檔案散發至 ESXi 主機。  |
|  資料中心              |  --  |        --          |  Microsoft 會進行所有的資料中心作業。  |
|  資料存放區               | 資料存放區. AllocateSpace、資料存放區、流覽、Datastore.Config、資料存放區. DeleteFile、資料存放區. FileManagement、資料存放區. UpdateVirtualMachineMetadata     |    --    |   -- |
|  ESX 代理程式管理員       |  --  |         --       |  Microsoft 會進行所有作業。  |
|  資料夾                  |  CloudAdmin 使用者擁有所有資料夾許可權。     |  --  |  --  |
|  全球                  |  Global. CancelTask、Global. GlobalTag、Global. Health、Global. LogEvent、Global. ManageCustomFields、Global. ServiceManagers、Global. SetCustomField、Global.SystemTag         |                  |    |
|  主機                    |  Hbr. HbrManagement      |        --          |  Microsoft 會進行所有其他主機操作。  |
|  InventoryService        |  InventoryService。標記      |        --          |  --  |
|  網路                 |  Network.Assign    |                  |  Microsoft 會進行所有其他的網路作業。  |
|  權限             |  --  |        --       |  Microsoft 會執行擁有權限作業。  |
|  設定檔驅動的儲存體  |  --  |        --       |  Microsoft 會進行所有的設定檔作業。  |
|  資源                |  CloudAdmin 使用者擁有所有資源許可權。        |      --       | --   |
|  排程的工作          |  CloudAdmin 使用者擁有所有 ScheduleTask 許可權。   |   --   | -- |
|  工作階段                |  GlobalMessage，會話. ValidateSession      |   --   |  Microsoft 會進行所有其他會話作業。  |
|  儲存體視圖           |  StorageViews. View   |        --          |  Microsoft 會執行所有其他的儲存體視圖作業， (設定服務) 。  |
|  工作                   |  --  |  --   |  Microsoft 會管理管理工作的延伸模組。  |
|  vApp                    |  CloudAdmin 使用者擁有所有 vApp 許可權。  |  --  |  --  |
|  虛擬機器         |  CloudAdmin 使用者擁有所有 VirtualMachine 許可權。  |  --  |  --  |
|  vService                |  CloudAdmin 使用者擁有所有 vService 許可權。  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>NSX-T 管理員存取和身分識別

使用「系統管理員」帳戶存取 NSX-T 管理員。 它具有完整的許可權，可讓您建立和管理 T1 路由器、邏輯交換器和所有服務。 這些許可權可讓您存取 NSX-T T0 路由器。 對 T0 路由器的變更可能會導致網路效能降低，或無法存取私人雲端。 在 Azure 入口網站中開啟支援要求，以向您的 NSX-T T0 路由器要求任何變更。
  
## <a name="next-steps"></a>後續步驟

下一步是瞭解私用 [雲端升級的概念][concepts-upgrades]。

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md