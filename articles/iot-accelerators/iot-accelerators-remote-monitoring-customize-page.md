---
title: 在遠端監視解決方案 UI 中新增頁面 - Azure | Microsoft Docs
description: 本文說明如何在遠端監視解決方案加速器 Web UI 中新增頁面。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: 0228f317e2d3380f2387dd557a27203eb3abc4ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240272"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>在遠端監視解決方案加速器 Web UI 中新增自訂頁面

本文說明如何在遠端監視解決方案加速器 Web UI 中新增頁面。 本文章說明：

- 如何準備本機開發環境。
- 如何將頁面新增至 Web UI。

其他操作說明指南會擴充此案例，將更多功能新增至您所新增的頁面。

## <a name="prerequisites"></a>Prerequisites

若要完成本操作指南中的部署，您必須在本機開發機器上安裝下列軟體：

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>準備適用於 UI 的本機開發環境

遠端監視解決方案加速器 UI 程式碼是使用 [React](https://reactjs.org/) JavaScript 架構來實作。 您可以在[遠端監視 WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub 存放庫中找到原始程式碼。

若要對 UI 進行變更並且測試，您可以在本機開發電腦上執行。 (選擇性) 本機複本可以連線到已部署的解決方案加速器執行個體，讓它能夠與真實或模擬的裝置互動。

若要準備本機開發環境，請使用 Git 將[遠端監視 WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) 存放庫複製到本機電腦：

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>新增頁面

若要在 Web UI 中新增頁面，您必須新增可定義此頁面的原始程式檔，然後修改一些現有檔案，讓 Web UI 知道新的頁面。

### <a name="add-the-new-files-that-define-the-page"></a>新增可定義頁面的檔案

為了讓您能夠開始，**src/walkthrough/components/pages/basicPage** 資料夾包含四個可定義簡單頁面的檔案：

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

建立新的資料夾 **src/components/pages/example** ，並將這四個檔案複製到其中。

### <a name="add-the-new-page-to-the-web-ui"></a>將頁面新增至 Web UI

若要將頁面新增至 Web UI，請對現有檔案進行下列變更：

1. 將新的頁面容器新增至 **src/components/pages/index.js** 檔案：

    ```js
    export * from './example/basicPage.container';
    ```

1. (選擇性) 新增新頁面的 SVG 圖示。 如需詳細資訊，請參閱 [webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md)。 您可以使用現有的 SVG 檔案。

1. 將頁面名稱新增至 translations 檔案 **public/locales/en/translations.json**。 Web UI 會使用[i18next](https://www.i18next.com/) 進行國際化。

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. 開啟 **src/components/app.js** 檔案，其可定義最上層的應用程式頁面。 將頁面新增至匯入清單：

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. 在相同的檔案中，將頁面新增至 `pagesConfig` 陣列。 設定路由的 `to` 位址、參考 SVG 圖示和先前新增的轉譯，以及將 `component` 設定為頁面的容器：

    ```js
    const pagesConfig = [
      //...
      {
        to: '/basicpage',
        exact: true,
        svg: svgs.tabs.example,
        labelId: 'tabs.basicPage',
        component: BasicPageContainer
      },
      //...
    ];
    ```

1. 將任何新的階層連結新增至 `crumbsConfig` 陣列：

    ```js
    const crumbsConfig = [
      //...
      {
        path: '/basicpage', crumbs: [
          { to: '/basicpage', labelId: 'tabs.basicPage' }
        ]
      },
      //...
    ];
    ```

    此範例頁面只有一個階層連結，但某些頁面可有更多階層連結。

儲存您的所有變更。 您已準備好以您新增的頁面執行 Web UI。

### <a name="test-the-new-page"></a>測試新頁面

在命令提示字元中瀏覽至存放庫本機複本的根目錄，執行下列命令來安裝必要的程式庫，並在本機執行 Web UI：

```cmd/sh
npm install
npm start
```

前面的命令在 本地運行[http://localhost:3000/dashboard](http://localhost:3000/dashboard)UI。

不需將 Web UI 的本機執行個體連線到已部署的解決方案加速器執行個體，您即可在儀表板上看見錯誤。 這些錯誤不會影響您測試新頁面的能力。

當網站在本機執行時，您現在可以編輯程式碼，並且看見 Web UI 動態更新。

## <a name="optional-connect-to-deployed-instance"></a>[選用] 連線到已部署的執行個體

(選擇性) 您可以將 Web UI 的本機執行複本連線到雲端的遠端監視解決方案加速器：

1. 使用 **pcs** CLI 來部署解決方案加速器的**基本**執行個體。 記下部署的名稱以及您為虛擬機器提供的認證。 如需詳細資訊，請參閱[使用 CLI 進行部署](iot-accelerators-remote-monitoring-deploy-cli.md)。

1. 使用 Azure 門戶或[az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)啟用 SSH 對解決方案中承載微服務的虛擬機器的 SSH 存取權限。 例如：

    ```azurecli
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    您只應該在測試和開發期間啟用 SSH 存取。 如果啟用 SSH，[應儘快再次禁用它](../security/fundamentals/network-best-practices.md)。

1. 使用 Azure 入口網站或 [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 來尋找虛擬機器的名稱和公用 IP 位址。 例如：

    ```azurecli
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. 使用 SSH 來利用上一個步驟的 IP 位址，以及您執行 **pcs** 來部署解決方案時所提供的認證，以連線到您的虛擬機器。

1. 若要讓本機 UX 能夠連線，請在虛擬機器中的 Bash 殼層中執行下列命令：

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. 當您看到命令完成且網站啟動之後，就可以從虛擬機器中斷連線。

1. 在[遠端監視 WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) 存放庫的本機複本中，編輯 **.env** 檔案，以新增已部署解決方案的 URL：

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>後續步驟

在本文中，您已了解可用來協助您在遠端監視解決方案加速器中自訂 Web UI 的資源。

您現在已定義了一個頁面，下一個步驟是[將自訂服務新增至遠端監視解決方案加速器 Web UI](iot-accelerators-remote-monitoring-customize-service.md)，以擷取要顯示在 UI 中的資料。

如需關於遠端監視解決方案加速器的詳細概念資訊，請參閱[遠端監視架構](iot-accelerators-remote-monitoring-sample-walkthrough.md)。
