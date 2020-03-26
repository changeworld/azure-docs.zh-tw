---
title: 設定與 Microsoft 的對等互連
titleSuffix: Azure
description: 對等互連概觀
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 576bc3e37711851acd7d6c7ac811a10e40080710
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "75908923"
---
# <a name="internet-peering-overview"></a>網際網路對等互連概觀

對等互連是 Microsoft 的全球網路 (AS8075) 與您網路之間的相互連線，其用途是交換往返於 Microsoft 線上服務與 Microsoft Azure 服務的網際網路流量。 電訊廠商或服務提供者可在我們的任何邊緣位置要求與 Microsoft 連線。 Microsoft 會檢查每個要求，以確保要求符合我們的對等互連原則。 您可以透過兩種方式設定與 Microsoft 網路的對等互連：

* **直接對等互連：**

    透過 Microsoft Edge 上的 Microsoft 網路與您的網路之間的直接實體連線來建立對等互連。 在這些連線上，會根據我們的路由原則和使用預先協商的合約設定 BGP 工作階段。 這也稱為 PNI。

* **交換對等互連：**

    這是指網際網路交換 (IX) 上的標準公用對等互連連線。 Microsoft 網路與您網路之間的實體連線會透過 IX 所操作的交換器網狀架構來建立。 BGP 工作階段會使用 IX 所提供的 IP 空間來設定。

## <a name="benefits-of-peering-with-microsoft"></a>與 Microsoft 對等互連的優點
* 使用與 Microsoft 的對等互連來傳遞 Microsoft 流量，可降低您的傳輸成本。
* 藉由減少對 Microsoft Edge 網路的網路躍點和延遲，為您的客戶改善效能。
* 藉由在備援位置與 Microsoft 進行對等互連，防止客戶流量在您的網路或傳輸提供者的網路中失敗。
* 了解對等互連連線的相關效能計量，並利用深入解析對您的網路進行疑難排解。

## <a name="benefits-of-using-azure-to-set-up-peering"></a>使用 Azure 來設定對等互連的優點

您可以使用 Azure PowerShell 或入口網站要求與 Microsoft 進行對等互連。 以這種方式設定的對等互連會以 Azure 資源的形式受到管理，並具有下列優點：
* 可利用簡單且自動化的步驟，設定及管理與 Microsoft 的對等互連。
* 可在單一位置輕鬆快速地檢視及管理您的所有對等互連。
* 可追蹤您所有連線的狀態和頻寬資料。
* 您可以使用相同的訂用帳戶來存取您的 Azure 雲端服務。

如果您已建立與 Microsoft 的對等互連，則稱為**舊版對等互連**。 您可以選擇以 Azure 資源的形式管理這類對等互連，以充分發揮上述優勢。 若要提交新的對等互連要求，或將舊版對等互連轉換成 Azure 資源，請參考下方的**後續步驟**一節中的連結。

## <a name="peering-policy"></a>對等互連原則
Microsoft 具有選擇性、但通常會開啟的對等互連原則。 對等的選擇會以效能、功能，以及可互蒙其利為原則，並且受限於特定技術、商業和法律需求。 如需詳細資訊，請參閱[對等互連原則](policy.md)。

## <a name="faq"></a>常見問題集
如需對等互連的常見問題集，請參閱[網際網路對等互連 - 常見問題集](faqs.md)。

## <a name="next-steps"></a>後續步驟

* 若要了解與 Microsoft 的直接對等互連的設定步驟，請遵循[直接對等互連逐步解說](walkthrough-direct-all.md)
* 若要了解與 Microsoft 的交換對等互連的設定步驟，請遵循[交換對等互連逐步解說](walkthrough-exchange-all.md)
* 了解 Azure 的一些其他重要[網路功能](https://docs.microsoft.com/azure/networking/networking-overview)。
