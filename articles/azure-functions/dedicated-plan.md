---
title: Azure Functions 專用主機
description: 瞭解在專用 App Service 主控方案上執行 Azure Functions 的優點。
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 0ebf83aa919d91f161b247539ae20873242a8ed8
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937581"
---
# <a name="dedicated-hosting-plans-for-azure-functions"></a>適用于 Azure Functions 的專用主機方案

本文說明如何將函數應用程式裝載在 App Service 方案中，包括 App Service 環境 (ASE) 。 如需其他裝載選項，請參閱 [主控方案文章](functions-scale.md)。

App Service 方案會針對要執行的應用程式定義一組計算資源。 這些計算資源類似于傳統裝載中的 [_伺服器_](https://wikipedia.org/wiki/Server_farm) 陣列。 您可以將一或多個函式應用程式設定為在相同的計算資源上執行 (App Service 規劃) 為其他 App Service 應用程式，例如 web 應用程式。 這些方案包括基本、標準、Premium 和隔離的 Sku。 如需 App Service 方案運作方式的詳細資訊，請參閱 [Azure App Service 方案深入概觀](../app-service/overview-hosting-plans.md)。

在下列情況下，請考慮 App Service 方案：

* 您有現有的、使用量過低的 VM 已在執行其他 App Service 執行個體。
* 您想要提供自訂映射，以在其上執行函數。

## <a name="billing"></a>計費

您會在 App Service 方案中支付函數應用程式的費用，如同其他 App Service 資源一樣。 這與具有耗用量型成本元件的 Azure Functions [耗用量方案](consumption-plan.md) 或 [Premium 方案](functions-premium-plan.md) 裝載不同。 無論方案中有多少個函式應用程式或 web 應用程式，您都只需支付方案的費用。 若要深入瞭解，請參閱 [App Service 定價頁面](https://azure.microsoft.com/pricing/details/app-service/windows/)。 

## <a name="always-on"></a><a name="always-on"></a> Always On

如果您在 App Service 方案上執行，您應該啟用 **Always on** 設定，讓函數應用程式正確執行。 在 App Service 方案中，函式的執行階段會在無作用幾分鐘後進入閒置狀態，因此只有 HTTP 觸發程序會「喚醒」您的函式。 **Always on** 設定只能在 App Service 方案上使用。 在取用方案中，平台會自動啟動函數應用程式。

即使已啟用 [永遠開啟] 選項，個別函式的執行逾時還是由 [host.json](functions-host-json.md#functiontimeout) 專案檔的 `functionTimeout` 設定來控制。

## <a name="scaling"></a>擴縮

您可以使用 App Service 方案，藉由新增更多 VM 實例來手動相應放大。 您也可以啟用自動調整規模，不過自動調整的速度會比高階方案的彈性規模更慢。 如需詳細資訊，請參閱[手動或自動調整執行個體計數規模](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)。 您也可以透過選擇不同的 App Service 方案來相應增加。 如需詳細資訊，請參閱[在 Azure 中為應用程式進行擴大](../app-service/manage-scale-up.md)。 

> [!NOTE] 
> 在 App Service 方案上執行 JavaScript ( # A0) 函式時，您應該選擇個 vcpu 較少的方案。 如需詳細資訊，請參閱 [選擇單一核心 App Service 方案](functions-reference-node.md#choose-single-vcpu-app-service-plans)。 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

## <a name="app-service-environments"></a>App Service 環境

在 [App Service 環境](../app-service/environment/intro.md) (ASE) 中執行，可讓您完全隔離您的函式，並利用比 App Service 計畫更高的實例數目。 若要開始設定，請參閱。

如果您只想要在虛擬網路中執行函數應用程式，您可以使用 [Premium 方案](functions-premium-plan.md)來執行此作業。 若要深入瞭解，請參閱 [建立 Azure Functions 私用網站存取](functions-create-private-site-access.md)。 

## <a name="next-steps"></a>後續步驟

+ [Azure Functions 裝載選項](functions-scale.md)
+ [Azure App Service 方案概觀](../app-service/overview-hosting-plans.md)