---
title: 使用 Azure 應用程式 Insights 進行效能和負載測試 |Microsoft Docs
description: 使用 Azure 應用程式 Insights 設定效能和負載測試
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77669857"
---
# <a name="performance-testing"></a>效能測試

> [!NOTE]
> 雲端負載測試服務已淘汰。 您可以在[這裡](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops)找到有關淘汰、服務可用性和替代服務的詳細資訊。

Application Insights 可讓您產生網站的負載測試。 如同[可用性測試](monitor-web-app-availability.md)，您可以從世界各地的 Azure 測試代理程式傳送基本要求或[多步驟要求](availability-multistep.md)。 效能測試可讓您模擬最多20000個同時使用者，最多可達60分鐘。

## <a name="create-an-application-insights-resource"></a>建立 Application Insights 資源

為了建立效能測試，您必須先建立 Application Insights 資源。 如果您已建立資源，請繼續進行下一節。

從 [Azure 入口網站中，選取 [**建立資源**] [  >  **開發人員工具**] [  >  **Application Insights** ]，然後建立 Application Insights 資源。

## <a name="configure-performance-testing"></a>設定效能測試

如果這是您第一次建立效能測試，請選取 [**設定組織**]，然後選擇要做為效能測試來源的 Azure DevOps 組織。

在 [**設定**] 下，移至 [**效能測試**]，然後按一下 [**新增**] 以建立測試。

![Fill at least the URL of your website](./media/performance-testing/new-performance-test.png)

若要建立基本效能測試，請選取**手動測試**的測試類型，並填寫您的測試所需的設定。

|設定| 最大值
|----------|------------|
| 使用者負載 | 20,000 |
| 持續時間（分鐘）  | 60 |  

建立測試之後，請按一下 [**執行測試**]。

測試完成後，您會看到看起來類似下列結果的結果：

![測試結果](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>設定 Visual Studio web 測試

Application Insights 先進的效能測試功能是建置於 Visual Studio 效能和負載測試專案之上。

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>後續步驟

* [多重步驟 Web 測試](availability-multistep.md)
* [Url ping 測試](monitor-web-app-availability.md)
