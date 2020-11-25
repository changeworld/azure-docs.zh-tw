---
title: 在遠端監視解決方案 UI 中新增飛出視窗 - Azure | Microsoft Docs
description: 本文說明如何在遠端監視解決方案加速器 Web UI 中的頁面上，新增飛出視窗。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: afd8c5b01b9f36606bf6bd43fbed747ca83ba320
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006110"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>在遠端監視解決方案加速器 Web UI 中新增自訂飛出視窗

本文說明如何在遠端監視解決方案加速器 Web UI 中的頁面上，新增飛出視窗。 本文章說明：

- 如何準備本機開發環境。
- 如何在 Web UI 中的頁面上新增飛出視窗。

本文中的飛出視窗範例會顯示在有格線的頁面上，[在遠端監視解決方案加速器 Web UI 中新增自訂格線](iot-accelerators-remote-monitoring-customize-grid.md)操作方式文章會說明如何新增。

## <a name="prerequisites"></a>Prerequisites

若要完成本操作指南中的部署，您必須在本機開發機器上安裝下列軟體：

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>開始之前

請先完成下列文章中的步驟再繼續：

- [在遠端監視解決方案加速器 Web UI 中新增自訂頁面](iot-accelerators-remote-monitoring-customize-page.md)。
- [在遠端監視解決方案加速器 Web UI 中新增自訂服務](iot-accelerators-remote-monitoring-customize-service.md)
- [在遠端監視解決方案加速器 Web UI 中新增自訂格線](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>新增飛出視窗

若要在 Web UI 中新增飛出視窗，您必須新增定義飛出視窗的原始程式檔，並修改一些現有檔案讓 Web UI 知道新的元件。

### <a name="add-the-new-files-that-define-the-flyout"></a>新增定義飛出視窗的檔案

為了讓您能夠開始，**src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout** 資料夾包含定義飛出視窗的檔案：

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exampleFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

將 **src/walkthrough/components/pages/pageWithFlyout/flyouts** 資料夾複製到 **src/components/pages/example** 資料夾。

### <a name="add-the-flyout-to-the-page"></a>在頁面上新增飛出視窗

修改 **src/components/pages/example/basicPage.js** 以新增飛出視窗。

新增 **Btn** 到 **components/shared** 的匯入，並新增 **svgs** 和 **ExampleFlyoutContainer** 的匯入：

```js
import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar,
  Btn
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';
import { svgs } from 'utilities';
import { ExampleFlyoutContainer } from './flyouts/exampleFlyout';
```

新增 **closedFlyoutState** 的 **const** 定義，並將其新增至建構函式中的狀態：

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

將下列函式新增至 **BasicPage** 類別：

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

將下列 **const** 定義新增至 **render** 函式：

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

在快顯功能表中新增用來開啟飛出視窗的按鈕：

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

在頁面內容中新增一些文字和飛出視窗容器：

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>測試飛出視窗

如果 Web UI 已不在本機執行，請在存放庫本機複本的根目錄中執行下列命令：

```cmd/sh
npm start
```

上一個命令會在本機於 `http://localhost:3000/dashboard` 執行 UI。 瀏覽至 [範例] 頁面，然後按一下 [開啟飛出視窗]。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解可用來協助您在遠端監視解決方案加速器 Web UI 中新增或自訂頁面的資源。

現在您已在頁面上定義飛出視窗，下一個步驟是[在遠端監視解決方案加速器 Web UI 中的儀表板新增面板](iot-accelerators-remote-monitoring-customize-panel.md)。

如需有關遠端監視解決方案加速器的詳細概念資訊，請參閱 [遠端監視架構](iot-accelerators-remote-monitoring-sample-walkthrough.md)。
