---
title: 關於 Azure Site Recovery 設定/進程/主要目標伺服器
description: 本文提供設定、處理和主要目標伺服器的總覽，在使用 Azure Site Recovery 設定內部部署 VMware Vm 至 Azure 的災難復原時
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80062080"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>關於 Site Recovery 元件 (設定、處理、主要目標) 

本文說明 [Site Recovery](site-recovery-overview.md) 服務將 VMware vm 和實體伺服器複寫至 Azure 時，所使用的設定、處理和主要目標伺服器。

## <a name="configuration-server"></a>組態伺服器

針對內部部署 VMware Vm 和實體伺服器的災難復原，部署內部部署 Site Recovery 設定伺服器。

**設定** | **詳細資料** | **連結**
--- | --- | ---
**Components**  | 設定伺服器機器會執行所有內部部署 Site Recovery 元件，包括設定伺服器、進程伺服器和主要目標伺服器。<br/><br/> 當您設定伺服器時，系統會自動安裝所有的元件。 | [閱讀](vmware-azure-common-questions.md#configuration-server) 設定伺服器常見問題。
**角色** | 組態伺服器會協調內部部署與 Azure 之間的通訊，以及管理資料複寫。 | 深入瞭解將 [VMware](vmware-azure-architecture.md) 和 [實體伺服器](physical-azure-architecture.md) 的災難復原到 Azure 的架構。
**VMware 需求** | 針對內部部署 VMware Vm 的災難復原，您必須將設定伺服器安裝並執行為內部部署、高可用性 VMware VM。 | [深入瞭解](vmware-azure-deploy-configuration-server.md#prerequisites) 必要條件。
**VMware 部署** | 建議您使用下載的 OVA 範本來部署設定伺服器。 此方法可讓您以簡單的方式設定符合所有需求和必要條件的設定伺服器。<br/><br/> 如果基於某些原因而無法使用 OVA 範本部署 VMware VM，您可以手動設定設定伺服器機器，如下所述，以進行實體機器嚴重損壞修復。 | 使用 OVA 範本進行[部署](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)。
**實體伺服器需求** | 若要在內部部署實體伺服器上進行嚴重損壞修復，請手動部署設定伺服器。 | [深入瞭解](physical-azure-set-up-source.md#prerequisites) 必要條件。
**實體伺服器部署** | 如果無法安裝為 VMware VM，您可以將它安裝在實體伺服器上。 | 手動[部署](physical-azure-set-up-source.md#set-up-the-source-environment)設定伺服器。

## <a name="process-server"></a>處理序伺服器

進程伺服器會在容錯移轉和容錯回復期間處理複寫資料，並安裝內部部署 VMware Vm 和實體伺服器的行動服務。

**設定** | **詳細資料** | **連結**
--- | --- | ---
**部署**  | 根據預設，部署設定伺服器時，會安裝進程伺服器。 <br/><br/> 內部部署的進程伺服器需要內部部署 VMware Vm 和實體伺服器的嚴重損壞修復和複寫。 | [深入了解](vmware-azure-architecture.md#architectural-components)。
** (內部部署) 的角色** | 從已啟用複寫的機器接收復寫資料。 <br/><br/> 使用快取、壓縮和加密將複寫資料優化，然後將其傳送至 Azure 儲存體。 <br/><br/> 在您想要複寫的內部部署 VMware Vm 和實體伺服器上執行 Site Recovery 行動服務的推送安裝。 <br/><br/> 執行內部部署機器的自動探索。 | [深入了解](vmware-azure-enable-replication.md)。
**從 Azure) 容錯回復的角色 (** | 從內部部署網站容錯移轉之後，您會在 Azure 中設定處理伺服器，作為 Azure VM，以處理容錯回復至內部部署位置。<br/><br/> Azure 中的進程伺服器是暫時性的。 容錯回復完成後，即可刪除 Azure VM。 | [深入了解](vmware-azure-set-up-process-server-azure.md)。
**調整大小** | 針對較大型的部署，您可以在內部部署環境中設定額外的相應放大進程伺服器。 額外的伺服器會相應放大容量，方法是處理大量的複寫機器，以及大量的複寫流量。<br/><br/> 您可以在兩部進程伺服器之間移動機器，以平衡複寫流量的負載。 | [深入了解](vmware-azure-set-up-process-server-scale.md)。

## <a name="master-target-server"></a>主要目標伺服器

主要目標伺服器會在從 Azure 容錯回復期間，處理複寫資料。

- 根據預設，主要目標伺服器會安裝在設定伺服器上。
- 針對大型部署，您可以新增額外的個別主要目標伺服器進行容錯回復。

## <a name="next-steps"></a>後續步驟

- 檢查 VMware Vm 和實體伺服器的嚴重損壞修復 [架構](vmware-azure-architecture.md) 。
- 針對 VMware Vm 和實體伺服器到 Azure 的災難復原，檢查其 [需求和必要條件](vmware-physical-azure-support-matrix.md) 。
