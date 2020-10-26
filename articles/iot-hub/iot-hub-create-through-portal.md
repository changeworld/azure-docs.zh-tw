---
title: 使用 Azure 入口網站建立 IoT 中樞 | Microsoft Docs
description: 如何透過 Azure 入口網站建立、管理和刪除 Azure IoT 中樞。 其中包括定價層、調整、安全性和傳訊組態的相關資訊。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 0a54d0c85902915d2ee62acd8a1d38b8db8b221c
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536040"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>使用 Azure 入口網站建立 IoT 中樞

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

此文章說明如何使用 [Azure 入口網站](https://portal.azure.com)來建立及管理 IoT 中樞。

若要使用此教學課程中的步驟，您需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>變更 IoT 中樞的設定

從 IoT 中樞窗格建立 IoT 中樞後，您可以變更此現有 IoT 中樞的設定。

![顯示 IoT 中樞設定的螢幕擷取畫面](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

以下是一些您可以為 IoT 中樞設定的屬性：

**定價與級別** ：您可以使用這個屬性移轉至不同的層，或設定 IoT 中樞單位數。 

**作業監視** ：開啟或關閉不同的監視類別，例如記錄與裝置到雲端訊息或雲端到裝置訊息相關的事件。

**IP 篩選器** ：您可以在這裡指定將由 IoT 中樞接受或拒絕的 IP 位址範圍。

**屬性** ：提供可以複製並在其他位置使用的屬性清單，例如資源識別碼、資源群組、位置等等。

### <a name="shared-access-policies"></a>共用存取原則

您也可以透過按一下 [設定]  區段中的 [共用存取原則]  來檢視或修改共用存取原則清單。 這些原則定義裝置與服務連線到 IoT 中樞的權限。 

按一下 [新增]  以開啟 [新增共用存取原則]  刀鋒視窗。  您可以輸入新原則名稱以及您想要與此原則產生關聯的權限，如下圖所示：

![顯示新增共用存取原則的螢幕擷取畫面](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* [登錄讀取]  和 [登錄寫入]  原則會授與讀取和寫入存取權給身分識別登錄。 後端雲端服務會使用這些許可權來管理裝置身分識別。 選擇寫入選項就會自動選擇讀取選項。

* **服務連接** 原則會授與存取服務端點的許可權。 後端雲端服務會使用此許可權來傳送和接收來自裝置的訊息，以及更新和讀取裝置對應項和模組對應項資料。

* [裝置連線]  原則會授與使用 IoT 中樞裝置端端點傳送和接收訊息的權限。 裝置會使用此許可權來傳送和接收來自 IoT 中樞的訊息、更新和讀取裝置對應項和模組對應項資料，以及執行檔案上傳。

按一下 [建立]  將此新建立的原則新增至現有的清單。

如需特定許可權所授與之存取權的詳細資訊，請參閱 [IoT 中樞許可權](./iot-hub-devguide-security.md#iot-hub-permissions)。

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="message-routing-for-an-iot-hub"></a>IoT 中樞的訊息路由

按一下 [傳訊]  下的 [訊息路由]  以查看 [訊息路由] 窗格，您可以在此窗格中定義中樞的路由與自訂端點。 [訊息路由](iot-hub-devguide-messages-d2c.md)可讓您管理資料從您的裝置傳送到您的端點的方式。 第一個步驟是新增新的路由。 接著，您可以將現有端點新增至路由，或建立一個具有支援類型的端點，例如 Blob 儲存體。 

![[訊息路由] 窗格](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>路由

路由是 [訊息路由] 窗格中的第一個索引標籤。 若要新增新的路由，請按一下 [+新增]  。 您會看到下列畫面。 

![顯示新增路由的螢幕擷取畫面](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

命名您的路由。 路由名稱在該中樞的路由清單中必須是唯一的。 

針對 [端點]  ，您可以從下拉式清單中選取一個端點，或新增一個新端點。 在此範例中，儲存體帳戶與容器已可用。 若要將它們新增為端點，請按一下端點下拉式清單旁邊的 [+新增]  ，然後選取 [Blob 儲存體]  。 下列畫面顯示指定儲存體帳戶與容器的位置。

![顯示為路由規則新增儲存體端點的螢幕擷取畫面](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

按一下 [挑選容器]  以選取儲存體帳戶與容器。 當您選取那些欄位時，它會返回 [端點] 窗格。 使用其餘欄位的預設值和 [建立]  ，為儲存體帳戶建立端點，並將它新增到路由規則。

針對 [資料來源]  ，選取 [裝置遙測訊息]。 

接下來，新增路由查詢。 在此範例中，具有值等於 `critical` 且名稱為 `level` 之應用程式屬性的訊息將路由傳送到儲存體帳戶。

![顯示儲存新路由規則的螢幕擷取畫面](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

按一下 [儲存]  以儲存路由規則。 您將返回 [訊息路由] 窗格，而且其中會顯示您的新路由規則。

### <a name="custom-endpoints"></a>自訂端點

按一下 [ **自訂端點** ] 索引標籤。您會看到任何已建立的自訂端點。 從這裡，您可以新增新端點，或刪除現有的端點。 

> [!NOTE]
> 如果您刪除路由，不會刪除指派給該路由的端點。 若要刪除端點，請按一下 [自訂端點] 索引標籤，選取您要刪除的端點，然後按一下 [刪除]。
>

您可以在[參考 - IoT 中樞端點](iot-hub-devguide-endpoints.md)中深入了解自訂端點。

您可以為 IoT 中樞定義最多 10 個自訂端點。 

若要查看如何搭配路由使用自訂端點的完整範例，請參閱[使用 IoT 中樞進行訊息路由](tutorial-routing.md)。

## <a name="find-a-specific-iot-hub"></a>尋找特定 IoT 中樞

以下是在您的訂用帳戶中尋找特定 IoT 中樞的兩種方式：

1. 如果您知道 IoT 中樞所屬的資源群組，請按一下 [資源群組]  ，然後從清單中選取該資源群組。 資源群組畫面會顯示該群組中的所有資源，包括 IoT 中樞。 按一下您要尋找的中樞。

2. 按一下 [所有資源]  。 在 [所有資源]  窗格中，有一個預設為 `All types` 的下拉式清單。 按一下下拉式清單，取消選取 `Select all`。 尋找 `IoT Hub` 並選取它。 按一下下拉式清單方塊將它關閉，將會篩選項目，只顯示您的 IoT 中樞。

## <a name="delete-the-iot-hub"></a>刪除 IoT 中樞

若要刪除 IoT 中樞，請找到您要刪除的 IoT 中樞，然後按一下 IoT 中樞名稱下方的 [刪除]  按鈕。

## <a name="next-steps"></a>下一步

遵循下列連結以深入了解如何管理 Azure IoT 中樞：

* [使用 IoT 中樞進行訊息路由](tutorial-routing.md)
* [監視您的 IoT 中樞](monitor-iot-hub.md)
