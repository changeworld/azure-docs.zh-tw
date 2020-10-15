---
title: Microsoft 商業市集的標準合約
description: 合作夥伴中心內 Azure Marketplace 和 AppSource 的標準合約
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 05/20/2020
ms.openlocfilehash: 3079cb18f7bc88d691081a7970f5833e8b8d6887
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078182"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Microsoft 商業市集的標準合約

Microsoft 會為 Microsoft 商業市集提供標準合約。 這有助於簡化客戶的採購程序、降低軟體廠商的法律複雜度，並促進市集中的交易。 您不必以商業市集發行者的身分製作自訂條款及條件，而是可以選擇依據[標準合約](https://go.microsoft.com/fwlink/?linkid=2041178)來提供軟體，讓客戶只需要審查並接受一次即可。

在合作夥伴中心建立供應項目時就會定義供應項目的條款及條件。 您可以選擇使用 Microsoft 商業市集的標準合約，而不是提供您自己的自訂條款及條件。

>[!Note]
>使用 Microsoft 商用 marketplace 的標準合約發佈供應專案後，您將無法使用自己的自訂條款及條件。 您可以根據標準合約*或*您自己的條款及條件來提供您的解決方案。 自訂條款及條件會定義于供應專案層級，並套用至所有方案;在合作夥伴中心的供應專案的 [ **屬性** ] 頁面中，撰寫您的自訂條款及條件。 如果您想要修改標準合約的條款，您可以透過標準合約的修正方式來執行此動作。

## <a name="standard-contract-amendments"></a>標準合約修訂

標準合約修訂可讓發行者選取標準合約以求簡化，再附上適用於其產品或業務的自訂條款。 如果客戶已檢閱過並接受了 Microsoft 標準合約，就只需要檢閱合約的修訂條文。

商用市集發行者有兩種可用的修訂：

* 通用修訂：這些修訂可普遍適用於所有客戶的標準合約。 在購買流程中，會向供應項目的每個客戶顯示通用修訂。 客戶必須先接受標準合約和修訂的條款，然後才能使用您的供應項目。

* 自訂修訂：這些修訂是標準合約的特殊修訂，僅透過 Azure 租用戶識別碼以特定客戶作為目標。 發行者可以選擇想要設為目標的租用戶。 只有租用戶的客戶才會在供應項目的購買流程中看到自訂的修訂條款。  客戶必須先接受標準合約和修訂的條款，然後才能使用您的供應項目。

>[!Note]
>這兩種類型的修訂會彼此堆疊。 以自訂修訂為目標的客戶，也會在購買期間取得標準合約的通用修訂。 修訂範圍限制為4000個字元，包括空格。

您可以針對下列供應項目類型，利用 Microsoft 商業市集的標準合約：Azure 應用程式 (解決方案範本和受控應用程式)、虛擬機器和 SaaS。

## <a name="customer-experience"></a>客戶體驗

在 Azure Marketplace 或 AppSource 的探索體驗期間，客戶將能夠看到與供應項目相關聯的條款，形式則為 Microsoft 商業市集的標準合約和任何通用修訂。

![Azure 入口網站的客戶探索體驗。](media/marketplace-publishers-guide/azure-discovery-process.png)

在 Azure 入口網站的購買過程中，客戶將能夠看到與供應項目相關聯的條款，形式則為Microsoft 商業市集的標準合約，以及任何通用和/或租用戶專屬的修訂。

![Azure 入口網站的客戶購買體驗。](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

客戶可以使用 Get-AzureRmMarketplaceTerms 來擷取供應項目的條款並加以接受。 標準合約和相關聯的修訂將會在 Cmdlet 的輸出中傳回。
