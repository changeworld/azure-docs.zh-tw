---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: f63510771e4bd71a3ab6cf048bc5fb5296042a4d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75564691"
---
## <a name="create-a-container-registry"></a>建立容器登錄庫

在本教學課程中，您會使用 Azure IoT Tools 擴充功能來建置模組，並從檔案建立**容器映像**。 接著，您會將此映像推送至儲存並管理映像的**登錄**。 最後，您會從登錄部署該映像，以在 IoT Edge 裝置上執行。

您可以使用任何與 Docker 相容的登錄來保存容器映像。 兩個熱門 Docker 登錄服務為 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 和 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。 本教學課程使用的是 Azure Container Registry。

如果您還沒有容器登錄，請依照下列步驟在 Azure 中建立新的容器登錄：

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源]   > [容器]   > [容器登錄]  。

2. 提供下列值以建立您的容器登錄：

   | 欄位 | 值 |
   | ----- | ----- |
   | 登錄名稱 | 提供唯一名稱。 |
   | 訂用帳戶 | 從下拉式清單中選取訂用帳戶。 |
   | 資源群組 | 建議您對於在 IoT Edge 快速入門和教學課程中建立的所有測試資源，使用相同的資源群組。 例如 **IoTEdgeResources**。 |
   | Location | 選擇接近您的位置。 |
   | 管理員使用者 | 設定為 [已啟用]  。 |
   | SKU | 選取 [基本]  。 |

3. 選取 [建立]  。

4. 建立容器登錄後請加以瀏覽，並且在左窗格中，從位於 [設定] 下方的功能表中選取 [存取金鑰]。

5. 複製 [登入伺服器]  、[使用者名稱]  及 [密碼]  的值，並將這些值儲存在方便的地方。 在這整個教學課程中，您會使用這些值來提供容器登錄的存取權。

   ![複製容器登錄的登入伺服器、使用者名稱和密碼](./media/iot-edge-create-container-registry/registry-access-key.png)