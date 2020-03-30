---
title: 設置 Azure 遷移應用程式
description: 瞭解如何設置 Azure 遷移設備以評估和遷移 VMware VM。
ms.topic: article
ms.date: 11/18/2019
ms.openlocfilehash: 0447443bb6a642cac09566450ea2d9bb6f6453d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337658"
---
# <a name="set-up-an-azure-migrate-appliance"></a>已設定 Azure Migrate 設備

本文總結了設置 Azure 遷移設備的選項。 

Azure 遷移設備是本地的羽量級設備部署，用於多種方案。

**案例** | **詳細資料**
--- | ---
使用 Azure 遷移評估 VMware VM：伺服器評估 | 發現 VM 應用和依賴項<br/><br/> 收集機器中繼資料和性能中繼資料進行評估。
使用 Azure 遷移評估超 VM：伺服器評估 | 發現超虛擬機器<br/><br/> 收集機器中繼資料和性能中繼資料進行評估。
評估物理機器 | 發現電腦為物理伺服器<br/><br/> 收集機器中繼資料和性能中繼資料進行評估。
使用無代理遷移複製 VMware VM。 | 複製 VMware VM，而無需在要複製的 VM 上安裝任何內容。


## <a name="deployment-options"></a>部署選項

您可以通過多種方式部署設備。

**案例** | **範本** | **指令碼** 
--- | --- | --- | ---
**評估 VMware VM** | 使用下載的 OVA 範本進行部署。<br/><br/> 瞭解如何[使用範本](how-to-set-up-appliance-vmware.md)部署設備，或啟動[評估教程](tutorial-prepare-vmware.md)，並在本教程期間使用範本部署設備。  | 使用 PowerShell 安裝程式腳本進行部署。<br/><br/>  [查看](deploy-appliance-script.md)設備腳本說明。
**評估 Hyper-V VMs** | 使用下載的 VHD 範本進行部署。 <br/><br/> 瞭解如何[使用範本](how-to-set-up-appliance-vmware.md)部署設備，或啟動[評估教程](tutorial-prepare-vmware.md)，並在本教程期間使用範本部署設備。 | 使用 PowerShell 安裝程式腳本進行部署。<br/><br/> [查看](deploy-appliance-script.md)設備腳本說明。 
**評估實體伺服器** | 沒有範本。 | 使用 PowerShell 安裝程式腳本進行部署。<br/><br/> 查看[設備腳本說明](how-to-set-up-appliance-physical.md)，或啟動[評估教程](tutorial-prepare-physical.md)，並在本教程中部署設備。
**複製 VMware VM（無代理）** | 使用下載的 OVA 範本進行部署。<br/><br/> 如果您已經評估了複製的 VM，則已在評估期間設置了設備。<br/><br/> 如果在不對其進行評估的情況下複製 VMware VM，請瞭解如何使用範本部署設備，或啟動[無代理遷移教程](tutorial-migrate-vmware.md)，並在本教程期間使用範本部署設備。 | 使用 PowerShell 安裝程式腳本進行部署。 <br/><br/> [查看](deploy-appliance-script.md)設備腳本說明。 




## <a name="next-steps"></a>後續步驟

[查看](migrate-appliance.md)設備要求。