---
title: 在遠端監視解決方案 UI 中新增服務 - Azure | Microsoft Docs
description: 本文說明如何在遠端監視解決方案加速器 Web UI 中新增服務。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447041"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>在遠端監視解決方案加速器 Web UI 中新增自訂服務

本文說明如何在遠端監視解決方案加速器 Web UI 中新增服務。 本文章說明：

- 如何準備本機開發環境。
- 如何將服務新增至 Web UI。

本文中的服務範例會提供格線資料，[在遠端監視解決方案加速器 Web UI 中新增自訂格線](iot-accelerators-remote-monitoring-customize-grid.md)操作方式文章會說明如何新增。

在 React 應用程式中，服務通常會與後端服務互動。 遠端監視解決方案加速器中的範例包括會與 IoT 中樞管理員和組態微服務互動的服務。

## <a name="prerequisites"></a>Prerequisites

若要完成本操作指南中的部署，您必須在本機開發機器上安裝下列軟體：

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>開始之前

請先完成[在遠端監視解決方案加速器 Web UI 中新增自訂頁面](iot-accelerators-remote-monitoring-customize-page.md)操作方式文章中的步驟再繼續。

## <a name="add-a-service"></a>新增服務

若要在 Web UI 中新增服務，您必須新增可定義此服務的原始程式檔，然後修改一些現有檔案，讓 Web UI 知道新的服務。

### <a name="add-the-new-files-that-define-the-service"></a>新增可定義服務的檔案

為了讓您能夠開始，**src/walkthrough/services** 資料夾包含可定義簡單服務的檔案：

**exampleService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

若要深入了解服務的實作方式，請參閱[您所遺失的回應式程式設計簡介](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)。

**model/exampleModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

將 **exampleService.js** 複製到 **src/services** 資料夾，並將 **exampleModels.js** 複製到 **src/services/models** 資料夾。

更新 **src/services** 資料夾中的 **index.js** 檔案，以匯出新的服務：

```js
export * from './exampleService';
```

更新 **src/services/models** 資料夾中的 **index.js** 檔案，以匯出新的模型：

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>設定從存放區對服務的呼叫

為了讓您能夠開始，**src/walkthrough/store/reducers** 資料夾包含歸納器範例：

**exampleReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

將 **exampleReducer.js** 複製到 **src/store/reducers** 資料夾。

若要深入了解歸納器和 **Epics**，請參閱 [redux-observable](https://redux-observable.js.org/)。

### <a name="configure-the-middleware"></a>設定中介軟體

若要設定中介軟體，請將歸納器新增至 **src/store** 資料夾中的 **rootReducer.js** 檔案：

```js
import { reducer as exampleReducer } from './reducers/exampleReducer';

const rootReducer = combineReducers({
  ...appReducer,
  ...devicesReducer,
  ...rulesReducer,
  ...simulationReducer,
  ...exampleReducer
});
```

將 Epics 新增至 **src/store** 資料夾中的 **rootEpics.js** 檔案：

```js
import { epics as exampleEpics } from './reducers/exampleReducer';

// Extract the epic function from each property object
const epics = [
  ...appEpics.getEpics(),
  ...devicesEpics.getEpics(),
  ...rulesEpics.getEpics(),
  ...simulationEpics.getEpics(),
  ...exampleEpics.getEpics()
];
```

## <a name="next-steps"></a>後續步驟

在本文中，您已了解可用來協助您在遠端監視解決方案加速器 Web UI 中新增或自訂服務的資源。

您現在已定義了一個服務，下一個步驟是[在遠端監視解決方案加速器 Web UI 中新增自訂格線](iot-accelerators-remote-monitoring-customize-grid.md)，以顯示服務所傳回的資料。

如需關於遠端監視解決方案加速器的詳細概念資訊，請參閱[遠端監視架構](iot-accelerators-remote-monitoring-sample-walkthrough.md)。
