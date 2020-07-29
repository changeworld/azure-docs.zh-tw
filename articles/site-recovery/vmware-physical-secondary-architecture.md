---
title: 架構-使用 Azure Site Recovery 在次要網站上進行 VMware/實體嚴重損壞修復
description: 本文概要說明使用 Azure Site Recovery.進行將內部部署 VMware 或實體 Windows/Linux 伺服器復原到次要 VMware 站台的災害復原期間，所使用的元件和架構。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: b0a46dcf8fe298494a53713f122b1bda8ce07e5e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "73954582"
---
# <a name="architecture-for-vmwarephysical-server-replication-to-a-secondary-on-premises-site"></a>將 VMware/實體伺服器複寫到次要內部部署站台的架構

本文說明使用 [Azure Site Recovery](site-recovery-overview.md) 設定內部部署 VMware 虛擬機器 (VM) 或實體 Windows/Linux 伺服器至次要 VMware 網站的災害復原複寫、容錯移轉及復原時所使用的架構和程序。


## <a name="architectural-components"></a>架構元件

**領域** | **元件** | **詳細資料**
--- | --- | ---
**Azure** | 您會使用 InMage Scout 部署此案例。 | 若要取得 InMage Scout，您必須要有 Azure 訂用帳戶。<br/><br/> 建立復原服務保存庫之後，您可下載 InMage Scout 並安裝最新的更新，以設定部署。
**進程伺服器** | 位於主要網站 | 您部署處理序伺服器來處理快取、壓縮和資料最佳化。<br/><br/> 它也會處理您想要保護的機器的整合代理程式推入安裝。
**設定伺服器** | 位於次要網站 | 組態伺服器會使用管理網站或 vContinuum 主控台來管理、設定和監視您的部署。
**vContinuum 伺服器** | 選擇性。 與組態伺服器安裝在相同的位置。 | 它會提供主控台來管理及監視您的受保護的環境。
**主要目標伺服器** | 位於次要網站 | 主要目標伺服器保留複製的資料。 它會從處理序伺服器接收資料，在次要網站中建立複本機器，並且保存資料保留點。<br/><br/> 您需要的主要目標伺服器的數目取決於您要保護的機器數目。<br/><br/> 如果您想要容錯回復到主要網站，您也需要主要目標伺服器。 此伺服器上會安裝整合代理程式。
**VMware ESX/ESXi 和 vCenter 伺服器** |  VM 裝載於 ESX/ESXi 主機。 主機是使用 vCenter 伺服器進行管理 | 您需要 VMware 基礎結構以便複寫 VMware VM。
**VM/實體伺服器** |  安裝於 VMware VM 上的整合代理程式和您想要複寫的實體伺服器 | 此代理程式會做為所有元件之間的通訊提供者。

## <a name="replication-process"></a>複寫程序

1. 您會在每個網站 (組態、處理序、主要目標) 中設定元件伺服器，並在您要複寫的機器上安裝整合代理程式。
2. 初始複寫之後，每部機器上的代理程式會將差異複寫變更傳送至處理序伺服器。
3. 處理序伺服器會最佳化此資料，並且將其傳輸至次要網站上的主要目標伺服器。 設定伺服器會管理複寫程序。

**圖 6：VMware 到 VMware 的複寫**

![VMware 至 VMware](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>後續步驟

[設定](vmware-physical-secondary-disaster-recovery.md)將 VMware VM 和實體伺服器災害復原至次要網站的作業。
