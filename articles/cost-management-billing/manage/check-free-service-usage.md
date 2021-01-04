---
title: 監視和追蹤 Azure 免費服務使用量
description: 了解如何在 Azure 入口網站中檢查免費服務使用量。 免費帳戶中包含的服務完全免費，除非您的使用量超過服務限制。
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: banders
ms.openlocfilehash: c7c28e64822a6aefa17e8baa4ef42a3b3fea8adb
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589771"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>檢查 Azure 免費帳戶隨附的免費服務使用量

對於 Azure 免費帳戶隨附的免費服務，除非您超過這些服務的限額，否則不會向您收費。 為了保持在限額內，您可以使用 Azure 入口網站來追蹤免費服務使用量。

## <a name="check-usage-in-the-azure-portal"></a>在 Azure 入口網站中檢查使用量

1.  登入 [Azure 入口網站](https://portal.azure.com)。
1.  尋找 **訂用帳戶**。  
    ![顯示在入口網站中搜尋訂用帳戶的螢幕擷取畫面](./media/check-free-service-usage/billing-search-subscriptions.png)
1.  選取您註冊 Azure 免費帳戶時建立的訂用帳戶。
1.  向下捲動以尋找可顯示免費服務使用量的表格。  
    ![顯示免費服務使用量的螢幕擷取畫面](./media/check-free-service-usage/subscription-usage-free-services.png)

該表有下列資料行：

* **計量：** 識別耗用服務的度量單位。
* **使用量/限制：** 目前月份的使用量和計量的限額。
* **狀態：** 服務的使用量狀態。 根據您的使用量，您可有下列其中一個狀態：
  * **不在使用中：** 您未使用計量，或計費的使用量未達到計費系統的標準。
  * **已在 \<Date> 超過：** 您在 \<Date> 時超過計量的限額。
  * **不太可能超過：** 您不太可能超過計量的限額。
  * **在 \<Date> 超過：** 您可能在 \<Date> 超過計量的限額。

> [!IMPORTANT]
>
> 免費服務僅適用於當您註冊 Azure 免費帳戶時所建立的訂用帳戶。 如果您在訂用帳戶概觀頁面中看不到免費服務表格，則表示不適用於訂用帳戶。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- [將您的 Azure 免費帳戶升級](upgrade-azure-subscription.md)
