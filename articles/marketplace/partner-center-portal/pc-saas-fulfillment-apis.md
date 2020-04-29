---
title: SaaS 履行 Api |Azure Marketplace
description: 引進了可讓您整合 SaaS 供應專案與 Azure Marketplace 的履行 Api 版本。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275725"
---
# <a name="saas-fulfillment-apis"></a>SaaS 履行 API

SaaS 履行 Api 可讓獨立軟體廠商（Isv）整合其 SaaS 應用程式與 Azure Marketplace。 這些 Api 可讓 ISV 應用程式參與所有啟用商務的管道：直接、合作夥伴導向（轉銷商）和現場 led。  它們是在 Azure Marketplace 上列出 transactable SaaS 供應專案的必要條件。

> [!WARNING]
> 此 API 的目前版本是第2版，應用於所有新的 SaaS 供應專案。  API 第1版已被取代，並加以維護以支援現有的供應專案。


## <a name="business-model-support"></a>商務模型支援

此 API 支援下列商務模型功能;您可以：

* 為供應專案指定多個方案。 這些方案具有不同的功能，而且價格可能不同。
* 針對每個網站或每個使用者計費模型提供供應專案。
* 提供每月和每年（付費）計費選項。
* 根據已協商的商業合約，為客戶提供私人定價。


## <a name="next-steps"></a>後續步驟

如果您尚未這麼做，請在[Azure 入口網站](https://ms.portal.azure.com)中註冊您的 SaaS 應用程式，如[註冊 Azure AD 應用程式](./pc-saas-registration.md)中所述。  之後，使用此介面的最新版本進行開發： [SaaS 履行 API 第2版](./pc-saas-fulfillment-api-v2.md)。
