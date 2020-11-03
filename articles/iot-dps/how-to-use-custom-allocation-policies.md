---
title: 使用 Azure IoT 中樞裝置布建服務的自訂配置原則
description: '如何使用自訂配置原則搭配 Azure IoT 中樞的裝置布建服務 (DPS) '
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 48b8737fc37a183405f42b958e38c328a2ce7cb8
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92739589"
---
# <a name="how-to-use-custom-allocation-policies"></a>如何使用自訂配置原則

自訂配置原則可讓您進一步掌控將裝置指派給 IoT 中樞的方式。 此作業可藉由在 [Azure 函式](../azure-functions/functions-overview.md)中使用自訂程式碼將裝置指派給 IoT 中樞來完成。 裝置佈建服務會呼叫您的 Azure 函式程式碼，提供關於裝置和註冊的所有資訊。 您的函式程式碼會執行並傳回用來佈建裝置的 IoT 中樞資訊。

藉由使用自訂配置原則，您可在裝置佈建服務所提供的原則不符合案例的需求時，定義您自己的配置原則。

例如，或許您想要檢查裝置在佈建期間所使用的憑證，並根據憑證屬性將裝置指派給 IoT 中樞。 或者，您可能將裝置的資訊儲存在資料庫中，而需要查詢資料庫以判斷應將裝置指派給哪個 IoT 中樞。

本文將示範如何使用以 C# 撰寫的 Azure 函式設定自訂配置原則。 我們會建立兩個新的 IoT 中樞，分別代表 *Contoso 烤箱部門* 和 *Contoso 熱泵部門* 。 要求佈建的裝置必須具有包含下列其中一個尾碼的註冊識別碼，才能進行佈建：

* **-contoso-tstrsd-007** ：Contoso 烤箱部門
* **-contoso-hpsd-088** ：Contoso 熱泵部門

裝置會根據註冊識別碼的前述必要尾碼之一進行佈建。 我們將使用 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中包含的佈建範例來模擬這些裝置。

您可以執行本文中的下列步驟：

* 使用 Azure CLI 建立兩個 Contoso 部門 IoT 中樞 ( **Contoso 烤箱部門** 和 **Contoso 熱泵部門** )
* 使用 Azure 函式為自訂配置原則建立新的群組註冊
* 建立兩個裝置模擬的裝置金鑰。
* 設定適用於 Azure IoT C SDK 的開發環境
* 模擬裝置，並確認它們已根據自訂配置原則中的範例程式碼進行布建

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

下列必要條件適用於 Windows 開發環境。 針對 Linux 或 macOS，請參閱 SDK 文件中[準備您的開發環境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)中的適當章節。

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 並啟用[使用 C++ 的桌面開發](https://docs.microsoft.com/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)工作負載。 也會支援 Visual Studio 2015 和 Visual Studio 2017。

* 已安裝最新版的 [Git](https://git-scm.com/download/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>建立布建服務和兩個部門 IoT 中樞

在本節中，您會使用 Azure Cloud Shell 來建立布建服務，以及兩個代表 **Contoso 烤箱部門** 和 **contoso 熱度泵部門** 的 IoT 中樞。

> [!TIP]
> 本文中使用的命令會在「美國西部」位置建立布建服務和其他資源。 建議您在最接近您支援裝置布建服務的區域中建立資源。 執行 `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` 命令，或移至 [Azure 狀態](https://azure.microsoft.com/status/)頁面並搜尋「裝置佈建服務」，即可檢視可用的位置清單。 在命令中，可指定一個字或多字格式的位置；例如：uswest、West US、WEST US 等等。此值不區分大小寫。 如果您使用多字格式來指定位置，此值會用引號括住；例如 `-- location "West US"`。
>

1. 使用 Azure Cloud Shell 以 [az group create](/cli/azure/group#az-group-create) 命令建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

    下列範例會在 *westus* 區域中建立名為 *contoso-us 資源群組* 的資源群組。 建議您將此群組用於在此文章中建立的所有資源。 這種方法會在您完成之後更輕鬆地進行清除。

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. 使用 Azure Cloud Shell 建立具有 [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create) 命令的裝置布建服務。 布建服務會新增至 *contoso-us-資源群組* 。

    下列範例會在 *westus* 位置建立名為 contoso-布建服務 *-1098* 的布建服務。 您必須使用唯一的服務名稱。 在服務名稱中組成您自己的尾碼，以取代 **1098** 。

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    此命令可能需要數分鐘才能完成。

3. 在 Azure Cloud Shell 中使用 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 命令建立 **Contoso 烤箱部門** IoT 中樞。 此 IoT 中樞將會新增至 *contoso-us-resource-group* 。

    下列範例會在 *westus* 位置建立名為 *contoso-烤箱-hub-1098* 的 IoT 中樞。 您必須使用唯一的中樞名稱。 請在中樞名稱中設定您自己的尾碼來取代 **1098** 。 自訂配置原則的範例程式碼在中樞名稱中必須要有 `-toasters-`。

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    此命令可能需要數分鐘才能完成。

4. 在 Azure Cloud Shell 中使用 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 命令建立 **Contoso 熱泵部門** IoT 中樞。 此 IoT 中樞也會新增至 *contoso-us-resource-group* 。

    下列範例會在 *westus* 位置建立名為 *contoso-heatpumps-hub-1098* 的 IoT 中樞。 您必須使用唯一的中樞名稱。 請在中樞名稱中設定您自己的尾碼來取代 **1098** 。 自訂配置原則的範例程式碼在中樞名稱中必須要有 `-heatpumps-`。

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    此命令可能需要數分鐘才能完成。

## <a name="create-the-custom-allocation-function"></a>建立自訂配置函式

在本節中，您會建立可實作自訂配置原則的 Azure 函式。 此函式會根據裝置的註冊識別碼是否包含字串 **（contoso-tstrsd-007** 或 **-contoso-hpsd-088** ），決定裝置應該註冊的部門 IoT 中樞。 它也會根據裝置為 toaster 還是熱度片來設定裝置對應項的初始狀態。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 從您的首頁選取 [+ 建立資源]。

2. 在 [搜尋] 方塊的「搜尋 Marketplace」中輸入「函數應用程式」。 從下拉式清單中，選取 [函數應用程式]，然後選取 [建立]。

3. 在 [函數應用程式] 建立頁面的 [基本] 索引標籤底下，為您的新函數應用程式輸入下列設定，然後選取 [檢閱 + 建立]：

    **資源群組** ：選取 **contoso-us-資源群組** ，以將本文中建立的所有資源保持在一起。

    **函數應用程式名稱** ：輸入唯一的函式應用程式名稱。 此範例使用 **contoso-function** --1098。

    **Publish** ：確認 [程式碼] 已選取。

    **執行階段堆疊** ：從下拉式清單選取 **.NET Core** 。

    **區域** ：選取與您資源群組相同的區域。 此範例會使用「美國西部」。

    > [!NOTE]
    > 預設會啟用 Application Insights。 本文並不需要 Application Insights，但其可能會協助您了解並調查您使用自訂配置時遇到的任何問題。 如果您想要的話，可以選取 [監視] 索引標籤，然後針對 [啟用 Application Insights] 選取 [否] 來停用 Application Insights。

    ![建立 Azure 函數應用程式以裝載自訂配置函式](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. 在 [摘要] 頁面上，選取 [建立] 以建立函數應用程式。 部署可能需要數分鐘的時間。 完成時，選取 [移至資源]。

5. 在函式應用程式 **總覽** 頁面的左窗格中，選取 [ **+** **函數** ] 旁的 [新增函式]。

    ![將函數新增至函數應用程式](./media/how-to-use-custom-allocation-policies/create-function.png)

6. 在 [ **適用于 .net 的 Azure Functions-** 開始使用] 頁面上，針對 [ **選擇部署環境** ] 步驟，選取 **入口網站內** 的磚，然後選取 [ **繼續** ]。

    ![選取入口網站開發環境](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. 在下一個頁面上，針對 [ **建立函數** ] 步驟，選取 [ **Webhook + API** ] 磚，然後選取 [ **建立** ]。 隨即建立名為 **>HTTPtrigger1** 的函式，而入口網站會顯示 **.csx** 程式碼檔案的內容。

8. 參考需要的 Nuget 套件。 若要建立初始裝置對應項，自訂配置函式會使用兩個必須載入裝載環境之 Nuget 套件中所定義的類別。 使用 Azure Functions，會使用函式的 *主機* 檔案參考 Nuget 套件。 在此步驟中，您會儲存並上傳 *function* 檔案。

    1. 將下列幾行複製到您最愛的編輯器中，並將檔案儲存在您的電腦上作為 *function。*

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.5.0" />  
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.16.0" />  
            </ItemGroup>  
        </Project>
        ```

    2. 在 **>HTTPtrigger1** 函式上，展開視窗右邊的 [ **視圖** 檔案] 索引標籤。

        ![開啟 view files](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. 選取 [ **上傳** ]，流覽至 **function** 檔案，然後選取 [ **開啟** ] 上傳檔案。

        ![選取上傳檔案](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. 以下列程式碼取代 **>HTTPtrigger1** 函式的程式碼，然後選取 [ **儲存** ]：

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>建立註冊

在本節中，您將建立使用自訂配置原則的新註冊群組。 為了簡單起見，此文章將在註冊中使用[對稱金鑰證明](concepts-symmetric-key-attestation.md)。 如需更安全的解決方案，請考慮使用 [X.509 憑證證明](concepts-x509-attestation.md)與信任鏈結。

1. 同樣地，在 [Azure 入口網站](https://portal.azure.com)上，開啟您的佈建服務。

2. 選取左窗格中的 [管理註冊] 索引標籤，然後選取頁面頂端的 [新增註冊群組] 按鈕。

3. 在 [ **新增註冊群組** ] 中，輸入下列資訊，然後選取 [ **儲存** ] 按鈕。

    **群組名稱** ：輸入 **contoso-custom-allocated-devices** 。

    **證明類型** ：選取 [對稱金鑰]。

    **自動產生金鑰** ：此核取方塊應已勾選。

    **選取要如何將裝置指派給中樞** ：選取 [自訂 (使用 Azure 函式)]。

    ![為對稱金鑰證明新增自訂配置註冊群組](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. 在 [新增 **註冊群組** ] 上，選取 [ **連結新的 iot 中樞** ] 以連結新的「部門 IoT 中樞」。

    針對這兩個部門 IoT 中樞執行此步驟。

    **訂用帳戶** ：如果您有多個訂用帳戶，請選擇您用來建立部門 IoT 中樞的訂用帳戶。

    **IoT 中樞** ：選取您建立的其中一個部門中樞。

    **存取原則** ：選擇 [iothubowner]。

    ![連結部門 IoT 中樞與佈建服務](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. 在 [新增註冊群組] 上，在兩個部門 IoT 中樞都已連結後，您必須以 IoT 中樞群組的形式將其選取作為註冊群組，如下所示：

    ![建立註冊的部門中樞群組](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. 在 [ **新增註冊群組** ] 中，向下滾動至 [ **選取 Azure** 函式] 區段，選取您在上一節中建立的函數應用程式。 然後選取您建立的函式，然後選取 [儲存] 以儲存註冊群組。

    ![選取函數並儲存註冊群組](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. 儲存註冊之後，請重新開啟它，並記下 [主要金鑰]。 您必須先儲存註冊，才能產生金鑰。 此金鑰將在後續用來產生模擬裝置的唯一裝置金鑰。

## <a name="derive-unique-device-keys"></a>衍生唯一的裝置金鑰

在本節中，您要建立兩個裝置金鑰。 一個金鑰將用於模擬的烤箱裝置。 另一個金鑰將用於模擬的熱泵裝置。

若要產生裝置金鑰，請使用您稍早記下的 **主要金鑰** 來計算每個裝置之裝置註冊識別碼的 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) ，然後將結果轉換為 Base64 格式。 如需關於為註冊群組建立衍生裝置金鑰的詳細資訊，請參閱[對稱金鑰證明](concepts-symmetric-key-attestation.md)的群組註冊小節。

針對本文的範例，請使用下列兩個裝置註冊識別碼，並計算這兩個裝置的裝置金鑰。 這兩種註冊識別碼都具有有效的尾碼，可用於自訂配置原則的範例程式碼：

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Linux 工作站

如果您使用的是 Linux 工作站，您可以使用 openssl 來產生衍生的裝置金鑰，如下列範例所示。

1. 將 **KEY** 的值取代為您先前記下的 [主要金鑰]。

    ```bash
    KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

    REG_ID1=breakroom499-contoso-tstrsd-007
    REG_ID2=mainbuilding167-contoso-hpsd-088

    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
    devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

    echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
    ```

    ```bash
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

### <a name="windows-based-workstations"></a>以 Windows 為基礎的工作站

如果您使用以 Windows 為基礎的工作站，您可以使用 PowerShell 來產生衍生的裝置金鑰，如下列範例所示。

1. 將 **KEY** 的值取代為您先前記下的 [主要金鑰]。

    ```powershell
    $KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

    $REG_ID1='breakroom499-contoso-tstrsd-007'
    $REG_ID2='mainbuilding167-contoso-hpsd-088'

    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.key = [Convert]::FromBase64String($key)
    $sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
    $sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
    $derivedkey1 = [Convert]::ToBase64String($sig1)
    $derivedkey2 = [Convert]::ToBase64String($sig2)

    echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
    ```

    ```powershell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

模擬裝置會使用每個註冊識別碼的衍生裝置金鑰來執行對稱金鑰證明。

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>準備 Azure IoT C SDK 開發環境

在本節中，您要準備用來建置 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的開發環境。 SDK 包含模擬裝置的範例程式碼。 這個模擬裝置將會嘗試在裝置開機順序期間進行佈建。

此節以 Windows 工作站為基礎來說明。 如需 Linux 範例，請參閱[如何針對多組織用戶佈建](how-to-provision-multitenant.md)中的 VM 設定。

1. 下載 [CMake 建置系統](https://cmake.org/download/)。

    在開始安裝 `CMake`**之前** ，請務必將 Visual Studio 先決條件 (Visual Studio 和「使用 C++ 進行桌面開發」工作負載) 安裝在您的機器上。 在符合先決條件，並且驗證過下載項目之後，請安裝 CMake 建置系統。

2. 尋找[最新版本](https://github.com/Azure/azure-iot-sdk-c/releases/latest) SDK 的標籤名稱。

3. 開啟命令提示字元或 Git Bash 殼層。 執行下列命令以複製最新版的 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存放庫。 使用您在上一個步驟中找到的標籤作為 `-b` 參數的值：

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    預期此作業需要幾分鐘的時間才能完成。

4. 在 git 存放庫的根目錄中建立 `cmake` 子目錄，並瀏覽至該資料夾。 從 `azure-iot-sdk-c` 目錄執行下列命令：

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. 請執行下列命令，以建置您開發用戶端平台特有的 SDK 版本。 `cmake` 目錄中會產生模擬裝置的 Visual Studio 解決方案。 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    如果 `cmake` 找不到 C++ 編譯器，您在執行命令時，可能會收到建置錯誤。 如果發生這種情況，請嘗試在 [Visual Studio 命令提示字元](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)中執行命令。

    建置成功後，最後幾行輸出會類似於下列輸出：

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="simulate-the-devices"></a>模擬裝置

在本節中，您將在先前設定的 Azure IoT C SDK 中更新名為 **prov\_dev\_client\_sample** 的佈建範例。

此範例程式碼會模擬將佈建要求傳送至裝置佈建服務執行個體的裝置開機順序。 此開機順序會使烤箱裝置接受辨識，並使用自訂配置原則指派給 IoT 中樞。

1. 在 Azure 入口網站中，選取您裝置佈建服務的 [概觀] 索引標籤，並記下 [識別碼範圍] **** 值。

    ![從入口網站刀鋒視窗擷取裝置佈建服務端點資訊](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. 在 Visual Studio 中，開啟先前藉由執行 CMake 而產生的 **azure_iot_sdks.sln** 解決方案檔案。 該方案檔案應位於下列位置：

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. 在 Visual Studio 的 [方案總管]  視窗中，瀏覽至 **Provision\_Samples** 資料夾。 展開名為 **prov\_dev\_client\_sample** 的範例專案。 展開 [來源檔案]  ，然後開啟 **prov\_dev\_client\_sample.c** 。

4. 找出 `id_scope` 常數，並將其值取代為您先前複製的 [識別碼範圍]  值。 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. 在相同的檔案中找出 `main()` 函式的定義。 確定 `hsm_type` 變數已設定為 `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`，如下所示：

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. 以滑鼠右鍵按一下 **prov\_dev\_client\_sample** 專案，然後選取 [設定為起始專案]  。

### <a name="simulate-the-contoso-toaster-device"></a>模擬 Contoso 烤箱裝置

1. 若要模擬 toaster 裝置，請在 **prov\_dev\_client\_sample.c** 中尋找已標成註解之 `prov_dev_set_symmetric_key_info()` 的呼叫。

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    取消註解函式呼叫，並將預留位置值 (包括角括號) 取代為您先前產生的 toaster 註冊識別碼和衍生裝置金鑰。 下列金鑰值 **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** 只是範例。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    儲存檔案。

2. 在 Visual Studio 功能表中，選取 [偵錯]   > [啟動但不偵錯]  以執行解決方案。 出現重新建置專案的提示時，選取 [是]  ，以在執行前重新建置專案。

    下列輸出是模擬 toaster 裝置的範例，已成功開機並聯機至布建服務實例，以透過自訂配置原則指派給烤箱 IoT 中樞：

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>模擬 Contoso 熱泵裝置

1. 若要模擬熱泵裝置，請使用您稍早產生的熱泵註冊識別碼和衍生裝置金鑰再次更新 **prov\_dev\_client\_sample.c** a 中的 `prov_dev_set_symmetric_key_info()`。 下列金鑰值 **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** 只是範例。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    儲存檔案。

2. 在 Visual Studio 功能表中，選取 [偵錯]   > [啟動但不偵錯]  以執行解決方案。 出現重新建置專案的提示時，選取 [是]，以在執行前重新建置專案。

    下列輸出是模擬的熱泵裝置的範例，已成功啟動並聯機至布建服務實例，以透過自訂配置原則指派給 Contoso 熱度泵 IoT 中樞：

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>對自訂配置原則進行疑難排解

下表顯示您可能會收到的預期案例和結果錯誤碼。 您可以利用此表格對 Azure Functions 的自訂配置原則失敗進行疑難排解。

| 案例 | 佈建服務的註冊結果 | 佈建 SDK 結果 |
| -------- | --------------------------------------------- | ------------------------ |
| Webhook 傳回「200 確定」，且 'iotHubHostName' 設定為有效的 IoT 中樞主機名稱 | 結果狀態：已指派  | SDK 傳回 PROV_DEVICE_RESULT_OK 與中樞資訊 |
| Webhook 傳回「200 確定」，且回應中包含 'iotHubHostName'，但設定為空字串或 Null | 結果狀態：失敗<br><br> 錯誤碼：CustomAllocationIotHubNotSpecified (400208) | SDK 傳回 PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Webhook 傳回「401 未授權」 | 結果狀態：失敗<br><br>錯誤碼：CustomAllocationUnauthorizedAccess (400209) | SDK 傳回 PROV_DEVICE_RESULT_UNAUTHORIZED |
| 建立了個別註冊以停用裝置 | 結果狀態：已停用 | SDK 傳回 PROV_DEVICE_RESULT_DISABLED |
| Webhook 傳回錯誤碼 >= 429 | DPS 的協調流程將會重試特定次數。 目前的重試原則為：<br><br>&nbsp;&nbsp;- 重試計數：10<br>&nbsp;&nbsp;- 初始間隔：1s<br>&nbsp;&nbsp;- 增量：9s | SDK 將會忽略錯誤，並在指定的時間內提交另一個取得狀態訊息 |
| Webhook 傳回任何其他狀態碼 | 結果狀態：失敗<br><br>錯誤碼：CustomAllocationFailed (400207) | SDK 傳回 PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續使用在此文章中建立的資源，可以將其保留。 如果不打算繼續使用這些資源，請使用下列步驟刪除此文章中建立的所有資源，以避免產生非必要費用。

以下步驟假設您依照指示在名為 **contoso-us-resource-group** 的相同資源群組中建立了此文章中的所有資源。

> [!IMPORTANT]
> 刪除資源群組是無法回復的動作。 資源群組和其中包含的所有資源都將永久刪除。 請確定您不會誤刪錯誤的資源群組或資源。 如果您在現有的資源群組內建立了 IoT 中樞，而該群組中包含您想要保留的資源，則您只需刪除 IoT 中樞本身即可，而不要刪除資源群組。
>

依名稱刪除資源群組：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取 [資源群組]  。

2. 在 [依名稱篩選] 文字方塊中，輸入您的資源所屬的資源群組名稱 **contoso-us-resource-group** 。 

3. 在結果清單中的資源群組右側，選取 [...]，然後按一下 [刪除資源群組]。

4. 系統將會要求您確認是否刪除資源。 再次輸入您的資源群組名稱以進行確認，然後選取 [刪除]。 片刻過後，系統便會刪除該資源群組及其所有內含的資源。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解重新布建，請參閱 [IoT 中樞裝置重新布建概念](concepts-device-reprovision.md) 
* 若要深入瞭解解除布建，請參閱如何取消布建 [先前可的裝置](how-to-unprovision-devices.md) 
