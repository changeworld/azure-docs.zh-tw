---
title: 快速入門 - 從 Azure 通訊服務取得電話號碼
description: 了解如何使用 Azure 入口網站購買通訊服務電話號碼。
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: a9c33fad6d376c2f568f4eefbb0379486b5c40f3
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044763"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站取得電話號碼

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

使用 Azure 入口網站購買電話號碼，以開始使用 Azure 通訊服務。

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [使用中的通訊服務資源。](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>取得手機號碼

若要開始佈建號碼，請移至 [Azure 入口網站](https://portal.azure.com)上的通訊服務資源。

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="顯示通訊服務資源主頁面的螢幕擷取畫面。":::

### <a name="getting-new-phone-numbers"></a>取得新的電話號碼

瀏覽至資源功能表中的 [電話號碼] 刀鋒視窗。

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="顯示通訊服務資源主頁面的螢幕擷取畫面。":::

按 [`Get`] 按鈕以啟動精靈。 [`Phone numbers`] 刀鋒視窗上的精靈將逐步引導您完成一系列的問題，協助您選擇最適合您案例的電話號碼。 

首先，您必須選擇您想要佈建電話號碼的 `Country/region`。 選取國家/地區之後，您必須選取最能滿足您需求的 `use case`。 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="顯示通訊服務資源主頁面的螢幕擷取畫面。":::

### <a name="select-a-phone-plan"></a>選取電話方案

選取電話方案分成兩個步驟： 

1. 選取[號碼類型](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. 選取[方案](../../concepts/telephony-sms/plan-solution.md#phone-number-plans-in-azure-communication-services)

我們目前提供兩種號碼類型：`Geographic` 和 `Toll-free`。 選取號碼類型後，您會看到幾個可供選擇的方案。

在我們的範例中，我們已選擇 `Toll-free` 號碼類型及 `Outbound calling` 和 `Inbound and Outbound SMS` 方案。

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="顯示通訊服務資源主頁面的螢幕擷取畫面。":::

從這裡按一下頁面底部的 [`Next: Numbers`] 按鈕，以自訂您想要佈建的電話號碼。

### <a name="customizing-phone-numbers"></a>自訂電話號碼

在 [`Numbers`] 頁面上，您將自訂想要佈建的電話號碼。

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="顯示通訊服務資源主頁面的螢幕擷取畫面。":::

> [!NOTE]
> 本快速入門會說明 `Toll-free` 號碼類型的自訂流程。 如果您已選擇 `Geographic` 號碼類型，可能會有稍微不同的體驗，但最終結果會是相同的。

從可用的區碼清單中選擇 [`Area code`]，然後輸入您想要佈建的數量，再按一下 [`Search`]，尋找符合您所選需求的號碼。 符合您需求的電話號碼將會連同其每月費用一起顯示。

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="顯示通訊服務資源主頁面的螢幕擷取畫面。":::

> [!NOTE]
> 可用性取決於您所選取的號碼類型、位置和方案。
> 在交易到期之前，號碼會保留一小段時間。 如果交易過期，您將需要重新選取號碼。

若要檢視購買摘要並下單，請按一下頁面底部的 [`Next: Summary`] 按鈕。

### <a name="place-order"></a>下單

摘要頁面將會檢視用來佈建電話號碼的號碼類型、功能、電話號碼及每月費用總計。

> [!NOTE]
> 顯示的價格是**每月週期性費用**，其中涵蓋您租用所選電話號碼的費用。 此檢視中未包含**隨用隨付的費用**，此費用會在您撥打或接聽來電時產生。 您可以在[這裡](../../concepts/pricing.md)取得價格清單。 這些費用取決於號碼類型和撥打電話的目的地。 例如，從西雅圖地區號碼撥打到紐約中的地區號碼，與從相同號碼撥打到英國行動電話號碼的每分鐘價格可能會有所不同。

最後，按一下頁面底部的 [`Place order`] 進行確認。

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="顯示通訊服務資源主頁面的螢幕擷取畫面。":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>在 Azure 入口網站上尋找您的電話號碼

在 [Azure 入口網站](https://portal.azure.com)上瀏覽至您的 Azure 通訊資源：

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="顯示通訊服務資源主頁面的螢幕擷取畫面。":::

選取功能表中的 [電話號碼] 刀鋒視窗，以管理您的電話號碼。

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="顯示通訊服務資源主頁面的螢幕擷取畫面。":::

> [!NOTE]
> 可能需要幾分鐘的時間，已佈建的號碼才會顯示在此頁面上。

### <a name="customizing-phone-number-features"></a>自訂電話號碼功能
在 `Numbers` 頁面上，您可以按一下您想要自訂方案的號碼來選取電話號碼。

:::image type="content" source="../media/manage-phone-azure-portal-capability-update.png" alt-text="顯示通訊服務資源主頁面的螢幕擷取畫面。":::

從可用呼叫和 SMS 功能的清單中選取功能，然後按一下 `Confirm` 以套用選項。

## <a name="troubleshooting"></a>疑難排解

常見問題：

- 目前只有美國支援購買電話號碼。 這會以資源相關訂用帳戶的帳單地址為依據。 目前，您無法將資源移至另一個訂用帳戶。

- 釋出電話號碼後，在計費週期結束之前，電話號碼將不會釋出或再讓人購買。

- 刪除通訊服務資源時，與該資源相關聯的電話號碼將會同時自動釋出。

## <a name="next-steps"></a>下一步

在此快速入門中，您已了解如何：

> [!div class="checklist"]
> * 購買電話號碼
> * 管理您的電話號碼
> * 釋出電話號碼

> [!div class="nextstepaction"]
> [傳送 SMS](../telephony-sms/send.md)
> [開始通話](../voice-video-calling/getting-started-with-calling.md)
