---
title: 包含檔案
description: 包含檔案
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 383cd286f89bde13f5e557792e980f0455e00917
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876643"
---
## <a name="deploy-and-configure-azure-media-services"></a>部署和設定 Azure 媒體服務

解決方案會使用 Azure 媒體服務帳戶來儲存 IoT Edge 閘道裝置所執行的物件偵測影片剪輯。

當您建立媒體服務帳戶時：

- 您必須提供帳戶名稱、Azure 訂用帳戶、位置、資源群組和儲存體帳戶。 在建立媒體服務帳戶時，使用預設設定來建立新的儲存體帳戶。

- 選擇 [美國東部] 區域作為位置。

- 在 [美國東部] 區域中，為媒體服務和儲存體帳戶建立名為 lva-rg 的新資源群組。 當您完成教學課程時，您可以藉由刪除 lva-rg 資源群組來輕鬆移除所有資源。

[在 Azure 入口網站中建立媒體服務帳戶](https://portal.azure.com/?r=1#create/Microsoft.MediaService)。

> [!TIP]
> 這些教學課程會在所有範例中使用 [美國東部] 區域。 您也可以使用最接近您的區域。

記下 scratchpad.txt 檔案中的**媒體服務**帳戶名稱。

當部署完成時，瀏覽至**媒體服務**帳戶的 [屬性] 頁面。 記下 scratchpad.txt 檔案中的**資源識別碼**，稍後設定 IoT Edge 模組時會用到此值。

接下來，為您的媒體服務資源設定 Azure Active Directory 服務主體。 選取 [API 存取]，然後選取 [服務主體驗證]。 使用與您媒體服務資源相同的名稱建立新的 Azure Active Directory 應用程式，並以「IoT Edge 存取」作為描述來建立祕密。

:::image type="content" source="./media/iot-central-video-analytics-part2/media-service-authentication.png" alt-text="設定適用於 Azure 媒體服務的 Azure AD 應用程式":::

產生秘密時，向下捲動至「**複製您的認證以連結您的服務主體應用程式**」區段。 然後選取 [JSON]。 您可以在這裡一次複製所有認證資訊。 記下 scratchpad.txt 檔案中的此資訊，稍後您設定 IoT Edge 裝置時會用到。

> [!WARNING]
> 這是您唯一可以檢視和儲存秘密的機會。 如果遺失了，您必須產生另一個秘密。

## <a name="create-the-azure-iot-central-application"></a>建立 Azure IoT Central 應用程式

在本節中，您會從範本建立新的 Azure IoT Central 應用程式。 您會在整個教學課程系列中，使用此應用程式來建置完整的解決方案。

若要建立新的 Azure IoT Central 應用程式：

1. 瀏覽至 [Azure IoT Central 的應用程式管理員](https://aka.ms/iotcentral)網站。

1. 以存取您 Azure 訂用帳戶時所用的認證來登入。

1. 若要開始建立新的 Azure IoT Central 應用程式，請在 [建置] 頁面上選取 [新增應用程式]。

1. 選取 [零售]  。 [零售] 頁面會顯示數個零售應用程式範本。

若要建立新的影片分析應用程式：

1. 選取 [影片分析 - 物件和動作偵測] 應用程式範本。 針對本教學課程中使用的裝置，其裝置範本皆包含在此範本中。 此範本包含範例儀表板，可供操作員用來執行監視和管理相機等工作。

1. (選擇性) 選擇易記的 [應用程式名稱]  。 此應用程式會以名為 Northwind Traders 的虛構零售商店為基礎。 本教學課程會使用「Northwind Traders 影片分析」作為**應用程式名稱**。

    > [!NOTE]
    > 如果您使用自訂的**應用程式名稱**，您仍然必須針對應用程式的 **URL** 使用唯一值。

1. 如果您有 Azure 訂用帳戶，請選取您的 [目錄]、[Azure 訂用帳戶]，並以 [美國] 作為 [位置]。 如果您沒有訂用帳戶，則可以啟用 [7 天免費試用]  並填妥必要的連絡人資訊。 本教學課程使用三部裝置 (兩部相機和一部 IoT Edge 裝置)，因此，如果您未使用免費試用版，將會依使用量計費。

    如需關於目錄、訂用帳戶和位置的詳細資訊，請參閱[建立應用程式快速入門](../articles/iot-central/core/quick-deploy-iot-central.md)。

1. 選取 [建立]。

    :::image type="content" source="./media/iot-central-video-analytics-part2/new-application.png" alt-text="設定適用於 Azure 媒體服務的 Azure AD 應用程式":::

### <a name="retrieve-the-configuration-data"></a>擷取設定資料

稍後在本教學課程中設定 IoT Edge 閘道時，您會需要來自 IoT Central 應用程式的某些設定資料。 IoT Edge 裝置需要此資訊，才能向應用程式註冊及連線到該應用程式。

在 [管理] 區段中選取**您的應用程式**，並記下 scratchpad.txt 檔案中的**應用程式 URL** 和**應用程式識別碼**：

:::image type="content" source="./media/iot-central-video-analytics-part2/administration.png" alt-text="設定適用於 Azure 媒體服務的 Azure AD 應用程式":::

選取 [API 權杖]，然後針對**操作員**角色產生名為 **LVAEdgeToken** 的新權杖：

:::image type="content" source="./media/iot-central-video-analytics-part2/token.png" alt-text="設定適用於 Azure 媒體服務的 Azure AD 應用程式":::

記下 scratchpad.txt 檔案中的權杖，以便稍後使用。 關閉對話方塊之後，您就無法再次檢視權杖。

在 [管理] 區段中選取 [裝置連線]，然後選取 [SAS-IoT-Devices]。

記下 *scratchpad.txt* 檔案中的裝置**主要金鑰**。 稍後設定 IoT Edge 裝置時，您會用到此「主要群組共用存取簽章權杖」。
