---
title: 教學課程：使用警示和動作群組監視 Azure Spring Cloud 資源 | Microsoft Docs
description: 了解如何使用 Spring Cloud 警示。
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.custom: devx-track-java
ms.openlocfilehash: c706f38dabbe21e5bafb096bf406e70af0855aa3
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089973"
---
# <a name="tutorial-how-to-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>教學課程：如何使用警示和動作群組監視 Spring Cloud 資源

**本文適用於：** ✔️ Java ✔️ C#

Azure Spring Cloud 警示支援根據可用的儲存體、要求率或資料使用量等條件來監視資源。 當比率或條件符合定義的規格時，警示就會傳送通知。

設定警示管線有兩個步驟： 
1. 設定動作群組，其中包含觸發警示時所要採取的動作，例如電子郵件、SMS、Runbook 或 Webhook。 動作群組可在不同的警示之間重複使用。
2. 設定警示規則。 規則會根據目標資源、計量、條件、時間彙總來繫結計量模式與動作群組。

## <a name="prerequisites"></a>必要條件

除了 Azure Spring 需求，本教學課程中的程序也會使用已部署的 Azure Spring Cloud 執行個體。  請遵循[快速入門](spring-cloud-quickstart.md)以開始使用。

下列程序會同時初始化從 Spring Cloud 執行個體左側導覽窗格中的 [警示] 選項啟動的 [動作群組] 和 [警示]。 (此程序也可以從 Azure 入口網站的 [監視器概觀] 頁面啟動。) 

從資源群組瀏覽至您的 Spring Cloud 執行個體。 選取左窗格中的 [警示]，然後選取 [管理動作]：

![入口網站資源群組頁面的螢幕擷取畫面](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>設定動作群組

若要開始初始化新的 [動作群組] 的程序，請選取 [+ 新增動作群組]。

![入口網站新增動作群組的螢幕擷取畫面](media/alerts-action-groups/action-1.png)

在 [新增動作群組] 頁面上：

 1. 指定 [動作群組名稱] 和 [簡短名稱]。

 1. 指定 [訂用帳戶] 和 [資源群組]。

 1. 指定 [動作名稱]。

 1. 選取 [動作類型]。  這會在右側開啟另一個窗格，以定義將在啟用時採取的動作。

 1. 使用右窗格中的選項來定義動作。  此案例使用電子郵件通知。

 1. 在右側的動作窗格中，按一下 [確定]。

 1. 在 [新增動作群組] 對話方塊中，按一下 [確定]。 

  ![入口網站定義動作的螢幕擷取畫面](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>設定警示 

先前的步驟已建立使用電子郵件的 [動作群組]。 您也可以使用電話通知、Webhook、Azure 函式等等。 下列步驟會設定**警示**。

1. 瀏覽回 [警示] 頁面，然後按一下 [管理警示規則]。

   ![入口網站定義警示的螢幕擷取畫面](media/alerts-action-groups/alerts-2.png)

1. 選取警示的 [資源]。

1. 按一下 [+ 新增警示規則]。

   ![入口網站新增警示規則的螢幕擷取畫面](media/alerts-action-groups/alerts-3.png)

1. 在 [建立規則] 頁面上，指定 [資源]。

1. [條件] 設定提供了許多用來監視 **Spring Cloud** 資源的選項。  按一下 [新增] 以開啟 [設定訊號邏輯] 窗格。

1. 選取條件。 此範例會使用 [系統 CPU 使用量百分比]。

   ![入口網站新增警示規則的螢幕擷取畫面 2](media/alerts-action-groups/alerts-3-1.png)

1. 向下捲動 [設定訊號邏輯] 窗格，以設定要監視的 [閾值]。

   ![入口網站新增警示規則的螢幕擷取畫面 3](media/alerts-action-groups/alerts-3-2.png)

1. 按一下 [完成] 。

   若要進一步了解可用於監視的條件，請參閱[使用者入口網站計量選項](spring-cloud-concept-metrics.md#user-metrics-options)。

1. 在 [動作] 下方，按一下 [選取動作群組]。 從 [動作] 窗格中選取先前定義的 [動作群組]。

   ![入口網站新增警示規則的螢幕擷取畫面 4](media/alerts-action-groups/alerts-3-3.png) 

1. 向下捲動，並在 [警示詳細資料] 下方為警示規則命名。

1. 設定 [嚴重性]。

1. 按一下 [建立警示規則]。

   ![入口網站新增警示規則的螢幕擷取畫面 5](media/alerts-action-groups/alerts-3-4.png)

1. 確認新的警示規則已啟用。

   ![入口網站新增警示規則的螢幕擷取畫面 6](media/alerts-action-groups/alerts-4.png)

您也可以使用 [計量] 頁面來建立規則：

![入口網站新增警示規則的螢幕擷取畫面 7](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何設定 Azure Spring Cloud 應用程式的警示和動作群組。 若要深入了解動作群組，請參閱：

> [!div class="nextstepaction"]
> [在 Azure 入口網站中建立和管理動作群組](../azure-monitor/platform/action-groups.md)

> [!div class="nextstepaction"]
> [動作群組中的 SMS 警示行為](../azure-monitor/platform/alerts-sms-behavior.md)