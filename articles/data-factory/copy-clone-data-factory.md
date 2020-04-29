---
title: 如何複製或重製 Azure Data Factory 中的資料處理站
description: 了解如何複製或重製 Azure Data Factory 中的資料處理站
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 5e44bda8648fbf26487b04cf36a8fd0ec085c411
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414097"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>如何複製或重製 Azure Data Factory 中的資料處理站

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

此文章說明如何複製或重製 Azure Data Factory 中的資料處理站。

## <a name="use-cases-for-cloning-a-data-factory"></a>重製資料處理站的使用案例

以下是一些您在複製或重製資料處理站時可能會發現很實用的情況：

-   **重新命名資源**。 Azure 不支援重新命名資源。 若要重新命名資料處理站，您可以使用不同的名稱重製資料處理站，然後刪除現有的資料處理站。

-   **針對變更進行偵錯** (當偵錯功能不敷使用時)。 有時候，若要測試您的變更，您可以在不同的處理站測試您的變更，然後再將它們套用到您的主處理站。 在大部分情況中，您都可以使用「偵錯」。 不過，觸發程序中的變更 (例如您的變更在自動叫用觸發程序時的行為方式或您的變更在一段期間內的行為) 可能無法輕鬆地在未簽入的情況下測試。 在這些案例中，重製處理站並在該處套用您的變更非常有意義。 由於 Azure Data Factory 主要依回合數目計費，第二個處理站不會導致產生任何額外的費用。

## <a name="how-to-clone-a-data-factory"></a>如何重製資料處理站

1. Azure 入口網站中的 Data Factory UI 可讓您隨著可讓您定義任何想要在重製您的資料處理站時變更之值的參數檔案將您整個資料處理站的承載匯出到 Resource Manager 範本。

1. 前提是您必須從 Azure 入口網站建立您的目標資料處理站。

1. 如果您的來源 factory 中有 SelfHosted IntegrationRuntime，您必須在目標處理站中使用相同的名稱來 precreate 它。 如果您想要在不同的工廠之間共用 SelfHosted 的 IRs，可以使用[這裡](source-control.md#best-practices-for-git-integration)發佈的模式。

1. 若您處於 GIT 模式，每次您從入口網站發行時，處理站的 Resource Manager 範本都會儲存到 GIT 中 (存放庫的 adf_publish 分支)。

1. 針對其他案例，您可以按一下入口網站中的 [匯出 Resource Manager 範本]**** 按鈕，以下載 Resource Manager 範本。

1. 下載 Resource Manager 範本之後，您可以透過標準 Resource Manager 範本部署方法來部署它。

1. 基於安全性理由，產生的 Resource Manager 範本不包含任何祕密資訊，例如連結服務的密碼。 因此，您必須以部署參數方式提供這些密碼。 若不想要提供參數，您必須從 Azure Key Vault 取得連結服務的連接字串與密碼。

## <a name="next-steps"></a>後續步驟

檢閱[使用Azure Data Factory UI 建立資料處理站](quickstart-create-data-factory-portal.md)以取得在 Azure 入口網站中建立資料處理站的指導方針。
