---
title: 使用 Azure 應用程式見解進行性能和負載測試 |微軟文檔
description: 使用 Azure 應用程式見解設置性能和負載測試
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669857"
---
# <a name="performance-testing"></a>效能測試

> [!NOTE]
> 基於雲的負載測試服務已被棄用。 有關棄用、服務可用性和替代服務的詳細資訊，請參閱[此處](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops)。

應用程式見解允許您為網站生成負載測試。 與[可用性測試](monitor-web-app-availability.md)一樣，您可以發送來自全球 Azure 測試代理的基本請求或[多步驟請求](availability-multistep.md)。 效能測試允許您類比多達 20，000 個同時使用者長達 60 分鐘。

## <a name="create-an-application-insights-resource"></a>建立 Application Insights 資源

為了創建效能測試，您首先需要創建應用程式見解資源。 如果已創建資源，請轉到下一節。

從 Azure 門戶中選擇 **"創建資源** > **開發人員工具** > **應用程式見解**"並創建應用程式見解資源。

## <a name="configure-performance-testing"></a>配置效能測試

如果這是您第一次創建效能測試，請選擇 **"設置組織"，** 並選擇 Azure DevOps 組織作為效能測試的源。

在 **"配置**"下，轉到**效能測試**，然後按一下 **"新建"** 以創建測試。

![Fill at least the URL of your website](./media/performance-testing/new-performance-test.png)

要創建基本效能測試，請選擇**手動測試**的測試類型並填寫測試所需的設置。

|設定| 最大值
|----------|------------|
| 使用者負載 | 20,000 |
| 持續時間（分鐘）  | 60 |  

創建測試後，按一下"**運行測試**"。

測試完成後，您將看到與以下結果類似的結果：

![測試結果](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>配置視覺化工作室 Web 測試

應用程式見解高級效能測試功能建立在 Visual Studio 性能和負載測試專案之上。

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>後續步驟

* [多重步驟 Web 測試](availability-multistep.md)
* [Url ping 測試](monitor-web-app-availability.md)
