---
title: 在遠端監視解決方案 UI 中新增面板 - Azure | Microsoft Docs
description: 本文說明如何在遠端監視解決方案加速器 Web UI 中的儀表板新增面板。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 694cc83ffce20a8744d7452a8f6d67c9ce23641c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82187223"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>在遠端監視解決方案加速器 Web UI 中的儀表板上新增自訂面板

本文說明如何在遠端監視解決方案加速器 Web UI 中的儀表板頁面新增面板。 本文章說明：

- 如何準備本機開發環境。
- 如何在 Web UI 中的儀表板頁面新增面板。

本文中的面板範例會顯示於現有儀表板頁面上。

## <a name="prerequisites"></a>必要條件

若要完成本操作指南中的部署，您必須在本機開發機器上安裝下列軟體：

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>在您開始使用 Intune 之前

請先完成[在遠端監視解決方案加速器 Web UI 中新增自訂頁面](iot-accelerators-remote-monitoring-customize-page.md)文章中的步驟再繼續。

## <a name="add-a-panel"></a>新增面板

若要在 Web UI 中新增面板，您需要新增定義面板的來源檔案，然後修改儀表板以顯示面板。

### <a name="add-the-new-files-that-define-the-panel"></a>新增可定義面板的檔案

為了讓您能夠開始，**src/walkthrough/components/pages/dashboard/panels/examplePanel** 資料夾包含定義面板的檔案，包括：

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

將 **src/walkthrough/components/pages/dashboard/panels/examplePanel** 資料夾複製到 **src/components/pages/dashboard/panels** 資料夾。

將下列匯出新增至 **src/walkthrough/components/pages/dashboard/panels/index.js** 檔案：

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>將面板新增至儀表板

修改 **src/components/pages/dashboard/dashboard.js** 以新增面板。

將面板範例新增至面板匯入清單：

```js
import {
  OverviewPanel,
  AlertsPanel,
  TelemetryPanel,
  AnalyticsPanel,
  MapPanel,
  transformTelemetryResponse,
  chartColorObjects,
  ExamplePanel
} from './panels';
```

在頁面內容的格線中新增下列資料格定義：

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>測試飛出視窗

如果 Web UI 已不在本機執行，請在存放庫本機複本的根目錄中執行下列命令：

```cmd/sh
npm start
```

上一個命令會在本機於 `http://localhost:3000/dashboard` 執行 UI。 瀏覽至 [儀表板]**** 頁面以檢視新的面板。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解可用來協助您在遠端監視解決方案加速器 Web UI 中新增或自訂儀表板的資源。

如需遠端監視解決方案加速器的詳細概念資訊，請參閱[遠端監視架構](iot-accelerators-remote-monitoring-sample-walkthrough.md)。
