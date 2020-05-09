---
title: 概念-身分識別和存取
description: 瞭解 Azure VMware 解決方案（AVS）的身分識別和存取概念
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: ce85b60b38db86af14b9e9aa06e568436dc76658
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740950"
---
# <a name="azure-vmware-solution-avs-identity-concepts"></a>Azure VMware 解決方案（AVS）身分識別概念

部署私人雲端時，會布建 vCenter 伺服器和 NSX-T 管理員。 您可使用 vCenter 來管理虛擬機器工作負載和 NSX-T 管理員，以擴充私用雲端軟體定義的網路。

存取和身分識別管理使用適用于 vCenter 的 CloudAdmin 群組許可權和管理的受限制系統管理員許可權。 此原則可確保您的私用雲端平臺可以自動升級。 這會定期傳遞最新的功能和修補程式。 如需私用雲端升級的詳細資訊，請參閱[私用雲端升級概念一文][concepts-upgrades]。

## <a name="vcenter-access-and-identity"></a>vCenter 存取和身分識別

VCenter 中的許可權是透過 CloudAdmin 群組來提供。 該群組可以在 vCenter 本機管理，或透過與 Azure Active Directory 整合 vCenter LDAP 單一登入。 在您部署私人雲端之後，系統會提供您啟用該整合的能力。

下表顯示 CloudAdmin 和 CloudGlobalAdmin 許可權。

|  許可權集合           | CloudAdmin | CloudGlobalAdmin | 註解 |
| :---                     |    :---:   |       :---:      |   :--:  |
|  警示                  | CloudAdmin 使用者在計算 ResourcePool 和 Vm 中具有警示的所有告警許可權。     |          --        |  -- |
|  自動部署             |  --  |        --        |  Microsoft 進行主機管理。  |
|  憑證            |  --  |        --       |  Microsoft 會進行憑證管理。  |
|  內容庫         | CloudAdmin 使用者具有在內容庫中建立和使用檔案的許可權。    |         已啟用 SSO。         |  Microsoft 會將內容庫中的檔案散發至 ESXi 主機。  |
|  資料中心              |  --  |        --          |  Microsoft 會執行所有的資料中心作業。  |
|  資料存放區               | 資料存放區. AllocateSpace、資料存放區. 流覽、資料存放區. DeleteFile、資料存放區. FileManagement、資料存放區 UpdateVirtualMachineMetadata     |    --    |   -- |
|  ESX 代理程式管理員       |  --  |         --       |  Microsoft 會執行所有作業。  |
|  資料夾                  |  CloudAdmin 使用者擁有所有資料夾許可權。     |  --  |  --  |
|  全域                  |  CancelTask、global. GlobalTag、global.asa、global. LogEvent、global. ManageCustomFields、Global. ServiceManagers、global. SetCustomField、global. SystemTag         |                  |    |
|  主機                    |  Hbr. HbrManagement      |        --          |  Microsoft 會執行所有其他的主機操作。  |
|  InventoryService        |  InventoryService。標記      |        --          |  --  |
|  網路                 |  Network.Assign    |                  |  Microsoft 會執行所有其他網路作業。  |
|  權限             |  --  |        --       |  Microsoft 會執行擁有權限作業。  |
|  設定檔驅動的儲存體  |  --  |        --       |  Microsoft 會執行所有的設定檔作業。  |
|  資源                |  CloudAdmin 使用者擁有所有資源許可權。        |      --       | --   |
|  排程的工作          |  CloudAdmin 使用者具有所有 ScheduleTask 許可權。   |   --   | -- |
|  課程                |  會話. GlobalMessage、會話. ValidateSession      |   --   |  Microsoft 會執行所有其他會話作業。  |
|  儲存體視圖           |  StorageViews。 View   |        --          |  Microsoft 會執行所有其他儲存體查看作業（設定服務）。  |
|  工作                   |  --  |  --   |  Microsoft 會管理管理工作的延伸模組。  |
|  vApp                    |  CloudAdmin 使用者具有所有 vApp 許可權。  |  --  |  --  |
|  虛擬機器         |  CloudAdmin 使用者具有所有 VirtualMachine 許可權。  |  --  |  --  |
|  vService                |  CloudAdmin 使用者具有所有 vService 許可權。  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>NSX-T 管理員存取與身分識別

您可以使用「系統管理員」帳戶來存取 NSX-T 管理員。 該帳戶具有完整許可權，可讓您建立和管理 T1 路由器、邏輯交換器和所有服務。 在 NSX-T 中的完整許可權也會提供您對 NSX-T T0 路由器的存取權。 對 T0 路由器的變更可能會導致網路效能降低，或失去對私人雲端的存取權。 若要符合支援需求，您必須在 Azure 入口網站中開啟支援要求，以要求對您的 NSX-T T0 路由器進行任何變更。
  
## <a name="next-steps"></a>後續步驟

下一步是瞭解私用[雲端升級的概念][concepts-upgrades]。

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md