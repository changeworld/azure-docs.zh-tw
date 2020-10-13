---
title: Application Insights JavaScript SDK 的原生回應外掛程式
description: 如何安裝和使用 Application Insights JavaScript SDK 的原生回應外掛程式。
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 3dac47aa7cf93ca882c4c1d0d191dabf0eb7178c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227095"
---
# <a name="native-react-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK 的原生回應外掛程式

Application Insights JavaScript SDK 的原生回應外掛程式會收集設備資訊，根據預設，此外掛程式會自動收集：

- **唯一的裝置識別碼** (也稱為安裝識別碼。 ) 
- **裝置型號名稱** (例如 iPhone X、Samsung Galaxy 折頁、Huawei P30 Pro 等 ) 
- **裝置類型** (例如，話筒、平板電腦等 ) 

## <a name="requirements"></a>需求

您必須使用 >= 2.0.0 的版本 `@microsoft/applicationinsights-web` 。 此外掛程式只會在回應原生應用程式中運作。 它無法與 [使用博覽會 framework 的應用程式](https://docs.expo.io/)搭配使用，因此不會與建立 React Native 應用程式搭配使用。

## <a name="getting-started"></a>開始使用

安裝和連結 [回應原生裝置資訊](https://www.npmjs.com/package/react-native-device-info) 套件。 使用您的應用程式，讓套件保持在最 `react-native-device-info` 新狀態，以收集最新的裝置名稱。

```zsh

npm install --save @microsoft/applicationinsights-react-native @microsoft/applicationinsights-web
npm install --save react-native-device-info
react-native link react-native-device-info

```

## <a name="initializing-the-plugin"></a>初始化外掛程式

若要使用此外掛程式，您必須建立外掛程式，並將其新增為 `extension` 現有的 Application Insights 實例。

```typescript
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactNativePlugin } from '@microsoft/applicationinsights-react-native';

var RNPlugin = new ReactNativePlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [RNPlugin]
    }
});
appInsights.loadAppInsights();

```

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解 JavaScript SDK，請參閱 [Application Insights JAVASCRIPT sdk 檔](javascript.md)。
- 若要瞭解 Kusto 查詢語言並查詢 Log Analytics 中的資料，請參閱 [記錄查詢總覽](../../azure-monitor/log-query/log-query-overview.md)。
