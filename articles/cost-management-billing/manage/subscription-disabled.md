---
title: 重新啟用已停用的 Azure 訂用帳戶
description: 描述當您何時可能有停用的 Azure 訂用帳戶，以及如何重新啟動它。
keywords: Azure 訂用帳戶已停用
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/19/2021
ms.author: banders
ms.openlocfilehash: d7b5f1ae6db633bd1af10b1a0de1392c2a1fbcef
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685524"
---
# <a name="reactivate-a-disabled-azure-subscription"></a>重新啟用已停用的 Azure 訂用帳戶

您的 Azure 訂用帳戶遭到停用的原因可能是點數已過期、達到消費限制、帳單逾期未繳、達到信用卡額度限制，或是帳戶管理員取消訂用帳戶。 查看您適用的問題，並遵循此文章中的步驟，以讓您的訂用帳戶重新啟動。

## <a name="your-credit-is-expired"></a>您的點數已過期

當您註冊免費的 Azure 帳戶時，您會獲得免費試用訂用帳戶，以提供您價值美金 200 員的 Azure 點數，讓您得以獲得 30 天和 12 個月的免費服務。 30 天結束時，Azure 就會停用訂用帳戶。 您的訂用帳戶會停用，以防止您不小心衍生超出訂用帳戶所隨附點數和免費服務的使用費。 若要繼續使用 Azure 服務，則必須[升級訂用帳戶](upgrade-azure-subscription.md)。 升級之後，您的訂用帳戶仍可存取免費服務 12 個月。 您只需支付超過免費服務數量限制的使用量費用。

## <a name="you-reached-your-spending-limit"></a>您已達到消費限制

擁有免費試用和 Visual Studio Enterprise 等點數的 Azure 訂用帳戶有其消費限制。 您最多隻能使用服務到包含的點數。 當您的使用量達到消費限制時，Azure 會在該計費期間的剩餘時間內停用您的訂用帳戶。 您的訂用帳戶會停用，以防止您不小心衍生超出訂用帳戶所隨附點數的使用費。 若要移除消費限制，請參閱 [Azure 入口網站中的移除消費限制](spending-limit.md#remove)。

> [!NOTE]
> 如果您有免費試用訂用帳戶並移除消費限制，則在免費試用結束時，您的訂用帳戶會轉換成採用隨用隨付費率的個別訂用帳戶。 您可以在建立訂用帳戶之後保留剩餘點數達整整 30 天之久。 您也可以在 12 個月內存取免費服務。

若要監視和管理 Azure 的計費活動，請參閱[規劃管理 Azure 成本](../understand/plan-manage-costs.md)。

## <a name="your-bill-is-past-due"></a>您的帳單逾期未繳

若要解決過去的到期餘額，請參閱下列其中一篇文章：

- 若為 Microsoft Online 訂用帳戶方案訂用帳戶（包括隨用隨付），請參閱在 [取得 azure 的電子郵件之後，為您的 azure 訂用帳戶解決逾期的到期餘額](resolve-past-due-balance.md)。
- 如 Microsoft 客戶合約訂用帳戶，請參閱 [如何支付帳單的 Microsoft Azure](../understand/pay-bill.md)。

## <a name="the-bill-exceeds-your-credit-card-limit"></a>帳單超出您的信用卡額度

若要解決此問題，請 [切換到不同的信用卡](change-credit-card.md)。 或如果您代表企業，您可以[切換為按發票付款](pay-by-invoice.md)。

## <a name="the-subscription-was-accidentally-canceled"></a>不小心取消訂閱

如果您是帳戶管理員且不小心取消隨用隨付訂用帳戶，您可以在 Azure 入口網站中重新啟用該訂用帳戶。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 移至 [訂用帳戶]，然後選取已取消的訂用帳戶。
1. 選取 [ **重新** 啟用]。
1. 選取 **[確定]** 以確認重新開機。  
    :::image type="content" source="./media/subscription-disabled/reactivate-sub.png" alt-text="顯示確認重新啟用的螢幕擷取畫面" :::

對於其他訂用帳戶類型，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以將您的訂用帳戶重新啟動。

## <a name="after-reactivation"></a>重新啟用之後

重新啟動訂用帳戶之後，建立或管理資源可能會有所延遲。 如果延遲超過30分鐘，請聯絡 [Azure 帳單支援](https://go.microsoft.com/fwlink/?linkid=2083458) 以取得協助。 大部分的 Azure 資源會自動繼續，且不需要任何動作。 不過，我們建議您檢查 Azure 服務資源，並重新啟動任何未自動恢復的資源。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- 了解如何[規劃管理 Azure 成本](../understand/plan-manage-costs.md)。
