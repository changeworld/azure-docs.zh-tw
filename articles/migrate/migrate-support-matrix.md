---
title: Azure Migrate 支援矩陣
description: 摘要說明 Azure Migrate 服務的支援設定和限制。
ms.topic: conceptual
ms.date: 07/23/2020
ms.author: raynew
ms.openlocfilehash: 2aab8e9caeaf61c2c8dd1bf29894b13a887e44de
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424818"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate 支援矩陣

您可以使用 [Azure Migrate 服務](./migrate-services-overview.md)來評估機器，並將其遷移至 Microsoft Azure 雲端。 本文將摘要說明 Azure Migrate 案例和部署的一般支援設定和限制。

## <a name="supported-assessmentmigration-scenarios"></a>支援的評估/移轉案例

下表摘要說明支援的探索、評估和移轉案例。

**部署** | **詳細資料** 
--- | --- 
**探索** | 您可以探索電腦中繼資料和動態效能資料。
**應用程式探索** | 您可以探索在 VMware VM 上執行的應用程式、角色和功能。 這項功能目前僅限於探索。 評量功能目前位於機器層級。 我們尚未提供應用程式、角色或以功能為基礎的評量。 
**評量** | 評估在 VMware VM、Hyper-V VM 和實體伺服器上執行的內部部署工作負載和資料。 使用 Azure Migrate Server 評量、Microsoft Data Migration Assistant (DMA) 以及其他工具和 ISV 供應專案來評估。
**移轉** | 將實體伺服器、VMware VM、Hyper-V VM、實體伺服器和雲端式 VM 上執行的工作負載和資料遷移至 Azure。 使用 Azure Migrate 伺服器評量和 Azure 資料庫移轉服務 (DMS)，以及其他工具和 ISV 供應項目來進行遷移。

> [!NOTE]
> 目前，ISV 工具無法將資料傳送至 Azure Government 中的 Azure Migrate。 您可以使用整合式 Microsoft 工具，或獨立使用合作夥伴工具。

## <a name="supported-tools"></a>支援的工具


資料表中摘要說明特定的工具支援。

**工具** | **評估** | **移轉** 
--- | --- | ---
Azure Migrate 伺服器評量 | 評估 [VMware VM](./tutorial-discover-vmware.md)、[Hyper-V VM](./tutorial-discover-hyper-v.md) 和[實體伺服器](./tutorial-discover-physical.md)。 |  不適用 (NA)
Azure Migrate 伺服器移轉 | NA | 遷移 [VMware VM](tutorial-migrate-vmware.md)、[Hyper-V VM](tutorial-migrate-hyper-v.md) 和[實體伺服器](tutorial-migrate-physical-virtual-machines.md)。
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) \(英文\) | NA | 遷移 VMware VM、Hyper-V VM、實體伺服器、公用雲端工作負載。 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| 評估 VMware VM、Hyper-V VM、實體伺服器、公用雲端工作負載。 | NA
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) \(英文\) | 評估和遷移 VMware VM、Hyper-V VM、實體伺服器、公用雲端工作負載。 |  遷移 VMware VM、Hyper-V VM、實體伺服器、公用雲端工作負載。
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) \(英文\) | 評估 VMware VM、Hyper-V VM、實體伺服器、公用雲端工作負載。| NA
[DMA](/sql/dma/dma-overview?view=sql-server-2017) | 評估 SQL Server 資料庫。 | NA
[DMS](../dms/dms-overview.md) | NA | 遷移 SQL Server、Oracle、MySQL、PostgreSQL、MongoDB。 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | 評估虛擬桌面基礎結構 (VDI) | NA
[Movere](https://www.movere.io/) | 評定 VMware Vm、Hyper-v Vm、Xen Vm、實體機器、工作站 (包括 VDI) 、公用雲端工作負載 | NA
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | NA | 遷移 VMWare VM、Hyper-V VM、Xen Vm、KVM VM、實體機器、公用雲端工作負載 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295) \(英文\)  | 評估 VMware VM、Hyper-V VM、實體伺服器、公用雲端工作負載。 | NA
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) \(英文\) | 評估 VMware VM、Hyper-V VM、實體伺服器、公用雲端工作負載和 SQL Server 資料庫。 | NA
[Webapp Migration Assistant](https://appmigration.microsoft.com/) | 評估 Web 應用程式 | 遷移 Web 應用程式。


## <a name="azure-migrate-projects"></a>Azure Migrate 專案

**支援** | **詳細資料**
--- | ---
訂用帳戶 | 您的訂用帳戶中可以有多個 Azure Migrate 專案。
Azure 權限 | 您必須有訂用帳戶中的「參與者」或「擁有者」權限，才能建立 Azure Migrate 專案。
VMware VM  | 在單一專案中評估最多 35,000 個 VMware VM。
Hyper-V VM    | 在單一專案中評估最多 35,000 個 Hyper-V VM。

一個專案可以包含 VMware VM 和 Hyper-V VM，數目上限為評量限制。

## <a name="azure-permissions"></a>Azure 權限

若要讓 Azure Migrate 與 Azure 搭配使用，您需要這些權限，才能開始評估和遷移機器。

**Task** | **權限** | **詳細資料**
--- | --- | ---
建立 Azure Migrate 專案 | 您的 Azure 帳戶需要可建立專案的權限。 | 設定 [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account)、[Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) 或 [實體伺服器](./tutorial-discover-physical.md#prepare-an-azure-user-account)。
註冊 Azure Migrate 設備| Azure Migrate 會使用輕量的 [Azure Migrate 設備](migrate-appliance.md)搭配 Azure Migrate 伺服器評量來評估機器，以及搭配 Azure Migrate 伺服器移轉來執行[無代理程式移轉](server-migrate-overview.md)。 此設備會探索機器，並將中繼資料和效能資料傳送至 Azure Migrate。<br/><br/> 註冊期間，註冊提供者 (Microsoft.OffAzure、Microsoft.Migrate 和 Microsoft.KeyVault) 會向設備中選擇的訂用帳戶註冊，讓訂用帳戶可搭配資源提供者使用。 若要註冊，您需要訂用帳戶的「參與者」或「擁有者」存取權。<br/><br/> **VMware**-在上線期間，Azure Migrate 會建立兩個 Azure Active Directory (Azure AD) 應用程式。 第一個應用程式會在設備代理程式和 Azure Migrate 服務之間進行通訊。 應用程式沒有許可權可進行 Azure 資源管理呼叫，或有資源的 Azure RBAC 存取權。 第二個應用程式只會存取在使用者訂用帳戶中建立的 Azure Key Vault，以進行無代理程式 VMware 移轉。 在無代理程式移轉中，Azure Migrate 必須建立 Key Vault 在訂用帳戶中管理複寫儲存體帳戶的存取金鑰。 它會在客戶租使用者中的 Azure Key Vault (上擁有 Azure RBAC 存取權，) 從設備起始探索。<br/><br/> **Hyper-V**-上線期間。 Azure Migrate 會建立一個 Azure AD 應用程式。 應用程式會在設備代理程式和 Azure Migrate 服務之間進行通訊。 應用程式沒有許可權可進行 Azure 資源管理呼叫，或有資源的 Azure RBAC 存取權。 | 設定 [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account)、[Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) 或[實體伺服器](./tutorial-discover-physical.md#prepare-an-azure-user-account)。
建立用於 VMware 無代理程式移轉的金鑰保存庫 | 若要使用無代理程式的 Azure Migrate 伺服器移轉來遷移 VMware VM，Azure Migrate 會建立 Key Vault 來管理訂用帳戶中複寫儲存體帳戶的存取金鑰。 若要建立保存庫，您可以在 Azure Migrate 專案所在的資源群組上設定權限 (擁有者、參與者及使用者存取管理員)。 | [設定](./tutorial-discover-vmware.md#prepare-an-azure-user-account)權限。

## <a name="supported-geographies-public-cloud"></a>支援的地理位置 (公用雲端)

您可以在公用雲端中的幾個地理位置上建立 Azure Migrate 專案。

- 雖然您只能在這些地理位置中建立專案，但您可以評估或遷移其他目標位置的機器。
- 專案地理位置只會用來儲存探索到的資料。
- 當您建立專案時，您會選取一個地理位置。 專案和相關資源會建立在地理位置的其中一個區域中。 區域是由 Azure Migrate 服務所配置。

**地理位置** | **中繼資料儲存位置**
--- | ---
亞太地區 | 東亞或東南亞
澳大利亞 | 澳大利亞東部、澳大利亞東南部
巴西 | 巴西南部
Canada | 加拿大中部或加拿大東部
歐洲 | 北歐或西歐
法國 | 法國中部
印度 | 印度中部或印度南部
日本 |  日本東部或日本西部
南韓 | 南韓中部或南韓南部
瑞士 | 瑞士北部
United Kingdom | 英國南部或英國西部
美國 | 美國中部或美國西部 2

> [!NOTE]
> 針對瑞士地理位置，瑞士西部僅適用于 REST API 使用者，且需要核准的訂用帳戶。

## <a name="supported-geographies-azure-government"></a>支援的地理位置 (Azure Government)

**Task** | **地理位置** | **詳細資料**
--- | --- | ---
建立專案 | 美國 | 中繼資料會儲存在 US Gov 亞利桑那州、US Gov 維吉尼亞州
目標評量 | 美國 | 目標區域：US Gov 亞利桑那州、US Gov 維吉尼亞州、US Gov 德克薩斯州
目標複寫 | 美國 | 目標區域：US DoD 中部、US DoD 東部、US Gov 亞利桑那州、US Gov 愛荷華州、US Gov 德克薩斯州、US Gov 維吉尼亞州


## <a name="vmware-assessment-and-migration"></a>VMware 評量及移轉

[請參閱](migrate-support-matrix-vmware.md) VMware VM 的 Azure Migrate 伺服器評量和伺服器移轉支援矩陣。

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V 評量及移轉

[請參閱](migrate-support-matrix-hyper-v.md) Hyper-V VM 的 Azure Migrate 伺服器評量和伺服器移轉支援陣列。



## <a name="azure-migrate-versions"></a>Azure Migrate 版本

Azure Migrate 服務有兩個版本：

- **目前的版本**：您可以使用此版本來建立新的 Azure Migrate 專案、探索內部部署電腦，以及協調評量與移轉。 [深入了解](whats-new.md)。
- **先前版本**：針對使用舊版 Azure Migrate (僅支援內部部署 VMware VM 的評量) 的客戶，您現在應該改為使用目前的版本。 在舊版中，您無法建立新的 Azure Migrate 專案，或是執行新的探索。

## <a name="next-steps"></a>後續步驟

- [存取 VMware VM](./tutorial-assess-vmware-azure-vm.md) 以進行移轉。
- [存取 Hyper-V VM](tutorial-assess-hyper-v.md) 以進行移轉。