---
title: 區域或 SKU 系列不可用 - Azure
description: 某些 SKU 系列對於此區域的選定訂閱不可用，這可能需要訂閱管理支援請求。
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: b0f0762ded6804c0b0d90a19223c082f0fb8fd49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843617"
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

創建支援請求以增加計算核心配額時，區域或 SKU 系列無法選擇。

## <a name="solution"></a>解決方法

我們先建議您考慮符合您的業務需求的替代地區或 SKU。

如果找不到合適的區域或 SKU，請按照以下步驟創建**訂閱管理**[支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)：

1. 在[Azure 門戶](https://portal.azure.com)功能表中，選擇 **"説明 + 支援**"。 然後選取 [新增支援要求]****。

1. 在 **"基本"** 中，對於**問題類型**，請選擇**訂閱管理**。

1. 選擇**訂閱**並在**摘要**中輸入簡要說明。

   !["新建支援請求"的基礎知識選項卡](./media/SKU-series-unavailable/support-request-basics.png)

1. 對於**問題類型**，**選擇"選擇問題類型**"。

1. 對於 **"選擇問題類型**"，請選擇一個選項，例如，"**無法訪問我的訂閱或資源** > **"我的問題未列在上面**。 選取 [儲存]****。

   ![指定請求的問題](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. 選擇 **"下一步"：解決方案**以探索可能的解決方案。 如有必要，請選擇 **"下一步：詳細資訊**"以繼續。

1. 輸入您可以提供的任何其他資訊以及您的聯繫資訊。

1. 選擇 **"審閱" = 創建**。 驗證資訊後，選擇 **"創建**"以創建請求。

## <a name="send-us-your-suggestions"></a>向我們發送您的建議

我們總是樂於聽取回饋和建議！ 請將您的 [建議](https://feedback.azure.com/forums/266794-support-feedback)傳送給我們。 此外，您可以在[推特](https://twitter.com/azuresupport)或[MSDN論壇上](https://social.msdn.microsoft.com/Forums/azure)與我們互動。

## <a name="learn-more"></a>深入了解

[Azure 支援常見問題集](https://azure.microsoft.com/support/faq)
