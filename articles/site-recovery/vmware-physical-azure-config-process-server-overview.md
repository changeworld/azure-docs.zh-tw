---
title: 關於 Azure 網站恢復配置/進程/主目標伺服器
description: 本文概述了使用 Azure 網站恢復將本地 VMware VM 的災害復原設置為 Azure 時使用的配置、進程和主目標伺服器。
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062080"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>關於網站恢復元件（配置、流程、主目標）

本文介紹了[網站恢復](site-recovery-overview.md)服務用於將 VMware VM 和物理伺服器複製到 Azure 的配置、進程和主目標伺服器。

## <a name="configuration-server"></a>組態伺服器

對於本地 VMware VM 和物理伺服器的災害復原，部署本地網站恢復佈建服務器。

**設定** | **詳細資料** | **連結**
--- | --- | ---
**元件**  | 佈建服務器電腦運行所有本地網站恢復元件，包括佈建服務器、進程伺服器和主目標伺服器。<br/><br/> 設置佈建服務器時，將自動安裝所有元件。 | [閱讀](vmware-azure-common-questions.md#configuration-server)佈建服務器常見問題解答。
**角色** | 組態伺服器會協調內部部署與 Azure 之間的通訊，以及管理資料複寫。 | 詳細瞭解[VMware](vmware-azure-architecture.md)和 Azure[物理伺服器](physical-azure-architecture.md)災害復原的體系結構。
**VMware 要求** | 要恢復本地 VMware VM 的災害復原，必須作為本地高度可用的 VMware VM 安裝和回合組態伺服器。 | [瞭解](vmware-azure-deploy-configuration-server.md#prerequisites)先決條件。
**VMware 部署** | 我們建議您使用下載的 OVA 範本部署佈建服務器。 此方法提供了一種設置符合所有要求和先決條件的佈建服務器的簡單方法。<br/><br/> 如果由於某種原因無法使用 OVA 範本部署 VMware VM，則可以手動設置佈建服務器電腦，如下所述物理電腦災害復原。 | 使用 OVA 範本[進行部署](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)。
**實體伺服器需求** | 要在本地物理伺服器上進行災害復原，可以手動部署佈建服務器。 | [瞭解](physical-azure-set-up-source.md#prerequisites)先決條件。
**物理伺服器部署** | 如果無法將其安裝為 VMware VM，則可以將其安裝在物理伺服器上。 | 手動[部署](physical-azure-set-up-source.md#set-up-the-source-environment)佈建服務器。

## <a name="process-server"></a>處理序伺服器

進程伺服器在容錯移轉和容錯移轉期間處理複製資料，並為本地 VMware VM 和物理伺服器安裝移動服務。

**設定** | **詳細資料** | **連結**
--- | --- | ---
**部署**  | 預設情況下，部署佈建服務器時，將安裝進程伺服器。 <br/><br/> 需要本地進程伺服器來恢復和複製本地 VMware VM 和物理伺服器。 | [深入了解](vmware-azure-architecture.md#architectural-components)。
**角色（本地**） | 從啟用用於複製的電腦接收復制資料。 <br/><br/> 使用緩存、壓縮和加密優化複製資料，並將其發送到 Azure 存儲。 <br/><br/> 在要複製的本地 VMware VM 和物理伺服器上執行網站恢復移動服務的推送安裝。 <br/><br/> 執行本地電腦的自動探索。 | [深入了解](vmware-azure-enable-replication.md)。
**角色（從 Azure 失敗）** | 從本地網站進行容錯移轉後，在 Azure 中設置進程伺服器（作為 Azure VM），以處理故障回退到本地位置。<br/><br/> Azure 中的進程伺服器是臨時的。 完成故障恢復後，可以刪除 Azure VM。 | [深入了解](vmware-azure-set-up-process-server-azure.md)。
**調整大小** | 對於較大的部署，可以在本地設置其他橫向擴展進程伺服器。 其他伺服器通過處理大量複製電腦和大量複製流量來擴展容量。<br/><br/> 您可以在兩個進程伺服器之間移動電腦，以便負載平衡複製流量。 | [深入了解](vmware-azure-set-up-process-server-scale.md)。

## <a name="master-target-server"></a>主要目標伺服器

主要目標伺服器會在從 Azure 容錯回復期間，處理複寫資料。

- 預設情況下，主目標伺服器安裝在佈建服務器上。
- 針對大型部署，您可以新增額外的個別主要目標伺服器進行容錯回復。

## <a name="next-steps"></a>後續步驟

- 查看 VMware VM 和物理伺服器的災害復原[體系結構](vmware-azure-architecture.md)。
- 查看 VMware VM 和物理伺服器到 Azure 的災害復原[的要求和先決條件](vmware-physical-azure-support-matrix.md)。
