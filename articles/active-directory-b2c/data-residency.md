---
title: 區域可用性和資料駐留
titleSuffix: Azure AD B2C
description: 區域可用性、資料駐留以及有關 Azure 活動目錄 B2C 預覽租戶的資訊。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3df0f581d0d2a1e5ca02202b4eeaede5a1dd5362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188843"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C：區域可用性和資料存留處

區域可用性和資料存留處是兩個非常不同的概念，這些概念套用到 Azure AD B2C 的方式與套用到其餘 Azure 的方式不同。 本文介紹了這兩個概念之間的差異，並比較了它們如何應用於 Azure 和 Azure AD B2C。

Azure AD B2C**通常在世界各地可用**，可以選擇**在美國、歐洲或亞太地區****使用資料駐留**選項。

「區域可用性」[](#region-availability)係指一項服務是否可供使用。

「資料存留處」[](#data-residency)係指使用者資料的儲存位置。

## <a name="region-availability"></a>區域可用性

Azure AD B2C 是透過 Azure 公用雲端在全球提供使用。

這與大多數其他 Azure 服務後跟的模式不同，後者通常將*可用性*與*資料駐留相結合*。 您可以在 Azure 的[依區域提供的產品](https://azure.microsoft.com/regions/services/)頁面和 [Azure Active Directory B2C 定價計算機](https://azure.microsoft.com/pricing/details/active-directory-b2c/)中看到這個的相關範例。

## <a name="data-residency"></a>資料存留處

Azure AD B2C 在美國、歐洲或亞太地區存儲使用者資料。

資料駐留由[創建 Azure AD B2C 租戶](tutorial-create-tenant.md)時選擇的國家/地區決定：

![預覽租戶的螢幕截圖](./media/data-residency/data-residency-b2c-tenant.png)

以下國家/地區的資料駐留在**美國**：

> 美國、加拿大、哥斯大黎加、多明尼加共和國、薩爾瓦多、瓜地馬拉、墨西哥、巴拿馬、波多黎各和千里達及托巴哥

以下國家/地區的資料駐留在**歐洲**：

> 阿爾及利亞、奧地利、亞塞拜然、巴林、白俄羅斯、比利時、保加利亞、克羅地亞、賽普勒斯、捷克共和國、丹麥、埃及、愛沙尼亞、芬蘭、法國、德國、希臘、匈牙利、冰島、愛爾蘭、以色列、義大利、約旦、哈薩克斯坦、肯雅、科威特、拉脫維亞、黎巴嫩、列支敦斯登、立陶宛、盧森堡、北馬其頓、馬爾他、黑山、摩洛哥、荷蘭、奈及利亞、挪威、阿曼、巴基斯坦、波蘭、葡萄牙、卡塔爾、羅馬尼亞、俄羅斯、沙烏地阿拉伯、塞爾維亞、斯洛伐克、斯洛維尼亞、南非、西班牙、瑞典、瑞士、突尼斯、土耳其、烏克蘭、阿拉伯聯合大公國和聯合王國。

以下國家/地區的資料駐留在**亞太地區**：

> 阿富汗、香港特別行政區、印度、印尼、日本、韓國、馬來西亞、菲律賓、新加坡、斯里蘭卡、臺灣和泰國。

以下國家/地區正在添加到清單中。 目前，您仍可透過挑選上述任何國家/地區來使用 Azure AD B2C。

> 阿根廷、澳大利亞、巴西、智利、哥倫比亞、厄瓜多爾、伊拉克、紐西蘭、巴拉圭、秘魯、烏拉圭和委內瑞拉。

## <a name="preview-tenant"></a>預覽租用戶

如果在 Azure AD B2C 的預覽期間創建了 B2C 租戶，則**租戶類型**很可能表示**預覽租戶**。

如果是這種情況，則必須將租戶僅用於開發和測試目的。 請勿為生產應用程式使用預覽租戶。

沒有從預覽 B2C 租戶到生產規模 B2C 租戶的**遷移路徑**。 您必須為生產應用程式創建新的 B2C 租戶。

刪除預覽 B2C 租戶並創建具有相同功能變數名稱的生產規模 B2C 租戶時，存在已知問題。 *您必須創建具有不同功能變數名稱的生產規模 B2C 租戶*。

![預覽租戶的螢幕截圖](./media/data-residency/preview-b2c-tenant.png)