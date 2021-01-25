---
title: 按發票支付 Azure 訂閱費
description: 了解如何按發票支付 Azure 訂用帳戶費用。 請參閱常見問題集以檢視更多資訊。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: a5c95ca2e1fbe00f410c0a5015f8ce695783b27b
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538212"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>按發票支付 Azure 訂閱費

本文適用對象為具有 Microsoft 客戶合約 (MCA) 的客戶和透過 Azure 網站註冊 Azure 的人員。 [檢查您對 Microsoft 客戶合約的存取權](#check-access-to-a-microsoft-customer-agreement)。 如果您透過 Microsoft 代表來註冊 Azure，則您的預設付款方式已設定為「支票或電匯」。

如果您切換為按發票付款，這表示您會在發票日期的 30 天內使用支票/電匯支付帳單費用。 若要符合按發票支付 Azure 訂用帳戶費用的資格，請將要求提交給 Azure 支援。 一旦您的要求獲得核准，您就可以在 Azure 入口網站中切換為發票付款 (支票/電匯)。

> [!IMPORTANT]
> * 發票付款 (支票/電匯) 僅適用於代表公司使用 Azure 的客戶。
> * 在切換為發票付款之前，請支付所有未付費用。
> * 目前，中國的全球 Azure 不支援發票付款。

## <a name="request-to-pay-by-invoice"></a>按發票付款的要求

1. 登入 Azure 入口網站來提交支援要求。 搜尋並選取 [說明 + 支援]。  
    ![搜尋 [說明及支援]、Microsoft Azure 入口網站](./media/pay-by-invoice/search-for-help-and-support.png)
1. 選取 [新增支援要求]。  
    ![[新增支援要求] 連結、[說明及支援] 畫面、Microsoft Azure 入口網站](./media/pay-by-invoice/help-and-support.png)
1. 選取 [帳單] 作為 [問題類型]。 「問題類型」為支援要求類別。 選取您想要依發票付款的訂用帳戶、選取支援方案，然後選取 [下一步]。
1. 選取 [付款] 作為 [問題類型]。 「問題類型」為支援要求子類別。
1. 選取 [切換為按發票付款] 作為 [問題子類型]。
1. 在 [詳細資料] 方塊中輸入下列資訊，並選取 [下一步]。
    - 新的或現有客戶：
    - 如果是現有客戶，目前的付款方法為：
    - 訂單識別碼 (要求發票選項)：
    - 帳戶管理員 Live ID (或組織識別碼) (應該是公司網域)：
    - 商業帳戶識別碼：
    - 公司名稱 (如加值稅或政府網站中註冊的名稱)：
    - 公司地址 (如加值稅或政府網站中註冊的地址)：
    - 公司網站：
    - 國家/地區：
    - 統一編號/加值稅識別碼：
    - 公司創立時間 (年)：
    - 之前與 Microsoft 合作的任何業務：
    - 連絡人姓名：
    - 連絡人電話：
    - 連絡人電子郵件：
    - 為什麼您想使用發票選項而不是信用卡的理由：
    - 如需增加核心數，請提供下列其他資訊：
        - (舊配額) 現有核心數：
        - (新配額) 要求的核心數：
        - 訂用帳戶的特定區域及系列：
    - [公司名稱] 和 [公司位址] 應符合您為 Azure 帳戶提供的資訊。 若要檢視或更新資訊，請參閱[變更您的 Azure 帳戶設定檔資訊](change-azure-account-profile.md)。
    - 請先在 Azure 入口網站中新增您的帳單連絡人資訊，才能核准信用限額。 連絡人詳細資料應該與公司的應付帳款或財務部門相關。
1. 確認您的連絡資訊和慣用的連絡方法，然後選取 [建立]。

如果我們因您所需要的信用額度而必須進行信用查核，我們會將信用查核應用程式傳送給您。 我們可能會要求您提供已通過稽核的公司財務報表。 如果未提供任何財務資訊或資訊強度不夠，而無法支援所需的信用額度，我們可能會要求您提供押金或擔保信用狀 (Standby Letter of Credit)，以核准您的信用額度檢查要求。

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>切換為發票付款 (支票/電匯)

一旦您獲准按發票付款後，您就可以在 Azure 入口網站中切換為發票付款 (支票/電匯)。

如果您有 Microsoft Online Services 方案帳戶，您可以將 Azure 訂用帳戶切換為支票/電匯。 擁有 Microsoft 客戶合約，您可以將帳單設定檔切換為支票/電匯。

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>將 Azure 訂用帳戶切換為支票/電匯

請遵循下列步驟，將您的 Azure 訂用帳戶切換為發票付款 (支票/電匯)。 *一旦您切換為發票付款 (支票/電匯) 後，就無法切換回信用卡付款。*

1. 移至 Azure 入口網站以帳戶管理員身分登入。 搜尋並選取 [成本管理 + 計費]。  
    ![顯示在 Azure 入口網站中搜尋 [成本管理和計費] 的螢幕擷取畫面。](./media/pay-by-invoice/search.png)
1. 選取您要切換至發票付款的訂用帳戶。
1. 選取 [**付款方式**]。
1. 在命令列中，選取 [按發票付款] 按鈕。  
    ![按發票付款按鈕、付款方法、Microsoft Azure 入口網站](./media/pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>將帳單設定檔切換為支票/電匯

請遵循下列步驟，將帳單設定檔切換為支票/電匯。 只有註冊 Azure 的人員可以變更帳單設定檔的預設付款方式。

1. 移至 Azure 入口網站以查看您的帳單資訊。 搜尋並選取 [成本管理 + 計費]。
1. 在功能表中，選擇 [帳單設定檔]。  
    ![[帳單設定檔] 功能表項目、成本管理和計費、Microsoft Azure 入口網站](./media/pay-by-invoice/billing-profile.png)
1. 選取帳單設定檔。
1. 在 [帳單設定檔] 功能表中，選取 [付款方式]。  
   ![[付款方式] 功能表項目、帳單設定檔、成本管理、Microsoft Azure 入口網站](./media/pay-by-invoice/billing-profile-payment-methods.png)
1. 選取橫幅，指出您有資格透過支票/電匯付款。  
    ![用於切換至支票/電匯、付款方式、Microsoft Azure 入口網站的橫幅](./media/pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="frequently-asked-questions"></a>常見問題集

*我為何會收到法律文件的要求？*

有時候如果您提供的資訊不完整或無法驗證，Microsoft 便需要法律文件。 範例可能包括：

* 帳戶名稱與公司名稱有所差異
* 名稱變更

## <a name="next-steps"></a>後續步驟

* 如有需要，請在 [Azure 帳戶中心](https://account.azure.com/Profile)更新您的帳單連絡人資訊。