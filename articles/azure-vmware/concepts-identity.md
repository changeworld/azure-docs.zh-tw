---
title: 概念-身分識別和存取
description: 深入瞭解 Azure VMware 解決方案的身分識別和存取概念
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 9d5b7b4c7c2e0d55cffc99a3f371494f40320a16
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750582"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Azure VMware 解決方案身分識別概念

部署私人雲端時，會布建 vCenter 伺服器和 NSX-T 管理員。 您可以使用 vCenter 來管理虛擬機器工作負載和 NSX-T 管理員，以擴充私用雲端軟體定義的網路。

存取和身分識別管理使用適用于 vCenter 的 CloudAdmin 群組許可權，以及適用于 NSX-T 管理員的受限制系統管理員許可權。 此原則可確保您的私用雲端平臺可以自動升級。 這會定期傳遞最新的功能和修補程式。 如需私用雲端升級的詳細資訊，請參閱 [私用雲端升級概念文章][concepts-upgrades] 。

## <a name="vcenter-access-and-identity"></a>vCenter 存取和身分識別

VCenter 中的許可權是透過 CloudAdmin 群組提供。 您可以在 vCenter 本機管理該群組，或透過與 Azure Active Directory 整合 vCenter LDAP 單一登入。 當您部署私人雲端之後，您就能夠啟用該整合。

CloudAdmin 和 CloudGlobalAdmin 許可權如下表所示。

|  許可權集合           | CloudAdmin | CloudGlobalAdmin | 註解 |
| :---                     |    :---:   |       :---:      |   :--:  |
|  警示                  | CloudAdmin 使用者在計算 ResourcePool 和 Vm 中具有警示的所有警報許可權。     |          --        |  -- |
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
|  Network (網路)                 |  Network.Assign    |                  |  Microsoft 會進行所有其他的網路作業。  |
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

您可以使用「系統管理員」帳戶存取 NSX-T 管理員。 該帳戶具有完整許可權，可讓您建立和管理 T1 路由器、邏輯交換器和所有服務。 在 NSX-T 中的完整許可權也可讓您存取 NSX-T T0 路由器。 對 T0 路由器的變更可能會導致網路效能降低，或無法存取私人雲端。 為了符合支援需求，您必須在 Azure 入口網站中開啟支援要求，以要求對您的 NSX-T T0 路由器進行任何變更。
  
## <a name="next-steps"></a>後續步驟

下一步是瞭解私用 [雲端升級的概念][concepts-upgrades]。

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md