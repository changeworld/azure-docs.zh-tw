---
title: SaaS 履行 API |Azure 應用商店
description: 介紹履行 API 的版本，使您能夠將 SaaS 產品與 Azure 應用商店集成。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275725"
---
# <a name="saas-fulfillment-apis"></a>SaaS 履行 API

SaaS 履行 API 使獨立的軟體供應商 （ISV） 能夠將其 SaaS 應用程式與 Azure 應用商店集成。 這些 API 使 ISV 應用程式能夠參與所有支援商務的管道：直接、合作夥伴主導（轉銷商）和現場主導。  它們是在 Azure 應用商店中列出可交易的 SaaS 產品/服務的要求。

> [!WARNING]
> 此 API 的當前版本為版本 2，應用於所有新的 SaaS 產品。  API 的版本 1 被棄用，並且正在維護以支援現有產品/服務。


## <a name="business-model-support"></a>業務模式支援

此 API 支援以下業務模型功能;您可以：

* 為產品/服務指定多個計畫。 這些計畫具有不同的功能，定價可能不同。
* 根據每個網站或每個使用者計費模型提供優惠。
* 提供每月和每年（預付）計費選項。
* 根據協商的業務協定為客戶提供私人定價。


## <a name="next-steps"></a>後續步驟

如果尚未這樣做，請在[Azure 門戶](https://ms.portal.azure.com)中註冊 SaaS 應用程式，如註冊[Azure AD 應用程式](./pc-saas-registration.md)中所述。  之後，使用此介面的最新版本進行開發[：SaaS 履行 API 版本 2](./pc-saas-fulfillment-api-v2.md)。
