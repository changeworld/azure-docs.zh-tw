---
title: 開始使用 Microsoft 客戶合約計費帳戶 - Azure
description: 了解您的 Microsoft 客戶合約計費帳戶，包括帳單設定檔和發票付款方式。
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 632fac953a54e661164152cda79082aed2c0bc06
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690471"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>開始使用您的 Microsoft 客戶合約計費帳戶

當您註冊使用 Azure 時，就會建立計費帳戶。 您可使用計費帳戶來管理發票、付款及追蹤成本。 您可擁有多個計費帳戶的存取權。 例如，您可能已針對您的個人專案註冊 Azure。 但也可以透過組織的 Enterprise 合約或 Microsoft 客戶合約獲得 Azure 的存取權。 針對上述每個案例，您會有不同的計費帳戶。

本文適用於 Microsoft 客戶合約的計費帳戶。 請[確認您是否有 Microsoft 客戶合約的存取權](#check-access-to-a-microsoft-customer-agreement)。

## <a name="your-billing-account"></a>您的計費帳戶

Microsoft 客戶合約的計費帳戶包含一或多個帳單設定檔，可讓您管理發票和付款方式。 每個帳單設定檔都包含一或多個發票區段，可讓您彙整帳單設定檔發票上的成本。

下圖顯示計費帳戶、帳單設定檔和發票區段之間的關聯性。

![顯示 Microsoft 客戶合約計費階層的圖表](./media/mca-overview/mca-billing-hierarchy.png)

計費帳戶上具有最高權限層級的角色。 根據預設，只有註冊 Azure 的使用者可取得計費帳戶的存取權。 這些角色應指派給需要檢視發票和追蹤整體組織成本的使用者 (例如財務或 IT 經理)。 如需詳細資訊，請參閱[計費帳戶角色和工作](../manage/understand-mca-roles.md#billing-account-roles-and-tasks)。

## <a name="billing-profiles"></a>帳單設定檔

帳單設定檔可讓您管理發票和付款方式。 您帳戶中的每個帳單設定檔都會在月初產生每月發票。 發票會分別包含所有 Azure 訂用帳戶以及上個月的其他購買所產生的個別費用。

系統會自動為您的計費帳戶建立帳單設定檔。 根據預設，會包含一個發票區段。 視您的需求而定，您可依專案、部門或開發環境來建立其他區段，以輕鬆追蹤和彙整成本。 您會看到帳單設定檔的這些發票區段反映出每個訂用帳戶的使用方式及指派給該訂用帳戶的購買。

帳單設定檔上的角色有權檢視和管理發票和付款方式。 請將這些角色指派給支付發票費用的使用者，例如組織中會計小組成員。 如需詳細資訊，請參閱[帳單設定檔角色和工作](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)。

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>每個帳單設定檔都會有每月發票

每個帳單設定檔都會在月初產生每月發票。 發票中包含上個月的所有費用。

您可以在 Azure 入口網站中檢視發票、下載文件，以及變更設定以透過電子郵件取得未來的發票。 如需詳細資訊，請參閱[下載 Microsoft 客戶合約的發票](../manage/download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)。

### <a name="invoice-payment-methods"></a>發票付款方式

每個帳單設定檔都有其用來支付發票的付款方式。 以下是支援的付款方式：

| 類型             | 定義  |
|------------------|-------------|
|Azure 點數    |  點數會自動套用至發票上符合資格的費用，以降低您需要支付的金額。 如需詳細資訊，請參閱[追蹤帳單設定檔的 Azure 點數餘額](../manage/mca-check-azure-credits-balance.md)。 |
|支票/電匯 | 如果您的帳戶經核准可使用支票/電匯付款。 您可以透過支票/電匯來支付發票的應付金額。 發票上會提供付款的指示 |
|信用卡 | 透過 Azure 網站註冊 Azure 的客戶，可以使用信用卡付費。 |

### <a name="apply-policies-to-control-purchases"></a>套用原則以控制購買

使用帳單設定檔套用原則，以控制 Azure Marketplace 和保留的購買。 您可以設定原則，以停用 Azure 保留和 Marketplace 產品的購買。 套用原則時，費用計入帳單設定檔的訂用帳戶將無法來進行這些購買。

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Azure 方案會決定訂用帳戶的定價和服務等級協定

Azure 方案會決定 Azure 訂用帳戶的定價和服務等級協定。 當您建立帳單設定檔時，將會自動啟用這些方案。 所有與帳單設定檔相關聯的發票區段，都可以使用這些方案。 具有發票區段存取權的使用者，可使用這些方案來建立 Azure 訂用帳戶。 Microsoft 客戶合約的計費帳戶支援下列 Azure 方案：

| 計畫             | 定義  |
|------------------|-------------|
|Microsoft Azure 方案   | 可讓使用者建立可執行任何工作負載的訂用帳戶。  |
|用於開發/測試的 Microsoft Azure 方案 | 可讓 Visual Studio 訂閱者建立限用於開發或測試工作負載的定用帳戶。 這些訂用帳戶可享有較低費率，並獲得在 Azure 入口網站中存取專屬虛擬機器映像的權益。 |

## <a name="invoice-sections"></a>發票區段

建立發票區段以彙整您發票上的成本。 例如，您可能會需要整個組織的單一發票，同時也想要依部門、小組或專案來彙整成本。 在此情況下，您可以設定單一帳單設定檔，而為每個部門、小組或專案分別建立一個發票區段。

在發票區段建立時，您可以為其他人授與適當權限，使其能夠建立將費用計入該區段的 Azure 訂用帳戶。 訂用帳戶的任何使用量費用和購買隨後都會計入該區段。

發票區段上的角色有權控制哪些人可建立 Azure 訂用帳戶。 請將這些角色指派給負責為組織中的小組設定 Azure 環境的使用者，例如工程部門負責人和技術架構設計人員。 如需詳細資訊，請參閱[發票區段角色和工作](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks)。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

若要了解您的計費帳戶，請參閱下列文章：

- [了解 Azure 中的 Microsoft 客戶合約管理角色](../manage/understand-mca-roles.md)
- [為 Microsoft 客戶合約建立額外的 Azure 訂用帳戶](../manage/create-subscription.md)
- [在您的發票上建立區段以彙整成本](../manage/mca-section-invoice.md)
