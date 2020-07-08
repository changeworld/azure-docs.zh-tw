---
title: 如何複製或重製 Azure Data Factory 中的資料處理站
description: 了解如何複製或重製 Azure Data Factory 中的資料處理站
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: chez-charlie
ms.author: chez
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 304c39f4b6f7852068d4e72adfad2d41eeefc26c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85552958"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>如何複製或重製 Azure Data Factory 中的資料處理站

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

此文章說明如何複製或重製 Azure Data Factory 中的資料處理站。

## <a name="use-cases-for-cloning-a-data-factory"></a>重製資料處理站的使用案例

以下是一些您在複製或重製資料處理站時可能會發現很實用的情況：

- **將 Data Factory 移**至新的區域。 如果您想要將 Data Factory 移至不同的區域，最好的方式是在目的地區域中建立複本，並刪除現有的複本。

- 重新**命名 Data Factory**。 Azure 不支援重新命名資源。 如果您想要重新命名資料處理站，您可以使用不同的名稱來複製 data factory，並刪除現有的資料處理站。

- **針對變更進行偵錯** (當偵錯功能不敷使用時)。 在大部分的情況下，您可以使用[Debug](iterative-development-debugging.md)。 在其他情況下，在複製的沙箱環境中測試變更會比較合理。 比方說，當觸發程式在檔案抵達與輪轉時間範圍以外引發時，參數化的 ETL 管線會如何運作，而不容易透過單獨的 Debug 進行測試。 在這些情況下，您可能會想要複製沙箱環境來進行實驗。 因為 Azure Data Factory 的費用主要是執行次數，所以第二個工廠不會導致任何額外的費用。

## <a name="how-to-clone-a-data-factory"></a>如何重製資料處理站

1. 必要條件是，您必須先從 Azure 入口網站建立目標 data factory。

1. 如果您處於 GIT 模式：
    1. 每次從入口網站發佈時，factory 的 Resource Manager 範本都會儲存至 adf 發佈分支中的 GIT \_
    1. 將新的處理站連線至_相同_的存放庫，並從 adf \_ 發佈分支進行組建。 管線、資料集和觸發程式等資源將會執行

1. 如果您處於 Live 模式：
    1. Data Factory UI 可讓您將資料處理站的整個承載匯出至 Resource Manager 範本檔案和參數檔案。 您可以從入口網站中的 [ **ARM 範本 \ 匯出 Resource Manager 範本**] 按鈕來存取它們。
    1. 您可以對參數檔案進行適當的變更，並在新的 factory 中交換新的值
    1. 接下來，您可以透過標準 Resource Manager 範本部署方法來部署它。

1. 如果您的來源 factory 中有 SelfHosted IntegrationRuntime，您必須在目標處理站中使用相同的名稱來 precreate 它。 如果您想要在不同的處理站之間共用 SelfHosted Integration Runtime，您可以使用[這裡](create-shared-self-hosted-integration-runtime-powershell.md)發佈的模式共用 SelfHosted IR。

1. 基於安全性理由，產生的 Resource Manager 範本不會包含任何秘密資訊，例如連結服務的密碼。 因此，您必須提供認證作為部署參數。 如果您的設定不需要手動輸入認證，請考慮改為從 Azure Key Vault 中取出連接字串和密碼。 [查看更多](store-credentials-in-key-vault.md)

## <a name="next-steps"></a>後續步驟

檢閱[使用Azure Data Factory UI 建立資料處理站](quickstart-create-data-factory-portal.md)以取得在 Azure 入口網站中建立資料處理站的指導方針。
