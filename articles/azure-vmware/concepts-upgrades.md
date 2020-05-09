---
title: 概念-私人雲端升級
description: 瞭解 Azure VMware 解決方案（AVS）中的重要升級程式和功能
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: c5c79625ef661f220a0ba88d6d24ab59e7228195
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740365"
---
# <a name="azure-vmware-solution-avs-upgrade-concepts"></a>Azure VMware 解決方案（AVS）升級概念

Azure VMware 解決方案（AVS）私人雲端的其中一個主要優點是為您維護平臺。 平臺維護包括自動升級至 VMware 驗證的軟體套件組合。 升級會定期完成，確保您一律擁有最新的軟體驗證版本。

## <a name="avs-private-cloud-software-upgrades"></a>AVS 私人雲端軟體升級

AVS 私用雲端平臺包含特定版本的 VMware vSphere、ESXi、vSAN 和 NSX-T 軟體。 私人雲端軟體配套會經過驗證，以用於新的私人雲端安裝，以及用於升級現有的私人雲端。

生命週期管理升級程式不需要為您的私人雲端停機。 升級程式可確保您會自動使用最新版的已驗證的 AVS 私用雲端軟體。 升級會定期套用，因此私用雲端絕不會超過最新版本之已驗證軟體配套的一個版本。 您將會收到已規劃的私人雲端升級通知。 如果您的私用雲端是在最新版的某個版本中，您可以延遲升級。

驗證時，會套用重大的修補程式和更新。 您將會在必要的重大升級前收到通知。 此原則可確保您的私用雲端會立即套用重要的修補程式和更新。

《私人雲端和叢集概念》[文章](concepts-private-clouds-clusters.md)和[常見問題](faq.md)中提供 VMware 軟體版本。

## <a name="next-steps"></a>後續步驟

下一步是[建立私人雲端](tutorial-create-private-cloud.md)。

<!-- LINKS - external -->

<!-- LINKS - internal -->
