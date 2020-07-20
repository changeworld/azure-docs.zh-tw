---
title: 無法使用區域或 SKU 系列 - Azure
description: 某些 SKU 系列不適用於此區域所選的訂用帳戶，這可能需要訂用帳戶管理支援要求。
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: troubleshooting
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: 87533b2e0c1397d0b8b2e9a89450a80a75c5e348
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763817"
---
# <a name="region-or-sku-unavailable"></a>無法使用區域或 SKU

本文說明如何解決無法存取區域或 VM SKU 的 Azure 訂用帳戶問題。

## <a name="symptoms"></a>徵狀

在部署虛擬機器時，您可能會收到下列其中一個錯誤碼︰

```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

在購買保留的虛擬機器執行個體時，您可能會收到下列其中一則錯誤訊息：

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

在建立支援要求以增加計算核心配額時，無法選取區域或 SKU 系列。

## <a name="solution"></a>解決方法

我們先建議您考慮符合您的業務需求的替代地區或 SKU。

如果您找不到適合的區域或 SKU，請遵循下列步驟來建立「訂用帳戶管理」[支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)：

1. 從 [Azure 入口網站](https://portal.azure.com)功能表中，選取 [説明 + 支援]。 然後選取 [新增支援要求]。

1. 在 [基本] 中，對於**問題類型**，選取 [訂用帳戶管理]。

1. 選取 [訂用帳戶]，然後在 [摘要] 中輸入簡短描述。

   ![新增支援要求的 [基本] 索引標籤](./media/SKU-series-unavailable/support-request-basics.png)

1. 針對 [問題類型]，選擇 [選取問題類型]。

1. 針對 [選取問題類型]，選擇選項 (例如，**無法存取我的訂用帳戶或資源** > **我的問題未列於上方**)。 選取 [儲存]。

   ![指定要求的問題](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. 完成時，選取 下一步:**解決方案**探索可能的解決方案。 如有必要，請選取 **[下一步：詳細資料]** 繼續進行。

1. 輸入您可以提供的其他任何資訊，以及您的連絡人資訊。

1. 選取 [檢閱 + 建立]。 驗證您的資訊之後，請選取 [建立] 以建立要求。

## <a name="send-us-your-suggestions"></a>請將您的建議傳送給我們

我們歡迎意見反應和建議！ 請將您的 [建議](https://feedback.azure.com/forums/266794-support-feedback)傳送給我們。 此外，您也可以在 [Twitter](https://twitter.com/azuresupport) 或 [Microsoft 問與答頁面](https://docs.microsoft.com/answers/products/azure)中與我們互動。

## <a name="learn-more"></a>深入了解

[Azure 支援常見問題集](https://azure.microsoft.com/support/faq)
