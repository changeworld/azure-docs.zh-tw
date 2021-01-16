---
title: 在 Azure 計量瀏覽器中查看多個資源
description: 瞭解如何使用 Azure 計量瀏覽器來視覺化多個資源。
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 4c895b287f72929e2a0571ccc2cae8cc4f673388
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250480"
---
# <a name="view-multiple-resources-in-the-azure-metrics-explorer"></a>在 Azure 計量瀏覽器中查看多個資源

資源範圍選取器可讓您在相同訂用帳戶和區域內的多個資源之間查看計量。 本文說明如何使用 Azure 監視器的 Azure 計量瀏覽器功能來查看多個資源。 

## <a name="select-a-resource"></a>選取資源 

從 [Azure 監視器]  功能表或從資源功能表的 [監視]  區段中，選取 [計量]  。 然後選擇 [ **選取範圍** ] 以開啟範圍選取器。 

使用範圍選取器來選取您想要查看其計量的資源。 如果您從資源的功能表開啟計量瀏覽器，則應該填入範圍。 

![顯示如何開啟資源範圍選取器的螢幕擷取畫面。](./media/metrics-charts/019.png)

## <a name="select-multiple-resources"></a>選取多個資源 

某些資源類型可以查詢多個資源的計量。 計量必須位於相同的訂用帳戶和位置。 在 [ **資源類型** ] 功能表頂端尋找這些資源類型。

![顯示與多個資源相容之資源功能表的螢幕擷取畫面。](./media/metrics-charts/020.png)

> [!WARNING] 
> 您必須具有訂用帳戶層級的「監視讀者」許可權，以將多個資源、資源群組或訂用帳戶的計量視覺化。 如需詳細資訊，請參閱 [使用 Azure 入口網站新增或移除 Azure 角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

若要將多個資源的計量視覺化，請從選取資源範圍選取器內的多個資源開始。 

![顯示如何選取多個資源的螢幕擷取畫面。](./media/metrics-charts/021.png)

> [!NOTE]
> 您選取的資源必須位於相同的資源類型、位置和訂用帳戶內。 無法選取不符合這些準則的資源。 

當您完成時，請 **選擇 [套用] 以儲存** 您的選取專案。 

## <a name="select-a-resource-group-or-subscription"></a>選取資源群組或訂用帳戶 

> [!WARNING]
> 您必須具有訂用帳戶層級的「監視讀者」許可權，以將多個資源、資源群組或訂用帳戶的計量視覺化。 

針對與多個資源相容的類型，您可以查詢訂用帳戶或多個資源群組的計量。 從選取訂用帳戶或一或多個資源群組開始： 

![顯示如何跨多個資源群組進行查詢的螢幕擷取畫面。](./media/metrics-charts/022.png)

選取資源類型和位置。 

![顯示所選資源群組的螢幕擷取畫面。](./media/metrics-charts/023.png)

您可以展開選取的範圍，以確認您的選取專案適用的資源。

![顯示群組內所選資源的螢幕擷取畫面。](./media/metrics-charts/024.png)

當您完成選取範圍時， **請選取 [** 套用]。 

## <a name="split-and-filter-by-resource-group-or-resources"></a>依資源群組或資源進行分割和篩選

繪製資源之後，您可以使用分割和篩選來取得更多資料的見解。 

分割可讓您將度量的不同區段彼此進行視覺化的比較。 例如，當您繪製多個資源的計量時，您可以選擇 [套用 **分割** ]，依資源識別碼或資源群組進行分割。 分割可讓您比較多個資源或資源群組之間的單一計量。  

例如，下圖顯示九部 Vm 之間的 CPU 百分比。 當您依資源識別碼進行分割時，會看到 CPU 與 VM 的差異百分比。 

![顯示如何使用分割來查看跨 Vm 的 CPU 百分比的螢幕擷取畫面。](./media/metrics-charts/026.png)

除了分割之外，您還可以使用篩選，只顯示您想要查看的資源群組。  例如，若要查看特定資源群組 Vm 的 CPU 百分比，您可以選取 [ **新增篩選** ] 以依資源群組進行篩選。 

在此範例中，我們會依 TailspinToysDemo 進行篩選。 在此，篩選器會移除與 TailspinToys 中的資源相關聯的計量。 

![顯示如何依資源群組篩選的螢幕擷取畫面。](./media/metrics-charts/027.png)

## <a name="pin-multiple-resource-charts"></a>釘選多個資源圖表 

在資源群組和訂用帳戶間視覺化度量的多資源圖表，需要使用者擁有訂用帳戶層級的「 *讀取* 者」許可權。 確定您釘選多個資源圖表之儀表板的所有使用者都有足夠的許可權。 如需詳細資訊，請參閱 [使用 Azure 入口網站新增或移除 Azure 角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

若要將多個資源圖表釘選到儀表板，請參閱 [釘選到儀表板](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts#pinning-to-dashboards)。 

## <a name="next-steps"></a>後續步驟

* [針對計量瀏覽器進行疑難排解](metrics-troubleshoot.md)
* [查看 Azure 服務的可用計量清單](metrics-supported.md)
* [查看已設定的圖表範例](metric-chart-samples.md)

