---
title: Azure 消費限制 | Microsoft Docs
description: 本文說明 Azure 消費限制的運作方式及如何移除此限制。
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: banders
ms.openlocfilehash: 5dbf885848d9866a184caee1da6b9000a26f83a9
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76314114"
---
# <a name="azure-spending-limit"></a>Azure 消費限制

Azure 消費限制可避免您的花費超過您的點數額度。 根據預設，系統會針對所有註冊 Azure 免費帳戶或包含多月點數之訂用帳戶類型的新客戶開啟消費限制。 消費限制等於點數金額，無法加已變更。 例如，如果您已註冊 Azure 免費帳戶，您的消費限制為 $200 且無法將它變更為 $500。 不過，您可以移除消費限制。 因此，您可能沒有任何限制，或您的限制等於點數金額。 這可防止您進行大部分的消費。 消費限制不適用於採用承諾用量方案或採用隨用隨付定價的訂用帳戶。 請參閱[完整的 Azure 訂用帳戶類型清單及消費限制的可用性](https://azure.microsoft.com/support/legal/offer-details/)。

## <a name="reaching-a-spending-limit"></a>達到消費限制

若您的使用方式導致用完您消費限制的費用，則您部署的服務在該計費期間的剩餘時間內將被停用。

舉例來說，若您花光 Azure 免費帳戶隨附的所有點數，您部署的 Azure 資源便會從生產環境中移除，且 Azure 虛擬機器會停止運作並解除配置。 您儲存體帳戶中的資料只能以唯讀方式提供。

如果您的訂用帳戶類型包含多月點數，您的訂用帳戶便會下一個計費週期開始時自動重新啟用。 接著，您便可以重新部署 Azure 資源，並完整存取您的儲存體帳戶和資料庫。

當您達到消費限制時，Azure 會傳送電子郵件通知給您。 登入 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，以查看訂用帳戶達到消費限制的通知。

如果您已註冊 Azure 免費帳戶並達到消費限制，即可升級為[隨用隨付](upgrade-azure-subscription.md)定價來移除消費限制並自動重新啟用您的訂用帳戶。

## <a name="remove-the-spending-limit-in-azure-portal"></a>在 Azure 入口網站中移除消費限制

<a id="remove"></a>

只要您的 Azure 訂用帳戶所關聯的付款方式有效，您就可以隨時移除消費限制。 對於具有多月點數的訂用帳戶類型 (例如 Visual Studio Enterprise 和 Visual Studio Professional)，您也可以在下一個計費週期開始時啟用消費限制。

若要移除您的消費限制，請依照下列步驟操作：

1. 登入 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 瀏覽至 [訂用帳戶]  。
1. 選取您的訂用帳戶。 例如，Visual Studio Enterprise  。
1. 在頁面頂端，選取 [管理]  。 系統會將您重新導向至 https://account.azure.com/ 。 在頁面右側選取 [移除消費限制]  。  
  ![顯示移除消費限制的範例](./media/spending-limit/account-azure-com-spending-limit.png)
1. 系統會將您重新導向回到 Azure 入口網站。 選取適合您的選項，然後選取付款方式。 選取 [完成]  。  
  ![選取一個選項來移除消費限制](./media/spending-limit/remove-spending-limit.png)

| 選項 | 效果 |
| --- | --- |
| 無限期移除消費限制 | 移除消費限制，不在下一個計費週期開始時自動開啟。 |
| 移除目前計費週期的消費限制 | 移除消費限制，讓它在下一個計費週期開始時自動開啟。 |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>您為何要移除消費限制

消費限制會使您無法部署或使用某些協力廠商和 Microsoft 的服務。 以下是您應該移除訂用帳戶消費限制的情況。

-  您打算部署第一方映像 (例如 Oracle) 或服務 (例如 Azure DevOps Services)。 此情況會讓您幾乎立即達到消費限制，而導致訂用帳戶遭停用。
- 您有不想要中斷的服務。 當您達到消費限制時，您部署的 Azure 資源會從生產環境中移除，而 Azure 虛擬機器則會停止並解除配置。 如果您有不想中斷的服務，則必須移除消費限制。
- 您的服務及資源有您不想遺失的設定 (例如虛擬 IP 位址)。 這些設定會在達到消費限制且服務和資源解除配置時遺失。

## <a name="turn-on-the-spending-limit-after-removing"></a>在移除消費限制之後再將其開啟

這項功能僅適用於針對包含多月點數的訂用帳戶類型無限期地移除消費限制時。 您可以使用這項功能，在下一個計費週期開始時自動開啟您的消費限制。

1. 登入[帳戶中心](https://account.windowsazure.com/Subscriptions)。
1. 選取黃色橫幅以變更消費限制選項。
1. 選擇 [在下一個計費週期 \<計費週期開始日期\> 啟用消費限制] 

## <a name="custom-spending-limit"></a>自訂消費限制

無法使用自訂消費限制。

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>消費限制不會防止所有費用

[某些在 Azure Marketplace 中發佈的外部服務](../understand/understand-azure-marketplace-charges.md)無法透過訂用帳戶點數來使用，並會產生另外的費用，即使您已設定消費限制亦然。 範例包括 Visual Studio 授權、Azure Active Directory Premium、支援方案，以及大部分的第三方品牌服務。 當您佈建新的外部服務時，系統會顯示警告，讓您知道服務會另外收費︰

![Marketplace 購買警告](./media/spending-limit/marketplace-warning01.png)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- 升級為採用[隨用隨付](upgrade-azure-subscription.md)定價的方案。
