---
title: Azure 遷移設備體系結構
description: 提供伺服器評估和遷移中使用的 Azure 遷移設備的概述。
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: d55d123bb056b46b5e78dd8ac836eeaf9b42fe70
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389013"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure 遷移設備體系結構

本文介紹 Azure 遷移設備體系結構和過程。 Azure 遷移設備是一種羽量級設備，部署在內部，用於發現 VM 和物理伺服器，以便遷移到 Azure。 

## <a name="deployment-scenarios"></a>部署案例

Azure 遷移設備用於以下方案。

**案例** | **工具** | **用於** 
--- | --- | ---
**VMware VM 評估** | Azure 遷移：伺服器評估 | 發現 VMware VM。<br/><br/> 發現電腦應用和依賴項。<br/><br/> 收集電腦中繼資料和性能中繼資料併發送到 Azure。
**VMware VM 遷移（無代理）** | Azure 遷移：伺服器遷移 | 發現 VMware VM<br/><br/>  使用[無代理遷移](server-migrate-overview.md)複製 VMware VM。
**超 V VM 評估** | Azure 遷移：伺服器評估 | 發現超虛擬機器。<br/><br/> 收集電腦中繼資料和性能中繼資料併發送到 Azure。
**物理機器** |  Azure 遷移：伺服器評估 |  發現物理伺服器。<br/><br/> 收集電腦中繼資料和性能中繼資料併發送到 Azure。

## <a name="appliance-components"></a>設備元件

本產品具有多個元件。

- **管理應用**：這是一個 Web 應用程式，用於在設備部署期間使用者輸入。 用於評估電腦以遷移到 Azure 時使用。
- **發現代理**：代理收集電腦配置資料。 用於評估電腦以遷移到 Azure 時使用。 
- **評估代理**：代理收集效能資料。 用於評估電腦以遷移到 Azure 時使用。
- **DRA 代理**：協調 VM 複製，並協調複製的電腦和 Azure 之間的通信。 僅在使用無代理遷移將 VMware VM 複製到 Azure 時使用。
- **閘道**：向 Azure 發送複製的資料。 僅在使用無代理遷移將 VMware VM 複製到 Azure 時使用。
- **自動更新服務**：更新設備元件（每 24 小時運行一次）。



## <a name="appliance-deployment"></a>設備部署

- Azure 遷移設備可以使用[Hyper-V](how-to-set-up-appliance-hyper-v.md)或[VMware](how-to-set-up-appliance-vmware.md)的範本進行設置，或者使用[適用于 VMware/Hyper-V](deploy-appliance-script.md)的 PowerShell 腳本安裝程式以及[物理伺服器](how-to-set-up-appliance-physical.md)。 
- 設備支援要求和部署先決條件匯總到[設備支援矩陣](migrate-appliance.md)中。


## <a name="appliance-registration"></a>設備註冊

在設備設置期間，您將設備註冊到 Azure 遷移，並且表中匯總的操作將發生。

**動作** | **詳細資料** | **許可權**
--- | --- | ---
**註冊來源提供者** | 這些資來源提供者在設備設置期間您選擇的訂閱中註冊：Microsoft.OffAzure、微軟.移民和微軟.KeyVault。<br/><br/> 註冊資源提供者可將您的訂用帳戶設定為可搭配資源提供者使用。 | 若要註冊資源提供者，您必須具有訂用帳戶的「參與者」或「擁有者」角色。
**創建 Azure AD 應用通信** | Azure 遷移創建 Azure 活動目錄 （Azure AD） 應用，用於在設備上運行的代理與其在 Azure 上運行的相應服務之間的通信（身份驗證和授權）。<br/><br/> 此應用沒有對 Azure 資源管理器調用或 RBAC 訪問任何資源的許可權。 | 創建應用需要 Azure 遷移[的這些許可權](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance)。
**創建 Azure AD 應用 - 金鑰保存庫** | 此應用僅用於將 VMware VM 不代理遷移到 Azure。<br/><br/> 它僅用於訪問使用者訂閱中為無代理遷移創建的金鑰保存庫。<br/><br/> 當從設備啟動發現時，它在 Azure 金鑰保存庫（在客戶的租戶中創建）上具有 RBAC 存取權限。 | 創建應用需要 Azure 遷移[的這些許可權](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance)。



## <a name="collected-data"></a>收集的資料

用戶端針對所有部署方案收集的資料匯總到[設備支援矩陣](migrate-appliance.md)中。

## <a name="discovery-and-collection-process"></a>發現和收集過程

![架構](./media/migrate-appliance-architecture/architecture.png)

設備使用以下過程與 vCenter 伺服器和 Hyper-V 主機/群集進行通信。

1. **開始發現**：
    - 在 Hyper-V 設備上啟動發現時，它將與 WinRM 埠 5985 （HTTP） 和 5986 （HTTPS） 上的 Hyper-V 主機進行通信。
    - 當您開始在 VMware 設備上發現時，預設情況下它與 TCP 埠 443 上的 vCenter 伺服器通信。 如果 vCenter 伺服器偵聽其他埠，則可以在設備 Web 應用中對其進行配置。
2. **收集中繼資料和效能資料**：
    - 設備使用通用訊息模型 （CIM） 會話從埠 5985 和 5986 上的 Hyper-V 主機收集 Hyper-V VM 資料。
    - 預設情況下，設備與埠 443 通信，以便從 vCenter 伺服器收集 VMware VM 資料。
3. **發送資料**：設備通過 SSL 埠 443 將收集的資料發送到 Azure 遷移伺服器評估和 Azure 遷移伺服器遷移。 設備可以通過 Internet 連接到 Azure，也可以將 ExpressRoute 用於公共/Microsoft 對等互連。
    - 對於效能資料，設備收集即時利用率資料。
        - VMware 每 20 秒收集一次效能資料，Hyper-V 每 30 秒收集一次，每個性能指標。
        - 收集的資料將匯總，以創建單個資料點 10 分鐘。
        - 峰值利用率值從所有 20/30 秒的資料點中選擇，併發送到 Azure 進行評估計算。
        - 根據評估屬性（50/90th/95th/99）中指定的百分位值，按昇冪對 10 分鐘點進行排序，並使用適當的百分位數值計算評估
    - 對於伺服器遷移，設備開始收集 VM 資料，並將其複製到 Azure。
4. **評估和遷移**：您現在可以使用 Azure 遷移伺服器評估從設備收集的中繼資料創建評估。 此外，您還可以使用 Azure 遷移伺服器遷移開始遷移 VMware VM 來協調無代理 VM 複製。





## <a name="appliance-upgrades"></a>設備升級

隨著在設備上運行的 Azure 遷移代理更新，設備將升級。 這是自動發生的，因為預設情況下在設備上啟用了自動更新。 您可以更改此預設設置以手動更新代理。

通過將HKEY_LOCAL_MACHINE_SOFTWARE_Microsoft_AzureAppliance"自動更新"鍵設置為 0 （DWORD），在註冊表中關閉自動更新。

 

## <a name="next-steps"></a>後續步驟

[查看](migrate-appliance.md)設備支援矩陣。

