---
title: 教學課程：在 Azure Spring Cloud 中調整應用程式 | Microsoft Docs
description: 了解如何在 Azure 入口網站中使用 Azure Spring Cloud 調整應用程式
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.custom: devx-track-java
ms.openlocfilehash: 33d25edafa9249300406365699930ded1c8ec75b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906858"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>教學課程：在 Azure Spring Cloud 中調整應用程式

**本文適用於：** ✔️ Java ✔️ C#

此教學課程示範如何使用 Azure 入口網站中的 Azure Spring Cloud 儀表板來調整任何微服務應用程式。

修改虛擬 CPU (vCPU) 數目和記憶體數量，以相應增加或減少您的應用程式。 修改應用程式執行個體的數目，將您的應用程式相應縮小或放大。

完成之後，您將了解如何以手動方式快速變更服務中的每個應用程式。 調整能在數秒內生效，且不需要進行任何程式碼變更或重新部署。

## <a name="prerequisites"></a>必要條件

若要遵循這些程序，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
* 已部署的 Azure Spring Cloud 服務執行個體。  請遵循[關於如何透過 Azure CLI 來部署應用程式的快速入門](spring-cloud-quickstart.md)來開始使用。
* 服務執行個體中至少已建立一個應用程式。

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>在 Azure 入口網站中，瀏覽至 [調整] 頁面

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 移至 Azure Spring Cloud 的 [概觀] 頁面。

1. 選取包含服務的資源群組。

1. 在頁面左側的功能表中，選取 [設定] 底下的 [應用程式] 索引標籤。

1. 選取您要調整的應用程式。 在此範例中，請選取名為 **account-service** 的應用程式。 接著，您應該會看到應用程式的 [概觀] 頁面。

1. 在頁面左側的功能表中，移至 [設定] 底下的 [調整] 索引標籤。 您應該會看到調整屬性的選項，如下一節所示。

## <a name="scale-your-application"></a>調整您的應用程式

如果您修改調整屬性，請記住下列注意事項：

* **CPU**：每個應用程式執行個體的 CPU 數目上限為 4 個。 應用程式的 CPU 總數會是此處所設定的值乘以應用程式執行個體的數目。

* **記憶體/GB**：每個應用程式執行個體的記憶體數量上限為 8 GB。 應用程式的記憶體總數會是此處所設定的值乘以應用程式執行個體的數目。

* **應用程式執行個體計數**：在標準層中，您可以擴增到最多 20 個執行個體。 此值會變更微服務應用程式個別執行中執行個體的數目。

請務必選取 [儲存] 以套用調整設定。

![Azure 入口網站中的調整服務](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

幾秒鐘之後，您所做的變更便會顯示在 [概觀] 頁面上，並在 [應用程式執行個體] 索引標籤中提供更多詳細資料。調整不需要進行任何程式碼變更或重新部署。

## <a name="upgrade-to-the-standard-tier"></a>升級至標準層
如果您是在基本層，且受限於其中一或多項[限制](spring-cloud-quotas.md)，您可以升級至標準層。 若要這麼做，請移至 [定價層] 功能表，方法是先選取 [標準層] 資料行，然後按一下 [升級] 按鈕。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已了解如何手動調整 Azure Spring Cloud 應用程式。 若要了解如何藉由設定警示來監視應用程式，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [了解如何設定警示](spring-cloud-tutorial-alerts-action-groups.md)
