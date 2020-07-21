---
title: 包含檔案
description: 包含檔案
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 07/08/2020
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 60012f79c3c04a4ff14c4a7f0609b6940d3402c4
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544237"
---
**設定和處理伺服器需求**


## <a name="hardware-requirements"></a>硬體需求

**元件** | **需求** 
--- | ---
CPU 核心 | 8 
RAM | 16 GB
磁碟數量 | 3，包括作業系統磁碟、處理序伺服器快取磁碟和用於容錯回復的保留磁碟機 
可用磁碟空間 (處理序伺服器快取) | 600 GB
可用磁碟空間 (保留磁碟) | 600 GB
 | 

## <a name="software-requirements"></a>軟體需求

**元件** | **需求** 
--- | ---
作業系統 | Windows Server 2012 R2 <br> Windows Server 2016
作業系統地區設定 | 英文（en-*）
Windows Server 角色 | 請勿啟用這些角色： <br> - Active Directory Domain Services <br>- 網際網路資訊服務 <br> - Hyper-V 
群組原則 | 請勿啟用這些群組原則： <br> - 防止存取命令提示字元。 <br> - 防止存取登錄編輯工具。 <br> - 檔案附件的信任邏輯。 <br> - 開啟指令碼執行。 <br> [深入了解](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 沒有預先存在的預設網站 <br> - 沒有預先存在的網站/應用程式接聽連接埠 443 <br>- 啟用[匿名驗證](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - 啟用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定 
FIPS （聯邦資訊處理標準） | 不要啟用 FIPS 模式
|

## <a name="network-requirements"></a>網路需求

**元件** | **需求** 
--- | --- 
IP 位址類型 | 靜態 
連接埠 | 443 (控制通道協調流程)<br>9443 (資料傳輸) 
NIC 類型 | VMXNET3 （如果設定伺服器是 VMware VM）
 |
**網際網路存取**（伺服器需要直接或透過 proxy 存取下列 url）：|
\*.backup.windowsazure.com | 用於所複寫資料的轉送和協調
\*.blob.core.windows.net | 用來存取儲存已複寫資料的儲存體帳戶。 您可以提供快取儲存體帳戶的特定 URL。
\*.hypervrecoverymanager.windowsazure.com | 用於複寫管理作業和協調
https:\//login.microsoftonline.com | 用於複寫管理作業和協調 
time.nist.gov | 用來檢查系統與通用時間之間的時間同步處理
time.windows.com | 用來檢查系統與通用時間之間的時間同步處理
| <ul> <li> https:\//management.azure.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> HTTPs： \/ /login.live.com </li><li> HTTPs： \/ /graph.windows.net </li><li> https:\//login.windows.net </li><li> *. services.visualstudio.com （選擇性） </li><li> HTTPs： \/ /www.live.com </li><li> HTTPs： \/ /www.microsoft.com </li></ul> | OVF 安裝程式需要存取這些額外的 Url。 它們是用於 Azure Active Directory 的存取控制和身分識別管理。
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | 以完成 MySQL 下載。 </br> 在幾個區域中，下載可能會重新導向至 CDN URL。 必要時，請確定 CDN URL 也會列入允許清單。
|

> [!NOTE]
> 如果您有[私人連結](../articles/site-recovery/hybrid-how-to-enable-replication-private-endpoints.md)可與 Site Recovery 保存庫連線，則設定伺服器不需要任何額外的網際網路存取。 這項例外狀況是在使用 OVA 範本設定 CS 機器時，您需要存取下列 Url （而不是私用連結存取- https://management.azure.com 、 https://www.live.com 和） https://www.microsoft.com 。 如果您不想要允許存取這些 Url，請使用統一安裝程式來設定 CS。

## <a name="required-software"></a>必要的軟體

**元件** | **需求** 
--- | ---
VMware vSphere PowerCLI | 如果組態伺服器在 VMware 虛擬機器上執行，則應該安裝 [PowerCLI 6.0 版](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)。
MySQL | 應該安裝 MySQL。 您可以手動安裝，或者 Site Recovery 可以安裝。 (如需詳細資訊，請參閱[進行設定](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings))
|

## <a name="sizing-and-capacity-requirements"></a>調整大小和容量需求

下表彙總組態伺服器的容量需求。 如果您要複寫多個 VMware Vm，請參閱[容量規劃考慮](../articles/site-recovery/site-recovery-plan-capacity-vmware.md)，並執行[Azure Site Recovery 部署規劃工具工具](../articles/site-recovery/site-recovery-deployment-planner.md)。


**CPU** | **記憶體** | **快取磁片** | **資料變更率** | **複寫的機器**
--- | --- | --- | --- | ---
8 個 vCPU<br/><br/> 2 個插槽 * 4 個核心 \@ 2.5 GHz | 16 GB | 300 GB | 500 GB 或更少 | < 100 部機器
12 個 vCPU<br/><br/> 2 個插槽 * 6 個核心 \@ 2.5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 到 150 部機器
16 個 vCPU<br/><br/> 2 個插槽 * 8 個核心 \@ 2.5 GHz | 32 GB | 1 TB | 1-2 TB | 150 到 200 部機器
|

