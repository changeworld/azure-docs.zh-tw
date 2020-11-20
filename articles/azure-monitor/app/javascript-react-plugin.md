---
title: Application Insights JavaScript SDK 的回應外掛程式
description: 如何安裝和使用適用于 Application Insights JavaScript SDK 的回應外掛程式。
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 3348654a83b6d0930d10e1f58e07455623b5861d
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981080"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK 的回應外掛程式

Application Insights JavaScript SDK 的回應外掛程式，可啟用：

- 追蹤路由變更
- 回應元件使用狀況統計資料

## <a name="getting-started"></a>開始使用

安裝 npm 套件：

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>基本使用方式

初始化與 Application Insights 的連接：

```javascript
// AppInsights.js
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from 'history';

const browserHistory = createBrowserHistory({ basename: '' });
const reactPlugin = new ReactPlugin();
const appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();
export { reactPlugin, appInsights };
```

使用較高順序的元件函式包裝您的元件，以啟用其上的 Application Insights：

```javascript
import React from 'react';
import { withAITracking } from '@microsoft/applicationinsights-react-js';
import { reactPlugin, appInsights } from './AppInsights';

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin, appInsights, MyComponent);
```

## <a name="configuration"></a>組態

| 名稱    | 預設 | 描述                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| history | null    | 回應路由器歷程記錄。 如需詳細資訊，請參閱「 [回應路由器封裝」檔](https://reactrouter.com/web/api/history)。 若要瞭解如何存取元件之外的歷程記錄物件，請參閱「 [回應路由器」常見問題](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>回應元件使用狀況追蹤

若要檢測各種回應元件使用方式追蹤，請套用 `withAITracking` 較高順序的元件函數。

它會從事件中測量 `ComponentDidMount` 事件的時間 `ComponentWillUnmount` 。 不過，為了使其更加精確，它會減去使用者閒置的時間 `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` 。

若要在 Azure 入口網站中查看此計量，您需要流覽至 Application Insights 資源、選取 [計量] 索引標籤，並設定空白圖表以顯示自訂計量名稱「回應元件參與時間 (秒) 」、選取度量的匯總 (總和、平均等 ) ，然後將分割套用為「元件名稱」。

![圖表的螢幕擷取畫面，顯示自訂度量「反應元件參與時間 (秒」) 「依「元件名稱」分割](./media/javascript-react-plugin/chart.png)

您也可以執行自訂查詢，以根據您的需求來分割 Application Insights 的資料，以產生報表和視覺效果。 在 Azure 入口網站流覽至 Application Insights 資源，從 [總覽] 索引標籤的頂端功能表中選取 [分析]，然後執行您的查詢。

![自訂度量查詢結果的螢幕擷取畫面。](./media/javascript-react-plugin/query.png)

> [!NOTE]
> 新的自訂計量可能需要10分鐘的時間才會出現在 Azure 入口網站中。

## <a name="using-react-hooks"></a>使用回應攔截

[回應](https://reactjs.org/docs/hooks-reference.html) 勾點是在回應應用程式中狀態和生命週期管理的方法，不需要依賴以類別為基礎的回應元件。 Application Insights 回應外掛程式提供許多與較高順序元件方法以類似方式運作的攔截式整合。

### <a name="using-react-context"></a>使用回應內容

Application Insights 的回應攔截是設計來使用 [回應內容](https://reactjs.org/docs/context.html) 做為其包含的外觀。 若要使用內容，請在上面初始化 Application Insights，然後匯入內容物件：

```javascript
import React from "react";
import { AppInsightsContext } from "@microsoft/applicationinsights-react-js";
import { reactPlugin } from "./AppInsights";

const App = () => {
    return (
        <AppInsightsContext.Provider value={reactPlugin}>
            /* your application here */
        </AppInsightsContext.Provider>
    );
};
```

此內容提供者會使 Application Insights 可作為 `useContext` 其所有子元件內的攔截器。

```javascript
import React from "react";
import { useAppInsightsContext } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    
    appInsights.trackMetric("Component 'MyComponent' is in use");
    
    return (
        <h1>My Component</h1>
    );
}
export default MyComponent;
```

### `useTrackMetric`

攔截 `useTrackMetric` `withAITracking` 會複寫較高順序元件的功能，而不會將其他元件新增至元件結構。 攔截器會採用兩個引數，第一個是可從攔截) 取得的 Application Insights 實例 (`useAppInsightsContext` ，以及用來追蹤 (的元件識別碼，例如其名稱) 。

```javascript
import React from "react";
import { useAppInsightsContext, useTrackMetric } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const trackComponent = useTrackMetric(appInsights, "MyComponent");
    
    return (
        <h1 onHover={trackComponent} onClick={trackComponent}>My Component</h1>
    );
}
export default MyComponent;
```

它的運作方式類似于較高順序的元件，但會回應攔截生命週期事件，而不是元件生命週期。 如果需要在特定互動上執行，則必須明確地將攔截器提供給使用者事件。

### `useTrackEvent`

攔截 `useTrackEvent` 可用來追蹤應用程式可能需要追蹤的任何自訂事件，例如按一下按鈕或其他 API 呼叫。 它會採用兩個引數，第一個是可從攔截) 取得的 Application Insights 實例 (`useAppInsightsContext` ，以及事件的名稱。

```javascript
import React, { useState, useEffect } from "react";
import { useAppInsightsContext, useTrackEvent } from "@microsoft/applicationinsights-react-js";

const ProductCart = () => {
    const appInsights = useAppInsightsContext();
    const trackCheckout = useTrackEvent(appInsights, "Checkout");
    const trackCartUpdate = useTrackEvent(appInsights, "Cart Updated");
    const [cart, setCart] = useState([]);
    
    useEffect(() => {
        trackCartUpdate({ cartCount: cart.length });
    }, [cart]);
    
    const performCheckout = () => {
        trackCheckout();
        // submit data
    };
    
    return (
        <div>
            <ul>
                <li>Product 1 <button onClick={() => setCart([...cart, "Product 1"])}>Add to Cart</button>
                <li>Product 2 <button onClick={() => setCart([...cart, "Product 2"])}>Add to Cart</button>
                <li>Product 3 <button onClick={() => setCart([...cart, "Product 3"])}>Add to Cart</button>
                <li>Product 4 <button onClick={() => setCart([...cart, "Product 4"])}>Add to Cart</button>
            </ul>
            <button onClick={performCheckout}>Checkout</button>
        </div>
    );
}
export default MyComponent;
```

使用攔截時，可提供資料裝載給它，以便在將其他資料儲存在 Application Insights 時，將其他資料新增至該事件。

## <a name="react-error-boundaries"></a>回應誤差界線

[錯誤界限](https://reactjs.org/docs/error-boundaries.html) 提供一種方式，可在回應應用程式中發生例外狀況時正常處理例外狀況，而且發生這類錯誤時，可能會需要記錄例外狀況。 Application Insights 的回應外掛程式提供錯誤界限元件，會在錯誤發生時自動記錄錯誤。

```javascript
import React from "react";
import { reactPlugin } from "./AppInsights";
import { AppInsightsErrorBoundary } from "@microsoft/applicationinsights-react-js";

const App = () => {
    return (
        <AppInsightsErrorBoundary onError={() => <h1>I believe something went wrong</h1>} appInsights={reactPlugin}>
            /* app here */
        </AppInsightsErrorBoundary>
    );
};
```

`AppInsightsErrorBoundary`需要將兩個 .props 傳遞給它、 `ReactPlugin` 針對應用程式所建立的實例，以及在發生錯誤時要轉譯的元件。 發生未處理的錯誤時， `trackException` 會以提供給錯誤界限的資訊來呼叫，並 `onError` 顯示元件。

## <a name="sample-app"></a>範例應用程式

查看 [Application Insights 反應示範](https://github.com/Azure-Samples/application-insights-react-demo)。

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解 JavaScript SDK，請參閱 [Application Insights JAVASCRIPT sdk 檔](javascript.md)。
- 若要瞭解 Kusto 查詢語言並查詢 Log Analytics 中的資料，請參閱 [記錄查詢總覽](../../azure-monitor/log-query/log-query-overview.md)。
