---
title: Azure Web 應用程式防火牆和 Azure 原則
description: 與 Azure 原則結合的 Azure Web 應用程式防火牆（WAF）有助於強制執行組織標準，並針對 WAF 資源進行大規模的合規性評估
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 12ad18edbb434bdfaec2ae817ea079a843661ef6
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86111334"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Azure Web 應用程式防火牆和 Azure 原則

與 Azure 原則結合的 Azure Web 應用程式防火牆（WAF）有助於強制執行組織標準，並針對 WAF 資源進行大規模的合規性評估。 Azure 原則是一項治理工具，可提供匯總的視圖來評估環境的整體狀態，並能夠向下切入每個資源的每個原則資料細微性。 Azure 原則也有助於讓您的資源透過對現有資源的大量補救和針對新資源進行自動補救的功能來實現合規性。

## <a name="azure-policies-for-web-application-firewall"></a>適用于 Web 應用程式防火牆的 Azure 原則

有數個內建的 Azure 原則可管理 WAF 資源。 原則及其功能的細目如下所示：

1. **應該啟用 Azure Front 門板服務的 Web 應用程式防火牆（WAF）**：如果資源建立上有 WAF，就會評估 Azure Front 門服務。 原則有三個效果： Audit、Deny 和 Disable。 Audit 追蹤當 Azure Front 門服務沒有 WAF，並讓使用者看到 Azure Front 服務不符合的情況時。 [拒絕] 會防止在未附加 WAF 時建立任何 Azure Front 門板服務。 [已停用] 會關閉此原則。

2. **應啟用應用程式閘道的 Web 應用程式防火牆（WAF）**：如果資源建立時有 WAF 存在，則會評估應用程式閘道。 原則有三個效果： Audit、Deny 和 Disable。 Audit 會追蹤應用程式閘道沒有 WAF，並讓使用者看到應用程式閘道不符合的情況。 [拒絕] 會防止在未附加 WAF 時建立任何應用程式閘道。 [已停用] 會關閉此原則。

3. **Web 應用程式防火牆（WAF）應使用 Azure Front 門板服務的指定模式**：要求在 Azure front 服務的所有 Web 應用程式防火牆原則上使用「偵測」或「防護」模式。 原則有三個效果： Audit、Deny 和 Disable。 當 WAF 不符合指定的模式時，Audit 會進行追蹤。 [拒絕] 會防止任何 WAF 在不是正確模式的情況下建立。 [已停用] 會關閉此原則。

4. **Web 應用程式防火牆（WAF）應該使用指定的模式進行應用程式閘道**：要求在應用程式閘道的所有 Web 應用程式防火牆原則上使用「偵測」或「防護」模式。 原則有三個效果： Audit、Deny 和 Disable。 當 WAF 不符合指定的模式時，Audit 會進行追蹤。 [拒絕] 會防止任何 WAF 在不是正確模式的情況下建立。 [已停用] 會關閉此原則。


## <a name="launch-an-azure-policy"></a>啟動 Azure 原則


1.  在 Azure 首頁的搜尋列中輸入原則，然後按一下 [Azure 原則] 圖示

2.  在 Azure 原則服務的 [**撰寫**] 底下，選取 [**指派**]。

![Azure Web 應用程式防火牆](../media/waf-azure-policy/policy-home.png)

3.  在 [指派] 頁面上，選取頂端的 [**指派原則**] 圖示。

![Azure Web 應用程式防火牆](../media/waf-azure-policy/assign-policy.png)

4.  在 [指派原則] 頁面的 [基本] 索引標籤上，更新下欄欄位：
    1.  **範圍**：選取哪些 Azure 訂用帳戶和資源群組應該受到 Azure 原則影響。
    2.  **排除**：從範圍選取要從原則中排除的任何資源 
    3.  **原則定義**：選取要套用至含有排除範圍之領域的 Azure 原則。 在搜尋列中輸入「Web 應用程式防火牆」，以選擇相關的 Web 應用程式防火牆 Azure 原則。

![Azure Web 應用程式防火牆](../media/waf-azure-policy/policy-listing.png)


5.  選取 [**參數**] 索引標籤，並更新原則參數。 若要進一步闡明參數的用途，請將滑鼠停留在參數名稱旁的資訊圖示上，以取得進一步的說明。

6.  選取 [**審查 + 建立**] 來完成您的 Azure 原則。 Azure 原則大約需要15分鐘的時間，才會對新資源啟用。
