---
title: 建立、建置和部署智慧型合約的教學課程 - Azure 區塊鏈服務
description: 本教學課程說明如何在 Visual Studio Code 中使用適用於 Ethereum 的 Azure 區塊鏈開發套件擴充功能，在 Azure 區塊鏈服務上建立、建置及部署智慧型合約。
ms.date: 04/22/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: dc23c680dfb2ed33cae2a251af16e1b1f25c6ac7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086652"
---
# <a name="tutorial-create-buildanddeploysmartcontracts-on-azure-blockchain-service"></a>教學課程：在 Azure 區塊鏈服務上建立、建置和部署智慧型合約

在本教學課程中，在 Visual Studio Code 中使用適用於 Ethereum 的 Azure 區塊鏈開發套件擴充功能，在 Azure 區塊鏈服務上建立、建置及部署智慧型合約。 您也可使用開發套件以透過交易執行智慧型合約函式。

您可以使用適用於 Ethereum 的 Azure 區塊鏈服務開發套件來：

> [!div class="checklist"]
> * 建立智慧型合約
> * 部署智慧型合約
> * 透過交易執行智慧型合約函式

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

* 完成[快速入門：使用 Visual Studio Code 連線至聯盟網路](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [適用於 Ethereum 的 Azure 區塊鏈服務開發套件擴充功能](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x 或更高版本](https://nodejs.org/download)
* [Git 2.10.x 或更高版本](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) 將 python.exe 新增至您的路徑。 Azure 區塊鏈服務開發套件需要您的路徑中有 Python 2.7.15 版。
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

在 Windows 上，node-gyp 模組需要已安裝的 C++ 編譯器。 您可以使用 MSBuild 工具：

* 如果已安裝 Visual Studio 2017，請將 npm 設定為使用 MSBuild 工具搭配 `npm config set msvs_version 2017 -g` 命令
* 如果已安裝 Visual Studio 2019，請設定 npm 的 MSBuild 工具路徑。 例如， `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* 否則，請在提升權限的「以系統管理員身分執行」  命令殼層中，使用 `npm install --global windows-build-tools` 來安裝獨立的 VS Build 工具。

如需 node-gyp 的詳細資訊，請參閱 [GitHub 上的 node-gyp 存放庫](https://github.com/nodejs/node-gyp)。

## <a name="create-a-smart-contract"></a>建立智慧型合約

適用於 Ethereum 的 Azure 區塊鏈服務開發套件會使用專案範本和 Truffle 工具來協助進行合約的 Scaffold、建置及部署。 開始之前，請完成先決條件[快速入門：使用 Visual Studio Code 連線至聯盟網路](connect-vscode.md)。 本快速入門引導您進行適用於 Ethereum 的 Azure 區塊鏈開發套件安裝和設定。

1. 從 VS Code 命令選擇區選擇 [Azure 區塊鏈服務:  新增 Solidity 專案]。
1. 選擇 [建立基本專案]  。
1. 建立名為 `HelloBlockchain` 的新資料夾，然後**選取新的專案路徑**。

Azure 區塊鏈服務開發套件會為您建立並初始化新的 Solidity 專案。 基本專案包含 **HelloBlockchain** 智慧型合約範例，以及要在 Azure 區塊鏈服務中對聯盟成員建置及部署的所有必要檔案。 可能需要數分鐘的時間才能建立好專案。 您可以選取 Azure 區塊鏈服務的輸出，以在 VS Code 的終端機面板中監視進度。

專案結構看起來會像下列範例：

   ![Solidity 專案](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>建置智慧型合約

智慧型合約位於專案的 [合約]  目錄中。 您必須先編譯智慧型合約才能將其部署至區塊鏈。 請使用 [組建合約]  命令來編譯專案中的所有智慧型合約。

1. 在 VS Code 總管的提要欄位中，展開專案中的 [合約]  資料夾。
1. 對 [HelloBlockchain.sol]  按一下滑鼠右鍵，然後從功能表中選擇 [建置合約]  。

    ![選擇 [建置合約] 功能表 ](./media/send-transaction/build-contracts.png)

Azure 區塊鏈服務開發套件會使用 Truffle 來編譯智慧型合約。

![Truffle 編譯器輸出](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>部署智慧型合約

Truffle 會使用移轉指令碼將合約部署至 Ethereum 網路。 移轉項目是位於專案 [移轉]  目錄中的 JavaScript 檔案。

1. 若要部署智慧型合約，請對 [HelloBlockchain.sol]  按一下滑鼠右鍵，然後從功能表中選擇 [部署合約]  。
1. 請在命令選擇區中選擇您的 Azure 區塊鏈聯盟網路。 當您建立專案時，系統會在專案的 Truffle 設定檔中新增聯盟區塊鏈網路。
1. 選擇 [產生助憶檔]  。 選擇檔案名稱，並將助憶檔案儲存在專案資料夾中。 例如： `myblockchainmember.env` 。 助憶檔案可用來為區塊鏈成員產生 Ethereum 私密金鑰。

Azure 區塊鏈服務開發套件會使用 Truffle 來執行移轉指令碼，以將合約部署至區塊鏈。

![已成功部署的合約](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>呼叫合約函式

**HelloBlockchain** 合約的 **SendRequest** 函式會變更 **RequestMessage** 狀態變數。 區塊鏈網路狀態的變更會透過交易來完成。 您可以使用 Azure 區塊鏈服務開發套件智慧型合約互動頁面，透過交易來呼叫 **SendRequest** 函式。

1. 若要與智慧型合約互動，請以滑鼠右鍵按一下 [HelloBlockchain.sol]  ，然後從功能表中選擇 [顯示智慧型合約互動頁面]  。

    ![從功能表選擇 [顯示智慧型合約互動頁面]](./media/send-transaction/contract-interaction.png)

1. 互動頁面可讓您選擇已部署的合約版本、呼叫函式、檢視目前狀態，以及檢視中繼資料。

    ![智慧型合約互動頁面範例](./media/send-transaction/interaction-page.png)

1. 若要呼叫智慧型合約函式，請選取合約動作並傳遞引數。 選擇 [SendRequest]  合約動作，然後輸入 **Hello, Blockchain!** 來作為 **requestMessage** 參數。 選取 [執行]  ，以透過交易呼叫 **SendRequest** 函式。

    ![執行 SendRequest 動作](./media/send-transaction/sendrequest-action.png)

交易處理完成後，互動區段就會反映狀態變更。

![合約狀態變更](./media/send-transaction/contract-state.png)

SendRequest 函式會設定 [RequestMessage]  和 [狀態]  欄位。 [RequestMessage]  的目前狀態是傳遞了 **Hello, Blockchain** 的引數。 [狀態]  欄位值則仍是 [要求]  。

## <a name="clean-up-resources"></a>清除資源

您可以刪除不再需要的資源，方法是刪除您在*建立區塊鏈成員*必要條件快速入門中所建立的 `myResourceGroup` 資源群組。

若要刪除資源群組：

1. 在 Azure 入口網站中，瀏覽至左側瀏覽窗格中的 [資源群組]  ，然後選取您想要刪除的資源群組。
1. 選取 [刪除資源群組]  。 輸入資源群組名稱並選取 [刪除]  ，以確定進行刪除。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用 Azure 區塊鏈服務開發套件建立 Solidity 專案範例。 您已建置並部署智慧型合約，然後在裝載於 Azure 區塊鏈服務的區塊鏈聯盟網路上，透過交易呼叫了函式。

> [!div class="nextstepaction"]
> [使用 Azure 區塊鏈服務開發區塊鏈應用程式](develop.md)
