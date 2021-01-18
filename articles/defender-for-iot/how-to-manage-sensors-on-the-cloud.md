---
title: 在 Defender for IoT 入口網站中上架及管理感應器
description: 瞭解如何在適用于 IoT 的 Defender 入口網站中上架、查看及管理感應器。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/27/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 427ea3884a3db6ba33405014435cf1f962670064
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562704"
---
# <a name="onboard-and-manage-sensors-in-the-defender-for-iot-portal"></a>在 Defender for IoT 入口網站中上架及管理感應器

本文說明如何在 [適用于 IoT 的 Defender 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)中上架、查看及管理感應器。

## <a name="onboard-sensors"></a>上線感應器

您可以使用適用于 IoT 的 Azure Defender 註冊感應器，並下載感應器啟用檔案，來讓感應器上線。

### <a name="register-the-sensor"></a>註冊感應器

註冊：

1. 移至 [適用于 IoT 的 Defender 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)中的 [**歡迎使用**] 頁面。
1. 選取 [上 **架] 感應器**。
1. 建立感應器名稱。 建議您將安裝的感應器 IP 位址納入為名稱的一部分，或使用容易辨識的名稱。 這可確保在適用于 IoT 的 Azure [Defender 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) 中的註冊名稱與感應器主控台中顯示的已部署感應器 IP 之間進行更輕鬆的追蹤和一致的命名。
1. 將感應器與 Azure 訂用帳戶產生關聯。
1. 使用 [ **雲端連線** ] 切換來選擇感應器管理模式。 如果切換為開啟狀態，則感應器會連接到雲端。 如果切換為關閉，則感應器會在本機管理。

   - **連線到雲端的感應器**：感應器所偵測到的資訊會顯示在感應器主控台中。 警示資訊是透過 IoT 中樞傳遞，並可與其他 Azure 服務共用，例如 Azure Sentinel。

   - **本機管理的感應器**：感應器所偵測到的資訊會顯示在感應器主控台中。 如果您是在有空調的網路中工作，而且想要統一查看多個本機受控感應器偵測到的所有資訊，請使用內部部署管理主控台。

   針對已連線到雲端的感應器，在上線期間定義的名稱是顯示在感應器主控台中的名稱。 您無法直接從主控台變更此名稱。 針對本機管理的感應器，在上線期間套用的名稱將會儲存在 Azure 中，並可在感應器主控台中更新。

1. 選擇 IoT 中樞作為此感應器與適用于 IoT 的 Azure Defender 之間的閘道。
1. 如果感應器已連線到雲端，請將其與 IoT 中樞建立關聯，然後定義網站名稱和區域。 您也可以新增描述性標記。 網站名稱、區域和標記是 [ [網站和感應器] 頁面](#view-onboarded-sensors)上的描述性專案。

### <a name="download-the-sensor-activation-file"></a>下載感應器啟用檔案

感應器啟用檔案包含感應器管理模式的相關指示。 您可以為您部署的每個感應器下載唯一的啟用檔案。 第一次登入感應器主控台的使用者，會將啟用檔案上傳至感應器。

若要下載啟用檔案：

1. 在 [上 **架感應器** ] 頁面上，選取 [ **下載啟用** 檔案]。
1. 使檔案可供使用者第一次登入感應器主控台的使用者存取。

## <a name="view-onboarded-sensors"></a>查看上線感應器

在 [適用于 IoT 的 Defender 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)上，您可以查看上線感應器的基本資訊。 

1. 選取 [ **網站和感應器**]。
1. 在 [ **網站和感應器** ] 頁面上，使用篩選和搜尋工具來尋找您所需的感應器資訊。

可用的資訊包括：

- 上線的感應器數目
- 雲端連線和本機管理的感應器數目
- 與上線感應器相關聯的中樞
- 新增有關感應器的詳細資料，例如在上架期間指派給感應器的名稱、與感應器相關聯的區域，或其他加上標記的描述性資訊

## <a name="manage-onboarded-sensors"></a>管理上線感應器

您可以使用適用于 [IoT 的 Defender 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) 進行與感應器相關的管理工作。

### <a name="export"></a>匯出

若要匯出上線感應器資訊，請選取 [**網站和感應器**] 頁面頂端的 [**匯出**] 圖示。

### <a name="edit"></a>編輯

使用 [ **網站] 和 [感應器** 編輯工具] 來新增和編輯網站名稱、區域和標記。

### <a name="delete"></a>刪除

如果您刪除已連線到雲端的感應器，資訊將不會傳送至 IoT 中樞。 當您不再使用它們時，請刪除本機連接的感應器。

若要刪除感應器：

1. 針對您要刪除的感應器，選取省略號 (**...**) 。 
1. 確認刪除。

### <a name="reactivate"></a>重新啟動

您可能會想要更新您的感應器管理所在的模式。 例如：

- **在連線到雲端的模式下工作，而不是在本機管理模式**：若要這樣做，請使用雲端連線感應器的啟用檔案，更新本機連線感應器的啟用檔。 重新啟用之後，感應器偵測會顯示在 [IoT 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)的感應器和 Defender 中。 順利上傳重新啟用檔案之後，會將新偵測到的警示資訊傳送至 Azure。

- **在本機連線模式下工作，而不是連線到雲端的模式**：若要這樣做，請使用本機受管理感應器的啟用檔案，更新連線到雲端的感應器的啟用檔案。 重新啟用之後，感應器偵測資訊只會顯示在感應器中。

- **將感應器與新的 IoT 中樞產生關聯**：若要這樣做，請重新註冊感應器，然後上傳新的啟用檔案。

若要重新啟用感應器：

1. 前往 [IoT 入口網站 Defender](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)上的 [**網站和感應器**] 頁面。

2. 選取您要上傳新啟用檔案的感應器。

3. 刪除感應器。

4. 在新模式中或透過新的 IoT **中樞，從上線頁面再次** 讓感應器上線。

5. 從 **下載啟用** 檔案頁面下載啟動檔案。

6. 登入適用于 IoT 的 Defender 感應器主控台。

7. 在感應器主控台中 **，選取 [****系統設定**]，然後選取 [重新啟用]。

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="上傳您的啟用檔案以重新開機感應器。":::

8. 選取 [ **上傳** ]，然後選取您儲存的檔案。

9. 選取 [啟用]  。 

## <a name="see-also"></a>請參閱

[啟用和設定感應器](how-to-activate-and-set-up-your-sensor.md)
