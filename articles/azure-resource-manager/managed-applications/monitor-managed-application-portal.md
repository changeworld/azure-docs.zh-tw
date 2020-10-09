---
title: 使用 Azure 入口網站來監視受管理的應用程式
description: 示範如何使用 Azure 入口網站來監視受控應用程式的可用性和警示。
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: afe78dd00ecebdc54b6d73c4c8324729e117d95b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75651744"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>監視受控應用程式的已部署執行個體

將受控應用程式部署到您的 Azure 訂用帳戶之後，您可能想要檢查應用程式的狀態。 此文章說明 Azure 入口網站中用來檢查狀態的選項。 您可以在受控應用程式中監視資源的可用性。 您也可以設定並檢視警示。

## <a name="view-resource-health"></a>檢視資源健康情況

1. 選取您的受控應用程式執行個體。

   ![選取受控應用程式](./media/monitor-managed-application-portal/select-managed-application.png)

1. 選取 [資源健康狀態]。

   ![選取資源健康狀態](./media/monitor-managed-application-portal/select-resource-health.png)

1. 在受控應用程式中監視資源的可用性。

   ![檢視資源健康情況](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>檢視警示

1. 選取 [警示] 。

   ![選取警示](./media/monitor-managed-application-portal/select-alerts.png)

1. 如果您已設定警示規則，就會看到已引發的警示相關資訊。

   ![檢視警示](./media/monitor-managed-application-portal/view-alerts.png)

1. 若要新增警示規則，請選取 [+ 新增警示規則]****。

   ![建立警示](./media/monitor-managed-application-portal/create-new-alert.png)

您可以針對受控應用程式執行個體或受控應用程式中的資源建立警示。 如需建立警示的相關資訊，請參閱 [Microsoft Azure 中的警示概觀](../../azure-monitor/platform/alerts-overview.md)。

## <a name="next-steps"></a>後續步驟

* 如需受控應用程式範例，請參閱[適用於 Azure 受控應用程式之範例專案](sample-projects.md)。
* 若要部署受控應用程式，請參閱[透過 Azure 入口網站部署服務目錄應用程式](deploy-service-catalog-quickstart.md)。