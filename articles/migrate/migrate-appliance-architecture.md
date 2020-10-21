---
title: Azure Migrate 設備架構
description: 提供伺服器評估和移轉中所使用 Azure Migrate 設備的概觀。
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: a01932a9e4f72d7ce6747214b53f124d54942894
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92312916"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure Migrate 設備架構

本文說明 Azure Migrate 設備的架構和流程。 Azure Migrate 設備是部署在內部部署的輕量設備，用來探索要遷移至 Azure 的 Vm 和實體伺服器。 

## <a name="deployment-scenarios"></a>部署案例

在下列案例中會使用 Azure Migrate 設備。

**案例** | **工具** | **用於** 
--- | --- | ---
**VMware VM 評估** | Azure Migrate：伺服器評量 | 探索 VMware Vm。<br/><br/> 探索機器應用程式和相依性。<br/><br/> 收集電腦中繼資料和效能中繼資料，並傳送至 Azure。
**VMware VM 遷移 (無代理程式) ** | Azure Migrate：伺服器移轉 | 探索 VMware VM<br/><br/>  使用 [無代理程式遷移](server-migrate-overview.md)來複寫 VMware vm。
**Hyper-V VM 評估** | Azure Migrate：伺服器評量 | 探索 Hyper-v Vm。<br/><br/> 收集電腦中繼資料和效能中繼資料，並傳送至 Azure。
**實體機器** |  Azure Migrate：伺服器評量 |  探索實體伺服器。<br/><br/> 收集電腦中繼資料和效能中繼資料，並傳送至 Azure。

## <a name="appliance-components"></a>設備元件

設備有許多元件。

- **管理應用程式**：這是在設備部署期間用於使用者輸入的 Web 應用程式。 在評估要移轉至 Azure 的機器時會使用。
- **探索代理程式**：代理程式會收集機器設定資料。 在評估要移轉至 Azure 的機器時會使用。 
- **收集器代理程式**：代理程式會收集效能資料。 在評估要移轉至 Azure 的機器時會使用。
- **DRA 代理程式**：協調 VM 複寫，並協調複寫機器與 Azure 之間的通訊。 只有在使用無代理程式移轉將 VMware VM 複寫至 Azure 時才使用。
- **閘道**：將複寫的資料傳送至 Azure。 只有在使用無代理程式移轉將 VMware VM 複寫至 Azure 時才使用。
- **自動更新服務**：更新設備元件 (每隔 24 小時執行一次)。



## <a name="appliance-deployment"></a>設備部署

- 您可以使用 [hyper-v](how-to-set-up-appliance-hyper-v.md) 或 [vmware](how-to-set-up-appliance-vmware.md) 的範本，或使用適用于 [VMware/hyper-v](deploy-appliance-script.md)的 PowerShell 腳本安裝程式和 [實體伺服器](how-to-set-up-appliance-physical.md)來設定 Azure Migrate 設備。 
- 設備支援需求和部署必要條件會在 [設備支援對照表](migrate-appliance.md)中摘要說明。


## <a name="appliance-registration"></a>設備註冊

在設備設定期間，您會向 Azure Migrate 註冊設備，並在資料表中摘要說明的動作。

**動作** | **詳細資料** | **權限**
--- | --- | ---
**註冊來源提供者** | 這些資源提供者會在您于設備設定期間選擇的訂用帳戶中註冊： Microsoft.offazure、Microsoft. KeyVault。<br/><br/> 註冊資源提供者可將您的訂用帳戶設定為可搭配資源提供者使用。 | 若要註冊資源提供者，您必須具有訂用帳戶的「參與者」或「擁有者」角色。
**建立 Azure AD 應用程式-通訊** | Azure Migrate 會在設備上執行的代理程式與在 Azure 上執行的個別服務之間，建立 Azure Active Directory (Azure AD) 應用程式之間進行通訊 (驗證與授權) 。<br/><br/> 此應用程式沒有在任何資源上進行 Azure Resource Manager 呼叫或 RBAC 存取的許可權。 | 您需要 [這些許可權](./tutorial-discover-vmware.md#prepare-an-azure-user-account) 才能 Azure Migrate 建立應用程式。
**建立 Azure AD 應用程式-金鑰保存庫** | 此應用程式僅適用于將 VMware Vm 遷移至 Azure 的無代理程式。<br/><br/> 它是專門用來存取在使用者的訂用帳戶中建立的金鑰保存庫，以進行無代理程式的遷移。<br/><br/> 當您從設備起始探索時，Azure key vault 上的 RBAC 存取 (在客戶的租使用者) 中建立。 | 您需要 [這些許可權](./tutorial-discover-vmware.md#prepare-an-azure-user-account) 才能 Azure Migrate 建立應用程式。



## <a name="collected-data"></a>收集的資料

用戶端針對所有部署案例所收集的資料，會在 [設備支援矩陣](migrate-appliance.md)中摘要說明。

## <a name="discovery-and-collection-process"></a>探索和收集程式

![架構](./media/migrate-appliance-architecture/architecture1.png)

設備會使用下列程式，與 vCenter Server 和 Hyper-v 主機/叢集進行通訊。

1. **開始探索**：
    - 當您在 Hyper-v 設備上開始探索時，它會與 WinRM 埠5985上的 Hyper-v 主機通訊 (HTTP) 。
    - 當您在 VMware 設備上開始探索時，它預設會與 TCP 通訊埠443上的 vCenter server 進行通訊。 如果 vCenter server 在不同的埠上接聽，您可以在設備 web 應用程式中進行設定。
2. **收集中繼資料和效能資料**：
    - 設備使用通用訊息模型 (CIM) 會話，從埠5985上的 Hyper-v 主機收集 Hyper-v VM 資料。
    - 根據預設，設備會與埠443通訊，以收集 vCenter Server 的 VMware VM 資料。
3. **傳送資料**：設備會將收集到的資料傳送到 Azure Migrate 伺服器評量，並透過 SSL 埠443來 Azure Migrate 伺服器遷移。 設備可透過網際網路連線到 Azure，或透過 ExpressRoute (需要 Microsoft 對等互連) 。
    - 針對效能資料，設備會收集即時使用量資料。
        - VMware 每隔20秒會收集一次效能資料，而針對每個效能標準，每隔30秒會收集一次。
        - 收集的資料會匯總成建立單一資料點10分鐘。
        - 從所有 20/30 秒資料點選取尖峰使用率值，並將其傳送至 Azure 進行評量計算。
        - 根據評量屬性中指定的百分位數值 (50/90/95/第 99) ，十分鐘的點會以遞增順序排序，並使用適當的百分位數值來計算評量。
    - 針對伺服器遷移，設備會開始收集 VM 資料，並將其複製到 Azure。
4. **評定和遷移**：您現在可以使用 Azure Migrate 伺服器評量，從設備所收集的中繼資料建立評量。 此外，您也可以使用 Azure Migrate Server 遷移來開始遷移 VMware Vm，以協調無代理程式 VM 複寫。

## <a name="appliance-upgrades"></a>設備升級

設備會隨著應用在裝置上執行的 Azure Migrate 代理程式更新而升級。 因為預設會在設備上啟用自動更新，所以會自動發生這種情況。 您可以變更此預設設定，以手動更新代理程式。

您可以將 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance 「自動更新」機碼設定為 0 (DWORD) ，以關閉登錄中的自動更新。


## <a name="next-steps"></a>後續步驟

請[參閱](migrate-appliance.md)設備支援矩陣。