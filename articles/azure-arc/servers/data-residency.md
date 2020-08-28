---
title: 資料存留處
description: 資料存放區，以及 Azure Arc 啟用的伺服器 (preview) 的相關資訊。
ms.topic: reference
ms.date: 08/25/2020
ms.custom: references_regions
ms.openlocfilehash: 8f207f5889c1764eebcc6081960ff70c0d5bca3a
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048851"
---
# <a name="azure-arc-enabled-servers-preview-data-residency"></a>Azure Arc 啟用的伺服器 (預覽) ：資料存放區

本文說明資料落地的概念，以及它如何套用至 Azure Arc 啟用的伺服器 (preview) 。

Azure Arc 啟用的伺服器 (預覽) **[可在](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)****美國、歐洲或亞太地區**預覽。

## <a name="data-residency"></a>資料存留處

Azure Arc 已啟用的伺服器 (預覽) 儲存 [AZURE VM 延伸](manage-vm-extensions.md) 模組設定設定 (也就是說，擴充功能所需的屬性) 值，必須在連線的電腦上啟用時才進行指定。 例如，當您啟用 Log Analytics VM 擴充功能時，它會要求 Log Analytics **工作區識別碼** 和 **主要金鑰**。

也會收集有關已連線電腦的中繼資料資訊。 具體來說：

* 作業系統名稱和版本
* 電腦名稱
* 電腦的完整功能變數名稱 (FQDN) 
* 連線的機器代理程式版本

啟用 Arc 的伺服器 (preview) 可讓您指定儲存資料的區域。 Microsoft 可能會複寫到其他區域以進行資料復原，但 Microsoft 不會在地理位置外部複寫或移動資料。 這項資料會儲存在設定 Azure Arc 機資源的區域中。 例如，如果電腦已在美國東部區域的 Arc 註冊，此資料會儲存在美國地區。

如需有關區域復原和合規性支援的詳細資訊，請參閱 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)。

## <a name="next-steps"></a>後續步驟

深入瞭解如何設計 [Azure 復原](/azure/architecture/reliability/architect)。