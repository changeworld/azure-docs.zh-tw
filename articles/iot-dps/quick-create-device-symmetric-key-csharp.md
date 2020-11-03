---
title: 快速入門 - 使用 C# 利用對稱金鑰將裝置佈建到 Azure IoT 中樞
description: 在本快速入門中，您將會使用裝置佈建服務的 C# 裝置 SDK 將對稱金鑰裝置佈建至 IoT 中樞
author: wesmc7777
ms.author: wesmc
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: f97840a05115bf5659a6f7579b72786e890051a2
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92429221"
---
# <a name="quickstart-provision-a-symmetric-key-device-using-c"></a>快速入門：使用 C# 佈建對稱金鑰裝置

在本快速入門中，您將了解如何使用 C#，將 Windows 開發機器當作裝置，佈建到 IoT 中樞。 此裝置將會使用對稱金鑰和個別註冊來搭配裝置佈建服務 (DPS) 執行個體進行驗證，以指派給 IoT 中樞。 [適用於 C# 的Azure IoT 範例](https://github.com/Azure-Samples/azure-iot-samples-csharp)的範例程式碼將用來佈建裝置。 

雖然此文章示範如何使用個別註冊來佈建，但您也可以使用註冊群組。 使用註冊群組的過程會有一些差異。 例如，您必須使用具有裝置唯一註冊識別碼的衍生裝置金鑰。 [使用對稱金鑰佈建裝置](how-to-legacy-device-symm-key.md)提供註冊群組範例。 如需註冊群組詳細資訊，請參閱[對稱金鑰證明的群組註冊](concepts-symmetric-key-attestation.md#group-enrollments)。

如果您不熟悉自動佈建程序，請檢閱[佈建](about-iot-dps.md#provisioning-process)概觀。 

此外，繼續進行此快速入門之前，請務必完成[使用 Azure 入口網站設定 IoT 中樞裝置佈建服務](./quick-setup-auto-provision.md)中的步驟。 此快速入門要求您必須已建立您的裝置佈建服務執行個體。

此文章以 Windows 工作站為基礎來說明。 不過，您可以在 Linux 上執行上述程序。 如需 Linux 範例，請參閱[針對多組織用戶管理佈建](how-to-provision-multitenant.md)。


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prerequisites

* 請確定您已在 Windows 電腦上安裝 [.NET Core 2.1 SDK](https://www.microsoft.com/net/download/windows) 或更新版本。

* 已安裝最新版的 [Git](https://git-scm.com/download/)。

<a id="setupdevbox"></a>


## <a name="create-a-device-enrollment"></a>建立裝置註冊

1. 登入 [Azure 入口網站](https://portal.azure.com)，選取左側功能表上的 [所有資源] 按鈕，然後開啟您的裝置佈建服務 (DPS) 執行個體。

2. 選取 [管理註冊] 索引標籤，然後選取頂端的 [新增個別註冊] 按鈕。 

3. 在 [新增註冊] 面板中，輸入下列資訊，然後按 [儲存] 按鈕。

   - **機制** ：選取 [對稱金鑰] 作為身分識別證明「機制」。

   - **自動產生金鑰** ︰選取此方塊。

   - **註冊識別碼** ：輸入註冊識別碼以識別註冊。 只使用小寫英數字元與破折號 ('-') 字元。 例如， **symm-key-csharp-device-01** 。

   - **IoT 中樞裝置識別碼：** 輸入裝置識別碼。 例如， **csharp-device-01** 。

     ![在入口網站中為對稱金鑰證明新增個別註冊](./media/quick-create-device-symmetric-key-csharp/create-individual-enrollment-csharp.png)

4. 一旦儲存您的註冊，將會產生 [主要金鑰] 與 [次要金鑰] 並新增到註冊項目。 您的對稱金鑰裝置註冊會在 [個別註冊] 索引標籤中的 [註冊識別碼] 欄下顯示為 **symm-key-csharp-device-01** 。 

5. 開啟註冊並複製您產生之 **主要金鑰** 和 **次要金鑰** 的值。 稍後當您新增要與裝置佈建範例程式碼搭配使用的環境變數時，將會使用此金鑰值和 **註冊識別碼** 。



## <a name="prepare-the-c-environment"></a>準備 C# 環境 

1. 開啟 Git CMD 或 Git Bash 命令列環境。 使用下列命令複製[適用於 C# 的 Azure IoT 範例](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存放庫：

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```



<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>準備裝置佈建程式碼

在本節中，您將新增下列四個環境變數，作為對稱金鑰裝置的裝置佈建範例程式碼參數。 

* `DPS_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PRIMARY_SYMMETRIC_KEY`
* `SECONDARY_SYMMETRIC_KEY`

佈建程式碼會根據這些變數來與 DPS 執行個體聯繫，以便驗證您的裝置。 然後，裝置會根據個別註冊組態，指派給已連結至 DPS 執行個體的 IoT 中樞。 佈建之後，範例程式碼會將一些測試遙測傳送至 IoT 中樞。

1. 在 [Azure 入口網站](https://portal.azure.com)的 [裝置佈建服務] 功能表上，選取 [概觀] 並且複製 [服務端點] 和 [識別碼範圍]。 您會將這些值用於 `PROVISIONING_HOST` 和 `DPS_IDSCOPE` 環境變數。

    ![服務資訊](./media/quick-create-device-symmetric-key-csharp/extract-dps-endpoints.png)

2. 開啟命令提示字元，並導覽至複製之範例存放庫中的 SymmetricKeySample：

    ```cmd
    cd provisioning\Samples\device\SymmetricKeySample
    ```

3. 在 SymmetricKeySample 資料夾的文字編輯器中開啟 Program.cs，並尋找設定 `individualEnrollmentPrimaryKey` 和 `individualEnrollmentSecondaryKey` 字串的程式碼行。 如下所示更新這幾行程式碼以使用環境變數，而不是對金鑰進行硬編碼。
 
    ```csharp
        //These are the two keys that belong to your individual enrollment. 
        // Leave them blank if you want to try this sample for an individual enrollment instead
        //private const string individualEnrollmentPrimaryKey = "";
        //private const string individualEnrollmentSecondaryKey = "";

        private static string individualEnrollmentPrimaryKey = Environment.GetEnvironmentVariable("PRIMARY_SYMMETRIC_KEY");;
        private static string individualEnrollmentSecondaryKey = Environment.GetEnvironmentVariable("SECONDARY_SYMMETRIC_KEY");;
    ```

    此外，請尋找設定 `registrationId` 字串的程式碼行並依照下列方式更新，以便也能使用環境變數，如下所示：

    ```csharp
        //This field is mandatory to provide for this sample
        //private static string registrationId = "";

        private static string registrationId = Environment.GetEnvironmentVariable("PROVISIONING_REGISTRATION_ID");;
    ```

    將變更儲存至 Program.cs。

3. 在命令提示字元中，新增從上一節中個別註冊複製的識別碼範圍、註冊識別碼、主要和次要對稱金鑰等環境變數。  

    下列命令是顯示命令語法的範例。 請務必使用正確的值。

    ```console
    set DPS_IDSCOPE=0ne00000A0A
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-csharp-device-01
    ```

    ```console
    set PRIMARY_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```

    ```console
    set SECONDARY_SYMMETRIC_KEY=Zx8/eE7PUBmnouB1qlNQxI7fcQ2HbJX+y96F1uCVQvDj88jFL+q6L9YWLLi4jqTmkRPOulHlSbSv2uFgj4vKtw==
    ```


4. 使用下列命令在本機建置和執行範例程式碼。

    ```console
    dotnet run
    ```

5. 預期的輸出看起來應該像下面這樣，其中顯示根據個別註冊設定獲指派裝置的已連結 IoT 中樞。 系統會將 "TestMessage" 範例字串傳送至中樞進行測試：

    ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run
    RegistrationID = symm-key-csharp-device-01
    ProvisioningClient RegisterAsync . . . Assigned
    ProvisioningClient AssignedHub: docs-test-iot-hub.azure-devices.net; DeviceID: csharp-device-01
    Creating Symmetric Key DeviceClient authentication
    DeviceClient OpenAsync.
    DeviceClient SendEventAsync.
    DeviceClient CloseAsync.
    Enter any key to exit
    ```
    
6. 在 Azure 入口網站中，瀏覽到連結至您佈建服務的 IoT 中樞，並開啟 [IoT 裝置] 刀鋒視窗。 將對稱金鑰裝置成功佈建到中樞之後，會顯示裝置識別碼，且 [狀態] 為 [啟用]。 如果您在執行裝置範例程式碼之前開啟了刀鋒視窗，可能需要按下頂端的 [重新整理] 按鈕。 

    ![已向 IoT 中樞註冊裝置](./media/quick-create-device-symmetric-key-csharp/hub-registration-csharp.png) 

> [!NOTE]
> 如果您在裝置的註冊項目中變更了 *初始裝置對應項狀態* (變更自預設值)，它即可從中樞提取所需的對應項狀態並採取適當的動作。 如需詳細資訊，請參閱[了解和使用 Azure IoT 中樞的裝置對應項](../iot-hub/iot-hub-devguide-device-twins.md)。
>


## <a name="clean-up-resources"></a>清除資源

如果您打算繼續使用並探索裝置用戶端範例，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請使用下列步驟來刪除本快速入門建立的所有資源。

1. 從 Azure 入口網站的左側功能表中，選取 [所有資源]，然後選取您的裝置佈建服務。 為您的服務開啟 [管理註冊]  ，然後選取 [個別註冊]  索引標籤。選取您在本快速入門中所註冊裝置的 [註冊識別碼]  旁的核取方塊，然後按窗格頂端的 [刪除]  按鈕。 
1. 從 Azure 入口網站的左側功能表中，選取 [所有資源]  ，然後選取您的 IoT 中樞。 開啟您中樞的 [IoT 裝置]  ，選取您在本快速入門所註冊裝置的 [裝置識別碼]  旁的核取方塊，然後按窗格頂端的 [刪除]  按鈕。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 IoT 中樞裝置佈建服務，將以 Windows 為基礎的對稱金鑰裝置佈建到您的 IoT 中樞。 若要了解如何使用 C# 佈建 X.509 憑證裝置，請繼續下方適用於 X.509 裝置的快速入門。 

> [!div class="nextstepaction"]
> [Azure 快速入門 - 使用 DPS 和 C# 佈建 X.509 裝置](quick-create-simulated-device-x509-csharp.md)
