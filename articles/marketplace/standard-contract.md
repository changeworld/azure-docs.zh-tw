---
title: 標準合同 |Azure 應用商店
description: Azure 應用商店和應用源的標準協定
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: facb500299f7196e0e1387c3a7267a917d13a3a6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681454"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>微軟商業市場的標準合同

為了簡化客戶的採購流程並降低軟體供應商的法律複雜性,Microsoft 為 Microsoft 商業市場提供了標準合同,以幫助促進市場交易。 商業市場出版商可以選擇根據標準合同提供他們的軟體,而不是制定自定義條款和條件,客戶只需審查並接受一次。 標準合同可以在這裡找到: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178).

在合作夥伴中心創建產品/服務時,將定義優惠的條款和條件。 您可以選擇為 Microsoft 商業市場使用標準合約,而不是提供您自己的自訂條款和條件。

>[!Note]
>使用 Microsoft 商業市場的標準合約發佈產品/服務後,您將無法使用自己的自定義條款和條件。 這是一個"或"方案。 您要要根據標準合約*或*您自己的條款和條件提供您的解決方案。 如果您想修改標準合同的條款,您可以通過標準合同修訂來修改。

## <a name="standard-contract-amendments"></a>標準合同修訂

標準合同修訂允許發佈商選擇標準合同,以便簡單,併為其產品或業務提供自定義條款。 客戶只需審核並接受 Microsoft 標準合同,就只需查看合同的修訂。

商業市場出版商有兩種修正案:

* 普遍修改:這些修訂適用於所有客戶的標準合同。 通用修訂顯示在採購流程中向產品/服務的每個客戶。 客戶必須接受標準合同的條款和修改,然後才能使用您的優惠。

* 自定義修訂:這些修訂是標準合同的特別修訂,僅通過 Azure 租戶 ID 針對特定客戶。 發行者可以選擇要定位的租戶。 只有租戶的客戶才會在優惠的採購流程中收到自定義修改條款。  客戶必須接受標準合同的條款和修改,然後才能使用您的優惠。

>[!Note]
>這兩種類型的修正相互疊加。 以定製修訂為目標的客戶在購買過程中還將獲得標準合同的普遍修訂。

您可以利用 Microsoft 商業市場的標準合約來獲取以下產品類型:Azure 應用程式(解決方案範本和託管應用程式)、虛擬機器、容器、容器應用程式、IoT 邊緣模組和 SaaS。

## <a name="customer-experience"></a>客戶體驗

在 Azure 市場或 AppSource 中的發現體驗期間,客戶將能夠看到與產品/服務相關的條款,即 Microsoft 商業市場的標準合約以及任何通用修訂。

![Azure 門戶客戶發現體驗。](media/marketplace-publishers-guide/azure-discovery-process.png)

在 Azure 門戶中的購買過程中,客戶將能夠將與產品/服務相關的條款視為 Microsoft 商業市場的標準合同以及任何通用和/或特定於租戶的修訂。

![Azure 門戶客戶購買體驗。](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

客戶可以使用獲取 AzureRmMarketplace術語檢索產品/服務的條款並接受它。 標準合同和相關修訂將在 cmdlet 的輸出中返回。
