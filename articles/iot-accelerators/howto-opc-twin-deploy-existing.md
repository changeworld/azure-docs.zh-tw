---
title: 如何將 OPC 對應項模組部署至現有的 Azure 專案 |Microsoft Docs
description: 本文說明如何將 OPC 對應項部署至現有的專案。 您也可以瞭解如何針對部署失敗進行疑難排解。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b971ec13c71ccfd7d28ae6987593d09201b9b764
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "73824126"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>將 OPC 對應項部署至現有的專案

OPC 對應項模組會在 IoT Edge 上執行，並為 OPC 對應項和登錄服務提供數個 Edge 服務。

OPC 對應項微服務可透過 OPC 對應項 IoT Edge 模組，協助工廠中的處理站運算子與 OPC UA 伺服器裝置之間的通訊。 微服務會透過其 REST API 公開 OPC UA 服務（流覽、讀取、寫入及執行）。 

OPC UA 裝置登錄微服務可讓您存取已註冊的 OPC UA 應用程式及其端點。 操作員和系統管理員可以註冊及取消註冊新的 OPC UA 應用程式，並流覽現有的 OPC UA 應用程式，包括其端點。 除了應用程式和端點管理之外，登錄服務也會將已註冊的 OPC 對應項 IoT Edge 模組目錄。 服務 API 可讓您控制邊緣模組的功能，例如啟動或停止伺服器探索（掃描服務），或啟用可使用 OPC 對應項微服務存取的新端點 twins。

模組的核心是監督員身分識別。 監督員會管理端點對應項，其對應至使用對應的 OPC UA 登錄 API 來啟用的 OPC UA 伺服器端點。 此端點 twins 會將從雲端中執行的 OPC 對應項微服務接收的 OPC UA JSON，轉譯成 OPC UA 二進位訊息，而這會透過可設定狀態的安全通道傳送至受控端點。 監督員也提供探索服務，將裝置探索事件傳送至 OPC UA 裝置上線服務以進行處理，其中這些事件會導致 OPC UA 登錄的更新。  本文說明如何將 OPC 對應項模組部署至現有的專案。

> [!NOTE]
> 如需部署詳細資料和指示的詳細資訊，請參閱 GitHub 存放[庫](https://github.com/Azure/azure-iiot-opc-twin-module)。

## <a name="prerequisites"></a>必要條件

請確定您已安裝 PowerShell 和[AzureRM powershell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)擴充功能。 如果您尚未這麼做，請複製此 GitHub 存放庫。 在 PowerShell 中執行下列命令：

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>將工業 IoT 服務部署至 Azure

1. 在您的 PowerShell 會話中，執行：

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. 遵循提示，將名稱指派給部署的資源群組和網站的名稱。   腳本會將微服務和其 Azure 平臺相依性部署到 Azure 訂用帳戶中的資源群組。  此腳本也會在您的 Azure Active Directory （AAD）租使用者中註冊應用程式，以支援 OAUTH 型驗證。  部署需要幾分鐘的時間。  成功部署解決方案後，您會看到的範例：

   ![產業 IoT OPC 對應項部署至現有的專案](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   輸出會包含公用端點的 URL。 

3. 腳本順利完成後，請選取您是否要儲存檔案 `.env` 。  `.env`如果您想要使用主控台之類的工具連接到雲端端點，或部署模組以進行開發和偵測，則需要環境檔案。

## <a name="troubleshooting-deployment-failures"></a>針對部署失敗進行疑難排解

### <a name="resource-group-name"></a>資源群組名稱

請確定您使用簡短且簡單的資源組名。  此名稱也會用來命名資源，因此它必須符合資源命名需求。  

### <a name="website-name-already-in-use"></a>網站名稱已在使用中

網站的名稱可能已在使用中。  如果您遇到此錯誤，您需要使用不同的應用程式名稱。

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory （AAD）註冊

部署腳本會嘗試在 Azure Active Directory 中註冊兩個 AAD 應用程式。  視您對所選 AAD 租使用者的許可權而定，部署可能會失敗。 有兩個選項：

1. 如果您從租使用者清單中選擇了 AAD 租使用者，請重新開機腳本，並從清單中選擇另一個。
2. 或者，在另一個訂用帳戶中部署私人 AAD 租使用者、重新開機腳本，然後選擇使用它。

> [!WARNING]
> 永遠不要繼續，而不進行驗證。  如果您選擇這樣做，任何人都可以從未經驗證的網際網路存取您的 OPC 對應項端點。   您一律可以選擇 [[本機] 部署選項](howto-opc-twin-deploy-dependencies.md)來啟動輪。

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>部署全功能產業 IoT 服務示範

而不只是服務和相依性，您也可以部署一個全功能示範。  其中的全部示範包含三個 OPC UA 伺服器、OPC 對應項模組、所有微服務，以及範例 Web 應用程式。  其適用于示範用途。

1. 請確定您有存放庫的複本（請參閱上文）。 在存放庫的根目錄中開啟 PowerShell 提示字元，然後執行：

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. 遵循提示，將新名稱指派給資源群組，並將名稱指派給網站。  成功部署之後，腳本會顯示 web 應用程式端點的 URL。

## <a name="deployment-script-options"></a>部署腳本選項

腳本會採用下列參數：

```powershell
-type
```

部署類型（vm、本機、示範）

```powershell
-resourceGroupName
```

可以是現有或新資源群組的名稱。

```powershell
-subscriptionId
```

選擇性，這是將部署資源的訂用帳戶識別碼。

```powershell
-subscriptionName
```

或訂用帳戶名稱。

```powershell
-resourceGroupLocation
```

選擇性，資源群組位置。 如果指定，將會嘗試在這個位置建立新的資源群組。

```powershell
-aadApplicationName
```

要在其中註冊的 AAD 應用程式名稱。

```powershell
-tenantId
```

要使用的 AAD 租使用者。

```powershell
-credentials
```

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何將 OPC 對應項部署至現有的專案，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [OPC UA 用戶端和 OPC UA PLC 的安全通訊](howto-opc-vault-secure.md)
