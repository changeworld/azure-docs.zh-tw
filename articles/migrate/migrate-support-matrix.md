---
title: Azure Migrate 支援矩陣
description: 提供支援設定的摘要，以及 Azure Migrate 服務的限制。
ms.topic: conceptual
ms.date: 01/28/2020
ms.author: raynew
ms.openlocfilehash: 81939292885e33ec6397eb4a437bef63b4ad0787
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990720"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate 支援矩陣

您可以使用[Azure Migrate 服務](migrate-overview.md)來評估電腦，並將機器遷移至 Microsoft Azure 雲端。 本文摘要說明 Azure Migrate 案例和部署的一般支援設定和限制。

## <a name="supported-assessmentmigration-scenarios"></a>支援的評估/遷移案例

下表摘要說明支援的探索、評估和遷移案例。

**部署** | **詳細資料** 
--- | --- 
**應用程式特定的探索** | 您可以探索在 VMware Vm 上執行的應用程式、角色和功能。 這項功能目前僅限於探索。 評量目前位於電腦層級。 我們尚未提供應用程式、角色或特定功能的評量。 
**內部部署評量** | 評估在 VMware Vm、Hyper-v Vm 和實體伺服器上執行的內部部署工作負載和資料。 評估使用 Azure Migrate Server 評估和 Microsoft Data Migration Assistant （DMA），以及其他工具和 ISV 供應專案。
**內部部署至 Azure 的遷移** | 將實體伺服器、VMware Vm、Hyper-v Vm、實體伺服器和雲端式 VM 上執行的工作負載和資料移轉至 Azure。 使用 Azure Migrate Server 評估和 Azure 資料庫移轉服務（DMS），以及其他工具和 ISV 供應專案來進行遷移。


## <a name="supported-tools"></a>支援的工具

資料表中摘要說明特定的工具支援。

**工具** | **評估** | **移轉** 
--- | --- | ---
Azure Migrate 伺服器評量 | 評估[VMware vm](tutorial-prepare-vmware.md)、 [hyper-v vm](tutorial-prepare-hyper-v.md)和[實體伺服器](tutorial-prepare-physical.md)。 |  無法使用（NA）
Azure Migrate 伺服器移轉 | NA | 遷移[VMware vm](tutorial-migrate-vmware.md)、 [hyper-v vm](tutorial-migrate-hyper-v.md)和[實體伺服器](tutorial-migrate-physical-virtual-machines.md)。
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) \(英文\) | NA | 遷移 VMware Vm、Hyper-v Vm、實體伺服器、公用雲端工作負載。 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| 評估 VMware Vm、Hyper-v Vm、實體伺服器、公用雲端工作負載。 | NA
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) \(英文\) | 評估和遷移 VMware Vm、Hyper-v Vm、實體伺服器、公用雲端工作負載。 |  遷移 VMware Vm、Hyper-v Vm、實體伺服器、公用雲端工作負載。
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) \(英文\) | 評估 VMware Vm、Hyper-v Vm、實體伺服器、公用雲端工作負載。| NA
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | 評估內部部署 SQL Server 資料庫。 | NA
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | NA | 遷移 SQL Server、Oracle、MySQL、于 postgresql、MongoDB。 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | 評估虛擬桌面基礎結構（VDI） | NA
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | 評估 VMWare Vm、Hyper-v Vm、Xen Vm、實體機器、工作站（包括 VDI）、公用雲端工作負載 | NA
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | NA | 遷移 VMWare Vm，Hyper-v Vm，Xen Vm，KVM Vm，實體機器，公用雲端工作負載 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295) \(英文\)  | 評估 VMware Vm、Hyper-v Vm、實體伺服器、公用雲端工作負載。 | NA
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) \(英文\) | 評估 VMware Vm、Hyper-v Vm、實體伺服器、公用雲端工作負載，以及 SQL Server 資料庫。 | NA
[Webapp 移轉小幫手](https://appmigration.microsoft.com/) | 評估 web 應用程式 | 遷移 web 應用程式。


## <a name="azure-migrate-projects"></a>Azure Migrate 專案

**支援** | **詳細資料**
--- | ---
訂閱 | 您可以在訂用帳戶中有多個 Azure Migrate 專案。
Azure 權限 | 您需要訂用帳戶中的「參與者」或「擁有者」許可權，才能建立 Azure Migrate 專案。
VMware VM  | 在單一專案中評估最多35000個 VMware Vm。
Hyper-V VM | 在單一專案中評估最多35000個 Hyper-v Vm。

專案可以包含 VMware Vm 和 Hyper-v Vm，最多可達評量限制。

## <a name="azure-permissions"></a>Azure 權限

若要讓 Azure Migrate 與 Azure 搭配使用，您需要這些許可權，才能開始評估和遷移機器。

**Task** | **權限** | **詳細資料**
--- | --- | ---
建立 Azure Migrate 專案 | 您的 Azure 帳戶需要可建立專案的權限。 | 針對[VMware](tutorial-prepare-vmware.md#assign-permissions-to-create-project)、 [hyper-v](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)或[實體伺服器](tutorial-prepare-physical.md#assign-permissions-to-create-project)進行設定。
註冊 Azure Migrate 設備 | Azure Migrate 使用輕量的[Azure Migrate 設備](migrate-appliance.md)，透過 Azure Migrate server 評估來評估 vmware vm，並使用 Azure Migrate 伺服器遷移來執行 vmware vm 的[無代理程式遷移](server-migrate-overview.md)。 此設備會探索 VM，並將 VM 的中繼資料和效能資料傳送至 Azure Migrate。<br/><br/> 在註冊期間，Azure Migrate 會建立兩個可唯一識別設備的 Azure Active Directory (Azure AD) 應用程式，並且需要可建立這些應用程式的權限。<br/><br/> - 第一個應用程式會與 Azure Migrate 服務端點進行通訊。<br/><br/> - 第二個應用程式會存取在註冊期間建立的 Azure Key Vault，以儲存 Azure AD 的應用程式資訊和設備組態設定。 | 針對[VMware](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance)、 [hyper-v](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)或[實體伺服器](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance)進行設定。
建立用於 VMware 無代理程式遷移的金鑰保存庫 | 若要遷移具有無代理程式 Azure Migrate 伺服器遷移的 VMware Vm，Azure Migrate 會建立 Key Vault 來管理訂用帳戶中複寫儲存體帳戶的存取金鑰。 若要建立保存庫，您可以在 Azure Migrate 專案所在的資源群組上設定許可權（擁有者、參與者和使用者存取系統管理員）。 | [設定](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault)許可權。

## <a name="supported-geographies"></a>支援的地理位置

您可以在數個地理位置中建立 Azure Migrate 專案。 雖然您只能在這些地理位置中建立專案，但您可以評估或遷移其他目標位置的機器。 專案地理位置只會用來儲存探索到的資料。

**地理位置** | **中繼資料儲存位置**
--- | ---
Azure 政府機構 | US Gov 維吉尼亞州
亞太地區 | 東亞或東南亞
澳洲 | 澳大利亞東部或澳大利亞東南部
巴西 | 巴西南部
加拿大 | 加拿大中部或加拿大東部
歐洲 | 北歐或西歐
法國 | 法國中部
印度 | 印度中部或印度南部
日本 |  日本東部或日本西部
南韓 | 韓國中部或南韓南部
英國 | 英國南部或英國西部
美國 | 美國中部或美國西部2


 > [!NOTE]
 > Azure Government 的支援目前僅適用于[舊版](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)的 Azure Migrate。



## <a name="vmware-assessment-and-migration"></a>VMware 評估與遷移

請[參閱](migrate-support-matrix-vmware.md)適用于 VMware vm 的 Azure Migrate server 評估和伺服器遷移支援對照表。

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V 評量及移轉

請[參閱](migrate-support-matrix-hyper-v.md)適用于 hyper-v vm 的 Azure Migrate server 評估和伺服器遷移支援對照表。



## <a name="azure-migrate-versions"></a>Azure Migrate 版本

Azure Migrate 服務有兩個版本：

- **目前版本**：使用此版本，您可以建立新的 Azure Migrate 專案、探索內部部署評估，以及協調評量與遷移。 [深入了解](whats-new.md#release-version-july-2019)。
- **先前版本**：針對使用舊版 Azure Migrate 的客戶（僅支援內部部署 VMware vm 的評估），您現在應該使用目前的版本。 在先前的版本中，您無法建立新的 Azure Migrate 專案或執行新的探索。

## <a name="next-steps"></a>後續步驟

- [評估 VMware vm](tutorial-assess-vmware.md)以進行遷移。
- [評估 Hyper-v vm](tutorial-assess-hyper-v.md)以進行遷移。

