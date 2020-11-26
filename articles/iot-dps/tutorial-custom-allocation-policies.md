---
title: 使用自訂配置原則搭配 Azure IoT 中樞裝置佈建服務 (DPS) 的教學課程
description: 使用自訂配置原則搭配 Azure IoT 中樞裝置佈建服務 (DPS) 的教學課程
author: wesmc7777
ms.author: wesmc
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 4cab1765a387bbae61c9c242a8e7a1ca881ea1f5
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966627"
---
# <a name="tutorial-use-custom-allocation-policies-with-device-provisioning-service-dps"></a>教學課程：使用自訂配置原則搭配裝置佈建服務 (DPS)

自訂配置原則可讓您進一步掌控將裝置指派給 IoT 中樞的方式。 此作業可藉由在佈建期間執行的 [Azure 函式](../azure-functions/functions-overview.md)中使用自訂程式碼，將裝置指派給 IoT 中樞來完成。 裝置佈建服務會呼叫您的 Azure 函式程式碼，提供關於裝置和註冊的所有資訊。 您的函式程式碼會執行並傳回用來佈建裝置的 IoT 中樞資訊。

藉由使用自訂配置原則，您可在裝置佈建服務所提供的原則不符合案例的需求時，定義您自己的配置原則。

例如，或許您想要檢查裝置在佈建期間所使用的憑證，並根據憑證屬性將裝置指派給 IoT 中樞。 或者，您可能將裝置的資訊儲存在資料庫中，而需要查詢資料庫以判斷應將裝置指派給哪個 IoT 中樞。

本文示範使用自訂配置原則的註冊群組，其使用以 C# 撰寫的 Azure 函式來佈建使用對稱金鑰的 toaster 裝置。 未辨識為 toaster 裝置的任何裝置，都不會佈建到 IoT 中樞。

裝置將使用包含在 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中的佈建範例程式碼來要求佈建。


在此教學課程中，您將執行下列動作：

> [!div class="checklist"]
> * 建立新的 Azure 函數應用程式以支援自訂配置函式
> * 使用 Azure 函式為自訂配置原則建立新的群組註冊
> * 建立兩個裝置的裝置金鑰
> * 從 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 設定範例裝置程式碼的開發環境
> * 執行裝置並確認其根據自訂配置原則進行佈建的情形


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

* 本文假設您已完成[使用 Azure 入口網站設定 IoT 中樞裝置佈建服務](./quick-setup-auto-provision.md)中的步驟，以建立您的 IoT 中樞和 DPS 執行個體。

* 已安裝最新版的 [Git](https://git-scm.com/download/)。

* 在 Windows 開發環境中，需要 [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019，並啟用 [「使用 C++ 的桌面開發」](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)工作負載。 也會支援 Visual Studio 2015 和 Visual Studio 2017。

* 針對 Linux 或 macOS，請參閱 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 文件中[準備您的開發環境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)中的適當章節。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-custom-allocation-function"></a>建立自訂配置函式

在本節中，您會建立可實作自訂配置原則的 Azure 函式。 此函式會根據裝置的註冊識別碼是否包含字串前置詞 **contoso-toaster**，決定是否應將裝置註冊到您的 IoT 中樞。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 從您的首頁選取 [+ 建立資源]。

2. 在 [搜尋] 方塊的「搜尋 Marketplace」中輸入「函數應用程式」。 從下拉式清單中，選取 [函數應用程式]，然後選取 [建立]。

3. 在 [函數應用程式] 建立頁面的 [基本] 索引標籤底下，為您的新函數應用程式輸入下列設定，然後選取 [檢閱 + 建立]：

    訂用帳戶：如果您有多個訂用帳戶，但未選取所需的訂用帳戶，請選取您要使用的訂用帳戶。

    **資源群組**：此欄位可讓您建立新的資源群組，或選擇現有群組以包含函數應用程式。 選擇包含您先前所建立要進行測試 IoT 中樞的相同資源群組，例如 **TestResources**。 您可以將所有相關資源一起放在群組中，一併加以管理。

    **函數應用程式名稱**：輸入唯一的函式應用程式名稱。 此範例使用 **contoso-function-app**。

    **Publish**：確認 [程式碼] 已選取。

    **執行階段堆疊**：從下拉式清單選取 **.NET Core**。

    **區域**：選取與您資源群組相同的區域。 此範例會使用「美國西部」。

    > [!NOTE]
    > 預設會啟用 Application Insights。 本文並不需要 Application Insights，但其可能會協助您了解並調查您使用自訂配置時遇到的任何問題。 如果您想要的話，可以選取 [監視] 索引標籤，然後針對 [啟用 Application Insights] 選取 [否] 來停用 Application Insights。

    ![建立 Azure 函數應用程式以裝載自訂配置函式](./media/tutorial-custom-allocation-policies/create-function-app.png)

4. 在 [摘要] 頁面上，選取 [建立] 以建立函數應用程式。 部署可能需要數分鐘的時間。 完成時，選取 [移至資源]。

5. 在左窗格的 [函式] 按一下 [函式]，然後按一下 [+ 新增] 以新增新的函式。

6. 在 [範本] 頁面上，選取 [HTTP 觸發程序] 磚，然後選取 [建立函式]。 系統會建立名為 **HttpTrigger1** 的函式，入口網站會顯示您函式的概觀頁面。

7. 針對新的函式，按一下 [程式碼 + 測試]。 入口網站會顯示 **run.csx** 程式碼檔案的內容。 

8. 將 **HttpTrigger1** 函式的程式碼取代為下列程式碼，然後選取 [儲存]。 您的自訂配置程式碼已準備好可供使用。

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

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
                // This is a Contoso Toaster 
                if (regId.Contains("contoso-toaster"))
                {
                    //Log IoT hubs configured for the enrollment
                    foreach(string hubString in hubs)
                    {
                        log.LogInformation("linkedHub : " + hubString);
                    }

                    obj.iotHubHostName = hubs[0];
                    log.LogInformation("Selected hub : " + obj.iotHubHostName);
                }
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
    }
    ```

9. 在 **run.csx** 程式碼檔案底部的下方，按一下 [記錄]，以從自訂配置函式監視記錄。 


## <a name="create-the-enrollment"></a>建立註冊

在本節中，您將建立使用自訂配置原則的新註冊群組。 為了簡單起見，此文章將在註冊中使用[對稱金鑰證明](concepts-symmetric-key-attestation.md)。 如需更安全的解決方案，請考慮使用 [X.509 憑證證明](concepts-x509-attestation.md)與信任鏈結。

1. 同樣地，在 [Azure 入口網站](https://portal.azure.com)上，開啟您的佈建服務。

2. 選取左窗格中的 [管理註冊] 索引標籤，然後選取頁面頂端的 [新增註冊群組] 按鈕。

3. 在 [新增註冊群組] 上輸入下表中的資訊，然後按一下 [儲存] 按鈕。

    | 欄位 | 描述和/或建議的值 |
    | :---- | :----------------------------- |
    | **群組名稱** | 輸入 **contoso-custom-allocated-devices** |
    | **證明類型** | 選取 [對稱金鑰] |
    | **自動產生金鑰** | 此核取方塊應已勾選。 |
    | **選取將裝置指派給中樞的方式** | 選取 [自訂 (使用 Azure 函式)] |
    | **選取可作為此群組指派對象的 IoT 中樞** | 選取您先前在完成快速入門時所建立的 IoT 中樞。 |
    | **選取 Azure 函式** | 選取包含您所建立函數應用程式的訂用帳戶。 然後針對函式選取 **contoso-function-app** 和 **HttpTrigger1**。 |

    ![為對稱金鑰證明新增自訂配置註冊群組](./media/tutorial-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. 儲存註冊之後，請重新開啟它，並記下 [主要金鑰]。 您必須先儲存註冊，才能產生金鑰。 此主要對稱金鑰將用來為稍後嘗試佈建的裝置產生唯一的裝置金鑰。 

## <a name="derive-unique-device-keys"></a>衍生唯一的裝置金鑰

裝置不會直接使用主要對稱金鑰。 相反地，您會使用主要金鑰來為每個裝置衍生裝置金鑰。 在本節中，您要建立兩個裝置金鑰。 一個金鑰將用於模擬的烤箱裝置。 另一個金鑰將用於模擬的熱泵裝置。 產生的金鑰可讓這兩個裝置嘗試註冊。 只有一個裝置註冊識別碼具有自訂配置原則範例程式碼可接受的有效尾碼。 如此一來，將會接受其中一個，而拒絕另一個

為了衍生裝置金鑰，您將使用先前記下的對稱金鑰，為每個裝置計算裝置註冊識別碼的 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)，並將結果轉換為 Base64 格式。 如需關於為註冊群組建立衍生裝置金鑰的詳細資訊，請參閱[對稱金鑰證明](concepts-symmetric-key-attestation.md)的群組註冊小節。

針對本文的範例，請使用下列兩個裝置註冊識別碼與下列程式碼，計算這兩個裝置的裝置金鑰：

* **contoso-toaster-007**
* **contoso-heatpump-088**

將 **KEY** 變數的值取代為您先前在建立註冊群組後所記下的 [主要金鑰]。 使用下列程式碼顯示的金鑰值和輸出只是一個範例。

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

如果您使用以 Windows 為基礎的工作站，您可以使用 PowerShell 來產生衍生的裝置金鑰，如下列範例所示。

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='contoso-toaster-007'
$REG_ID2='contoso-heatpump-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($key)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

#### <a name="bash"></a>[Bash](#tab/bash)

如果您使用 Linux 工作站，您可以使用 openssl 來產生衍生的裝置金鑰，如下列 Bash 範例所示。

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=contoso-toaster-007
REG_ID2=contoso-heatpump-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>準備 Azure IoT C SDK 開發環境

裝置將使用包含在 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中的佈建範例程式碼來要求佈建。

在本節中，您要準備用來建置 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的開發環境。 SDK 包含模擬裝置的範例程式碼。 這個模擬裝置將會嘗試在裝置開機順序期間進行佈建。

此節以 Windows 工作站為基礎來說明。 如需 Linux 範例，請參閱[如何針對多組織用戶佈建](how-to-provision-multitenant.md)中的 VM 設定。

1. 下載 [CMake 建置系統](https://cmake.org/download/)。

    在開始安裝 `CMake`**之前**，請務必將 Visual Studio 先決條件 (Visual Studio 和「使用 C++ 進行桌面開發」工作負載) 安裝在您的機器上。 在符合先決條件，並且驗證過下載項目之後，請安裝 CMake 建置系統。

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

    如果 `cmake` 找不到 C++ 編譯器，您在執行命令時，可能會收到建置錯誤。 如果發生這種情況，請嘗試在 [Visual Studio 命令提示字元](/dotnet/framework/tools/developer-command-prompt-for-vs)中執行命令。

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

1. 在 Azure 入口網站中，選取您裝置佈建服務的 [概觀] 索引標籤，並記下 [識別碼範圍]**** 值。

    ![從入口網站刀鋒視窗擷取裝置佈建服務端點資訊](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. 在 Visual Studio 中，開啟先前藉由執行 CMake 而產生的 **azure_iot_sdks.sln** 解決方案檔案。 該方案檔案應位於下列位置：

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. 在 Visual Studio 的 [方案總管]  視窗中，瀏覽至 **Provision\_Samples** 資料夾。 展開名為 **prov\_dev\_client\_sample** 的範例專案。 展開 [來源檔案]  ，然後開啟 **prov\_dev\_client\_sample.c**。

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

6. 在 `main()` 函式中，尋找 `Prov_Device_Register_Device()` 的呼叫。 在呼叫之前，請新增下列幾行程式碼，以使用 [`Prov_Device_Set_Provisioning_Payload()`](/azure/iot-hub/iot-c-sdk-ref/prov-device-client-h/prov-device-set-provisioning-payload) 在佈建期間傳遞自訂 JSON 承載。 這可用來提供自訂配置函式的詳細資訊。 這也可以用來傳遞裝置類型，而不是檢查註冊識別碼。

    ```c
    // An example custom payload
    const char* custom_json_payload = "{\"MyDeviceFirmwareVersion\":\"12.0.2.5\",\"MyDeviceProvisioningVersion\":\"1.0.0.0\"}";

    prov_device_result = Prov_Device_Set_Provisioning_Payload(prov_device_handle, custom_json_payload);
    if (prov_device_result != PROV_DEVICE_RESULT_OK)
    {
        (void)printf("\r\nFailure setting provisioning payload: %s\r\n", MU_ENUM_TO_STRING(PROV_DEVICE_RESULT, prov_device_result));
    }
    ```

7. 以滑鼠右鍵按一下 **prov\_dev\_client\_sample** 專案，然後選取 [設定為起始專案]  。

### <a name="simulate-the-contoso-toaster-device"></a>模擬 Contoso 烤箱裝置

1. 若要模擬 toaster 裝置，請在 **prov\_dev\_client\_sample.c** 中尋找已標成註解之 `prov_dev_set_symmetric_key_info()` 的呼叫。

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    取消註解函式呼叫，並將預留位置值 (包括角括號) 取代為您先前產生的 toaster 註冊識別碼和衍生裝置金鑰。 下列金鑰值 **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** 只是範例。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-toaster-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    儲存檔案。

2. 在 Visual Studio 功能表中，選取 [偵錯]   > [啟動但不偵錯]  以執行解決方案。 出現重新建置專案的提示時，選取 [是]  ，以在執行前重新建置專案。

    下列文字是對 toaster 裝置執行自訂配置函式程式碼後產生的範例記錄輸出。 請注意，已成功選取 toaster 裝置的中樞。 另請注意 `payload` 成員，其中包含您新增至程式碼的自訂 JSON 內容。 這可供您的程式碼在 `deviceRuntimeContext` 中使用。

    按一下入口網站中函式程式碼底下的 [記錄]，即可取得此記錄：

    ```cmd
    2020-09-23T11:44:37.505 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=4596d45e-086f-4e86-929b-4a02814eee40)
    2020-09-23T11:44:41.380 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:44:41.381 [Information] Request.Body:...
    2020-09-23T11:44:41.381 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-toaster-007","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:44:41.687 [Information] linkedHub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Selected hub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Response
    2020-09-23T11:44:41.688 [Information] {"iotHubHostName":"contoso-toasters-hub-1098.azure-devices.net"}
    2020-09-23T11:44:41.689 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=4596d45e-086f-4e86-929b-4a02814eee40, Duration=4347ms)    
    ```

    以下範例裝置輸出會顯示說明模擬的 toaster 裝置在成功開機後連線至佈建服務執行個體，並依自訂配置原則指派給烤箱 IoT 中樞的情形：

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: contoso-toaster-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>模擬 Contoso 熱泵裝置

1. 若要模擬熱泵裝置，請使用您稍早產生的熱泵註冊識別碼和衍生裝置金鑰再次更新 **prov\_dev\_client\_sample.c** a 中的 `prov_dev_set_symmetric_key_info()`。 下列金鑰值 **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** 只是範例。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-heatpump-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    儲存檔案。

2. 在 Visual Studio 功能表中，選取 [偵錯]   > [啟動但不偵錯]  以執行解決方案。 出現重新建置專案的提示時，選取 [是]，以在執行前重新建置專案。

    下列文字是對熱泵裝置執行自訂配置函式程式碼後產生的範例記錄輸出。 自訂配置原則會拒絕此註冊，並出現 HTTP 錯誤 400 不正確的要求。 請注意 `payload` 成員，其中包含您新增至程式碼的自訂 JSON 內容。 這可供您的程式碼在 `deviceRuntimeContext` 中使用。

    按一下入口網站中函式程式碼底下的 [記錄]，即可取得此記錄：

    ```cmd
    2020-09-23T11:50:23.652 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68)
    2020-09-23T11:50:23.653 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:50:23.654 [Information] Request.Body:...
    2020-09-23T11:50:23.654 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-heatpump-088","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:50:23.654 [Information] Unknown device registration
    2020-09-23T11:50:23.654 [Information] Response
    2020-09-23T11:50:23.654 [Information] Unrecognized device registration.
    2020-09-23T11:50:23.655 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68, Duration=11ms)
    ```

    以下範例裝置輸出會顯示模擬的熱泵裝置在開機後連線至佈建服務執行個體，並使用自訂配置原則嘗試註冊至 IoT 中樞的情形。 這會失敗，並出現錯誤 (`Custom allocation failed with status code: 400`)，因為自訂配置原則設計為只允許 toaster 裝置：


    ```cmd
    Provisioning API Version: 1.3.7
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_prov_transport_process_json_reply Line:658 Provisioning Failure: OperationId: 4.eb89f3e8407a3711.2525bd34-02e9-4e91-a9c0-4dbc4ad5de66 - Date: 2020-09-23T17:05:58.2363145Z - Msg: Custom allocation failed with status code: 400
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_transport_mqtt_common.c Func:_prov_transport_common_mqtt_dowork Line:1014 Unable to process registration reply.
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_on_transport_registration_data Line:770 Failure retrieving data from the provisioning service
    
    Failure registering device: PROV_DEVICE_RESULT_DEV_AUTH_ERROR
    Press enter key to exit:    
    ```
    
## <a name="clean-up-resources"></a>清除資源

如果您打算繼續使用在此文章中建立的資源，可以將其保留。 如果不打算繼續使用這些資源，請使用下列步驟刪除此文章中建立的所有資源，以避免產生非必要費用。

以下步驟假設您依照指示在名為 **contoso-us-resource-group** 的相同資源群組中建立了此文章中的所有資源。

> [!IMPORTANT]
> 刪除資源群組是無法回復的動作。 資源群組和其中包含的所有資源都將永久刪除。 請確定您不會誤刪錯誤的資源群組或資源。 如果您在現有的資源群組內建立了 IoT 中樞，而該群組中包含您想要保留的資源，則您只需刪除 IoT 中樞本身即可，而不要刪除資源群組。
>

依名稱刪除資源群組：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取 [資源群組]  。

2. 在 [依名稱篩選] 文字方塊中，輸入您的資源所屬的資源群組名稱 **contoso-us-resource-group**。 

3. 在結果清單中的資源群組右側，選取 [...]，然後按一下 [刪除資源群組]。

4. 系統將會要求您確認是否刪除資源。 再次輸入您的資源群組名稱以進行確認，然後選取 [刪除]。 片刻過後，系統便會刪除該資源群組及其所有內含的資源。

## <a name="next-steps"></a>後續步驟

* 如需更深入的自訂配置原則範例，請參閱[如何使用自訂配置原則](how-to-use-custom-allocation-policies.md)。
* 若要深入了解如何重新佈建，請參閱 [IoT 中樞裝置重新佈建的概念](concepts-device-reprovision.md)。
* 若要深入了解如何取消佈建，請參閱[如何取消佈建先前自動佈建的裝置](how-to-unprovision-devices.md)。