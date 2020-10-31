---
title: Azure Web 應用程式防火牆和 Azure 原則
description: Azure Web 應用程式防火牆 (WAF) 結合 Azure 原則有助於強制執行組織標準，並大規模評估 WAF 資源的合規性
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 6f05bd5a9798f2feec4424474a63625f812e7148
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100587"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Azure Web 應用程式防火牆和 Azure 原則

Azure Web 應用程式防火牆 (WAF) 結合 Azure 原則有助於強制執行組織標準，並大規模評估 WAF 資源的合規性。 Azure 原則是一種治理工具，可提供匯總的觀點來評估環境的整體狀態，並可向下切入到每個資源的每個原則細微性。 Azure 原則也可透過針對現有資源進行大量補救，以及針對新資源自動補救，來協助將您的資源帶入合規性。

## <a name="azure-policy-for-web-application-firewall"></a>Web 應用程式防火牆的 Azure 原則

有幾個內建的 Azure 原則定義可管理 WAF 資源。 原則定義和其功能的細目如下所示：

1. **應針對 Azure Front Door 服務啟用 Web 應用程式防火牆 (WAF)** ：如果資源建立上有 WAF，則會評估 Azure Front Door 的服務。 原則有三種效果： Audit、Deny 和 Disable。 當 Azure Front Door 服務沒有 WAF，讓使用者看到 Azure Front Door 服務不符合的情況時，就會追蹤追蹤。 如果未附加 WAF，Deny 會防止建立任何 Azure Front Door 服務。 [停用] 會關閉此原則。

2. **應針對應用程式閘道啟用 Web 應用程式防火牆 (WAF)** ：如果資源建立上有 WAF 存在，則會評估應用程式閘道。 原則有三種效果： Audit、Deny 和 Disable。 當應用程式閘道沒有 WAF，並讓使用者看到不符合的應用程式閘道時，就會進行追蹤。 拒絕：如果未附加 WAF，則不會建立任何應用程式閘道。 [停用] 會關閉此原則。

3. **Web 應用程式防火牆 (WAF) 應針對 Azure Front Door 服務使用指定的模式** ：要求在 Azure Front Door 服務的所有 Web 應用程式防火牆原則上使用「偵測」或「防護」模式。 原則有三種效果： Audit、Deny 和 Disable。 當 WAF 不符合指定的模式時，Audit 會追蹤。 Deny 可防止在不是正確模式的情況下建立任何 WAF。 [停用] 會關閉此原則。

4. **Web 應用程式防火牆 (WAF) 應使用指定的應用程式閘道模式** ：要求在應用程式閘道的所有 Web 應用程式防火牆原則上使用「偵測」或「防護」模式。 原則有三種效果： Audit、Deny 和 Disable。 當 WAF 不符合指定的模式時，Audit 會追蹤。 Deny 可防止在不是正確模式的情況下建立任何 WAF。 [停用] 會關閉此原則。


## <a name="launch-an-azure-policy"></a>啟動 Azure 原則


1.  在 Azure 首頁的搜尋列中輸入原則，然後按一下 Azure 原則圖示

2.  在 Azure 原則服務的 [ **撰寫** 中] 底下，選取 [ **指派** ]。

[!div class="mx-imgBorder"]
![Azure web 應用程式防火牆](../media/waf-azure-policy/policy-home.png)

3.  在 [指派] 頁面上，選取頂端的 [ **指派原則** ] 圖示。

[!div class="mx-imgBorder"]
![在 [指派原則] 頁面上顯示 [基本] 索引標籤的螢幕擷取畫面。](../media/waf-azure-policy/assign-policy.png)

4.  在 [指派原則] 頁面的 [基本] 索引標籤上，更新下欄欄位：
    1.  **範圍** ：選取受原則定義影響的 Azure 訂用帳戶和資源群組。
    2.  **排除** 專案：從範圍中選取要從原則指派中排除的任何資源。
    3.  **原則定義** ：選取要套用至包含排除範圍之範圍的原則定義。 在搜尋列中輸入「Web 應用程式防火牆」，以選擇相關的 Web 應用程式防火牆 Azure 原則。

[!div class="mx-imgBorder"]
![顯示可用原則定義的螢幕擷取畫面。](../media/waf-azure-policy/policy-listing.png)


5.  選取 [ **參數** ] 索引標籤，並更新原則指派參數。 若要進一步闡明參數的功能，請將滑鼠停留在參數名稱旁邊的資訊圖示，以進行進一步的澄清。

6.  選取 [ **審核 + 建立** ] 以完成您的原則指派。 原則指派需要大約15分鐘的時間，才會對新資源啟用。
