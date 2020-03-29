---
title: Azure 遷移支援矩陣
description: 提供 Azure 遷移服務的支援設置和限制摘要。
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: raynew
ms.openlocfilehash: bf719f9179384ec3dca99d2429f569ef209b5daa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127709"
---
# <a name="azure-migrate-support-matrix"></a>Azure 遷移支援矩陣

可以使用 Azure[遷移服務](migrate-overview.md)評估和將電腦遷移到 Microsoft Azure 雲。 本文總結了 Azure 遷移方案和部署的一般支援設置和限制。

## <a name="supported-assessmentmigration-scenarios"></a>支援的評估/遷移方案

下表總結了支援的發現、評估和遷移方案。

**部署** | **詳細資料** 
--- | --- 
**特定于應用的發現** | 您可以發現在 VMware VM 上運行的應用、角色和功能。 目前，此功能僅限於發現。 評估目前處於機器級別。 我們尚未提供應用、角色或特定于功能的評估。 
**本地評估** | 評估在 VMware VM、超 VM 和物理伺服器上運行的本地工作負載和資料。 使用 Azure 遷移伺服器評估和 Microsoft 資料移轉助理 （DMA） 以及其他工具和 ISV 產品進行評估。
**本地遷移到 Azure** | 將物理伺服器、VMware VM、超 VM、物理伺服器和基於雲的 VMS 上運行的工作負載和資料移轉到 Azure。 使用 Azure 遷移伺服器評估和 Azure 資料庫移轉服務 （DMS） 以及其他工具和 ISV 產品進行遷移。


## <a name="supported-tools"></a>支援工具

下表匯總了特定工具支援。

**工具** | **評定** | **遷移** 
--- | --- | ---
Azure Migrate 伺服器評量 | 評估[VMware VM、](tutorial-prepare-vmware.md)[超 VM](tutorial-prepare-hyper-v.md)和[物理伺服器](tutorial-prepare-physical.md)。 |  不可用 （NA）
Azure Migrate 伺服器移轉 | NA | 遷移[VMware VM、](tutorial-migrate-vmware.md)[超 VM](tutorial-migrate-hyper-v.md)和[物理伺服器](tutorial-migrate-physical-virtual-machines.md)。
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) \(英文\) | NA | 遷移 VMware VM、超虛擬機器、物理伺服器和公共雲工作負載。 
[雲霧化](https://www.cloudamize.com/platform#tab-0)| 評估 VMware VM、超虛擬機器、物理伺服器、公共雲工作負載。 | NA
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) \(英文\) | 評估和遷移 VMware VM、超虛擬機器、物理伺服器、公共雲工作負載。 |  遷移 VMware VM、超虛擬機器、物理伺服器和公共雲工作負載。
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) \(英文\) | 評估 VMware VM、超虛擬機器、物理伺服器、公共雲工作負載。| NA
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | 評估本地 SQL Server 資料庫。 | NA
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | NA | 遷移 SQL 伺服器、Oracle、MySQL、PostgreSQL、MongoDB。 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | 評估虛擬桌面基礎架構 （VDI） | NA
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | 評估 VMWare VM、超 VM、Xen VM、物理電腦、工作站（包括 VDI）、公共雲工作負載 | NA
[機架軟體](https://go.microsoft.com/fwlink/?linkid=2102735) | NA | 遷移 VMWare VM、超 VM、Xen VM、KVM、電腦虛擬機器、物理電腦、公共雲工作負載 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295) \(英文\)  | 評估 VMware VM、超虛擬機器、物理伺服器、公共雲工作負載。 | NA
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) \(英文\) | 評估 VMware VM、超 VM、物理伺服器、公共雲工作負載和 SQL Server 資料庫。 | NA
[Webapp 遷移助手](https://appmigration.microsoft.com/) | 評估 Web 應用 | 遷移 Web 應用。


## <a name="azure-migrate-projects"></a>Azure 遷移專案

**支援** | **詳細資料**
--- | ---
訂用帳戶 | 訂閱中可以有多個 Azure 遷移專案。
Azure 權限 | 您需要訂閱中的參與者或擁有者許可權才能創建 Azure 遷移專案。
VMware VM  | 在單個專案中評估多達 35，000 個 VMware VM。
Hyper-V VM    | 在單個專案中評估多達 35，000 台 Hyper-V VM。

專案可以同時包括 VMware VM 和超 V VM，但達到評估限制。

## <a name="azure-permissions"></a>Azure 權限

對於 Azure 遷移以使用 Azure，在開始評估和遷移電腦之前，需要這些許可權。

**任務** | **許可權** | **詳細資料**
--- | --- | ---
建立 Azure Migrate 專案 | 您的 Azure 帳戶需要可建立專案的權限。 | 為[VMware、](tutorial-prepare-vmware.md#assign-permissions-to-create-project)[超 V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)或[物理伺服器](tutorial-prepare-physical.md#assign-permissions-to-create-project)設置。
註冊 Azure Migrate 設備| Azure 遷移使用羽量級[Azure 遷移設備](migrate-appliance.md)評估具有 Azure 遷移伺服器評估的電腦，並使用 Azure 遷移伺服器遷移運行 VMware VM[的無代理遷移](server-migrate-overview.md)。 本設備發現電腦，並將中繼資料和效能資料發送到 Azure 遷移。<br/><br/> 在註冊期間，註冊提供程式（Microsoft.OffAzure、Microsoft.Migrate 和 Microsoft.KeyVault）在設備中選擇的訂閱中註冊，以便訂閱與資來源提供者一起工作。 要註冊，您需要訂閱上的參與者或擁有者存取權限。<br/><br/> **VMware**- 在載入過程中，Azure 遷移將創建兩個 Azure 活動目錄 （Azure AD） 應用。 第一個應用在設備代理和 Azure 遷移服務之間通信。 應用沒有對 Azure 資源管理調用或具有資源 RBAC 存取權限的許可權。 第二個應用訪問僅在使用者訂閱中創建的 Azure 金鑰保存庫，用於無代理 VMware 遷移。 在無代理遷移中，Azure 遷移創建金鑰保存庫來管理訂閱中複製存儲帳戶的訪問金鑰。 從設備啟動發現時，它在 Azure 金鑰保存庫（客戶租戶中）具有 RBAC 存取權限。<br/><br/> **超V**-在載入期間。 Azure 遷移將創建一個 Azure AD 應用。 應用在設備代理和 Azure 遷移服務之間通信。 應用沒有對 Azure 資源管理調用或具有資源 RBAC 存取權限的許可權。 | 為[VMware、](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance)[超 V](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)或[物理伺服器](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance)設置。
為 VMware 無代理遷移創建金鑰保存庫 | 要使用無代理 Azure 遷移伺服器遷移 VMware VM，Azure 遷移將創建一個金鑰保存庫來管理訂閱中複製存儲帳戶的訪問金鑰。 要創建保存庫，請對 Azure 遷移專案所在的資源組設置許可權（擁有者或參與者和使用者訪問管理員）。 | [設置](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault)許可權。

## <a name="supported-geographies"></a>支援的地理位置

可以在多個地理位置創建 Azure 遷移專案。 儘管只能在這些地理位置中創建專案，但可以評估或遷移其他目標位置的電腦。 專案地理位置只會用來儲存探索到的資料。

**地理** | **中繼資料儲存位置**
--- | ---
Azure Government | US Gov 維吉尼亞州
亞太地區 | 東亞或東南亞
澳大利亞 | 澳大利亞東部或澳大利亞東南部
巴西 | 巴西南部
Canada | 加拿大中部或加拿大東部
歐洲 | 北歐或西歐
法國 | 法國中部
印度 | 印度中部或南部
日本 |  日本東部或日本西部
南韓 | 韓國中部或韓國南部
United Kingdom | 英國南部或英國西部
美國 | 美國中部或美國西部 2


 > [!NOTE]
 > 對 Azure 政府的支援目前僅適用于[舊版本的](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)Azure 遷移。



## <a name="vmware-assessment-and-migration"></a>VMware 評估和遷移

[查看](migrate-support-matrix-vmware.md)VMware VM 的 Azure 遷移伺服器評估和伺服器遷移支援矩陣。

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V 評量及移轉

[查看](migrate-support-matrix-hyper-v.md)超 V VM 的 Azure 遷移伺服器評估和伺服器遷移支援矩陣。



## <a name="azure-migrate-versions"></a>Azure Migrate 版本

Azure Migrate 服務有兩個版本：

- **當前版本**：使用此版本可以創建新的 Azure 遷移專案、發現本地評估以及協調評估和遷移。 [深入了解](whats-new.md)。
- **以前的版本**：對於使用早期版本的 Azure 遷移的客戶（僅支援本地 VMware VM 評估），現在應使用當前版本。 在前面版本中，無法創建新的 Azure 遷移專案或執行新的發現。

## <a name="next-steps"></a>後續步驟

- [評估 VMware VM](tutorial-assess-vmware.md)的遷移。
- [評估超 VM](tutorial-assess-hyper-v.md)的遷移。

