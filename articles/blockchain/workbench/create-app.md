---
title: 建立區塊鏈應用程式 - Azure Blockchain Workbench
description: 關於如何為 Azure Blockchain Workbench 預覽版建立區塊鏈應用程式的教學課程。
ms.date: 10/14/2019
ms.topic: tutorial
ms.reviewer: brendal
ms.openlocfilehash: 7be79757f506ea9fb854a248e77f0ff4f1b74f47
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073108"
---
# <a name="tutorial-create-a-blockchain-application-for-azure-blockchain-workbench"></a>教學課程：為 Azure Blockchain Workbench 建立區塊鏈應用程式

您可以使用 Azure Blockchain Workbench 建立區塊鏈應用程式，來表示組態和智慧合約程式碼所定義的多方工作流程。

您將學習如何：

> [!div class="checklist"]
> * 設定區塊鏈應用程式
> * 建立智慧合約程式碼檔案
> * 在 Blockchain Workbench 中新增區塊鏈應用程式
> * 在區塊鏈應用程式中新增成員

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

* Blockchain Workbench 部署。 如需詳細資訊，請參閱 [Azure Blockchain Workbench 部署](deploy.md)，以取得部署的詳細資訊。
* 租用戶中與 Blockchain Workbench 相關聯的 Azure Active Directory 使用者。 如需詳細資訊，請參閱[在 Azure Blockchain Workbench 中新增 Azure AD 使用者](manage-users.md#add-azure-ad-users)。
* Blockchain Workbench 系統管理員帳戶。 如需詳細資訊，請參閱 [Azure Blockchain Workbench 中的 Blockchain Workbench 系統管理員](manage-users.md#manage-blockchain-workbench-administrators)。

## <a name="hello-blockchain"></a>你好，區塊鏈！

讓我們建置一個基本應用程式，讓要求者傳送要求，並讓回應者傳送要求的回應。
例如，要求可以是 "Hello, how are you?"，回應可以是 "I'm great!"。 要求和回應都會記錄在基礎區塊鏈上。

請依照步驟來建立應用程式檔案，或者也可以[從 GitHub 下載範例](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain)。

## <a name="configuration-file"></a>組態檔

組態中繼資料會定義區塊鏈應用程式的高階工作流程和互動模型。 組態中繼資料會表示區塊鏈應用程式的工作流程階段和互動模型。

1. 在您慣用的編輯器中，建立名為 `HelloBlockchain.json` 的檔案。
2. 新增下列 JSON 以定義區塊鏈應用程式的組態。

    ``` json
    {
      "ApplicationName": "HelloBlockchain",
      "DisplayName": "Hello, Blockchain!",
      "Description": "A simple application to send request and get response",
      "ApplicationRoles": [
        {
          "Name": "Requestor",
          "Description": "A person sending a request."
        },
        {
          "Name": "Responder",
          "Description": "A person responding to a request"
        }
      ],
      "Workflows": [
        {
          "Name": "HelloBlockchain",
          "DisplayName": "Request Response",
          "Description": "A simple workflow to send a request and receive a response.",
          "Initiators": [ "Requestor" ],
          "StartState": "Request",
          "Properties": [
            {
              "Name": "State",
              "DisplayName": "State",
              "Description": "Holds the state of the contract.",
              "Type": {
                "Name": "state"
              }
            },
            {
              "Name": "Requestor",
              "DisplayName": "Requestor",
              "Description": "A person sending a request.",
              "Type": {
                "Name": "Requestor"
              }
            },
            {
              "Name": "Responder",
              "DisplayName": "Responder",
              "Description": "A person sending a response.",
              "Type": {
                "Name": "Responder"
              }
            },
            {
              "Name": "RequestMessage",
              "DisplayName": "Request Message",
              "Description": "A request message.",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "ResponseMessage",
              "DisplayName": "Response Message",
              "Description": "A response message.",
              "Type": {
                "Name": "string"
              }
            }
          ],
          "Constructor": {
            "Parameters": [
              {
                "Name": "message",
                "Description": "...",
                "DisplayName": "Request Message",
                "Type": {
                  "Name": "string"
                }
              }
            ]
          },
          "Functions": [
            {
              "Name": "SendRequest",
              "DisplayName": "Request",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "requestMessage",
                  "Description": "...",
                  "DisplayName": "Request Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            },
            {
              "Name": "SendResponse",
              "DisplayName": "Response",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "responseMessage",
                  "Description": "...",
                  "DisplayName": "Response Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            }
          ],
          "States": [
            {
              "Name": "Request",
              "DisplayName": "Request",
              "Description": "...",
              "PercentComplete": 50,
              "Value": 0,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": ["Responder"],
                  "AllowedInstanceRoles": [],
                  "Description": "...",
                  "Function": "SendResponse",
                  "NextStates": [ "Respond" ],
                  "DisplayName": "Send Response"
                }
              ]
            },
            {
              "Name": "Respond",
              "DisplayName": "Respond",
              "Description": "...",
              "PercentComplete": 90,
              "Value": 1,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": [],
                  "AllowedInstanceRoles": ["Requestor"],
                  "Description": "...",
                  "Function": "SendRequest",
                  "NextStates": [ "Request" ],
                  "DisplayName": "Send Request"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

3. 儲存 `HelloBlockchain.json` 檔案。

組態檔有數個區段。 每個區段的相關詳細資料如下所示：

### <a name="application-metadata"></a>應用程式中繼資料

組態檔的開頭包含應用程式的相關資訊，包括應用程式名稱和說明。

### <a name="application-roles"></a>應用程式角色

應用程式角色區段會定義可在區塊鏈應用程式中採取行動或參與的使用者角色。 您可以定義一組以功能為基礎的不同角色。 在要求-回應的案例中，負責產生要求的實體要求者功能和負責產生回應的實體回應者會有所區別。

### <a name="workflows"></a>工作流程

工作流程會定義合約的一或多個階段與動作。 在要求-回應案例中，工作流程的第一個階段 (狀態) 是要求者 (角色) 採取動作 (轉換) 來傳送要求 (函式)。 下一個階段 (狀態) 是回應者 (角色) 採取動作 (轉換) 來傳送回應 (函式)。 應用程式的工作流程可能涉及要說明合約流程所需的屬性、函式和狀態。

如需組態檔內容的詳細資訊，請參閱 [Azure Blockchain Workbench 組態參考](configuration.md)。

## <a name="smart-contract-code-file"></a>智慧合約程式碼檔案

智慧合約可表示區塊鏈應用程式的商務邏輯。 目前，Blockchain Workbench 支援將 Ethereum 用於區塊鏈總帳。 Ethereum 使用 [Solidity](https://solidity.readthedocs.io) 來作為其程式設計語言，以撰寫智慧合約的自我強制執行商務邏輯。

Solidity 中的智慧合約類似於物件導向語言中的類別。 每個合約都包含用來實作智慧合約階段和動作的狀態與函式。

在您慣用的編輯器中，建立稱為 `HelloBlockchain.sol` 的檔案。

### <a name="version-pragma"></a>版本 pragma

您最好要指定作為目標的 Solidity 版本。 指定版本可協助避免與未來的 Solidity 版本不相容。

請在 `HelloBlockchain.sol` 智慧合約程式碼檔案的頂端新增下列版本 pragma。

``` solidity
pragma solidity >=0.4.25 <0.6.0;
```

### <a name="configuration-and-smart-contract-code-relationship"></a>組態和智慧合約程式碼關聯性

Blockchain Workbench 會使用組態檔和智慧合約程式碼檔案來建立區塊鏈應用程式。 組態中所定義的內容和智慧合約中的程式碼之間具有關聯性。 合約詳細資料、函式、參數和類型必須相符才能建立應用程式。 Blockchain Workbench 會在建立應用程式前先驗證檔案。

### <a name="contract"></a>合約

在 `HelloBlockchain.sol` 智慧合約程式碼檔案中新增 **contract** 標頭。

``` solidity
contract HelloBlockchain {
```

### <a name="state-variables"></a>狀態變數

狀態變數可儲存每個合約執行個體的狀態值。 合約中的狀態變數必須符合組態檔中所定義的工作流程屬性。

在 `HelloBlockchain.sol` 智慧合約程式碼檔案的合約中新增狀態變數。

``` solidity
    //Set of States
    enum StateType { Request, Respond}

    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;

    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>建構函式

建構函式可定義工作流程新的智慧合約執行個體的輸入參數。 建構函式所需要的參數會定義為組態檔中的建構函式參數。 兩個檔案中的參數數目、順序和類型都必須相符。

在建構函式中，撰寫您想要在建立合約之前執行的任何商務邏輯。 例如，使用起始值對狀態變數進行初始化。

在 `HelloBlockchain.sol` 智慧合約程式碼檔案的合約中新增建構函式。

``` solidity
    // constructor function
    constructor(string memory message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    }
```

### <a name="functions"></a>函式

函式是合約中商務邏輯的可執行單位。 函式所需要的參數會定義為組態檔中的函式參數。 兩個檔案中的參數數目、順序和類型都必須相符。 函式會與組態檔中 Blockchain Workbench 工作流程的轉換相關聯。 執行轉換動作可移至合約為應用程式的工作流程所決定的下一個階段。

撰寫您想要在函式中執行的任何商務邏輯。 例如，修改狀態變數的值。

1. 在 `HelloBlockchain.sol` 智慧合約程式碼檔案的合約中新增下列函式。

    ``` solidity
        // call this function to send a request
        function SendRequest(string memory requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
        }
    
        // call this function to send a response
        function SendResponse(string memory responseMessage) public
        {
            Responder = msg.sender;
    
            ResponseMessage = responseMessage;
            State = StateType.Respond;
        }
    }
    ```

2. 儲存 `HelloBlockchain.sol` 智慧合約程式碼檔案。

## <a name="add-blockchain-application-to-blockchain-workbench"></a>在 Blockchain Workbench 中新增區塊鏈應用程式

若要在 Blockchain Workbench 中新增區塊鏈應用程式，請上傳組態檔和智慧合約檔案以定義應用程式。

1. 在網頁瀏覽器中瀏覽至 Blockchain Workbench 的網址。 例如，`https://{workbench URL}.azurewebsites.net/`。部署 Blockchain Workbench 時會建立 Web 應用程式。 如需如何尋找 Blockchain Workbench 網址的資訊，請參閱 [Blockchain Workbench Web URL](deploy.md#blockchain-workbench-web-url)
2. 以 [Blockchain Workbench 系統管理員](manage-users.md#manage-blockchain-workbench-administrators)的身分登入。
3. 選取 [應用程式]   > [新增]  。 [新增應用程式]  窗格隨即顯示。
4. 選取 [上傳合約組態]   > [瀏覽]  來找到您所建立的 **HelloBlockchain.json** 組態檔。 系統會自動驗證組態檔。 選取 [顯示]  連結以顯示驗證錯誤。 先修正驗證錯誤再部署應用程式。
5. 選取 [上傳合約程式碼]   > [瀏覽]  來找到 **HelloBlockchain.sol** 智慧合約程式碼檔案。 系統會自動驗證程式碼檔案。 選取 [顯示]  連結以顯示驗證錯誤。 先修正驗證錯誤再部署應用程式。
6. 選取 [部署]  以根據組態和智慧合約檔案建立區塊鏈應用程式。

區塊鏈應用程式的部署需要數分鐘的時間。 部署完成時，新的應用程式會顯示在 [應用程式]  中。 

> [!NOTE]
> 您也可以使用 [Azure Blockchain Workbench REST API](/rest/api/azure-blockchain-workbench) 建立區塊鏈應用程式。

## <a name="add-blockchain-application-members"></a>新增區塊鏈應用程式成員

在應用程式中新增應用程式成員以起始合約並對其採取動作。 若要新增應用程式成員，您必須是 [Blockchain Workbench 系統管理員](manage-users.md#manage-blockchain-workbench-administrators)。

1. 選取 [應用程式]   > [Hello, Blockchain!]  。
2. 與應用程式相關聯的成員數目會顯示在頁面右上角。 若為新的應用程式，成員數目會是零。
3. 選取頁面右上角的 [成員]  連結。 隨即會顯示目前的應用程式成員清單。
4. 在成員資格清單中，選取 [新增成員]  。
5. 選取或輸入您想要新增的成員名稱。 只有存在於 Blockchain Workbench 租用戶的 Azure AD 使用者才會列出。 如果找不到使用者，則需要[新增 Azure AD 使用者](manage-users.md#add-azure-ad-users)。
6. 選取成員的 [角色]  。 針對第一個成員，選取 [要求者]  作為角色。
7. 選取 [新增]  以在應用程式中新增與角色相關聯的成員。
8. 在應用程式中新增另一個具有**回應者**角色的成員。

如需如何在 Blockchain Workbench 中管理使用者的詳細資訊，請參閱[在 Azure Blockchain Workbench 中管理使用者](manage-users.md)

## <a name="next-steps"></a>後續步驟

在這篇操作說明文章中，您已建立基本的要求和回應應用程式。 若要了解如何使用應用程式，請繼續閱讀下一篇操作說明文章。

> [!div class="nextstepaction"]
> [使用區塊鏈應用程式](use.md)
