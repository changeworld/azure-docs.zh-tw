---
title: '為路由事件啟用受控識別 (預覽) '
titleSuffix: Azure Digital Twins
description: 瞭解如何為 Azure 數位 Twins 啟用系統指派的身分識別，並使用它來轉送事件。
author: baanders
ms.author: baanders
ms.date: 1/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7de8e1eb1fd5311059bfb35d22b679a8c1f5ba9d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948732"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview"></a>啟用受控識別來路由傳送 Azure 數位 Twins 事件 (預覽) 

本文說明如何為[Azure 數位 Twins 實例啟用系統指派](concepts-security.md#managed-identity-for-accessing-other-resources-preview)的身分識別 (目前為預覽) ，並在將事件轉送至支援的目的地（例如[事件中樞](../event-hubs/event-hubs-about.md)、[服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)   目的地和[Azure 儲存體容器](../storage/blobs/storage-blobs-introduction.md)）時使用身分識別。

以下是本文所涵蓋的步驟： 

1. 使用系統指派的身分識別建立 Azure 數位 Twins 實例，或在現有的 Azure 數位 Twins 實例上啟用系統指派的身分識別。 
1. 將適當的角色或角色新增至身分識別。 例如，如果端點是事件中樞，請將 **Azure 事件中樞資料** 傳送者角色指派給身分識別，如果端點是服務匯流排，則將 **Azure 服務匯流排資料** 傳送者角色。
1. 在 Azure 數位 Twins 中建立可使用系統指派的身分識別來進行驗證的端點。

## <a name="enable-system-managed-identities-for-an-instance"></a>針對實例啟用系統管理的身分識別 

當您在 Azure 數位 Twins 實例上啟用系統指派的身分識別時，Azure 會在 [Azure Active Directory (Azure AD) ](../active-directory/fundamentals/active-directory-whatis.md)中自動為其建立身分識別。 接著，該身分識別就可以用來向 Azure 數位 Twins 端點驗證事件轉送。

您可以為 Azure 數位 Twins 實例啟用系統管理的身分識別， **作為實例初始設定的一部分**，或 **稍後在已經存在的實例上啟用**。

這兩種建立方法都會提供相同的設定選項和您實例的相同最終結果。 本節說明如何進行這兩項作業。

### <a name="add-a-system-managed-identity-during-instance-creation"></a>在實例建立期間新增系統管理的身分識別

在本節中，您將瞭解如何在目前建立的 Azure 數位 Twins 實例上啟用系統管理的身分識別。 本節著重于建立程式的受控識別步驟;如需建立新 Azure 數位 Twins 實例的完整逐步解說，請參閱 how [*to：設定實例和驗證*](how-to-set-up-instance-portal.md)。

系統管理的身分識別選項位於實例設定的 [ **Advanced** ] 索引標籤中。

在此索引標籤中，選取 [**系統管理** 的身分識別] 的 [**開啟**] 選項，以開啟此功能。

:::image type="content" source="media/how-to-enable-managed-identities/create-instance-advanced.png" alt-text="Azure 入口網站的螢幕擷取畫面，其中顯示 Azure 數位 Twins 的 [建立資源] 對話方塊的 [Advanced] 索引標籤。索引標籤名稱、系統管理的身分識別的開啟選項，以及導覽按鈕 (複習 + 建立、上一個、下一個： Advanced) ，都有一個醒目提示。":::

然後，您可以使用下方瀏覽按鈕繼續進行其餘的實例設定。

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>將系統管理的身分識別新增至現有的實例

在本節中，您將使用 [Azure 入口網站](https://portal.azure.com) 將系統管理的身分識別新增至已存在的 Azure 數位 Twins 實例。

1. 首先，在瀏覽器中流覽至 [Azure 入口網站](https://portal.azure.com) 。

1. 在入口網站的搜尋列中搜尋您的實例名稱，然後選取它以查看其詳細資料。

1. 在左側功能表中，選取 [身分 **識別 (預覽])** 。

1. 在此頁面上，選取 [ **開啟** ] 選項以開啟此功能。

1. 點擊 [ **儲存** **]** 按鈕，然後確認。

    :::image type="content" source="media/how-to-enable-managed-identities/identity-digital-twins.png" alt-text="Azure 入口網站的螢幕擷取畫面，其中顯示 Azure 數位 Twins 實例的身分識別 (預覽) 頁面。[Azure 數位 Twins 實例] 功能表中的頁面名稱周圍有一個醒目提示，[狀態]、[儲存] 按鈕和 [是確認] 按鈕的 [開啟] 選項。":::

儲存變更之後，此頁面上將會顯示更多欄位，以取得新身分識別的 **物件識別碼** 和 **許可權**。

如有需要，您可以從這裡複製 **物件識別碼** ，並使用 [ **許可權** ] 按鈕來查看指派給身分識別的 Azure 角色。 若要設定某些角色，請繼續下一節。

## <a name="assign-azure-roles-to-the-identity"></a>將 Azure 角色指派給身分識別 

為您的 Azure 數位 Twins 實例建立系統指派的身分識別之後，您必須為其指派適當的角色，以使用不同類型的 [端點](concepts-route-events.md) 進行驗證，以便將事件轉送至支援的目的地。 本節說明角色選項，以及如何將它們指派給系統指派的身分識別。

>[!NOTE]
> 這是一個很重要的步驟，如果沒有，身分識別就無法存取您的端點，而且也不會提供事件。

### <a name="supported-destinations-and-azure-roles"></a>支援的目的地和 Azure 角色 

以下是身分識別存取端點所需的最小角色，視目的地類型而定。 具有較高許可權的角色 (如資料擁有者角色) 也可運作。

| Destination | Azure 角色 |
| --- | --- |
| Azure 事件中心 | Azure 事件中樞資料傳送者 |
| Azure 服務匯流排 | Azure 服務匯流排資料傳送者 |
| Azure 儲存體容器 | 儲存體 Blob 資料參與者 |

如需端點、路由和 Azure 數位 Twins 中支援路由的目的地類型的詳細資訊，請參閱 [*概念：事件路由*](concepts-route-events.md)。

### <a name="assign-the-role"></a>指派角色

>[!NOTE]
> 此區段必須由具有管理使用者對 Azure 資源存取權的 Azure 使用者完成， (包括授與並委派) 的許可權。 符合這項需求的通用角色是 *擁有* 者、 *帳戶管理員*，或是 *使用者存取系統管理員* 和 *參與者* 的組合。 如需 Azure 數位 Twins 角色之許可權需求的詳細資訊，請參閱 how [*to：設定實例和驗證*](how-to-set-up-instance-portal.md#prerequisites-permission-requirements)。

若要將角色指派給身分識別，請從開啟 [Azure 入口網站](https://portal.azure.com)開始。

1. 在入口網站的搜尋列中搜尋其名稱，以流覽至您的端點資源 (您的事件中樞、服務匯流排主題或儲存體容器) 。 
1. 選取左側功能表中的 [ **存取控制] (IAM)** 。
1. 選取 [ **+ 新增** ] 按鈕以新增角色指派。

    :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-1.png" alt-text="Azure 入口網站的螢幕擷取畫面，顯示事件中樞的 [存取控制] (IAM) 頁面。[+ 新增] 按鈕會反白顯示。" lightbox="media/how-to-enable-managed-identities/add-role-assignment-1.png":::

1. 在 [ **新增角色指派** ] 頁面上，填入下列值：
    * **角色**：從下拉式功能表中選取所需的角色
    * **指派存取權給**：選擇 **使用者、群組或服務主體**
    * **選取**：在此，您將選取要指派角色的 Azure 數位 Twins 實例受控識別。 受控識別的名稱符合實例的名稱，因此請搜尋 Azure 數位 Twins 實例的名稱。 當您選取結果時，實例的身分識別就會顯示在 [ **選取的成員** ] 區段中。

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-2.png" alt-text="將列出的欄位填入 [新增角色指派] 對話方塊":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

當您完成輸入詳細資料時，請選取 [ **儲存**]。

## <a name="create-an-endpoint-with-identity-based-authorization"></a>使用以身分識別為基礎的授權來建立端點

為您的 Azure 數位 Twins 實例設定系統管理的身分識別，並為其指派適當的角色 () ，您可以建立能夠使用身分識別進行驗證的 Azure 數位 Twins [端點](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) 。 此選項只適用于「事件中樞」和「服務匯流排類型」端點， (事件方格) 不支援此選項。

>[!NOTE]
> 您無法編輯已使用以金鑰為基礎的身分識別所建立的端點，以變更為以身分識別為基礎的驗證。 第一次建立端點時，您必須選擇驗證類型。

遵循 [指示來建立 Azure 數位 Twins 端點](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins)。

當您到達完成端點類型所需詳細資料的步驟時，請務必針對驗證類型選取 [以身分 **識別為基礎** ]。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="建立事件中樞類型端點的螢幕擷取畫面。" lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

完成端點的設定，然後選取 [ **儲存**]。

## <a name="considerations-for-disabling-system-managed-identities"></a>停用系統管理的身分識別的考慮

因為身分識別是與使用它的端點分開管理，所以請務必考慮對身分識別或其角色所做的任何變更，都可能對您 Azure 數位 Twins 實例中的端點有何影響。 如果身分識別已停用，或已移除端點的必要角色，則端點可能會變成無法存取，而且會中斷事件的流程。

若要繼續使用現在已停用受控識別設定的端點，您必須刪除該端點，並使用不同的驗證類型 [重新建立](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) 。 在這次變更之後，事件可能需要一小時的時間才能繼續傳遞至端點。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure AD 中的受控識別： 
* [*適用於 Azure 資源的受控識別*](../active-directory/managed-identities-azure-resources/overview.md)