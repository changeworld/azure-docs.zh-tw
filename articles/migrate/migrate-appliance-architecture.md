---
title: Azure Migrate 設備架構
description: 提供伺服器評估和遷移中所使用的 Azure Migrate 設備的總覽。
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: d55d123bb056b46b5e78dd8ac836eeaf9b42fe70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80389013"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure Migrate 設備架構

本文說明 Azure Migrate 設備架構和流程。 Azure Migrate 設備是一種部署在內部部署環境的輕量設備，用來探索要遷移至 Azure 的 Vm 和實體伺服器。 

## <a name="deployment-scenarios"></a>部署案例

在下列案例中會使用 Azure Migrate 設備。

**案例** | **工具** | **用於** 
--- | --- | ---
**VMware VM 評估** | Azure Migrate：伺服器評估 | 探索 VMware Vm。<br/><br/> 探索機器應用程式和相依性。<br/><br/> 收集機器中繼資料和效能中繼資料，並將其傳送至 Azure。
**VMware VM 遷移（無代理程式）** | Azure Migrate：伺服器遷移 | 探索 VMware Vm<br/><br/>  使用[無代理程式遷移](server-migrate-overview.md)來複寫 VMware vm。
**Hyper-v VM 評估** | Azure Migrate：伺服器評估 | 探索 Hyper-v Vm。<br/><br/> 收集機器中繼資料和效能中繼資料，並將其傳送至 Azure。
**實體機器** |  Azure Migrate：伺服器評估 |  探索實體伺服器。<br/><br/> 收集機器中繼資料和效能中繼資料，並將其傳送至 Azure。

## <a name="appliance-components"></a>設備元件

設備有許多元件。

- **管理應用程式**：這是在設備部署期間用於使用者輸入的 web 應用程式。 用來評估要遷移至 Azure 的機器。
- **探索代理程式**：代理程式會收集電腦設定資料。 用來評估要遷移至 Azure 的機器。 
- **評量代理程式**：代理程式會收集效能資料。 用來評估要遷移至 Azure 的機器。
- **DRA 代理程式**：協調 VM 複寫，並協調複寫機器與 Azure 之間的通訊。 只有在使用無代理程式遷移將 VMware Vm 複寫至 Azure 時才使用。
- **閘道**：將複寫的資料傳送至 Azure。 只有在使用無代理程式遷移將 VMware Vm 複寫至 Azure 時才使用。
- **自動更新服務**：更新設備元件（每隔24小時執行一次）。



## <a name="appliance-deployment"></a>設備部署

- 您可以使用[hyper-v](how-to-set-up-appliance-hyper-v.md)或[vmware](how-to-set-up-appliance-vmware.md)的範本，或使用適用于[VMware/hyper-v](deploy-appliance-script.md)的 PowerShell 腳本安裝程式，以及[實體伺服器](how-to-set-up-appliance-physical.md)來設定 Azure Migrate 設備。 
- 設備支援需求和部署必要條件會在[設備支援矩陣](migrate-appliance.md)中匯總。


## <a name="appliance-registration"></a>設備註冊

在設備安裝期間，您會向 Azure Migrate 註冊設備，並在資料表中摘要說明的動作。

**動作** | **詳細資料** | **權限**
--- | --- | ---
**註冊來源提供者** | 這些資源提供者會在您于設備設定期間所選擇的訂用帳戶中註冊： OffAzure、Microsoft. 遷移和 KeyVault。<br/><br/> 註冊資源提供者可將您的訂用帳戶設定為可搭配資源提供者使用。 | 若要註冊資源提供者，您必須具有訂用帳戶的「參與者」或「擁有者」角色。
**建立 Azure AD 應用程式通訊** | Azure Migrate 會建立 Azure Active Directory （Azure AD）應用程式，以便在應用裝置上執行的代理程式和其各自在 Azure 上執行的服務之間進行通訊（驗證和授權）。<br/><br/> 此應用程式沒有許可權，無法在任何資源上進行 Azure Resource Manager 呼叫或 RBAC 存取。 | 您需要[這些許可權](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance)，才能建立應用程式 Azure Migrate。
**建立 Azure AD 應用程式-金鑰保存庫** | 此應用程式只會針對 VMware Vm 到 Azure 的無代理程式遷移而建立。<br/><br/> 它會專門用來存取在使用者的訂用帳戶中建立的金鑰保存庫，以進行無代理程式遷移。<br/><br/> 當從設備啟動探索時，它在 Azure 金鑰保存庫（在客戶的租使用者中建立）上具有 RBAC 存取權。 | 您需要[這些許可權](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance)，才能建立應用程式 Azure Migrate。



## <a name="collected-data"></a>收集的資料

所有部署案例的用戶端所收集的資料會在[設備支援矩陣](migrate-appliance.md)中匯總。

## <a name="discovery-and-collection-process"></a>探索和收集程式

![架構](./media/migrate-appliance-architecture/architecture.png)

設備會使用下列程式，與 vCenter Server 和 Hyper-v 主機/叢集通訊。

1. **開始探索**：
    - 當您在 Hyper-v 應用裝置上啟動探索時，它會與 WinRM 埠5985（HTTP）和5986（HTTPS）上的 Hyper-v 主機通訊。
    - 當您在 VMware 設備上啟動探索時，它預設會與 TCP 埠443上的 vCenter server 通訊。 如果 vCenter server 在不同的埠上接聽，您可以在應用裝置 web 應用程式中加以設定。
2. **收集中繼資料和效能資料**：
    - 設備會使用通用訊息模型（CIM）會話，從埠5985和5986上的 Hyper-v 主機收集 Hyper-v VM 資料。
    - 設備預設會與埠443通訊，以從 vCenter Server 收集 VMware VM 資料。
3. **傳送資料**：設備會將收集到的資料傳送至 Azure Migrate Server 評估，並透過 SSL 埠 443 Azure Migrate 伺服器遷移。 設備可以透過網際網路連線到 Azure，或您可以使用 ExpressRoute 搭配公用/Microsoft 對等互連。
    - 針對效能資料，應用裝置會收集即時使用量資料。
        - VMware 每隔20秒會收集一次效能資料，針對每個效能計量，每隔30秒為 Hyper-v。
        - 收集的資料會匯總以建立10分鐘的單一資料點。
        - [尖峰使用率] 值會從所有 20/30 秒的資料點選取，並傳送至 Azure 進行評估計算。
        - 根據評估屬性中指定的百分位數值（第50個/第10部/第 95/第99），以遞增順序排序十分鐘點，並使用適當的百分位數值來計算評量
    - 針對伺服器遷移，應用裝置會開始收集 VM 資料，並將其複寫至 Azure。
4. **評估和遷移**：您現在可以從使用 Azure Migrate Server 評估的設備所收集的中繼資料來建立評量。 此外，您也可以使用 Azure Migrate Server 遷移來開始遷移 VMware Vm，以協調無代理程式 VM 複寫。





## <a name="appliance-upgrades"></a>設備升級

設備會隨著應用裝置上執行的 Azure Migrate 代理程式更新而升級。 這會自動發生，因為預設會在設備上啟用自動更新。 您可以變更此預設設定，以手動更新代理程式。

您可以將 HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance 「自動更新」金鑰設定為0（DWORD），以關閉登錄中的自動更新。

 

## <a name="next-steps"></a>後續步驟

請[參閱](migrate-appliance.md)設備支援矩陣。

