---
title: Azure API 管理執行個體的容量 | Microsoft Docs
description: 本文說明容量計量是什麼，以及如何明智地決定是否要調整 Azure API 管理執行個體。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.custom: fasttrack-edit
ms.openlocfilehash: b6d949b50be348e72cedfc3710383308b04de106
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80336016"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Azure API 管理執行個體的容量

**容量**是最重要的[Azure 監視器](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis)計量，可讓您做出明智的決策，決定是否要調整 API 管理實例以容納更多負載。 其建構非常複雜，而且會造成某些行為。

本文說明**容量**是什麼及其行為方式。 文中會說明如何在 Azure 入口網站中存取**容量**計量，並建議何時該考慮調整或升級 API 管理執行個體。

> [!IMPORTANT]
> 本文討論如何根據其容量計量來監視和調整您的 Azure API 管理實例。 不過，瞭解個別 API 管理實例實際上已*達到*其容量時，會發生什麼事，同樣重要。 Azure API 管理不會套用任何服務層級節流，以避免實例的實體多載。 當實例達到其實體容量時，其行為會類似于任何無法處理傳入要求的多載 web 伺服器：延遲將會增加，連線將會遭到捨棄，並會發生逾時錯誤等等。這表示 API 用戶端應該準備好處理這種可能性，與任何其他外部服務（例如，藉由套用重試原則）類似。

## <a name="prerequisites"></a>先決條件

若要依照本文中的步驟進行，您必須有：

+ 有效的 Azure 訂用帳戶。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM 執行個體。 如需詳細資訊，請參閱[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>容量是什麼

![容量計量](./media/api-management-capacity/capacity-ingredients.png)

**容量**是 API 管理實例上的負載指示器。 這項指標會反映資源使用量 (CPU、記憶體) 和網路佇列長度。 CPU 和記憶體使用量會揭露下列項目所取用的資源數量：

+ API 管理資料平面服務，例如要求處理，其中可能包含轉送要求或執行原則。
+ API 管理管理平面服務，例如透過 Azure 入口網站或 ARM 套用的管理動作，或來自[開發人員入口網站](api-management-howto-developer-portal.md)的負載。
+ 選取的作業系統進程，包括牽涉到新連接上 TLS 交握成本的處理常式。

總**容量**是每個 API 管理執行個體單位本身所擁有數值的平均值。

雖然**容量**計量是設計來呈現您的 API 管理實例的問題，但在某些情況下，問題不會反映在**容量**計量的變更中。

## <a name="capacity-metric-behavior"></a>容量計量行為

由於本身建構的關係，在現實生活中**容量**會受到許多變數所影響，例如：

+ 連線模式 (要求新連線與重複使用現有連線)
+ 要求和回應的大小
+ 每個 API 上所設定的原則或傳送要求的用戶端數目。

要求上的作業越複雜，所取用的**容量**越高。 例如，複雜的轉換原則所取用的 CPU，會比簡單要求轉送所取用的還多。 後端服務回應緩慢也會增加容量。

> [!IMPORTANT]
> **容量**不是所處理要求數目的直接量值。

![容量計量突增](./media/api-management-capacity/capacity-spikes.png)

**容量**也可能會間歇性突增，即使沒有要處理的要求，容量也可能大於零。 之所以如此，是因為在決定是否要調整執行個體時，不應將系統或平台特有的動作列入考量。

低**容量**計量不一定表示您的 API 管理實例不會遇到任何問題。
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>使用 Azure 入口網站檢查容量
  
![容量計量](./media/api-management-capacity/capacity-metric.png)  

1. 在[Azure 入口網站](https://portal.azure.com/)中，流覽至您的 APIM 實例。
2. 選取 [**計量**]。
3. 從紫色區段中，選取可用計量中的 [容量]**** 計量，並保留預設的 [平均值]**** 彙總。

    > [!TIP]
    > 請隨時看一下每個位置的 [容量]**** 計量細項，以免錯誤解譯。

4. 從綠色區段中，選取 [位置]**** 來依維度分割計量。
5. 從區段頂端列選取所需的時間範圍。

    您可以設定計量警示，以得知有非預期的情況發生。 例如，當您的 APIM 實例已超過預期的尖峰容量超過20分鐘時，就會收到通知。

    >[!TIP]
    > 您可以設定警示以在服務容量即將不足時收到通知，也可以使用 Azure 監視器自動調整功能來自動新增 Azure API 管理單位。 調整作業會花大約 30 分鐘的時間，因此您應該據此規劃您的規則。  
    > 您只能調整主要位置。

## <a name="use-capacity-for-scaling-decisions"></a>使用容量來做出調整決策

**容量**計量可供用來明智地決定是否要調整 API 管理執行個體以容納更多負載。 請考慮：

+ 查看一下長期趨勢和平均。
+ 忽略很可能與負載增加無關的突增 (請參閱＜容量計量行為＞一節的說明)。
+ 在**容量**值長時間 (例如 30 分鐘) 超過 60% 或 70% 時，升級或調整執行個體。 不同的值或許更適合您的服務或案例。

>[!TIP]  
> 如果您可以事先評估流量，請在您預期的工作負載上測試 APIM 執行個體。 您可以逐漸增加租用戶的要求負載，並監視尖峰負載對應的容量計量值。 請遵循上一節的步驟，使用 Azure 入口網站來了解任何特定時刻使用了多少容量。

## <a name="next-steps"></a>後續步驟

[如何調整或升級 Azure API 管理服務執行個體](upgrade-and-scale.md)