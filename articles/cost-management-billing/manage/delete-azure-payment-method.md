---
title: 刪除 Azure 帳單付款方式
description: 說明如何刪除 Azure 訂用帳戶所使用的付款方式。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 09/24/2020
ms.author: banders
ms.openlocfilehash: a579dd22aa814340b4b72d74907739c942570c23
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270664"
---
# <a name="delete-an-azure-billing-payment-method-preview"></a>刪除 Azure 帳單付款方式 (預覽)

本文件提供可協助您從不同類型的 Azure 訂用帳戶中刪除付款方式 (例如信用卡) 的指示。 您可以刪除下列項目的付款方式：

- Microsoft 客戶合約 (MCA)
- Microsoft Online Services 方案 (MOSP)，也稱為隨用隨付

無論您的 Azure 訂用帳戶類型為何，您都必須先將其取消，才能刪除其相關聯的付款方式。

不支援移除其他 Azure 訂用帳戶類型 (例如 Microsoft 合作夥伴合約和 Enterprise 合約) 的付款方式。

## <a name="delete-an-mca-payment-method"></a>刪除 MCA 付款方式

只有建立 Microsoft 客戶合約帳戶的使用者才可刪除付款方式。

若要刪除 Microsoft 客戶合約的付款方式，請執行下列步驟。

1. 在 https://portal.azure.com/ 登入 Azure 入口網站。
1. 巡覽至 [成本管理 + 計費]。
1. 如有必要，請選取計費範圍。
1. 在左側功能表清單的 [帳單] 底下，選取 [帳單設定檔]。  
    :::image type="content" source="./media/delete-azure-payment-method/billing-profiles.png" alt-text="此範例螢幕擷取畫面顯示 Azure 入口網站中的帳單設定檔" lightbox="./media/delete-azure-payment-method/billing-profiles.png" :::
1. 在帳單設定檔清單中，選取使用付款方式的帳戶。  
    :::image type="content" source="./media/delete-azure-payment-method/select-billing-profile.png" alt-text="此範例螢幕擷取畫面顯示 Azure 入口網站中的帳單設定檔" :::
1. 在左側功能表清單的 [設定] 底下，選取 [付款方式]。
1. 在帳單設定檔的 [付款方式] 頁面上，付款方式的表格會顯示在 [您的信用卡] 區段底下。 尋找您要刪除的信用卡，選取省略符號 ( **…** )，然後選取 [刪除]。  
    :::image type="content" source="./media/delete-azure-payment-method/delete-credit-card.png" alt-text="此範例螢幕擷取畫面顯示 Azure 入口網站中的帳單設定檔" :::
1. [刪除付款方式] 頁面隨即出現。 Azure 會檢查付款方式是否正在使用中。
    - 如果付款方式並未使用中，則會啟用 [刪除] 選項。 選取此選項可刪除信用卡資訊。
    - 如果付款方式正在使用中，則必須加以取代或卸離。 請繼續閱讀以下各節。 這幾節會說明如何**卸離**您的訂用帳戶正在使用的付款方式。

### <a name="detach-payment-method-used-by-an-mca-billing-profile"></a>卸離 MCA 帳單設定檔所使用的付款方式

如果某個 MCA 帳單設定檔正在使用您的付款方式，您將會看到類似下列範例的訊息。

:::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-microsoft-customer-agreement.png" alt-text="此範例螢幕擷取畫面顯示 Azure 入口網站中的帳單設定檔" :::

若要卸離付款方式，必須符合條件清單。 若有任何條件不符，則會出現指示說明如何符合條件。 此外也會出現一個連結，讓您前往可解決條件問題的位置。

當所有條件皆符合時，您即可從帳單設定檔卸離付款方式。

> [!NOTE]
> 當預設的付款方式遭卸離時，帳單設定檔會進入_非使用中_狀態。 在此程序中刪除的任何項目都無法復原。 帳單設定檔設定為非使用中之後，您必須註冊新的 Azure 訂用帳戶，才能建立新的資源。

#### <a name="to-detach-a-payment-method"></a>卸離付款方式

1. 在 [刪除付款方式] 區域中，選取 [卸離目前的付款方式] 連結。
1. 如果所有條件皆符合，請選取 [卸離]。 否則，請繼續下一個步驟。
1. 如果 [卸離] 選項無法使用，則會顯示條件清單。 執行列出的動作。 選取 [卸離預設付款方式] 區域中顯示的連結。 以下是更正動作的範例，說明您需要採取的動作。  
    :::image type="content" source="./media/delete-azure-payment-method/azure-subscriptions.png" alt-text="此範例螢幕擷取畫面顯示 Azure 入口網站中的帳單設定檔" :::
1. 當您選取 [更正動作] 連結時，系統會將您重新導向至要執行動作的 Azure 頁面。 執行任何必要的更正動作。
1. 如有必要，請完成所有其他更正動作。
1. 瀏覽回 [成本管理 + 計費] > [帳單設定檔] > [付款方式]。 選取 [卸離]。 在 [卸離預設付款方式] 頁面底部，選取 [卸離]。

> [!NOTE]
> - 取消訂用帳戶之後，最多可能需要 90 天的時間才會刪除訂用帳戶。 如果您想要縮短等待的時間，請開啟 Azure 支援要求，並要求立即刪除訂用帳戶。
> - 在帳單設定檔先前所有的費用都已結清後，您才能刪除付款方式。 如果還在有效的計費週期內，則必須等到計費週期結束後，才能刪除您的付款方式。 **在等待計費週期結束時，請確定所有其他卸離條件均相符**。

## <a name="delete-a-mosp-payment-method"></a>刪除 MOSP 付款方式

您必須是帳戶管理員，才能刪除付款方式。

如果有 MOSP 訂用帳戶正在使用您的付款方式，請執行下列步驟。

1. 在 https://portal.azure.com/ 登入 Azure 入口網站。
1. 巡覽至 [成本管理 + 計費]。
1. 如有必要，請選取計費範圍。
1. 在左側功能表清單的 [帳單] 底下，選取 [付款方式]。
1. 在 [付款方式] 區域中，選取您的付款方式所在的_行_。 請勿選取付款方式連結。 可能沒有視覺效果會指出您已選取付款方式。
1. 選取 [刪除]  。  
    :::image type="content" source="./media/delete-azure-payment-method/delete-mosp-payment-method.png" alt-text="此範例螢幕擷取畫面顯示 Azure 入口網站中的帳單設定檔" :::
1. 在 [刪除付款方式] 區域中，若所有條件皆符合，請選取 [刪除]。 如果 [刪除] 選項無法使用，請繼續進行下一個步驟。
1. 條件清單隨即顯示。 執行列出的動作。 選取 [刪除付款方式] 區域中顯示的連結。  
    :::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-mosp.png" alt-text="此範例螢幕擷取畫面顯示 Azure 入口網站中的帳單設定檔" :::
1. 當您選取 [更正動作] 連結時，系統會將您重新導向至要執行動作的 Azure 頁面。 執行任何必要的更正動作。
1. 如有必要，請完成所有其他更正動作。
1. 瀏覽回 [成本管理 + 計費] > [帳單設定檔] > [付款方式]，並刪除付款方式。

> [!NOTE]
> 取消訂用帳戶之後，最多可能需要 90 天的時間才會刪除訂用帳戶。 如果您想要縮短等待的時間，請開啟 Azure 支援要求，並要求立即刪除訂用帳戶。

## <a name="next-steps"></a>下一步

- 如需 Azure 訂用帳戶的詳細資訊，請參閱[取消您的 Azure 訂用帳戶](cancel-azure-subscription.md)。
- 如需關於新增或更新信用卡的詳細資訊，請參閱[新增或更新用於 Azure 的信用卡](change-credit-card.md)。