---
title: 在遠端監視解決方案 UI 中新增方格 - Azure | Microsoft Docs
description: 本文說明如何在遠端監視解決方案加速器 Web UI 中的頁面上，新增方格。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.openlocfilehash: e27c1c4303129467c0bd05152570e26f129585a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186283"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>在遠端監視解決方案加速器 Web UI 中新增自訂格線

本文說明如何在遠端監視解決方案加速器 Web UI 中的頁面上，新增方格。 本文章說明：

- 如何準備本機開發環境。
- 如何在 Web UI 中的頁面上新增方格。

本文中的方格範例會顯示來自服務的資料，而[在遠端監視解決方案加速器 Web UI 中新增自訂服務](iot-accelerators-remote-monitoring-customize-service.md)操作說明文章會說明新增方式。

## <a name="prerequisites"></a>Prerequisites

若要完成本操作指南中的部署，您必須在本機開發機器上安裝下列軟體：

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>在您開始使用 Intune 之前

請先完成下列文章中的步驟再繼續：

- [在遠端監視解決方案加速器 Web UI 中新增自訂頁面](iot-accelerators-remote-monitoring-customize-page.md)。
- [在遠端監視解決方案加速器 Web UI 中新增自訂服務](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>新增格線

若要在 Web UI 中新增方格，您必須新增定義方格的原始程式檔，並修改一些現有檔案讓 Web UI 知道新的元件。

### <a name="add-the-new-files-that-define-the-grid"></a>新增可定義方格的檔案

為了讓您能夠開始，**src/walkthrough/components/pages/pageWithGrid/exampleGrid** 資料夾包含可定義方格的檔案：

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

將 **src/walkthrough/components/pages/pageWithGrid/exampleGrid** 資料夾複製到 **src/components/pages/example** 資料夾。

### <a name="add-the-grid-to-the-page"></a>在頁面上新增方格

如下所示修改 **src/components/pages/example/basicPage.container.js** 以匯入服務定義：

```js
import { connect } from 'react-redux';
import { translate } from 'react-i18next';

import {
  epics as exampleEpics,
  getExamples,
  getExamplesError,
  getExamplesLastUpdated,
  getExamplesPendingStatus
} from 'store/reducers/exampleReducer';
import { BasicPage } from './basicPage';

// Pass the data
const mapStateToProps = state => ({
  data: getExamples(state),
  error: getExamplesError(state),
  isPending: getExamplesPendingStatus(state),
  lastUpdated: getExamplesLastUpdated(state)
});

// Wrap the dispatch method
const mapDispatchToProps = dispatch => ({
  fetchData: () => dispatch(exampleEpics.actions.fetchExamples())
});

export const BasicPageContainer = translate()(connect(mapStateToProps, mapDispatchToProps)(BasicPage));
```

如下所示修改 **src/components/pages/example/basicPage.js** 以新增方格：

```js
// Copyright (c) Microsoft. All rights reserved.

import React, { Component } from 'react';

import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';

import './basicPage.css';

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null };
  }

  componentDidMount() {
    const { isPending, lastUpdated, fetchData } = this.props;
    if (!lastUpdated && !isPending) fetchData();
  }

  onGridReady = gridReadyEvent => this.gridApi = gridReadyEvent.api;

  onContextMenuChange = contextBtns => this.setState({ contextBtns });

  render() {
    const { t, data, error, isPending, lastUpdated, fetchData } = this.props;
    const gridProps = {
      onGridReady: this.onGridReady,
      rowData: isPending ? undefined : data || [],
      onContextMenuChange: this.onContextMenuChange,
      t: this.props.t
    };

    return [
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
      </ContextMenu>,
      <PageContent className="basic-page-container" key="page-content">
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
    ];
  }
}
```

如下所示修改 **src/components/pages/example/basicPage.test.js** 以更新測試：

```js
// Copyright (c) Microsoft. All rights reserved.

import React from 'react';
import { shallow } from 'enzyme';
import 'polyfills';

import { BasicPage } from './basicPage';

describe('BasicPage Component', () => {
  it('Renders without crashing', () => {

    const fakeProps = {
      data: undefined,
      error: undefined,
      isPending: false,
      lastUpdated: undefined,
      fetchData: () => { },
      t: () => { },
    };

    const wrapper = shallow(
      <BasicPage {...fakeProps} />
    );
  });
});
```

## <a name="test-the-grid"></a>測試方格

如果 Web UI 已不在本機執行，請在存放庫本機複本的根目錄中執行下列命令：

```cmd/sh
npm start
```

上一個命令會在本機於 `http://localhost:3000/dashboard` 執行 UI。 瀏覽至 [範例]**** 頁面，以查看來自服務的方格顯示資料。

## <a name="select-rows"></a>選取資料列

有兩個選項可讓使用者選取方格中的資料列：

### <a name="hard-select-rows"></a>硬式選取資料列

如果使用者需要同時處理多個資料列，請在資料列上使用核取方塊：

1. 將 **checkboxColumn** 新增至提供給方格的 **columnDefs**，以啟用資料列硬式選取。 **checkboxColumn** 定義於 **/src/components/shared/pcsGrid/pcsGrid.js**：

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. 若要存取所選的項目，您可取得內部方格 API 的參考：

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. 硬式選取方格中的資料列時，請提供頁面的內容按鈕：

    ```js
    this.contextBtns = [
      <Btn key="context-btn-1" svg={svgs.reconfigure} onClick={this.doSomething()}>Button 1</Btn>,
      <Btn key="context-btn-2" svg={svgs.trash} onClick={this.doSomethingElse()}>Button 2</Btn>
    ];
    ```

    ```js
    onHardSelectChange = (selectedObjs) => {
      const { onContextMenuChange, onHardSelectChange } = this.props;
      // Show the context buttons when there are rows checked.
      if (isFunc(onContextMenuChange)) {
        onContextMenuChange(selectedObjs.length > 0 ? this.contextBtns : null);
      }
      //...
    }
    ```

1. 按一下內容按鈕後，即可取得硬式選取的項目來執行工作：

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('Hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>軟式選取資料列

如果使用者只需要處理單一資料列，請為 **columnDefs** 中的一或多個資料行設定軟式選取連結。

1. 在 **exampleGridConfig.js** 中，新增 **SoftSelectLinkRenderer** 作為 **columnDef** 的 **cellRendererFramework**。

    ```js
    export const exampleColumnDefs = {
      id: {
        headerName: 'examples.grid.name',
        field: 'id',
        sort: 'asc',
        cellRendererFramework: SoftSelectLinkRenderer
      }
    };
    ```

1. 按一下軟式選取連結後，它會觸發 **onSoftSelectChange** 事件。 對該資料列執行任何所需的動作，例如開啟詳細資料飛出視窗。 此範例只會寫入至主控台：

    ```js
    onSoftSelectChange = (rowId, rowData) => {
      //Note: only the Id is reliable, rowData may be out of date
      const { onSoftSelectChange } = this.props;
      if (rowId) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', rowId);
        this.setState({ softSelectedId: rowId });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(rowId, rowData);
      }
    }
    ```

## <a name="next-steps"></a>後續步驟

在本文中，您已了解可用來協助您在遠端監視解決方案加速器 Web UI 中新增或自訂頁面的資源。

您現在已定義了一個方格，下一個步驟是[將自訂飛出視窗新增至遠端監視解決方案加速器 Web UI](iot-accelerators-remote-monitoring-customize-flyout.md) (其顯示在範例頁面上)。

如需關於遠端監視解決方案加速器的詳細概念資訊，請參閱[遠端監視架構](iot-accelerators-remote-monitoring-sample-walkthrough.md)。
