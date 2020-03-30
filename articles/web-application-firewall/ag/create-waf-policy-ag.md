---
title: 為應用程式閘道創建 Web 應用程式防火牆 （WAF） 策略
description: 瞭解如何為應用程式閘道創建 Web 應用程式防火牆策略。
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: 3e8cd2f1e594cd6a60296b2df135f275641df313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086974"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>為應用程式閘道創建 Web 應用程式防火牆策略

將 WAF 策略與攔截器關聯允許單個 WAF 後面的多個網站受到不同策略的保護。 例如，如果 WAF 後面有五個網站，則可以有五個單獨的 WAF 策略（每個攔截器一個策略），以自訂一個網站的排除規則、自訂規則和託管規則集，而不會影響其他四個網站。 如果希望單個策略應用於所有網站，則只需將策略與應用程式閘道（而不是單個攔截器）相關聯，即可使其全域應用。 策略也可以應用於基於路徑的路由規則。 

您可以根據需要制定盡可能多的策略。 創建策略後，它必須與應用程式閘道關聯才能生效，但它可以與應用程式閘道和攔截器的任意組合相關聯。 

如果應用程式閘道應用了策略，然後對該應用程式閘道上的攔截器應用了不同的策略，則攔截器的策略將生效，但僅針對分配給攔截器的攔截器。 應用程式閘道策略仍適用于未為其分配特定策略的所有其他攔截器。 

   > [!NOTE]
   > 以每個網站和每個 URI 為主的 WAF 原則都處於公開預覽狀態。 這表示此功能受限於 Microsoft 的補充使用規定。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

所有新的 Web 應用程式防火牆的 WAF 設置（自訂規則、託管 rulset 配置、排除等）都位於 WAF 策略中。 如果您有現有的 WAF，這些設置可能仍然存在於 WAF 配置中。有關如何移動到新的 WAF 策略的步驟，請參閱本文後面的[WAF 配置遷移到 WAF 策略](#migrate)。 

## <a name="create-a-policy"></a>建立原則

首先，使用 Azure 門戶使用託管預設規則集 （DRS） 創建基本 WAF 策略。

1. 在門戶的左上角，選擇 **"創建資源**"。 搜索**WAF**，選擇**Web 應用程式防火牆**，然後選擇 **"創建**"。
2. 在**創建 WAF 策略**頁"**基礎知識"** 選項卡上，輸入或選擇以下資訊，接受其餘設置的預設值，然後選擇 **"查看 + 創建**：

   |設定  |值  |
   |---------|---------|
   |政策     |區域 WAF（應用程式閘道）|
   |訂用帳戶     |選擇訂閱名稱|
   |資源群組     |選取您的資源群組|
   |原則名稱     |為 WAF 策略鍵入唯一名稱。|
3. 在 **"關聯"** 選項卡上，輸入以下設置之一，然後選擇 **"添加**：

   |設定  |值  |
   |---------|---------|
   |關聯應用程式閘道     |選擇應用程式閘道設定檔名稱。|
   |助理監聽器     |選擇應用程式閘道攔截器的名稱，然後選擇 **"添加**"。|

   > [!NOTE]
   > 如果將策略分配給已具有策略的應用程式閘道（或攔截器），則原始策略將被新策略覆蓋並替換。
4. 選取 [檢閱 + 建立]****，然後選取 [建立]****。

   ![WAF 策略基礎知識](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>配置 WAF 規則（可選）

創建 WAF 策略時，預設情況下處于*檢測*模式。 在檢測模式下，WAF 不會阻止任何請求。 相反，匹配的 WAF 規則將記錄在 WAF 日誌中。 要查看 WAF 在操作中，可以將模式設置更改為 *"預防*"。 在"預防"模式下，所選 CRS 規則集中定義的匹配規則將被阻止和/或記錄在 WAF 日誌中。

## <a name="managed-rules"></a>託管規則

預設情況下啟用 Azure 管理的 OWASP 規則。 要禁用規則組中的單個規則，請展開該規則組中的規則，選擇規則編號前面的核取方塊，然後在上面的選項卡上選擇 **"禁用**"。

[![託管規則](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>自訂規則

要創建自訂規則，請在 **"自訂規則"** 選項卡下選擇 **"添加自訂規則**"。這將打開自訂規則配置頁。 以下螢幕截圖顯示了一個自訂規則示例，該規則配置為在查詢字串包含文本*塊塊*時阻止請求。

[![編輯自訂規則](../media/create-waf-policy-ag/edit-custom-rule.png)](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>將 WAF 配置遷移到 WAF 策略

如果您有現有的 WAF，您可能注意到門戶中的一些更改。 首先，您需要確定在 WAF 上啟用的策略類型。 有三種潛在狀態：

- 無 WAF 策略
- 僅自訂規則策略
- WAF 原則

通過在門戶中查看 WAF，可以判斷您的 WAF 處於哪個狀態。 如果 WAF 設置可見，並且可以從應用程式閘道視圖中更改，則 WAF 處於狀態 1。

[![WAF](../media/create-waf-policy-ag/waf-configure.png)配置](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

如果選擇**Web 應用程式防火牆**，並且它顯示關聯的策略，WAF 處於狀態 2 或狀態 3。 導航到策略後，如果**策略僅**顯示自訂規則和關聯應用程式閘道，則它是僅自訂規則策略。

![WAF 自訂規則](../media/create-waf-policy-ag/waf-custom-rules.png)

如果它還顯示策略設置和託管規則，則它是完整的 Web 應用程式防火牆策略。 

![WAF 策略設置](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>遷移到 WAF 策略

如果您只有自訂規則 WAF 策略，則可能需要遷移到新的 WAF 策略。 今後，防火牆策略將支援 WAF 策略設置、託管規則集、排除和禁用的規則組。 本質上，以前在應用程式閘道內完成的所有 WAF 配置現在都通過 WAF 策略完成。 

僅禁用對自訂規則的編輯。 要編輯任何 WAF 設置（如禁用規則、添加排除項等），必須遷移到新的頂級防火牆策略資源。

為此，請創建*Web 應用程式防火牆策略*並將其關聯到您選擇的應用程式閘道和攔截器。 此新策略**必須**與當前 WAF 配置完全相同，這意味著必須將每個自訂規則、排除規則、禁用規則等複製到要創建的新策略中。 獲得與應用程式閘道關聯的策略後，可以繼續更改 WAF 規則和設置。 還可以使用 Azure PowerShell 執行此操作。 有關詳細資訊，請參閱將[WAF 策略與現有應用程式閘道相關聯](associate-waf-policy-existing-gateway.md)。

或者，可以使用遷移腳本遷移到 WAF 策略。 有關詳細資訊，請參閱使用[Azure PowerShell 遷移 Web 應用程式防火牆策略](migrate-policy.md)。

## <a name="next-steps"></a>後續步驟

瞭解有關[Web 應用程式防火牆 CRS 規則組和規則](application-gateway-crs-rulegroups-rules.md)的更多內容。
