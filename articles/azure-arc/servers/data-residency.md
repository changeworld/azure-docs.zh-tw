---
title: 資料存留處
description: 資料存放區，以及 Azure Arc 啟用的伺服器的相關資訊。
ms.topic: reference
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 73acb07b8caa3c1368c936463f04969c29d37985
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327760"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Azure Arc 啟用的伺服器：資料存放區

本文說明資料存放區的概念，以及它如何套用至 Azure Arc 啟用的伺服器。

Azure Arc 啟用的伺服器 **[適用](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** 于 **美國、歐洲、英國或亞太地區**。

## <a name="data-residency"></a>資料存留處

Azure Arc 啟用的伺服器會儲存 [AZURE VM 擴充](manage-vm-extensions.md) 功能設定 (也就是說，擴充功能所) 的屬性值，必須在連線的電腦上啟用時才需要指定。 例如，當您啟用 Log Analytics VM 擴充功能時，它會要求 Log Analytics **工作區識別碼** 和 **主要金鑰**。

也會收集有關已連線電腦的中繼資料資訊。 尤其是：

* 作業系統名稱和版本
* 電腦名稱
* 電腦的完整功能變數名稱 (FQDN) 
* 連線的機器代理程式版本

啟用 Arc 的伺服器可讓您指定將儲存資料的區域。 Microsoft 可能會複寫到其他區域以進行資料復原，但 Microsoft 不會在地理位置外部複寫或移動資料。 這項資料會儲存在設定 Azure Arc 機資源的區域中。 例如，如果電腦已在美國東部區域的 Arc 註冊，此資料會儲存在美國地區。

如需有關區域復原和合規性支援的詳細資訊，請參閱 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)。

## <a name="next-steps"></a>後續步驟

深入瞭解如何設計 [Azure 復原](/azure/architecture/reliability/architect)。