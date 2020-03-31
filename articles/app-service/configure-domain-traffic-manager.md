---
title: 使用流量管理器配置 DNS 名稱
description: 瞭解如何為 Azure 應用服務應用配置自訂域，該應用應用與流量管理器集成以進行負載平衡。
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: f8322c12669e41fc7c9aa88e99f95cf1b26ea87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944146"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>通過流量管理器集成在 Azure 應用服務中配置自訂功能變數名稱

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> 若為雲端服務，請參閱[設定 Azure 雲端服務的自訂網域名稱](../cloud-services/cloud-services-custom-domain-name.md)。

當您使用[Azure 流量管理器](/azure/traffic-manager/)將平衡流量載入到[Azure 應用服務](overview.md)時，可以使用**\<流量管理器-終結點>.流量管理器.net**訪問應用服務應用。 您可以使用應用服務應用分配自訂功能變數名稱（如 www\.contoso.com，以便為使用者提供更可識別的功能變數名稱。

本文介紹如何使用與[流量管理器](../traffic-manager/traffic-manager-overview.md)集成的應用服務應用配置自訂功能變數名稱。

> [!NOTE]
> 使用流量管理器終結點配置功能變數名稱時，僅支援[CNAME](https://en.wikipedia.org/wiki/CNAME_record)記錄。 由於不支援記錄，因此也不支援根域映射（如contoso.com。
> 

## <a name="prepare-the-app"></a>準備應用程式

要將自訂 DNS 名稱映射到與 Azure 流量管理器集成的應用，Web 應用[的應用服務方案](https://azure.microsoft.com/pricing/details/app-service/)必須位於**標準**層或更高級別。 在此步驟中，您要確定 App Service 應用程式位於支援的定價層。

### <a name="check-the-pricing-tier"></a>檢查定價層

在[Azure 門戶](https://portal.azure.com)中，搜索並選擇**應用服務**。

在 [應用程式服務]**** 頁面上，選取您的 Azure 應用程式名稱。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/select-app.png)

在應用頁面的左側導航中，選擇 **"向上縮放"（應用服務方案）。**

![相應增加功能表](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

會以藍色框線醒目顯示應用程式目前的層。 檢查以確保應用位於**標準**層或以上（**生產**或**隔離**類別中的任何層）。 如果是，請關閉 **"向上縮放**"頁並跳過以創建[CNAME 映射](#create-the-cname-mapping)。

![檢查定價層](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>相應增加 App Service 方案

如果需要擴展應用，請選擇 **"生產**"類別中的任何定價層。 如需其他選項，請按一下 [查看其他選項]****。

按一下 [套用]****。

## <a name="create-traffic-manager-endpoint"></a>創建流量管理器終結點

按照["添加或刪除終結點](../traffic-manager/traffic-manager-endpoints.md)"中的步驟，將應用服務應用添加為流量管理器設定檔中的終結點。

應用服務應用處于受支援的定價層後，在添加終結點時，該應用將顯示在可用應用服務目標清單中。 如果應用未列出，[請驗證應用的定價層](#prepare-the-app)。

## <a name="create-the-cname-mapping"></a>創建 CNAME 映射
> [!NOTE]
> 要配置[您購買的應用服務域，](manage-custom-dns-buy-domain.md)請跳過此部分，然後轉到[啟用自訂域](#enable-custom-domain)。
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

雖然每個域供應商的具體細節各不相同，但您*從*與應用集成的流量管理器功能變數名稱（如**contoso.com）***映射到*流量管理器功能變數名稱 **（contoso.trafficmanager.net）。**

> [!NOTE]
> 如果記錄已在使用中，而您需要事先將您的應用程式繫結到該記錄，您可以建立其他的 CNAME 記錄。 例如，要先發制人地將**\.www contoso.com**綁定到你的應用，請創建從**awverify.www**到**contoso.trafficmanager.net**的 CNAME 記錄。 然後，您可以將"www\.contoso.com"添加到應用，而無需更改"www"CNAME 記錄。 有關詳細資訊，請參閱[將活動 DNS 名稱遷移到 Azure 應用服務](manage-custom-dns-migrate-domain.md)。

在網域提供者處完成 DNS 記錄的新增或修改後，請儲存變更。

## <a name="enable-custom-domain"></a>啟用自訂域
功能變數名稱記錄傳播後，請使用瀏覽器驗證自訂功能變數名稱是否解析為應用服務應用。

> [!NOTE]
> 需要一些時間，CNAME 才能傳播至整個 DNS 系統。 您可以使用服務，例如<a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a>驗證 CNAME 是否可用。
> 
> 

1. 域解析成功後，返回[Azure 門戶](https://portal.azure.com)中的應用頁面
2. 從左側導航中，選擇 **"自訂域** > **添加主機名稱**"。
4. 鍵入您之前映射的自訂功能變數名稱，然後選擇 **"驗證**"。
5. 確保**主機名稱記錄類型**設置為**CNAME（www\.example.com 或任何子域）。**

6. 由於應用服務應用現在與流量管理器終結點集成，因此應在**CNAME 配置**下看到流量管理器功能變數名稱。 選擇它，然後按一下 **"添加自訂域**"。

    ![將 DNS 名稱新增至應用程式](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 Azure App Service 中使用 SSL 繫結保護自訂 DNS 名稱](configure-ssl-bindings.md)
