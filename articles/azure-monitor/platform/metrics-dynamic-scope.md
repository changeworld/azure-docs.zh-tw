---
title: 在計量瀏覽器中查看多個資源
description: 瞭解如何將 Azure 監視器計量瀏覽器上的多個資源視覺化
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 724809dbce3ca1b5a36f4da0ba5c03d0f78897f5
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577645"
---
# <a name="viewing-multiple-resources-in-metrics-explorer"></a>在計量瀏覽器中查看多個資源

資源範圍選取器可讓您在相同訂用帳戶和區域內的多個資源之間查看計量。 以下是如何在 Azure 監視器計量瀏覽器中查看多個資源的指示。 

## <a name="selecting-a-resource"></a>選取一項資源 

從 [Azure 監視器]  功能表或從資源功能表的 [監視]  區段中，選取 [計量]  。 按一下 [選取範圍] 按鈕以開啟資源範圍選取器，這可讓您選取您想要查看其計量) 的資源 (。 如果您從資源的功能表開啟計量瀏覽器，則應該已經填入此值。 

![以紅色醒目提示資源範圍選取器的螢幕擷取畫面](./media/metrics-charts/019.png)

## <a name="selecting-multiple-resources"></a>選取多個資源 

某些資源類型已啟用查詢多個資源的計量的能力，只要它們位於相同的訂用帳戶和位置中即可。 您可以在 [資源類型] 下拉式清單頂端找到這些資源類型。 

![顯示與多重資源相容之資源下拉式清單的螢幕擷取畫面 ](./media/metrics-charts/020.png)

> [!WARNING] 
> 您必須具有訂用帳戶層級的「監視讀者」許可權，以將多個資源、資源群組或訂用帳戶的計量視覺化。 若要這樣做，請遵循 [本檔](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)中的指示。

若要將多個資源的計量視覺化，請從選取資源範圍選取器內的多個資源開始。 

![顯示如何選取多個資源的螢幕擷取畫面](./media/metrics-charts/021.png)

> [!NOTE]
> 您只能選取相同資源類型、位置和訂用帳戶內的多個資源。 此準則以外的資源將無法選取。 

當您完成選取時，按一下 [套用] 按鈕以儲存您的選取專案。 

## <a name="selecting-a-resource-group-or-subscription"></a>選取資源群組或訂用帳戶 

> [!WARNING]
> 您必須具有訂用帳戶層級的「監視讀者」許可權，以將多個資源、資源群組或訂用帳戶的計量視覺化。 

針對多重資源相容的類型，您也可以查詢訂用帳戶或多個資源群組中的計量。 從選取訂用帳戶或一或多個資源群組開始： 

![顯示如何跨多個資源群組進行查詢的螢幕擷取畫面 ](./media/metrics-charts/022.png)

然後，您必須選取資源類型和位置，才能繼續套用新的範圍。 

![顯示所選資源群組的螢幕擷取畫面 ](./media/metrics-charts/023.png)

您也可以展開選取的範圍，以確認這將套用至哪些資源。

![顯示群組內所選資源的螢幕擷取畫面 ](./media/metrics-charts/024.png)

當您完成選取範圍之後，請按一下 [套用] 以儲存您的選取專案。 

## <a name="splitting-and-filtering-by-resource-group-or-resources"></a>依資源群組或資源分割和篩選

繪製資源之後，您可以使用分割和篩選工具來取得資料的更深入解析。 

分割可讓您將度量的不同區段彼此進行視覺化的比較。 例如，當您繪製多個資源的計量時，您可以使用「套用分割」工具依資源識別碼或資源群組進行分割。 這可讓您輕鬆地比較多個資源或資源群組之間的單一度量。  

例如，以下是9VMs 之間 CPU 百分比的圖表。 藉由依資源識別碼進行分割，您可以輕鬆地查看每個 VM 的 CPU 百分比差異。 

![顯示如何使用分割來查看每個 VM 的 CPU 百分比的螢幕擷取畫面](./media/metrics-charts/026.png)

除了分割之外，您還可以使用篩選功能，只顯示您想要查看的資源群組。  比方說，如果您想要查看特定資源群組 Vm 的 CPU 百分比，您可以使用「新增篩選」工具依資源群組進行篩選。 在此範例中，我們會依 TailspinToys 進行篩選，這會移除與 TailspinToysDemo 中資源相關聯的度量。 

![顯示如何依資源群組進行篩選的螢幕擷取畫面](./media/metrics-charts/027.png)

## <a name="pinning-your-multi-resource-charts"></a>釘選多資源圖表 

> [!WARNING] 
> 您必須具有訂用帳戶層級的「監視讀者」許可權，以將多個資源、資源群組或訂用帳戶的計量視覺化。 若要這樣做，請遵循 [本檔](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)中的指示。 

若要釘選多個資源的圖表，請遵循 [此處](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts#create-alert-rules)的指示。 

## <a name="next-steps"></a>後續步驟

* [計量瀏覽器的疑難排解](metrics-troubleshoot.md)
* [查看 Azure 服務的可用計量清單](metrics-supported.md)
* [查看已設定的圖表範例](metric-chart-samples.md)

