---
title: 使用 Azure 入口網站在 Azure 事件方格夥伴上架
description: 使用 Azure 入口網站來讓 Azure 事件方格夥伴上線。
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 36fab35923b8a536a9054e5dc4bfa4c5b82172a7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102841"
---
# <a name="onboard-as-an-azure-event-grid-partner-using-the-azure-portal"></a>使用 Azure 入口網站在 Azure 事件方格夥伴上架
本文說明協力廠商 SaaS 提供者（也稱為「事件發行者」或「夥伴」）上線至「事件方格」的方式，以便從服務發佈事件，以及使用者如何使用這些事件。

> [!IMPORTANT]
> 如果您不熟悉合作夥伴事件，請參閱 [合作夥伴事件總覽](partner-events-overview.md) ，以取得了解重要概念的詳細介紹，並遵循本文中的步驟。

## <a name="onboarding-process-for-event-publishers-partners"></a> (合作夥伴) 的事件發行者上架程式
簡而言之，讓使用者使用您的服務事件時，通常會涉及下列程式：

1. 在繼續進行後續步驟之前，將您對成為事件方格服務小組的夥伴 **感興趣** 。
1. 藉由建立 **註冊** 來建立合作夥伴主題類型。 
1. 建立 **命名空間** 。
1.  (單一步驟) 建立 **事件通道** 和 **夥伴主題** 。
1. 端對端測試夥伴事件功能。

針對步驟 #4，您應該決定想要提供何種使用者體驗。 下列選項可供您選擇：
- 使用我們的 SDK 和/或 REST API 建立事件通道及其對應的夥伴主題，來提供您自己的解決方案，通常是 web 圖形化使用者介面， (GUI) 體驗。 使用這個選項，您可以要求使用者提供訂用帳戶和資源群組，您將在其中建立夥伴主題。
- 使用 Azure 入口網站或 CLI 來建立事件通道和相關聯的夥伴主題。 使用此選項時，您必須具有使用者的 Azure 訂用帳戶中的「取得」和「資源群組」，才能建立夥伴主題。 

本文說明如何使用 Azure 入口網站作為 Azure 事件方格夥伴上架。 

> [!NOTE]
> 註冊夥伴主題類型是選擇性步驟。 若要協助您決定是否應該建立夥伴主題類型，請參閱 [由事件發行者管理的資源](partner-events-overview.md#resources-managed-by-event-publishers)。

## <a name="communicate-your-interest-in-becoming-a-partner"></a>傳達您對成為合作夥伴的興趣
填寫 [此表單](https://aka.ms/gridpartnerform) ，並與事件方格小組聯絡 [GridPartner@microsoft.com](mailto:GridPartner@microsoft.com) 。 我們會與您對話，以提供合作夥伴事件使用案例、角色、上架程式、功能、定價等的詳細資訊。

## <a name="prerequisites"></a>Prerequisites
若要完成其餘步驟，請確定您有：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。
- Azure [租](../active-directory/develop/quickstart-create-new-tenant.md)使用者。

## <a name="register-a-partner-topic-type-optional"></a>註冊夥伴主題類型 (選用) 
1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從左側導覽窗格中選取 [ **所有服務** ]，然後在 [搜尋] 列中輸入 **事件方格夥伴註冊** ，然後選取它。 
1. 在 [ **事件方格夥伴註冊** ] 頁面上，選取工具列上的 [ **+ 新增** ]。 

    :::image type="content" source="./media/onboard-partner/add-partner-registration-link.png" alt-text="新增夥伴註冊連結":::
1. 在 [ **建立夥伴主題類型註冊-基本** ] 頁面上，輸入下列資訊： 
    1. 在 [ **專案詳細資料** ] 區段中，依照下列步驟執行：
        1. 選取您的 Azure **訂用帳戶** 。 
        1. 選取現有的 Azure **資源群組** ，或建立新的資源群組。 
    1. 在 [ **註冊詳細資料** ] 區段中，依照下列步驟執行：
        1. 在 [ **註冊名稱** ] 中，輸入註冊的名稱。 
        1. 在 [ **組織名稱** ] 中，輸入您的組織名稱。 
    1. 在 [ **夥伴資源類型** ] 區段中，輸入將在 [ **夥伴主題建立** ] 頁面上顯示之資源類型的詳細資料： 
        1. 針對 [ **夥伴資源類型名稱** ]，輸入資源類型的名稱。 這會是將在您的 Azure 訂用帳戶中建立的夥伴主題類型。 
        2. 在 [ **顯示名稱** ] 中，為合作夥伴主題輸入使用者易記的顯示名稱 (資源) 類型。 
        3. 輸入 **資源類型的描述** 。 
        4. 輸入 **案例的描述** 。 它應該解釋您的資源的合作夥伴主題可以使用的方式或案例。  

            :::image type="content" source="./media/onboard-partner/create-partner-registration-page.png" alt-text="新增夥伴註冊連結":::            
1. 選取頁面底部的 **[下一步：自訂服務]** 。 在 [ **建立夥伴註冊** ] 頁面的 [ **客戶服務** ] 索引標籤上，輸入訂閱者使用者將在事件來源發生問題時用來與您聯繫的資訊。
    1. 輸入 **電話號碼** 。
    1. 輸入電話號碼的 **分機** 號碼。
    1. 輸入支援網站 **URL** 。 
    
        :::image type="content" source="./media/onboard-partner/create-partner-registration-customer-service.png" alt-text="新增夥伴註冊連結":::        
1. 選取頁面底部的 **[下一步：標記]** 。 
1. 在 [ **標記** ] 頁面上，設定下列值。 
    1. 為想要新增的標記輸入 **名稱** 和 **值** 。 此為 **選用** 步驟。 
    1. 選取頁面底部的 [ **審核 + 建立** ]，以建立註冊 (夥伴主題類型) 。

## <a name="create-a-partner-namespace"></a>建立夥伴命名空間

1. 在 [Azure 入口網站中，從左側導覽功能表中選取 [ **所有服務** ]，然後在 [搜尋] 列中輸入 **事件方格夥伴命名空間** ，然後從清單中選取它。 
1. 在 [ **事件方格夥伴命名空間** ] 頁面上，選取工具列上的 [ **+ 新增** ]。 
    
    :::image type="content" source="./media/onboard-partner/add-partner-namespace-link.png" alt-text="新增夥伴註冊連結":::
1. 在 [ **建立夥伴命名空間-基本** ] 頁面上，指定下列資訊。
    1. 在 [ **專案詳細資料** ] 區段中，執行下列步驟： 
        1. 選取 Azure **訂** 用帳戶。
        1. 選取現有的 **資源群組** ，或建立資源群組。 
    1. 在 [ **命名空間詳細資料** ] 區段中，執行下列步驟：
        1. 輸入命名空間的 **名稱** 。 
        1. 選取命名空間的 **位置** 。 
    1. 在 [ **註冊詳細資料** ] 區段中，執行下列步驟，將命名空間與夥伴註冊相關聯。 
        1. 選取夥伴註冊所在的 **訂** 用帳戶。 
        1. 選取包含夥伴註冊的 **資源群組** 。 
        1. 從下拉式清單中選取 **夥伴註冊** 。
    1. 選取頁面底部的 **[下一步：標記]** 。

        :::image type="content" source="./media/onboard-partner/create-partner-namespace-basics-page.png" alt-text="新增夥伴註冊連結":::
1. 在 [ **標記** ] 頁面上，將標記新增 (選擇性) 。
    1. 為想要新增的標記輸入 **名稱** 和 **值** 。 此為 **選用** 步驟。
    1. 選取頁面底部的 [檢閱 + 建立]。         
1. 在 [ **審核 + 建立** ] 頁面上，檢查詳細資料，然後選取 [ **建立** ]。 

## <a name="create-an-event-channel"></a>建立事件通道
> [!IMPORTANT]
> 您必須向您的使用者要求 Azure 訂用帳戶、資源群組、位置和合作夥伴主題名稱，才能建立您的使用者將擁有及管理的夥伴主題。

1. 移至您所建立之命名空間的 [ **總覽** ] 頁面。 

    :::image type="content" source="./media/onboard-partner/partner-namespace-overview.png" alt-text="新增夥伴註冊連結":::
    partner-namespace-overview.png
1. 選取工具列上的 [ **+ 事件頻道** ]。 
1. 在 [ **建立事件通道-基本** ] 頁面上，指定下列資訊。 
    1. 在 [ **通道詳細資料** ] 區段中，執行下列步驟：
        1. 針對 [ **事件通道名稱** ]，輸入事件通道的名稱。 
        1. 輸入 **來源** 。 請參閱 [雲端事件1.0 規格](https://github.com/cloudevents/spec/blob/v1.0/spec.md#source-1) ，以瞭解適用于來源的適當值。 另請參閱 [此雲端事件架構範例](cloud-event-schema.md#sample-event-using-cloudevents-schema)。
        1. 輸入來源 (什麼？ ) 。
    1. 在 [ **目的地詳細資料** ] 區段中，輸入將為此事件通道建立之目的地夥伴主題的詳細資料。 
        1. 輸入將在其中建立夥伴主題的訂用帳戶 **識別碼** 。 
        1. 輸入將在其中建立夥伴主題資源的 **資源組名** 。 
        1. 輸入 **夥伴主題的名稱** 。 
    1. 選取頁面底部的 **[下一步：篩選]** 。 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-basics-page.png" alt-text="新增夥伴註冊連結":::
1. 在 [ **篩選** ] 頁面上加入篩選。 請執行下列步驟：
    1. 篩選每個事件的屬性。 只會傳遞符合所有篩選準則的事件。 最多可以指定25個篩選準則。 比較不區分大小寫。 用於篩選的有效索引鍵會根據事件架構而有所不同。 在下列範例中，、、 `eventid` `source` `eventtype` 和 `eventtypeversioin` 都可以用於索引鍵。 您也可以使用 `.` 做為嵌套運算子的資料裝載內的自訂屬性。 例如： `data` 、 `data.key` 、 `data.key1.key2` 。
    1. 選取頁面底部的 **[下一步：其他功能]** 。 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-filters-page.png" alt-text="新增夥伴註冊連結":::
        create-event-channel-filters-page.png
1. 在 [ **其他功能** ] 頁面上，您可以設定 **夥伴主題** 的 **到期時間** 和描述。 
    1. **到期時間** 是指如果客戶未啟用，則會自動刪除主題及其相關聯事件通道的時間。 如果未提供時間，則會使用預設值7天。 選取核取方塊，以指定您自己的到期時間。 
    1. 因為本主題是使用者不會建立的資源，所以 **描述** 可協助使用者瞭解本主題的本質。 如果未設定，則會提供一般描述。 選取核取方塊以設定您自己的夥伴主題描述。 
    1. 完成時，選取 [下一步:檢閱 + 建立]。 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-additional-features-page.png" alt-text="新增夥伴註冊連結":::
1. 在 [ **審核 + 建立** ] 上，檢查設定，然後選取 [ **建立** ] 以建立事件通道。 

## <a name="next-steps"></a>後續步驟
- [合作夥伴主題概觀](partner-topics-overview.md)
- [合作夥伴主題上架表單](https://aka.ms/gridpartnerform)
- [Auth0 合作夥伴主題](auth0-overview.md)
- [如何使用 Auth0 合作夥伴主題](auth0-how-to.md)
