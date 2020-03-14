---
title: VMware 嚴重損壞修復-Azure Site Recovery 中的設定伺服器需求
description: 本文說明部署組態伺服器以便使用 Azure Site Recovery 進行 VMware 至 Azure 的災害復原時的支援和需求
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 0b0942b517c8dc83c048bd1203a58d9861515dfb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257403"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>VMware 至 Azure 災害復原的組態伺服器需求

當您使用 [Azure Site Recovery](site-recovery-overview.md) 將 VMware VM 和實體伺服器災害復原到 Azure 時，便會部署內部部署設定伺服器。

- 設定伺服器會協調內部部署 VMware 與 Azure 之間的通訊。 它也會管理資料複寫。
- [深入了解](vmware-azure-architecture.md)組態伺服器元件和程序。

## <a name="configuration-server-deployment"></a>組態伺服器部署

針對 VMware VM 至 Azure 的災害復原，您可以部署組態伺服器作為 VMware VM。

- Site Recovery 會提供您從 Azure 入口網站下載的 OVA 範本，並將該範本匯入至 vCenter Server 來設定組態伺服器 VM。
- 當您使用.OVA 範本部署組態伺服器時，VM 會自動遵從本文所列的需求。
- 我們強烈建議您使用.OVA 範本設定組態伺服器。 不過，如果您要設定 VMware VM 災害復原且無法使用 OVA 範本，您可以使用[所提供的這些指示](physical-azure-set-up-source.md)來部署組態伺服器。
- 如果您要部署組態伺服器，以便進行內部部署實體機器至 Azure 的災害復原，請遵循[這篇文章](physical-azure-set-up-source.md)中的指示。 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>後續步驟
為 [VMware VM](vmware-azure-tutorial.md) 設定以 Azure 作為目標的災害復原。
