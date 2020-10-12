---
title: 區域可用性與資料落地
titleSuffix: Azure AD B2C
description: 區域可用性、資料存放區，以及 Azure Active Directory B2C 預覽租使用者的相關資訊。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/06/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: f8c6f7daecd38babaa4f2961d04a6cd4c3b4dbed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840552"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C：區域可用性和資料存留處

區域可用性和資料存留處是兩個非常不同的概念，這些概念套用到 Azure AD B2C 的方式與套用到其餘 Azure 的方式不同。 本文說明這兩個概念之間的差異，並比較它們如何套用到 Azure 與 Azure AD B2C。

Azure AD B2C 已在**全球正式推出**，並可選擇在**美國、歐洲或亞太地區**的**資料常駐**。

「區域可用性」[](#region-availability)係指一項服務是否可供使用。

「資料存留處」[](#data-residency)係指使用者資料的儲存位置。

## <a name="region-availability"></a>區域可用性

Azure AD B2C 是透過 Azure 公用雲端在全球提供使用。

這不同于模型，後面接著大部分其他的 Azure 服務，這通常會有與*資料*存放區的*可用性*。 您可以在 Azure 的[依區域提供的產品](https://azure.microsoft.com/regions/services/)頁面和 [Azure Active Directory B2C 定價計算機](https://azure.microsoft.com/pricing/details/active-directory-b2c/)中看到這個的相關範例。

## <a name="data-residency"></a>資料存留處

Azure AD B2C 會將使用者資料儲存在美國、歐洲或亞太地區區域。

資料落地取決於您在 [建立 Azure AD B2C 租](tutorial-create-tenant.md)使用者時所選取的國家/地區：

![[建立租使用者] 表單的螢幕擷取畫面，選擇 [國家或地區]。](./media/data-residency/data-residency-b2c-tenant.png)

下列國家/地區的資料位於 **美國** ：

> 美國 (US) 、加拿大 (CA) 、哥斯大黎加 (CR) 、多明尼加共和國 () 、薩爾瓦多 (SV) 、瓜地馬拉 (GT) 、墨西哥 (MX) 、巴拿馬 (PA) 、波多黎各 (PR) 和特立尼達 & 多巴哥 (TT) 

下列國家/地區的資料位於 **歐洲** ：

> 阿爾及利亞 (DZ) 、奧地利 (AT) 、亞塞拜然 (AZ) 、巴林 (BH) 、白俄羅斯 () 、比利時 () 、保加利亞 (BG) 、克羅地亞 (HR) 、賽普勒斯 (CY) 、捷克共和國 (CZ) 、丹麥 (的 EE) 、芬蘭 (FT) 、法國 (FR) 、德國 (DE) 、希臘 (GR) 、匈牙利 (HU) 、冰島 (是) 、愛爾蘭 (IE) 、以色列 (IL) 、義大利 (IT) 、約旦 (JO) 、義大利 (KZ) 、肯雅 (的) 、列支敦斯登 (的) 、立陶宛 (LT) 、盧森堡 (LU) 、北美洲馬其頓 (ML) 、馬爾他 (MT) 、黑山 (ME) 、摩洛哥 (MA) 、荷蘭 (NL) 、奈及利亞 (NG) 、挪威 (NO) 、阿曼 (的) 、葡萄牙 (PK) 、阿姆斯特丹 (QA) 、羅馬尼亞 (RO) 、俄羅斯 (RU) 、沙烏地阿拉伯 (SA) 、塞爾維亞 (RS) 、斯洛伐克 (SK) 、斯洛維尼亞 (ST) 、南非 (ZA) 、西班牙 (ES) 、瑞典 (SE) 、瑞士 (CH) 、突尼斯 (TN) 、土耳其 (的) 、突尼斯 (和英國) 

資料位於下列國家/地區的 **亞太地區** ：

> 阿富汗 (AF) 、香港特別行政區 (香港特別行政區) 、印度 (（) 、印尼 (ID) 、日本 (JP) 、韓國 (KR) 、馬來西亞 (MY) 、斯里蘭卡 (PH) 、新加坡 (SG) 、斯里蘭卡 (LK) 、臺灣 (隸書) 和泰國 () 。

下列國家/地區正在新增至清單中。 目前，您仍可透過挑選上述任何國家/地區來使用 Azure AD B2C。

> 阿根廷、澳大利亞、巴西、智利、哥倫比亞、厄瓜多爾、伊拉克、紐西蘭、巴拉圭、秘魯、烏拉圭和委內瑞拉。

## <a name="preview-tenant"></a>預覽租用戶

如果您已在 Azure AD B2c 預覽期間建立 B2C 租使用者，則您的租使用者 **類型** 可能會顯示 [ **預覽租**使用者]。

如果是這種情況，您必須只針對開發和測試目的使用您的租使用者。 請勿將預覽租使用者用於生產應用程式。

沒有從預覽 B2C 租使用者到生產級別 B2C 租使用者的**遷移路徑**。 您必須為生產應用程式建立新的 B2C 租使用者。

當您刪除預覽 B2C 租使用者，並建立具有相同功能變數名稱的生產級別 B2C 租使用者時，會發生已知的問題。 *您必須使用不同的功能變數名稱建立生產級別 B2C 租使用者*。

![租使用者類型的螢幕擷取畫面，作為預覽租使用者。](./media/data-residency/preview-b2c-tenant.png)