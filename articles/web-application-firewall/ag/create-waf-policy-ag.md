---
title: 建立應用程式閘道的 Web 應用程式防火牆（WAF）原則
description: 瞭解如何建立應用程式閘道的 Web 應用程式防火牆原則。
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: 3e8cd2f1e594cd6a60296b2df135f275641df313
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086974"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>建立應用程式閘道的 Web 應用程式防火牆原則

將 WAF 原則與接聽程式產生關聯，可讓單一 WAF 背後的多個網站受到不同原則的保護。 例如，如果您的 WAF 背後有五個網站，您可以有五個不同的 WAF 原則（每個接聽程式各一個），以自訂一個網站的排除專案、自訂規則和受控規則集，而不會影響其他四個。 如果您想要將單一原則套用至所有網站，您可以只將原則與應用程式閘道（而不是個別的接聽程式）建立關聯，使其成為全域適用。 原則也可以套用至以路徑為基礎的路由規則。 

您可以視需要建立任意數量的原則。 一旦您建立原則，它必須與應用程式閘道相關聯，才會生效，但它可以與應用程式閘道和接聽項的任何組合相關聯。 

如果您的應用程式閘道已套用原則，然後將不同的原則套用至該應用程式閘道上的接聽程式，則接聽程式的原則會生效，但只適用于指派給它們的接聽程式。 應用程式閘道原則仍然會套用至未獲指派特定原則的所有其他接聽程式。 

   > [!NOTE]
   > 以每個網站和每個 URI 為主的 WAF 原則都處於公開預覽狀態。 這表示此功能受限於 Microsoft 的補充使用規定。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

所有新的 Web 應用程式防火牆的 WAF 設定（自訂規則、受管理的 rulset 設定、排除等等）都是在 WAF 原則內部運作。 如果您有現有的 WAF，這些設定可能仍存在於您的 WAF config 中。如需如何移至新 WAF 原則的步驟，請參閱本文稍後的將[您的 WAF Config 遷移至 WAF 原則](#migrate)。 

## <a name="create-a-policy"></a>建立原則

首先，使用 Azure 入口網站，建立具有受控預設規則集（DRS）的基本 WAF 原則。

1. 在入口網站的左上方，選取 [**建立資源**]。 搜尋**WAF**，選取 [ **Web 應用程式防火牆**]，然後選取 [**建立**]。
2. 在 [**建立 WAF 原則**] 頁面的 [**基本**] 索引標籤上，輸入或選取下列資訊、接受其餘設定的預設值，然後選取 [**檢查 + 建立**]：

   |設定  |值  |
   |---------|---------|
   |的原則     |區域 WAF （應用程式閘道）|
   |訂用帳戶     |選取您的訂用帳戶名稱|
   |資源群組     |選取您的資源群組|
   |原則名稱     |輸入 WAF 原則的唯一名稱。|
3. 在 [**關聯**] 索引標籤上，輸入下列其中一項設定，然後選取 [**新增**]：

   |設定  |值  |
   |---------|---------|
   |關聯應用程式閘道     |選取您的應用程式閘道設定檔名稱。|
   |關聯接聽程式     |選取您應用程式閘道接聽程式的名稱，然後選取 [**新增**]。|

   > [!NOTE]
   > 如果您將原則指派給已有原則的應用程式閘道（或接聽程式），則會覆寫原始原則，並將其取代為新的原則。
4. 選取 [檢閱 + 建立]，然後選取 [建立]。

   ![WAF 原則基本概念](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>設定 WAF 規則（選擇性）

當您建立 WAF 原則時，預設會處於*偵測*模式。 在偵測模式中，WAF 不會封鎖任何要求。 相反地，相符的 WAF 規則會記錄在 WAF 記錄中。 若要查看作用中的 WAF，您可以將模式設定變更為 [*預防*]。 在預防模式中，您選取的 CRS 規則集內所定義的比對規則會被封鎖及/或記錄在 WAF 記錄中。

## <a name="managed-rules"></a>受控規則

預設會啟用 Azure 管理的 OWASP 規則。 若要停用規則群組中的個別規則，請展開該規則群組中的規則，選取規則編號前面的核取方塊，然後選取上方索引標籤上的 [**停**用]。

[![受控規則](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>自訂規則

若要建立自訂規則，請選取 [自訂**規則**] 索引標籤底下的 [**新增自訂規則**]這會開啟 [自訂規則設定] 頁面。 下列螢幕擷取畫面顯示設定為在查詢字串包含文字*blockme*時封鎖要求的範例自訂規則。

[![編輯自訂規則](../media/create-waf-policy-ag/edit-custom-rule.png)](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate"></a>將您的 WAF Config 遷移至 WAF 原則

如果您有現有的 WAF，您可能已注意到入口網站中有一些變更。 首先，您必須識別您已在 WAF 上啟用的原則類型。 有三種可能的狀態：

- 沒有 WAF 原則
- 僅限自訂規則原則
- WAF 原則

您可以藉由在入口網站中查看 WAF 來判斷您的狀態。 如果 [WAF] 設定是可見的，而且可以從 [應用程式閘道] 視圖中變更，則您的 WAF 會處於 [狀態 1]。

[![WAF configuration](../media/create-waf-policy-ag/waf-configure.png)](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

如果您選取 [ **Web 應用程式防火牆**]，它會顯示相關聯的原則，表示 WAF 處於狀態2或狀態3。 導覽至原則之後，如果**只**顯示自訂規則和相關聯的應用程式閘道，則它是僅限自訂規則的原則。

![WAF 自訂規則](../media/create-waf-policy-ag/waf-custom-rules.png)

如果它也顯示原則設定和受控規則，則是完整的 Web 應用程式防火牆原則。 

![WAF 原則設定](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>遷移至 WAF 原則

如果您有僅 WAF 原則的自訂規則，您可能會想要移至新的 WAF 原則。 接下來，防火牆原則將支援 WAF 原則設定、受控規則集、排除專案，以及停用的規則群組。 基本上，先前在應用程式閘道內完成的所有 WAF 設定，現在都是透過 WAF 原則完成。 

只會停用自訂規則的 WAF 原則編輯。 若要編輯任何 WAF 設定，例如停用規則、新增排除專案等等，您必須遷移至新的最上層防火牆原則資源。

若要這麼做，請建立*Web 應用程式防火牆原則*，並將它與您所選擇的應用程式閘道和接聽程式產生關聯。 這個新原則**必須**與目前的 WAF 設定完全相同，這表示每個自訂規則、排除、停用規則等都必須複製到您要建立的新原則中。 一旦有與應用程式閘道相關聯的原則之後，您就可以繼續對 WAF 規則和設定進行變更。 您也可以使用 Azure PowerShell 來執行此動作。 如需詳細資訊，請參閱[建立 WAF 原則與現有應用程式閘道的關聯](associate-waf-policy-existing-gateway.md)。

（選擇性）您可以使用遷移腳本來遷移至 WAF 原則。 如需詳細資訊，請參閱[使用 Azure PowerShell 遷移 Web 應用程式防火牆原則](migrate-policy.md)。

## <a name="next-steps"></a>後續步驟

深入瞭解[Web 應用程式防火牆 CRS 規則群組與規則](application-gateway-crs-rulegroups-rules.md)。
