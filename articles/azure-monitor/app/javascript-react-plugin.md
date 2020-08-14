---
title: Application Insights JavaScript SDK 的回應外掛程式
description: 如何安裝和使用適用于 Application Insights JavaScript SDK 的回應外掛程式。
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 677810c21f9ea6151e2ffe7a4e2b9cdc8473a09f
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227092"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK 的回應外掛程式

Application Insights JavaScript SDK 的回應外掛程式會啟用：

- 路由變更的追蹤
- 回應元件使用統計資料

## <a name="getting-started"></a>開始使用

安裝 npm 套件：

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>基本使用方式

```javascript
import React from 'react';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin, withAITracking } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from "history";

const browserHistory = createBrowserHistory({ basename: '' });
var reactPlugin = new ReactPlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin,appInsights, MyComponent);

```

## <a name="configuration"></a>組態

| 名稱    | 預設 | 描述                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| history | null    | 回應路由器歷程記錄。 如需詳細資訊，請參閱 [回應路由器套件檔](https://reactrouter.com/web/api/history)。 若要瞭解如何存取元件外部的歷程記錄物件，請參閱 [回應-路由器常見問題](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>回應元件使用方式追蹤

若要檢測各種「回應元件」使用方式追蹤，請套用 `withAITracking` 較高順序的元件函數。

它會 `ComponentDidMount` 透過事件測量事件中的時間 `ComponentWillUnmount` 。 不過，為了使其更精確，它會減去使用者閒置的時間 `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` 。

若要在 Application Insights Azure 入口網站中查看此計量，請選取 [計量] 索引標籤，並將空的圖表設定為顯示自訂計量名稱「回應元件參與時間 (秒) 」、選取匯總 (總和、平均等等，並將分割為「元件名稱」。

![顯示自訂度量的圖表螢幕擷取畫面「) 「依「元件名稱分割」的「將元件投入時間 (秒」](./media/javascript-react-plugin/chart.png)

您也可以根據您的需求，執行自訂查詢來分割 Application Insights 資料，以產生報表和視覺效果。 在 [Azure 入口網站流覽至 Application Insights 資源，從 [總覽] 索引標籤的頂端功能表選取 [分析]，然後執行您的查詢。

![自訂度量查詢結果的螢幕擷取畫面。](./media/javascript-react-plugin/query.png)

> [!NOTE]
> 最多可能需要10分鐘的時間，新的自訂計量才會出現在 Azure 入口網站中。

## <a name="sample-app"></a>範例應用程式

查看 [Application Insights 反應示範](https://github.com/Azure-Samples/application-insights-react-demo)。

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解 JavaScript SDK，請參閱 [Application Insights JAVASCRIPT sdk 檔](javascript.md)。
- 若要瞭解 Kusto 查詢語言並查詢 Log Analytics 中的資料，請參閱 [記錄查詢總覽](../../azure-monitor/log-query/log-query-overview.md)。
