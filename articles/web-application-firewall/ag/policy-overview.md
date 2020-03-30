---
title: Azure Web 應用程式防火牆 （WAF） 策略概述
description: 本文概述了 Web 應用程式防火牆 （WAF） 全域、每個網站和每個 URI 策略。
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: fff4fb5707c07098fd7fac261a36909224365cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060276"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Azure Web 應用程式防火牆 （WAF） 策略概述

Web 應用程式防火牆策略包含所有 WAF 設置和配置。 這包括排除項、自訂規則、託管規則等。 然後，這些策略將關聯到應用程式閘道（全域）、攔截器（每個網站）或基於路徑的規則（每個 URI），以便它們生效。

> [!NOTE]
> 每個網站和每個 URI 策略的 Azure Web 應用程式防火牆 （WAF） 處於公共預覽版中。
> 
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 特定功能可能不受支援、功能可能受限，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以創建的策略數量沒有限制。 創建策略時，必須將其與應用程式閘道關聯才能生效。 它可以與應用程式閘道、攔截器和基於路徑的規則的任意組合相關聯。

## <a name="global-waf-policy"></a>全球 WAF 策略

當您全域關聯 WAF 策略時，應用程式閘道 WAF 後面的每個網站都使用相同的託管規則、自訂規則、排除和任何其他配置的設置進行保護。

如果希望單個策略應用於所有網站，則可以將策略與應用程式閘道相關聯。 有關詳細資訊，請參閱[為應用程式閘道創建 Web 應用程式防火牆策略](create-waf-policy-ag.md)，以便使用 Azure 門戶創建和應用 WAF 策略。 

## <a name="per-site-waf-policy"></a>每個網站 WAF 策略

使用每個網站 WAF 策略，您可以使用每個網站策略保護單個 WAF 背後的具有不同安全需求的多個網站。 例如，如果 WAF 後面有五個網站，則可以有五個單獨的 WAF 策略（每個攔截器一個策略），以自訂每個網站的排除項、自訂規則、託管規則集和所有其他 WAF 設置。

假設應用程式閘道已應用全域原則。 然後，對該應用程式閘道上的攔截器應用不同的策略。 攔截器的策略現在僅對該攔截器生效。 應用程式閘道的全域原則仍適用于所有其他攔截器和基於路徑的規則，這些規則沒有為其分配特定的策略。

## <a name="per-uri-policy"></a>每個 URI 策略

對於更多自訂（下為 URI 級別），您可以將 WAF 策略與基於路徑的規則相關聯。 如果單個網站中的某些頁面需要不同的策略，則可以對僅影響給定 URI 的 WAF 策略進行更改。 這可能適用于付款或登錄頁面，或任何其他需要比 WAF 背後的其他網站更具體的 WAF 策略的 URI。

與每個網站 WAF 策略一樣，更具體的策略將覆蓋較不具體的策略。 這意味著 URL 路徑映射上的每個 URI 策略將覆蓋其上方的任何每個網站或全域 WAF 策略。

## <a name="example"></a>範例

假設您有三個網站：contoso.com、fabrikam.com和adatum.com都位於同一應用程式閘道後面。 您希望將 WAF 應用於所有三個網站，但您需要通過adatum.com增加安全性，因為客戶可以訪問、流覽和購買產品。

您可以將全域原則應用於 WAF，並在必要時使用一些基本設置、排除或自訂規則來阻止某些誤報阻塞流量。 在這種情況下，無需運行全域 SQL 注入規則，因為fabrikam.com和contoso.com是靜態頁，沒有 SQL 後端。 因此，您可以在全域原則中禁用這些規則。

此全球政策適用于contoso.com和fabrikam.com，但您需要更加小心處理登錄資訊和付款adatum.com。 您可以將每個網站策略應用於基準攔截器，並保留 SQL 規則運行。 還假設有一個 Cookie 阻止某些流量，因此您可以為該 Cookie 創建排除項以阻止誤報。 

ADATUM.COM/PAYMENTS URI 是您需要小心的地方。 因此，對該 URI 應用另一個策略，並保留所有規則，並刪除所有排除項。

在此示例中，您有一個適用于兩個網站的全域原則。 您有一個適用于一個網站的每個網站策略，然後是應用於一個基於路徑的特定規則的每個 URI 策略。 請參閱此示例（如果存在連結時在此處插入連結）如何創建每個網站和每個 URI 策略。

## <a name="existing-waf-configurations"></a>現有 WAF 配置

所有新的 Web 應用程式防火牆的 WAF 設置（自訂規則、託管規則集配置、排除等）都存在於 WAF 策略中。 如果您有現有的 WAF，這些設置可能仍存在於 WAF 配置中。 有關遷移到新的 WAF 策略的詳細資訊，將[WAF 配置遷移到 WAF 策略](https://docs.microsoft.com/azure/web-application-firewall/ag/migrate-policy)。 


## <a name="next-steps"></a>後續步驟

使用 Azure PowerShell 創建每個網站和每個 URI 策略。
