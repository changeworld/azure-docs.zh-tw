---
title: 購買自訂功能變數名稱
description: 瞭解如何購買 App Service 網域，並使用它作為應用程式 Azure App Service 的自訂網域。
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/30/2020
ms.custom: seodec18
ms.openlocfilehash: 6f0ff7a54c2ad1fa1af649c8082498b442783c7e
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96607973"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>針對 Azure App Service 購買自訂網域名稱

App Service 網域是直接在 Azure 中管理的自訂網域。 它們可以讓 [Azure App Service](overview.md) 的自訂網域管理作業變得很簡單。 本教學課程會示範如何購買 App Service 網域，並將 DNS 名稱指派給 Azure App Service。

若為 Azure VM 或 Azure 儲存體，請參閱[將 App Service 網域指派給 Azure VM 或 Azure 儲存體](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage)。 若為雲端服務，請參閱[設定 Azure 雲端服務的自訂網域名稱](../cloud-services/cloud-services-custom-domain-name-portal.md)。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

* [建立 App Service 應用程式](./index.yml)，或使用您針對另一個教學課程建立的應用程式。 應用程式應該在 Azure 公用區域中。 目前不支援 Azure 國家/地區雲端。
* [移除訂用帳戶的消費限制](../cost-management-billing/manage/spending-limit.md#remove)。 您無法使用免費的訂用帳戶信用額度購買 App Service 網域。

## <a name="buy-an-app-service-domain"></a>購買 App Service 網域

如需 App Service 網域的定價資訊，請造訪 [App Service 定價頁面](https://azure.microsoft.com/pricing/details/app-service/windows/) ，並向下向下 App Service 網域。

1. 開啟 [Azure 入口網站](https://portal.azure.com)並使用您的 Azure 帳戶登入。

1. 在搜尋列中，搜尋並選取 **App Service 網域**。

    ![入口網站流覽至 Azure App Service 網域](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. 在 [ **App Service 網域** ] 視圖中，按一下 [ **新增**]。

    ![按一下 App Service 網域中的 [新增]](./media/app-service-web-tutorial-custom-domain/add-app-service-domain.png)

1. 選取 [ **按一下] 以試用 App Service 網域建立體驗的較新版本**。

    ![使用新體驗建立 App Service 網域](./media/app-service-web-tutorial-custom-domain/select-new-create-experience.png)

### <a name="basics-tab"></a>[基本] 索引標籤

1. 在 [ **基本** ] 索引標籤中，使用下表設定設定：  

   | 設定  | 描述 |
   | -------- | ----------- |
   | **訂用帳戶** | 用來購買網域的訂用帳戶。 |
   | **資源群組** | 要放置網域的資源群組。 例如，您的應用程式所在的資源群組。 |
   | **網域** | 輸入您想要的網域。 例如， **contoso.com**。 如果您想要的網域無法使用，您可以從可用網域的建議清單中選取，或嘗試不同的網域。 |

    > [!NOTE]
    > App Service 網域支援下列 [頂層網域](https://wikipedia.org/wiki/Top-level_domain)：_com_、_net_、_co.uk_、_org_、_nl_、_in_、_biz_、_org.uk_，以及 _co.in_。
    >
    >
    
2. 完成時，請按 **[下一步：連絡人資訊]**。

### <a name="contact-information-tab"></a>連絡人資訊索引標籤

1. 依 [>icann](https://go.microsoft.com/fwlink/?linkid=2116641) 要求提供網域註冊所需的資訊。 

    請務必填寫所有必要欄位，並盡可能正確填寫。 連絡人資訊的資料不正確可能會導致無法購買網域。

1. 完成時，請按 **[下一步： Advanced]**。

### <a name="advanced-tab"></a>進階索引標籤

1. 在 [ **Advanced （Advanced** ）] 索引標籤中，設定選用設定：  

   | 設定  | 描述 |
   | -------- | ----------- |
   | **自動續約** | 預設為啟用。 您 App Service 網域會以一年的增量註冊給您。 自動更新可確保您的網域註冊不會過期，而且會保留網域的擁有權。 續訂時，您的 Azure 訂用帳戶會自動收取年度網域註冊費用。 若要退出，請選取 [ **停** 用]。 如果已停用自動更新，您可以 [手動更新](#renew-the-domain)。 |
   | **隱私權保護** | 預設為啟用。 隱私權保護會從 WHOIS 資料庫中隱藏您的網域註冊連絡人資訊。 「隱私權保護」已包含在「年度網域註冊」費用中。 若要退出，請選取 [ **停** 用]。 |

2. 完成時，請按 **[下一步：標記]**。

### <a name="finish"></a>[完成]

1. 在 [ **標記** ] 索引標籤中，設定您要用於 App Service 網域的標記。 使用 App Service 網域不需要標記，但這是 [Azure 中的一項功能，可協助您管理資源](../azure-resource-manager/management/tag-resources.md)。

1. 按 **[下一步]： [檢查 + 建立]**。

1. 在 [ **審核 + 建立** ] 索引標籤中，檢查您的網域訂單。 完成後，按一下 [建立]。

    > [!NOTE]
    > App Service 網域會使用 GoDaddy 來註冊網域，並使用 Azure DNS 來裝載網域。 除了年度網域註冊費用之外，Azure DNS 適用的使用費用。 如需相關資訊，請參閱 [Azure DNS 定價](https://azure.microsoft.com/pricing/details/dns/)。
    >
    >

1. 網域註冊完成時，您會看到 [ **移至資源** ] 按鈕。 選取它以查看其管理頁面。

    ![已建立 App Service 網域。 前往資源](./media/app-service-web-tutorial-custom-domain/deployment-complete.png)

您現在已經準備好將 App Service 應用程式指派給此自訂網域。

## <a name="prepare-the-app"></a>準備應用程式

若要將自訂 DNS 名稱對應至 Web 應用程式，Web 應用程式的 [App Service 方案](https://azure.microsoft.com/pricing/details/app-service/)必須是付費層 (若為 Azure Functions，則為 [共用]、[基本]、[標準]、[進階] 或 [取用])。 在此步驟中，您要確定 App Service 應用程式位於支援的定價層。

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="navigate-to-the-app-in-the-azure-portal"></a>瀏覽至 Azure 入口網站中的應用程式

1. 在頂端搜尋列中，搜尋並選取 [ **應用程式服務**]。

    ![搜尋應用程式服務](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. 選取應用程式的名稱。

    ![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/select-app.png)

    您看到 App Service 應用程式的管理分頁。  

### <a name="check-the-pricing-tier"></a>檢查定價層

1. 在應用程式分頁的左側導覽中，捲動到 [設定] 區段，然後選取 [擴大 (App Service 方案)]。

    ![相應增加功能表](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. 會以藍色框線醒目顯示應用程式目前的層。 請檢查以確定您的應用程式不是位於 **F1** 層。 **F1** 層不支援自訂 DNS。 

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="選取 [擴大] (App Service 方案) 的 [應用程式] 頁面左側導覽功能表的螢幕擷取畫面。":::

1. 如果 App Service 方案不是位於 **F1** 層，請關閉 [相應增加] 頁面，然後跳至 [[購買網域]](#buy-an-app-service-domain)。

### <a name="scale-up-the-app-service-plan"></a>擴大 App Service 方案

1. 選取任何非免費層 (**D1**、**B1**、**B2**、**B3** 或「生產」類別中的任何一層)。 如需其他選項，請按一下 [查看其他選項]  。

1. 按一下 [套用]。

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="[生產] 類別目錄中自訂網域定價層的螢幕擷取畫面，其中已醒目提示 [執行] 索引標籤、B1 方案和 [套用] 按鈕。":::

    當您看見下列通知時，表示擴充作業已完成。

    ![擴充作業確認](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="map-app-service-domain-to-your-app"></a>將 App Service 網域對應至您的應用程式

只要在相同的訂用帳戶中，您就可以輕鬆地將 App Service 網域中的主機名稱對應至 App Service 應用程式。 您可以直接在應用程式中對應 App Service 網域或其任何子域，而 Azure 會為您建立必要的 DNS 記錄。

> [!NOTE]
> 如果網域和應用程式位於不同的訂用帳戶中，您可以將 App Service 網域對應到應用程式，就像 [對應外部購買的網域](app-service-web-tutorial-custom-domain.md)一樣。 在此情況下，Azure DNS 是外部網域提供者，而且您需要 [手動新增必要的 DNS 記錄](#manage-custom-dns-records)。
>

### <a name="map-the-domain"></a>對應網域

1. 在應用程式頁面的左側導覽中，移至 [ **設定** ] 區段，然後選取 [ **自訂網域**]。

    ![顯示 [自訂網域] 功能表的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. 選取 [新增自訂網域]。

    ![顯示 [新增主機名稱] 項目的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. 輸入 App Service 網域 (例如 **contoso.com**) 或子域 (例如 **www.contoso.com**) ，然後按一下 [ **驗證**]。

    > [!NOTE]
    > 如果您在 App Service 功能變數名稱中進行了打字錯誤，頁面底部會出現驗證錯誤，告訴您遺漏某些 DNS 記錄。 您不需要針對 App Service 網域手動新增這些記錄。 只要確定您輸入的功能變數名稱正確，再按一下 [ **驗證** ] 即可。
    >
    > ![顯示驗證錯誤的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

1. 接受 [ **主機名稱] 記錄類型** ，然後按一下 [ **新增自訂網域**]。

    ![顯示 [新增自訂網域] 按鈕的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

1. 可能需要一些時間，新的自訂網域才會反映在應用程式的 [自訂網域] 頁面中。 重新整理瀏覽器以更新資料。

    ![顯示新增 CNAME 記錄的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > 您自訂網域的 **不安全** 標籤表示它尚未系結至 TLS/SSL 憑證。 任何從瀏覽器到自訂網域的 HTTPS 要求都會收到錯誤或警告 (視瀏覽器而定)。 若要新增 TLS 繫結，請參閱[在 Azure App Service 中使用 TLS/SSL 繫結保護自訂 DNS 名稱](configure-ssl-bindings.md)。
    
### <a name="test-the-custom-domain"></a>測試自訂網域

若要測試自訂網域，請在瀏覽器中流覽至該網域。

## <a name="renew-the-domain"></a>續訂網域

您購買的 App Service 網域自購買起一年內有效。 按照預設，網域會設定為自動續訂下一年度，並使用您的付款方式付費。 您可以手動更新功能變數名稱。

如果您想要關閉自動續訂，或想要以手動方式續訂網域，請依照下列步驟進行。

1. 在搜尋列中，搜尋並選取 **App Service 網域**。

    ![入口網站流覽至 Azure App Service 網域](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. 在 [App Service 網域] 區段中，選取您想要設定的網域。

1. 從網域的左側導覽中，選取 [網域續訂]。 若要停止自動更新網域，請選取 [ **關閉**]。 此設定會立即生效。

    ![顯示自動更新網域之選項的螢幕擷取畫面。](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

    > [!NOTE]
    > 離開頁面時，請按一下 **[確定]**，以忽略 [將捨棄未儲存的編輯] 錯誤。
    >

若要以手動方式續訂您的網域，請選取 [續訂網域]。 不過， [在網域到期前的90天之前](#when-domain-expires)，此按鈕不會處於作用中狀態。

如果您的網域更新成功，您會在24小時內收到電子郵件通知。

## <a name="when-domain-expires"></a>網域到期時

Azure 會處理過期或過期的 App Service 網域，如下所示：

* 如果已停用自動更新：網域到期前的90天，則會傳送更新通知電子郵件給您，並在入口網站中啟用 [ **更新網域** ] 按鈕。
* 如果已啟用自動更新：在您的網域到期日之後，Azure 會嘗試向您收取功能變數名稱更新的費用。
* 如果自動更新期間發生錯誤 (例如，您的檔案上的卡片) 到期，或自動更新已停用且您允許網域過期，則 Azure 會通知您網域到期並將您的功能變數名稱停用。 您可以 [手動更新](#renew-the-domain) 網域。
* 在第4天和第12天到期之後，Azure 會傳送額外的通知電子郵件給您。 您可以 [手動更新](#renew-the-domain) 網域。
* 在到期後的19天，您的網域仍維持在保存狀態，但會受限於兌換費用。 您可以致電客戶支援人員以更新您的功能變數名稱，並受限於任何適用的續約和兌換費用。
* 在第25天到期之後，Azure 會讓您的網域與功能變數名稱產業拍賣服務一起進行拍賣。 您可以致電客戶支援人員以更新您的功能變數名稱，並受限於任何適用的續約和兌換費用。
* 到期後的30天內，您就無法再兌換您的網域。

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>管理自訂 DNS 記錄

在 Azure 中，App Service 網域的 DNS 記錄是使用 [Azure DNS](https://azure.microsoft.com/services/dns/) 來管理。 和針對外部購買的網域一樣，您可以新增、移除及更新 DNS 記錄。 管理自訂 DNS 記錄：

1. 在搜尋列中，搜尋並選取 **App Service 網域**。

    ![入口網站流覽至 Azure App Service 網域](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. 在 [App Service 網域] 區段中，選取您想要設定的網域。

1. 在 [ **總覽** ] 頁面中，選取 [ **管理 DNS 記錄**]。

    ![顯示何處可以存取 DNS 記錄的螢幕擷取畫面。](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

如需如何編輯 DNS 記錄的相關資訊，請參閱[如何在 Azure 入口網站中管理 DNS 區域](../dns/dns-operations-dnszones-portal.md)。

## <a name="cancel-purchase-delete-domain"></a>取消購買 (刪除網域)

在您購買 App Service 網域之後，可以在五天內取消購買並獲得全額退費。 您也可以在五天之後刪除 App Service 網域，但無法收到退款。

1. 在搜尋列中，搜尋並選取 **App Service 網域**。

    ![入口網站流覽至 Azure App Service 網域](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. 在 [App Service 網域] 區段中，選取您想要設定的網域。

1. 在網域的左側導覽中，選取 [ **主機名稱** 系結]。 這裡會列出來自所有 Azure 服務的主機名稱繫結。

    ![顯示主機名稱系結頁面的螢幕擷取畫面。](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

1. 選取 [...]，刪除每個主機名稱系結 **...**  > **Delete**。 刪除所有繫結之後，請選取 [儲存]。

    <!-- ![Screenshot that shows where to delete the hostname bindings.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png) -->

1. 在網域的左側導覽中，選取 **[總覽**]。 

1. 如果已購買網域的取消期間尚未到期，請選取 [取消購買]。 否則，您會看到 [刪除] 按鈕。 若要在不退款情況下刪除網域，請選取 [刪除]。

    ![顯示刪除或取消已購買網域之位置的螢幕擷取畫面。](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

1. 選取 [ **是]** 以確認操作。

    作業完成之後，該網域就會從您的訂用帳戶中釋放，並可再次供任何人購買。 

## <a name="direct-default-url-to-a-custom-directory"></a>將預設 URL 導向自訂目錄

根據預設，App Service 會將 Web 要求導向應用程式程式碼的根目錄。 若要將它們導向子目錄（例如 `public` ），請參閱重新 [導向至自訂目錄](app-service-web-tutorial-custom-domain.md#redirect-to-a-custom-directory)。

## <a name="next-steps"></a>後續步驟

瞭解如何將自訂的 TLS/SSL 憑證系結至 App Service。

> [!div class="nextstepaction"]
> [使用 Azure App Service 中的 TLS 系結來保護自訂 DNS 名稱](configure-ssl-bindings.md)
