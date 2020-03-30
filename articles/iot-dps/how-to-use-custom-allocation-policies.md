---
title: 使用 Azure IoT 中心設備佈建服務的自訂分配策略
description: 如何將自訂分配策略與 Azure IoT 中心設備佈建服務 （DPS） 一起使用
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 87ffca1957d4ec449753f1966ed05cf3948f5ca2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453948"
---
# <a name="how-to-use-custom-allocation-policies"></a>如何使用自訂配置原則

自訂配置原則可讓您進一步掌控將裝置指派給 IoT 中樞的方式。 此作業可藉由在 [Azure 函式](../azure-functions/functions-overview.md)中使用自訂程式碼將裝置指派給 IoT 中樞來完成。 裝置佈建服務會呼叫您的 Azure 函式程式碼，提供關於裝置和註冊的所有資訊。 您的函式程式碼會執行並傳回用來佈建裝置的 IoT 中樞資訊。

通過使用自訂分配策略，當設備預配服務提供的策略不符合方案要求時，可以定義自己的分配策略。

例如，您可能希望檢查設備在預配期間使用的證書，並根據證書屬性將設備分配給 IoT 中心。 或者，您可能將資訊存儲在設備的資料庫中，並且需要查詢資料庫以確定設備應分配給哪個 IoT 中心。

本文將示範如何使用以 C# 撰寫的 Azure 函式設定自訂配置原則。 我們會建立兩個新的 IoT 中樞，分別代表 *Contoso 烤箱部門*和 *Contoso 熱泵部門*。 要求佈建的裝置必須具有包含下列其中一個尾碼的註冊識別碼，才能進行佈建：

* **-contoso-tstrsd-007**：Contoso 烤箱部門
* **-contoso-hpsd-088**：Contoso 熱泵部門

裝置會根據註冊識別碼的前述必要尾碼之一進行佈建。 我們將使用 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中包含的佈建範例來模擬這些裝置。

在本文中執行以下步驟：

* 使用 Azure CLI 建立兩個 Contoso 部門 IoT 中樞 (**Contoso 烤箱部門**和 **Contoso 熱泵部門**)
* 使用 Azure 函式為自訂配置原則建立新的群組註冊
* 建立兩個裝置模擬的裝置金鑰。
* 設定適用於 Azure IoT C SDK 的開發環境
* 類比設備並驗證它們是否根據自訂分配策略中的示例代碼進行預配

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

下列必要條件適用於 Windows 開發環境。 針對 Linux 或 macOS，請參閱 SDK 文件中[準備您的開發環境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)中的適當章節。

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 並啟用[使用 C++ 的桌面開發](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads)工作負載。 也會支援 Visual Studio 2015 和 Visual Studio 2017。

* 已安裝最新版的 [Git](https://git-scm.com/download/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>創建預配服務和兩個部門 IoT 中心

在本節中，您可以使用 Azure 雲外殼創建預配服務和兩個 IoT 中心，表示**Contoso Toasters 部門和** **Contoso 熱泵部門**。

> [!TIP]
> 本文中使用的命令在美國西部位置創建預配服務和其他資源。 我們建議您在支援設備預配服務的區域中創建資源。 執行 `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` 命令，或移至 [Azure 狀態](https://azure.microsoft.com/status/)頁面並搜尋「裝置佈建服務」，即可檢視可用的位置清單。 在命令中，位置可以指定為一個單詞或多字格式;例如：西部、美國西部、美國西部等。該值不區分大小寫。 如果您使用多字格式來指定位置，此值會用引號括住；例如 `-- location "West US"`。
>

1. 使用 Azure Cloud Shell 以 [az group create](/cli/azure/group#az-group-create) 命令建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

    下面的示例在*西部*區域創建名為*contoso-us-resource-group*的資源組。 建議您將此群組用於在此文章中建立的所有資源。 此方法將使清理完成後更容易。

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. 使用 Azure 雲外殼使用[az iot dps 創建](/cli/azure/iot/dps#az-iot-dps-create)命令創建設備預配服務。 預配服務將添加到*contoso-us-us-Resource 組*。

    下面的示例在*西部*位置創建名為*contoso 預配服務-1098*的預配服務。 您必須使用唯一的服務名稱。 在服務名稱中用**1098**組成您自己的尾碼。

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    此命令可能需要數分鐘才能完成。

3. 在 Azure Cloud Shell 中使用 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 命令建立 **Contoso 烤箱部門** IoT 中樞。 此 IoT 中樞將會新增至 *contoso-us-resource-group*。

    下面的示例*在西部*位置創建名為*contoso-toasters-hub-1098*的 IoT 中心。 必須使用唯一的中心名稱。 請在中樞名稱中設定您自己的尾碼來取代 **1098**。 自訂配置原則的範例程式碼在中樞名稱中必須要有 `-toasters-`。

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    此命令可能需要數分鐘才能完成。

4. 在 Azure Cloud Shell 中使用 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 命令建立 **Contoso 熱泵部門** IoT 中樞。 此 IoT 中樞也會新增至 *contoso-us-resource-group*。

    下面的示例*在西部*位置創建名為*contoso-熱泵-集線器-1098*的 IoT 中心。 必須使用唯一的中心名稱。 請在中樞名稱中設定您自己的尾碼來取代 **1098**。 自訂配置原則的範例程式碼在中樞名稱中必須要有 `-heatpumps-`。

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    此命令可能需要數分鐘才能完成。

## <a name="create-the-custom-allocation-function"></a>創建自訂分配函數

在本節中，您將創建實現自訂分配策略的 Azure 函數。 此函數根據設備註冊 ID 是否包含字串 **-contoso-tstrsd-007**或 **-contoso-hpsd-088，** 決定設備應註冊哪個部門 IoT 中心。 它還根據設備是烤麵包機還是熱泵設置設備孿生設備的初始狀態。

1. 登錄到 Azure[門戶](https://portal.azure.com)。 從主頁中選擇 **+創建資源**。

2. 在 *"搜索市場"* 搜索框中，鍵入"功能應用"。 從下拉清單中選擇 **"功能應用**"，然後選擇 **"創建**"。

3. 在**功能應用**創建頁上，在"**基本"** 選項卡下，輸入新功能應用的以下設置，然後選擇 **"審閱 + 創建**：

    **資源組**：選擇**contoso-us 資源組**，以保持本文中創建的所有資源在一起。

    **函數應用名稱**：輸入唯一的函數應用名稱。 此示例使用**contoso 函數-app-1098**。

    **發佈**：驗證**是否選擇了代碼**。

    **運行時堆疊**：從下拉清單中選擇 **.NET 核心**。

    **區域**：選擇與資源組相同的區域。 此示例使用**美國西部**。

    > [!NOTE]
    > 預設情況下，應用程式見解已啟用。 本文不需要應用程式見解，但它可以説明您瞭解和調查自訂分配時遇到的任何問題。 如果您願意，您可以通過選擇 **"監視"** 選項卡，然後選擇 **"否****啟用應用程式見解**"來禁用應用程式見解。

    ![創建 Azure 函數應用以承載自訂分配功能](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. 在 **"摘要"** 頁上，選擇 **"創建**"以創建函數應用。 部署可能需要數分鐘的時間。 完成後，選擇 **"轉到資源**"。

5. 在函數應用 **"概述"** 頁的左側窗格中，選擇**+****"功能**"旁邊的"以添加新功能"。

    ![向函數應用添加函數](./media/how-to-use-custom-allocation-policies/create-function.png)

6. 在 **.NET - 入門頁上的 Azure 函數**中，對於 **"選擇部署環境**"步驟，選擇**門戶內**磁貼，然後選擇"**繼續**"。

    ![選擇門戶開發環境](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. 在下一頁上，對於 **"創建功能"** 步驟，選擇**Webhook + API**磁貼，然後選擇 **"創建**"。 將創建名為**HttpTrigger1**的函數，門戶顯示**run.csx**代碼檔的內容。

8. 參考所需的 Nuget 包。 要創建初始設備孿生，自訂分配函數使用必須在兩個 Nuget 包中定義的類，這些類必須載入到託管環境中。 使用 Azure 函數，使用*函數.host*檔引用 Nuget 包。 在此步驟中，您將保存並上載*函數.host*檔。

    1. 將以下行複製到您最喜愛的編輯器中，並將該檔另存到電腦上的*函數。*

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

    2. 在**HttpTrigger1**函數上，展開視窗右側的 **"查看檔**"選項卡。

        ![打開視圖檔](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. 選擇 **"上載**"，流覽到**函數.proj**檔，然後選擇 **"打開"** 以上載檔。

        ![選擇上傳檔](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. 將**HttpTrigger1**函數的代碼替換為以下代碼，然後選擇 **：：**

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

在本節中，您將創建一個使用自訂分配策略的新註冊組。 為了簡單起見，此文章將在註冊中使用[對稱金鑰證明](concepts-symmetric-key-attestation.md)。 如需更安全的解決方案，請考慮使用 [X.509 憑證證明](concepts-security.md#x509-certificates)與信任鏈結。

1. 仍在[Azure 門戶](https://portal.azure.com)上，打開預配服務。

2. 選擇 **"在**左側窗格中管理註冊"，然後選擇頁面頂部的 **"添加註冊組"** 按鈕。

3. 在 **"添加註冊組**"上，輸入以下資訊，然後選擇"**保存**"按鈕。

    **群組名稱**：輸入 **contoso-custom-allocated-devices**。

    **證明類型**：選取 [對稱金鑰]****。

    **自動產生金鑰**：此核取方塊應已勾選。

    **選取要如何將裝置指派給中樞**：選取 [自訂 (使用 Azure 函式)]****。

    ![為對稱金鑰證明新增自訂配置註冊群組](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. 在 **"添加註冊組**"上，選擇 **"連結新的 IoT 中心**"以連結兩個新的部門 IoT 中心。

    為兩個部門 IoT 中心執行此步驟。

    **訂用帳戶**：如果您有多個訂用帳戶，請選擇您用來建立部門 IoT 中樞的訂用帳戶。

    **IoT 中樞**：選取您建立的其中一個部門中樞。

    **存取原則**：選擇 [iothubowner]****。

    ![連結部門 IoT 中樞與佈建服務](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. 在 [新增註冊群組]**** 上，在兩個部門 IoT 中樞都已連結後，您必須以 IoT 中樞群組的形式將其選取作為註冊群組，如下所示：

    ![建立註冊的部門中樞群組](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. 在 **"添加註冊組**"上，向下滾動到 **"選擇 Azure 函數"** 部分，選擇您在上一節中創建的"功能"應用。 然後選擇您創建的函數，然後選擇"保存"以保存註冊組。

    ![選擇函數並保存註冊組](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. 儲存註冊之後，請重新開啟它，並記下 [主要金鑰]****。 您必須先儲存註冊，才能產生金鑰。 此金鑰將在後續用來產生模擬裝置的唯一裝置金鑰。

## <a name="derive-unique-device-keys"></a>衍生唯一的裝置金鑰

在本節中，您將創建兩個唯一的設備金鑰。 一個金鑰將用於模擬的烤箱裝置。 另一個金鑰將用於模擬的熱泵裝置。

要生成設備金鑰，請使用前面記錄**的主金鑰**計算每個設備的設備註冊 ID 的[HMAC-SHA256，](https://wikipedia.org/wiki/HMAC)並將結果轉換為 Base64 格式。 如需關於為註冊群組建立衍生裝置金鑰的詳細資訊，請參閱[對稱金鑰證明](concepts-symmetric-key-attestation.md)的群組註冊小節。

針對本文的範例，請使用下列兩個裝置註冊識別碼，並計算這兩個裝置的裝置金鑰。 這兩種註冊識別碼都具有有效的尾碼，可用於自訂配置原則的範例程式碼：

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Linux 工作站

如果您使用的是 Linux 工作站，則可以使用 openssl 生成派生的設備金鑰，如以下示例所示。

1. 將 **KEY** 的值取代為您先前記下的 [主要金鑰]****。

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

如果您使用的是基於 Windows 的工作站，則可以使用 PowerShell 生成派生的設備金鑰，如以下示例所示。

1. 將 **KEY** 的值取代為您先前記下的 [主要金鑰]****。

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

在本節中，準備用於構建[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)的開發環境。 SDK 包含模擬裝置的範例程式碼。 這個模擬裝置將會嘗試在裝置開機順序期間進行佈建。

此節以 Windows 工作站為基礎來說明。 如需 Linux 範例，請參閱[如何針對多組織用戶佈建](how-to-provision-multitenant.md)中的 VM 設定。

1. 下載 [CMake 建置系統](https://cmake.org/download/)。

    在開始`CMake`安裝**之前**，在電腦上安裝 Visual Studio 的先決條件（Visual Studio 和"具有C++"工作負載的桌面開發）非常重要。 在符合先決條件，並且驗證過下載項目之後，請安裝 CMake 建置系統。

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

    如果未`cmake`找到C++編譯器，則在運行命令時可能會收到建置錯誤。 如果發生這種情況，請嘗試在[Visual Studio 命令提示符](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)中運行該命令。

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

在本節中，更新位於以前設置的 Azure IoT C SDK 中名為**prov\_dev\_開發人員用戶端\_示例**的預配示例。

此範例程式碼會模擬將佈建要求傳送至裝置佈建服務執行個體的裝置開機順序。 此開機順序會使烤箱裝置接受辨識，並使用自訂配置原則指派給 IoT 中樞。

1. 在 Azure 入口網站中，選取您裝置佈建服務的 [概觀]**** 索引標籤，並記下 [識別碼範圍]**__** 值。

    ![從入口網站刀鋒視窗擷取裝置佈建服務端點資訊](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. 在 Visual Studio 中，開啟先前藉由執行 CMake 而產生的 **azure_iot_sdks.sln** 解決方案檔案。 該方案檔案應位於下列位置：

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. 在 Visual Studio 的 [方案總管]** 視窗中，瀏覽至 **Provision\_Samples** 資料夾。 展開名為 **prov\_dev\_client\_sample** 的範例專案。 展開 [來源檔案]****，然後開啟 **prov\_dev\_client\_sample.c**。

4. 找出 `id_scope` 常數，並將其值取代為您先前複製的 [識別碼範圍]**** 值。 

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

6. 以滑鼠右鍵按一下 **prov\_dev\_client\_sample** 專案，然後選取 [設定為起始專案]****。

### <a name="simulate-the-contoso-toaster-device"></a>模擬 Contoso 烤箱裝置

1. 若要模擬 toaster 裝置，請在 **prov\_dev\_client\_sample.c** 中尋找已標成註解之 `prov_dev_set_symmetric_key_info()` 的呼叫。

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    取消注釋函式呼叫，並將預留位置值（包括角度括弧）替換為以前生成的烤麵包機註冊 ID 和派生設備金鑰。 下列金鑰值 **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** 只是範例。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    儲存檔案。

2. 在 Visual Studio 功能表上，選擇 > **"調試開始而不調試**"以運行解決方案。 **Debug** 在重建專案的提示中，選擇 **"是**"以在運行之前重建專案。

    以下輸出是類比烤麵包機設備成功啟動並連接到預配服務實例的示例，該實例通過自訂分配策略分配給 Toasters IoT 中心：

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

2. 在 Visual Studio 功能表上，選擇 > **"調試開始而不調試**"以運行解決方案。 **Debug** 出現重新建置專案的提示時，選取 [是]****，以在執行前重新建置專案。

    以下輸出是類比熱泵設備成功啟動並連接到預配服務實例的示例，該實例通過自訂分配策略分配給 Contoso 熱泵 IoT 中心：

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

下表顯示了預期方案和可能收到的結果錯誤代碼。 您可以利用此表格對 Azure Functions 的自訂配置原則失敗進行疑難排解。

| 狀況 | 佈建服務的註冊結果 | 佈建 SDK 結果 |
| -------- | --------------------------------------------- | ------------------------ |
| Webhook 傳回「200 確定」，且 'iotHubHostName' 設定為有效的 IoT 中樞主機名稱 | 結果狀態：已指派  | SDK 傳回 PROV_DEVICE_RESULT_OK 與中樞資訊 |
| Webhook 傳回「200 確定」，且回應中包含 'iotHubHostName'，但設定為空字串或 Null | 結果狀態：失敗<br><br> 錯誤碼：CustomAllocationIotHubNotSpecified (400208) | SDK 傳回 PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Webhook 傳回「401 未授權」 | 結果狀態：失敗<br><br>錯誤碼：CustomAllocationUnauthorizedAccess (400209) | SDK 傳回 PROV_DEVICE_RESULT_UNAUTHORIZED |
| 建立了個別註冊以停用裝置 | 結果狀態：已停用 | SDK 傳回 PROV_DEVICE_RESULT_DISABLED |
| Webhook 傳回錯誤碼 >= 429 | DPS 的協調流程將會重試特定次數。 目前的重試原則為：<br><br>&nbsp;&nbsp;- 重試計數：10<br>&nbsp;&nbsp;- 初始間隔：1s<br>&nbsp;&nbsp;- 增量：9s | SDK 將會忽略錯誤，並在指定的時間內提交另一個取得狀態訊息 |
| Webhook 傳回任何其他狀態碼 | 結果狀態：失敗<br><br>錯誤碼：CustomAllocationFailed (400207) | SDK 傳回 PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>清除資源

如果您計畫繼續使用本文中創建的資源，則可以離開這些資源。 如果您不打算繼續使用資源，請使用以下步驟刪除本文中創建的所有資源，以避免不必要的費用。

以下步驟假設您依照指示在名為 **contoso-us-resource-group** 的相同資源群組中建立了此文章中的所有資源。

> [!IMPORTANT]
> 刪除資源群組是無法回復的動作。 資源群組和其中包含的所有資源都將永久刪除。 請確定您不會誤刪錯誤的資源群組或資源。 如果您在現有的資源群組內建立了 IoT 中樞，而該群組中包含您想要保留的資源，則您只需刪除 IoT 中樞本身即可，而不要刪除資源群組。
>

依名稱刪除資源群組：

1. 登錄到 Azure[門戶](https://portal.azure.com)並選擇**資源組**。

2. 在 [依名稱篩選]**** 文字方塊中，輸入您的資源所屬的資源群組名稱 **contoso-us-resource-group**。 

3. 在結果清單中的資源群組右側，選取 [...]****，然後按一下 [刪除資源群組]****。

4. 系統將要求您確認刪除資源組。 再次輸入您的資源群組名稱以進行確認，然後選取 [刪除]****。 片刻過後，系統便會刪除該資源群組及其所有內含的資源。

## <a name="next-steps"></a>後續步驟

* 要瞭解更多重新調配，請參閱[IoT 中心設備重新調配概念](concepts-device-reprovision.md) 
* 要瞭解更多取消預配，請參閱[如何取消預配以前已自動預配的設備](how-to-unprovision-devices.md) 
