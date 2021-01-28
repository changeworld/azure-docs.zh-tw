---
ms.openlocfilehash: 729839c8e881f507e103c4644c012d8dffc5c8c6
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956245"
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
    
    指令碼成功完成時，您應該會在訂用帳戶中看到所有所需資源。 腳本會設定總計12個資源：
    1. **串流端點** -這將有助於播放錄製的 AMS 資產。
    1. **虛擬機器** -這是將作為 edge 裝置的虛擬機器。
    1. **磁片** -這是連接至虛擬機器的儲存磁片，用來儲存媒體和構件。
    1. **網路安全性群組** -這可用來篩選 azure 虛擬網路中 azure 資源的進出網路流量。
    1. **網路介面** -這可讓 Azure 虛擬機器與網際網路、Azure 和其他資源進行通訊。
    1. 防禦連線-這可讓您使用瀏覽器和 Azure 入口網站連接到 **您的虛擬** 機。
    1. **公用 IP 位址** -這可讓 Azure 資源與網際網路和公眾面向的 azure 服務進行通訊
    1. **虛擬網路** -這可讓許多類型的 Azure 資源（例如您的虛擬機器）安全地彼此通訊，以及與網際網路和內部部署網路通訊。 深入瞭解 [虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
    1. **Iot 中樞** -這可作為 iot 應用程式、IoT Edge 模組與其管理裝置之間雙向通訊的中央訊息中樞。
    1. **媒體服務帳戶** -這有助於在 Azure 中管理和串流處理媒體內容。
    1. **儲存體帳戶** -您必須有一個主要儲存體帳戶，且您可以有任意數目的次要儲存體帳戶與您的媒體服務帳戶相關聯。 如需詳細資訊，請參閱 [Azure 儲存體帳戶與 Azure 媒體服務帳戶](https://docs.microsoft.com/azure/media-services/latest/storage-account-concept)。
    1. **Container registry** -這有助於儲存和管理您的私人 Docker 容器映射和相關構件。

1. 在指令碼完成後，選取大括弧以公開資料夾結構。 您會在 *~/clouddrive/lva-sample* 目錄下看到一些檔案。 與本快速入門相關的項目包括：

     * * **~/clouddrive/lva-sample/edge-deployment/.env** - 此檔案包含 Visual Studio Code 用來將模組部署至邊緣裝置的屬性。
     _ * **~/clouddrive/lva-sample/appsetting.json** _ - Visual Studio Code 會使用此檔案來執行範例程式碼。
     
    在下一節的 Visual Studio Code 中設定開發環境時，您將需要這些檔案。 目前，您可以將這些檔案複製到本機檔案。
    
    ![應用程式設定](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> 如果您遇到已建立的 Azure 資源問題，請檢視我們的_ *[疑難排解指南](../../../troubleshoot-how-to.md#common-error-resolutions)* *，以解決一些經常發生的問題。