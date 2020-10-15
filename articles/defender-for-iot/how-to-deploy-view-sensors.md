---
title: 在適用于 IoT 的 Azure Defender 中查看及管理上線感應器
description: 本文說明如何查看和刪除上線感應器，以及下載適用于 IoT 的 Azure Defender 中上線感應器的新啟用檔案。
author: rkarlin
ms.author: rkarlin
ms.date: 10/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 6aec19d413e1730bc1599f6cbac1c62e9b304ecd
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094314"
---
# <a name="view-and-manage-onboarded-sensors"></a>查看和管理上線感應器

本文說明如何查看和刪除上線感應器，以及下載上線感應器的新啟用檔案。

## <a name="update-sensor-management-mode"></a>更新感應器管理模式

您可能會想要更新您的感應器管理的模式。 當您這樣做時，您需要從 [感應器管理] 頁面移除目前的感應器，然後上傳新的啟用檔案。

- **在雲端管理模式下工作，而不是在本機管理模式**：使用雲端受控感應器的啟用檔案，更新本機受控感應器的啟用檔。 重新啟用之後，感應器偵測會顯示在感應器和適用于 IoT 的 Azure Defender 入口網站中。 在重新啟用檔案成功上傳之後，會將新偵測到的警示資訊傳送至 Azure。

- **在本機管理的模式下工作，而不是使用雲端管理模式**：使用本機受管理感應器的啟用檔案，更新雲端受控感應器的啟用檔。 重新啟用之後，感應器偵測資訊只會顯示在感應器中。

- **將感應器與新的 Iot 中樞產生關聯**：您可能想要將感應器與新的 iot 中樞建立關聯。 若要這樣做，請重新註冊感應器，然後上傳新的啟用檔。

**若要重新啟用感應器：**

1. 流覽至適用于 IoT 的 Azure Defender 的 [ **感應器管理** ] 頁面。

2. 選取您要上傳新啟用檔案的感應器。

3. 刪除它。

4. 在新模式中或透過新的 IoT **中樞，從上線頁面再次** 讓感應器上線。

5. 從 **下載啟用** 檔案頁面下載啟動檔案。

6. 登入適用于 IoT 的 Azure Defender 感應器主控台。

7. 在感應器主控台中 **，選取 [****系統設定**]，然後選取 [重新啟用]。

   ![重新開機顯示的螢幕擷取畫面](media/updates/image14.png)

8. 選取 [ **上傳** ]，然後選取您儲存的檔案。

9. 選取 [啟用]  。
 
## <a name="delete-sensors"></a>刪除感應器

如果您刪除雲端管理的感應器，將不會將資訊傳送到 IoT 中樞。

當您不再使用本機管理的感應器時，請將其刪除。

**若要刪除感應器：**

1. 流覽至 [適用于 IoT 的 Azure Defender **感應器管理** ] 頁面。

2. 選取您要刪除的感應器。

3. 選取 [ **刪除感應器**]。

## <a name="next-steps"></a>後續步驟

若要深入瞭解設定選項，請繼續進行模組設定的操作指南。
> [!div class="nextstepaction"]
> [模組設定操作指南](./how-to-agent-configuration.md)
