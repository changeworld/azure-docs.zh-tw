---
title: 關於 Azure Site Recovery 設定/進程/主要目標伺服器
description: 本文概述使用 Azure Site Recovery 設定內部部署 VMware Vm 至 Azure 的嚴重損壞修復時的設定、處理和主要目標伺服器。
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062080"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>關於 Site Recovery 元件（設定、處理、主要目標）

本文說明[Site Recovery](site-recovery-overview.md)服務用來將 VMware vm 和實體伺服器複寫至 Azure 的設定、處理和主要目標伺服器。

## <a name="configuration-server"></a>組態伺服器

針對內部部署 VMware Vm 和實體伺服器的嚴重損壞修復，請部署內部部署 Site Recovery 設定伺服器。

**設定** | **詳細資料** | **連結**
--- | --- | ---
**元件**  | 設定伺服器電腦會執行所有內部部署 Site Recovery 元件，包括設定伺服器、進程伺服器和主要目標伺服器。<br/><br/> 設定伺服器時，會自動安裝所有元件。 | [閱讀](vmware-azure-common-questions.md#configuration-server)設定伺服器常見問題。
**角色** | 組態伺服器會協調內部部署與 Azure 之間的通訊，以及管理資料複寫。 | 深入瞭解[VMware](vmware-azure-architecture.md)和[實體伺服器](physical-azure-architecture.md)嚴重損壞修復至 Azure 的架構。
**VMware 需求** | 針對內部部署 VMware Vm 的嚴重損壞修復，您必須安裝並執行設定伺服器做為內部部署、高可用性 VMware VM。 | [瞭解](vmware-azure-deploy-configuration-server.md#prerequisites)必要條件。
**VMware 部署** | 建議您使用下載的 OVA 範本來部署設定伺服器。 此方法可讓您直接設定符合所有需求和必要條件的設定伺服器。<br/><br/> 如果基於某些原因而無法使用 OVA 範本部署 VMware VM，您可以手動設定伺服器電腦，如下所述進行實體機器嚴重損壞修復。 | 使用 OVA 範本進行[部署](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)。
**實體伺服器需求** | 針對內部部署實體伺服器上的嚴重損壞修復，您可以手動部署設定伺服器。 | [瞭解](physical-azure-set-up-source.md#prerequisites)必要條件。
**實體伺服器部署** | 如果無法安裝為 VMware VM，您可以將它安裝在實體伺服器上。 | 手動[部署](physical-azure-set-up-source.md#set-up-the-source-environment)設定伺服器。

## <a name="process-server"></a>處理序伺服器

進程伺服器會在容錯移轉和容錯回復期間處理複寫資料，並安裝內部部署 VMware Vm 和實體伺服器的行動服務。

**設定** | **詳細資料** | **連結**
--- | --- | ---
**部署**  | 根據預設，部署設定伺服器時，會安裝進程伺服器。 <br/><br/> 需要內部部署進程伺服器，才能進行內部部署 VMware Vm 和實體伺服器的嚴重損壞修復和複寫。 | [深入了解](vmware-azure-architecture.md#architectural-components)。
**角色（內部部署**） | 從啟用複寫的機器接收復寫資料。 <br/><br/> 使用快取、壓縮和加密來優化複寫資料，並將其傳送至 Azure 儲存體。 <br/><br/> 在您想要複寫的內部部署 VMware Vm 和實體伺服器上執行 Site Recovery 行動服務的推送安裝。 <br/><br/> 執行內部部署機器的自動探索。 | [深入了解](vmware-azure-enable-replication.md)。
**角色（從 Azure 容錯回復）** | 從內部部署網站容錯移轉之後，您會在 Azure 中將進程伺服器設定為 Azure VM，以處理容錯回復至您的內部部署位置。<br/><br/> Azure 中的進程伺服器是暫時性的。 容錯回復完成之後，即可刪除 Azure VM。 | [深入了解](vmware-azure-set-up-process-server-azure.md)。
**調整大小** | 對於較大型的部署，內部部署您可以設定額外的相應放大進程伺服器。 額外的伺服器會藉由處理較大量的複寫電腦和較大量的複寫流量來相應放大容量。<br/><br/> 您可以在兩個進程伺服器之間移動機器，以便對複寫流量進行負載平衡。 | [深入了解](vmware-azure-set-up-process-server-scale.md)。

## <a name="master-target-server"></a>主要目標伺服器

主要目標伺服器會在從 Azure 容錯回復期間，處理複寫資料。

- 根據預設，主要目標伺服器會安裝在設定伺服器上。
- 針對大型部署，您可以新增額外的個別主要目標伺服器進行容錯回復。

## <a name="next-steps"></a>後續步驟

- 檢查 VMware Vm 和實體伺服器的嚴重損壞修復[架構](vmware-azure-architecture.md)。
- 針對 VMware Vm 和實體伺服器至 Azure 的嚴重損壞修復，檢查其[需求和必要條件](vmware-physical-azure-support-matrix.md)。
