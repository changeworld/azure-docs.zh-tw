---
title: Azure 消費限制
description: 本文說明 Azure 消費限制的運作方式及如何移除此限制。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 8b592b3f6356a31682fe761d0a5cf098dc91a02f
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282665"
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

只要您的 Azure 訂用帳戶所關聯的付款方式有效，您就可以隨時移除消費限制。 對於具有多個月點數的訂用帳戶類型 (例如 Visual Studio Enterprise 和 Visual Studio Professional)，您可以選擇無限期或僅針對目前計費週期移除消費限制。 如果您選擇目前計費週期，則會在下一個計費週期開始時自動啟用消費限制。

如果您使用 Azure 免費帳戶，請參閱[升級您的 Azure 訂用帳戶](upgrade-azure-subscription.md)來移除消費限制。 若不是的話，請依照下列步驟移除消費限制：

<a id="remove"></a>

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]  。

    ![顯示搜尋 [成本管理 + 帳單] 的螢幕擷取畫面 ](./media/spending-limit/search-bar.png)

1. 在 [我的訂用帳戶]  清單中，選取訂用帳戶。 例如，Visual Studio Enterprise  。

   ![在概觀中顯示 [我的訂閱] 方格的螢幕擷取畫面](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > 如果您在這裡未看到某些 Visual Studio 訂用帳戶，原因可能是您在某個時間點變更了訂用帳戶目錄。 若要看到這些訂用帳戶，您必須將目錄切換到原始目錄 (您最初註冊時所在的目錄)。 然後，重複步驟 2。

1. 在 [訂用帳戶] 概觀中，按一下橙色橫幅以移除消費限制。

    ![顯示 [移除消費限制] 橫幅的螢幕擷取畫面](./media/spending-limit/msdn-remove-spending-limit-banner-x.png)

1. 選擇您是否要無限期移除消費限制，或只將其用於目前計費週期。

      ![顯示 [移除消費限制] 刀鋒視窗的螢幕擷取畫面](./media/spending-limit/remove-spending-limit-blade-x.png)

      | 選項 | 效果 |
      | --- | --- |
      | 無限期移除消費限制 | 消費限制不會在下一個計費週期開始時自動重新開啟。 不過，您可以隨時將其重新開啟。 |
      | 移除目前計費週期的消費限制 | 消費限制會在下一個計費週期開始時自動開啟。 |


1. 按一下 [選取付款方式]  以選擇您訂用帳戶的付款方式。 這將成為您訂用帳戶的使用中付款方式。

1. 按一下 [完成]  。


## <a name="why-you-might-want-to-remove-the-spending-limit"></a>您為何要移除消費限制

消費限制會使您無法部署或使用某些協力廠商和 Microsoft 的服務。 以下是您應該移除訂用帳戶消費限制的情況。

-  您打算部署第三方映像 (例如 Oracle) 或服務 (例如 Azure DevOps Services)。 此情況會讓您幾乎立即達到消費限制，而導致訂用帳戶遭停用。
- 您有不想要中斷的服務。 當您達到消費限制時，您部署的 Azure 資源會從生產環境中移除，而 Azure 虛擬機器則會停止並解除配置。 如果您有不想中斷的服務，則必須移除消費限制。
- 您的服務及資源有您不想遺失的設定 (例如虛擬 IP 位址)。 這些設定會在達到消費限制且服務和資源解除配置時遺失。

## <a name="turn-on-the-spending-limit-after-removing"></a>在移除消費限制之後再將其開啟

這項功能僅適用於針對包含多月點數的訂用帳戶類型無限期地移除消費限制時。 您可以使用這項功能，在下一個計費週期開始時自動開啟您的消費限制。


1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]  。

    ![顯示搜尋 [成本管理 + 帳單] 的螢幕擷取畫面 ](./media/spending-limit/search-bar.png)

1. 在 [我的訂用帳戶]  清單中，選取訂用帳戶。 例如，Visual Studio Enterprise  。

   ![在概觀中顯示 [我的訂閱] 方格的螢幕擷取畫面](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > 如果您在這裡未看到某些 Visual Studio 訂用帳戶，原因可能是您在某個時間點變更了訂用帳戶目錄。 若要看到這些訂用帳戶，您必須將目錄切換到原始目錄 (您最初註冊時所在的目錄)。 然後，重複步驟 2。

1. 在訂用帳戶概觀中，按一下頁面頂端的橫幅，並將消費限制重新開啟。

## <a name="custom-spending-limit"></a>自訂消費限制

無法使用自訂消費限制。

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>消費限制不會防止所有費用

[某些在 Azure Marketplace 中發佈的外部服務](../understand/understand-azure-marketplace-charges.md)無法透過訂用帳戶點數來使用，並會產生另外的費用，即使您已設定消費限制亦然。 範例包括 Visual Studio 授權、Azure Active Directory Premium、支援方案，以及大部分的第三方品牌服務。 當您佈建新的外部服務時，系統會顯示警告，讓您知道服務會另外收費︰

![Marketplace 購買警告](./media/spending-limit/marketplace-warning01.png)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- 升級為採用[隨用隨付](upgrade-azure-subscription.md)定價的方案。
