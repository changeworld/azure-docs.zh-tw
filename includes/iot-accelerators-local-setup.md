---
title: 包含檔案
description: 包含檔案
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "67174412"
---
## <a name="download-the-source-code"></a>下載原始程式碼

遠端監視原始程式碼存放庫包含您執行微服務 Docker 映像所需的原始程式碼和 Docker 設定檔。

若要複製及建立本機版本的存放庫，請使用您的命令列環境來瀏覽到您本機電腦上的適當資料夾。 然後執行下列其中一組命令，複製 .NET 存放庫：

若要下載最新版本的 .NET 微服務實作，請執行：

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> 除了您用來在本機執行微服務的指令碼之外，這些命令還會下載所有微服務的原始程式碼。 雖然您在 Docker 中執行微服務時不需要原始程式碼，但如果您稍後打算修改解決方案加速器並在本機測試您的變更，原始程式碼就會很有用。

## <a name="deploy-the-azure-services"></a>部署 Azure 服務

雖然此文章會示範如何在本機執行微服務，但它們會仰賴雲端中執行的 Azure 服務。 使用下列指令碼部署 Azure 服務。 下列指令碼範例假設您在 Windows 電腦上使用 .NET 存放庫。 若您是在另一個環境作業，請適當地調整路徑、檔案副檔名與路徑分隔符號。

### <a name="create-new-azure-resources"></a>建立新的 Azure 資源

若您尚未建立必要的 Azure 資源，請依照下列步驟執行：

1. 在您的命令列環境中，瀏覽到您複製之存放庫複本的 **\services\scripts\local\launch** 資料夾。

1. 執行下列命令來安裝 **pcs** CLI 工具，並登入至您的 Azure 帳戶：

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. 執行 **start.cmd** 指令碼。 指令碼會提示您輸入下列資訊：
   * 解決方案名稱。
   * 要使用的 Azure 訂用帳戶。
   * 要使用之 Azure 資料中心的位置。

     指令碼會使用您的解決方案名稱在 Azure 中建立資源群組。 此資源群組包含解決方案加速器使用的 Azure 資源。 一旦您不再需要對應的資源，您可以刪除此資源群組。

     此指令碼也會將一組有前置詞 **PCS** 的環境變數新增到您的本機電腦。 這些環境變數會提供遠端監視的詳細資料，以便能夠從 Azure Key Vault 資源讀取。 此 Key Vault 的資源是遠端監視將從中讀取其設定值的位置。

     > [!TIP]
     > 當腳本完成時，它也會將環境變數儲存至名** \<為主資料夾\>\\的檔案。\\\<電腦解決方案\>名稱. env**。 您可以將這些環境變數用於未來的解決方案加速器部署。 請注意，當您執行 **docker-compose** 時，您本機電腦上設定的任何環境變數會覆寫 **services\\scripts\\local\\.env** 檔案中的值。

1. 退出命令列環境。

### <a name="use-existing-azure-resources"></a>使用現有的 Azure 資源

如果您已經建立必要的 Azure 資源，請在本機電腦上建立對應的環境變數。
為下列各項設定環境變數：
* **PCS_KEYVAULT_NAME** -Azure Key Vault 資源的名稱
* **PCS_AAD_APPID** -AAD 應用程式識別碼
* **PCS_AAD_APPSECRET** -AAD 應用程式密碼

將從此 Azure Key Vault 資源讀取設定值。 這些環境變數可能會儲存在** \<您的主資料夾\>\\中。\\\<電腦解決方案\>名稱.** 部署中的 env 檔案。 請注意，當您執行 **docker-compose** 時，您本機電腦上設定的環境變數會覆寫 **services\\scripts\\local\\.env** 檔案中的值。

微服務所需的部分設定會儲存在初始部署所建立**Key Vault**的實例中。 Keyvault 中的對應變數應該視需要修改。