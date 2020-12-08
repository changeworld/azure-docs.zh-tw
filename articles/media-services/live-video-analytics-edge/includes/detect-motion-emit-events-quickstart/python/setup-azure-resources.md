---
ms.openlocfilehash: 5fa8377b3cdddb2818c04c5a75e347ff73dc07d8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509557"
---
本教學課程需要下列 Azure 資源：

* IoT 中樞
* 儲存體帳戶
* Azure 媒體服務帳戶
* Azure 中的 Linux VM，已安裝 [IoT Edge 執行階段](../../../../../iot-edge/how-to-install-iot-edge.md)

在本快速入門中，我們建議您使用 [Live Video Analytics 資源設定指令碼](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)，在您的 Azure 訂用帳戶中部署所需的資源。 若要這樣做，請依照下列步驟執行：

1. 開啟 [Azure Cloud Shell](https://shell.azure.com)。
1. 如果您第一次使用 Cloud Shell，系統會提示您選取用來建立儲存體帳戶和 Microsoft Azure 檔案共用的訂用帳戶。 選取 [建立儲存體]，以建立可供儲存 Cloud Shell 工作階段資訊的儲存體帳戶。 此儲存體帳戶與指令碼將建立來搭配您 Azure 媒體服務帳戶使用的帳戶不同。
1. 在 Cloud Shell 視窗左側的下拉式功能表中，選取 [Bash] 作為您的環境。

    ![環境選取器](../../../media/quickstarts/env-selector.png)
1. 執行下列命令。

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    如果指令碼成功完成，您應該會在訂用帳戶中看到所有所需資源。
1. 在指令碼完成後，選取大括弧以公開資料夾結構。 您會在 *~/clouddrive/lva-sample* 目錄下看到一些檔案。 與本快速入門相關的項目包括：

     * * **~/clouddrive/lva-sample/edge-deployment/.env** - 此檔案包含 Visual Studio Code 用來將模組部署至邊緣裝置的屬性。
     ***~/clouddrive/lva-sample/appsetting.json*** - Visual Studio Code 會使用此檔案來執行範例程式碼。
     
    在下一節的 Visual Studio Code 中設定開發環境時，您將需要這些檔案。 目前，您可以將這些檔案複製到本機檔案。
    
    ![應用程式設定](../../../media/quickstarts/clouddrive.png)
