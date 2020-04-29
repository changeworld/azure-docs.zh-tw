---
title: 標準合約 |Azure Marketplace
description: Azure Marketplace 和 AppSource 的標準合約
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: facb500299f7196e0e1387c3a7267a917d13a3a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681454"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商業 marketplace 的標準合約

為了簡化客戶的採購程式，並降低軟體廠商的法律複雜度，Microsoft 為 Microsoft 商業 marketplace 提供了標準合約，以便協助在 marketplace 中進行交易。 商業 marketplace 發行者可以選擇在標準合約下提供其軟體，而不是製作自訂的條款及條件，而客戶只需要審查並接受一次。 您可以在以下位置找到標準合約[https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178)：。

供應專案的條款及條件是在合作夥伴中心建立供應專案時所定義。 您可以選擇使用 Microsoft 商業 marketplace 的標準合約，而不是提供您自己的自訂條款及條件。

>[!Note]
>當您使用 Microsoft 商業 marketplace 的標準合約發佈供應專案之後，就無法使用自己的自訂條款及條件。 這是「或」案例。 您可以根據標準合約*或*您自己的條款及條件來提供您的解決方案。 如果您想要修改標準合約的條款，您可以透過標準合約修訂來執行此動作。

## <a name="standard-contract-amendments"></a>標準合約修訂

標準合約修訂可讓發行者針對簡單性選取標準合約，並使用其產品或企業的自訂詞彙。 如果客戶已審查並接受 Microsoft 標準合約，則只需要審查合約的修訂。

商用 marketplace 發行者有兩種可用的改正：

* 通用修訂：這些修訂通用適用于所有客戶的標準合約。 在購買流程中，每個供應專案的客戶都會看到通用修訂。 客戶必須先接受標準合約和修訂條款，才能使用您的供應專案。

* 自訂改正：這些修訂是標準合約的特殊修訂，僅透過 Azure 租使用者識別碼以特定客戶為目標。 發行者可以選擇想要設為目標的租使用者。 只有租使用者的客戶才會看到供應專案購買流程中的自訂修訂條款。  客戶必須先接受標準合約和修訂條款，才能使用您的供應專案。

>[!Note]
>這兩種類型的改正堆疊在彼此之上。 以自訂修訂為目標的客戶，也會在購買期間取得標準合約的通用修訂。

您可以針對下列供應專案類型，利用 Microsoft 商業 marketplace 的標準合約： Azure 應用程式（解決方案範本和受控應用程式）、虛擬機器、容器、容器應用程式、IoT Edge 模組和 SaaS。

## <a name="customer-experience"></a>客戶體驗

在 Azure marketplace 或 AppSource 的探索體驗期間，客戶將能夠看到與供應專案相關聯的條款，做為 Microsoft 商業 marketplace 的標準合約和任何通用修訂。

![Azure 入口網站客戶探索體驗。](media/marketplace-publishers-guide/azure-discovery-process.png)

在 Azure 入口網站的購買過程中，客戶將能夠看到與供應專案相關聯的條款，做為 Microsoft 商業 marketplace 的標準合約，以及任何通用和/或租使用者專屬的修訂。

![Azure 入口網站客戶購買體驗。](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

客戶可以使用 AzureRmMarketplaceTerms 來取得供應專案的條款並接受。 標準合約和相關的改正將會在 Cmdlet 的輸出中傳回。
