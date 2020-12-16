---
title: 針對網域與 TLS/SSL 憑證進行疑難排解
description: 尋找當您在 Azure App Service 中設定網域或 TLS/SSL 憑證時，可能會遇到之常見問題的解決方案。
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 691cbd79e82432c8e919dcbb51642a76000296dc
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607604"
---
# <a name="troubleshoot-domain-and-tlsssl-certificate-problems-in-azure-app-service"></a>針對 Azure App Service 中的網域與 TLS/SSL 憑證問題進行疑難排解

本文列出當您在 Azure App Service 中為 web 應用程式設定網域或 TLS/SSL 憑證時，可能會遇到的常見問題。 文中也會描述這些問題的可能原因和解決方案。

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 前往 [Azure 支援網站](https://azure.microsoft.com/support/options/) ，然後選取 [ **取得支援**]。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>憑證問題

### <a name="you-cant-add-a-tlsssl-certificate-binding-to-an-app"></a>您無法將 TLS/SSL 憑證系結新增至應用程式 

#### <a name="symptom"></a>徵狀

當您新增 TLS 系結時，會收到下列錯誤訊息：

「無法新增 SSL 繫結。 無法為現有 VIP 設定憑證，因為已經有另一個 VIP 使用該憑證。」

#### <a name="cause"></a>原因

多個應用程式之間，如果有多個基於相同 IP 位址的 IP 為主 SSL 繫結，可能會發生此問題。 例如，應用程式 A 的以 IP 為主 SSL 使用舊憑證。 應用程式 B 的以 IP 為主 SSL 使用同一個 IP 位址的新憑證。 當您使用新憑證來更新應用程式 TLS 系結時，會因為另一個應用程式正在使用相同的 IP 位址而失敗，並出現此錯誤。 

#### <a name="solution"></a>解決方案 

若要修正問題，請使用下列其中一種方法：

- 在使用舊憑證的應用程式上刪除以 IP 為主的 SSL 繫結。 
- 建立使用新憑證的新 IP 為主 SSL 繫結。

### <a name="you-cant-delete-a-certificate"></a>您無法刪除憑證 

#### <a name="symptom"></a>徵狀

當您嘗試刪除憑證時，收到下列錯誤訊息：

「無法刪除憑證，因為目前正在使用 TLS/SSL 系結。 您必須先移除 TLS 系結，才能刪除憑證。」

#### <a name="cause"></a>原因

如果有另一個應用程式使用憑證，可能會出現這個問題。

#### <a name="solution"></a>解決方案

從應用程式移除該憑證的 TLS 系結。 接著再嘗試刪除憑證。 如果您仍然無法刪除憑證，請清除網際網路瀏覽器快取，然後在新的瀏覽器視窗中重新開啟 Azure 入口網站。 接著再嘗試刪除憑證。

### <a name="you-cant-purchase-an-app-service-certificate"></a>您無法購買 App Service 憑證 

#### <a name="symptom"></a>徵狀
您無法從 Azure 入口網站購買 [Azure App Service 憑證](./configure-ssl-certificate.md#import-an-app-service-certificate)。

#### <a name="cause-and-solution"></a>原因和解決方案
以下是這個問題發生的原因：

- App Service 方案為免費或共用。 這些定價層不支援 TLS。 

    **解決方案**：將應用程式的 App Service 方案升級為標準版。

- 訂用帳戶的信用卡無效。

    **解決方案**：為訂用帳戶新增有效的信用卡。 

- 訂用帳戶供應項目不支援購買 Microsoft Student 之類的 App Service 憑證。  

    **解決方案**：升級您的訂用帳戶。 

- 訂用帳戶已達到單一訂用帳戶允許的購買限制。

    **解決方案**：隨用隨付和 EA 訂用帳戶類型的 App Service 憑證有購買 10 個憑證的限制。 其他訂用帳戶類型的限制則為 3 個。 若要提高限制，請連絡 [Azure支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- App Service 憑證已標示為詐騙。 您收到以下錯誤訊息：「您的憑證已被標幟為可能是詐騙。 要求目前正在進行檢閱。 如果憑證未在24小時內變成可用，請洽詢 Azure 支援服務」。

    **解決方案**：如果憑證被標示為詐騙，而且在接下來的 24 小時內未能解決，請遵循以下步驟：

    1. 登入 [Azure 入口網站](https://portal.azure.com)。
    2. 前往 [App Service 憑證]，然後選取憑證。
    3. 選取 **憑證** 設定  >  **步驟2：確認**  >  **網域驗證**。 此步驟會傳送電子郵件通知給 Azure 憑證提供者，尋求解決問題。

## <a name="custom-domain-problems"></a>自訂網域問題

### <a name="a-custom-domain-returns-a-404-error"></a>自訂網域傳回 404 錯誤 

#### <a name="symptom"></a>徵狀

當您使用自訂網域名稱瀏覽至往暫時，收到以下錯誤訊息：

「錯誤 404 - 找不到 Web 應用程式。」

#### <a name="cause-and-solution"></a>原因和解決方案

**原因 1** 

您設定的自訂網域遺漏 CNAME 或 A 記錄。 

**原因 1 的解決方案**

- 如果您已新增 A 記錄，請務必一併新增 TXT 記錄。 如需詳細資訊，請參閱[建立 A 記錄](./app-service-web-tutorial-custom-domain.md#create-the-a-record)。
- 如果您的應用程式不一定要使用根網域，建議您使用 CNAME 記錄，避免使用 A 記錄。
- CNAME 記錄和 A 記錄不應同時用於同一個網域。 此問題可能會導致衝突，並導致無法解析網域。 

**原因 2** 

網際網路瀏覽器快取的網域可能還是舊有的 IP 位址。 

**原因 2 的解決方案**

清除瀏覽器。 如果您使用 Windows 裝置，可以執行命令 `ipconfig /flushdns`。 使用 [WhatsmyDNS.net](https://www.whatsmydns.net/) 來確認網域是否指向應用程式的 IP 位址。

### <a name="you-cant-add-a-subdomain"></a>您無法新增子網域 

#### <a name="symptom"></a>徵狀

您無法將新主機名稱新增至應用程式來指派子網域。

#### <a name="solution"></a>解決方案

- 請洽詢訂用帳戶管理員，確認您擁有將主機名稱新增至應用程式的權限。
- 如果您需要更多子域，建議您將裝載的網域變更為 Azure 功能變數名稱服務 (DNS) 。 只要使用 Azure DNS，您就可以將 500 個主機名稱新增至應用程式。 如需詳細資訊，請參閱[新增子網域](/archive/blogs/waws/mapping-a-custom-subdomain-to-an-azure-website)。

### <a name="dns-cant-be-resolved"></a>無法解析 DNS

#### <a name="symptom"></a>徵狀

您收到下列錯誤訊息：

「找不到 DNS 記錄。」

#### <a name="cause"></a>原因
這個問題發生的原因如下：

- 存留時間 (TTL) 期間尚未到期。 請檢查網域的 DNS 組態以判斷 TTL 值，然後等待期間到期。
- DNS 組態不正確。

#### <a name="solution"></a>解決方案
- 等候 48 小時讓該問題自行解決。
- 如果您可以變更 DNS 組態中的 TTL 設定，請將值變更為 5 分鐘，看看是否能解決問題。
- 使用 [WhatsmyDNS.net](https://www.whatsmydns.net/) 來確認網域是否指向應用程式的 IP 位址。 如果情況並非如此，請設定 A 記錄來更正應用程式的 IP 位址。

### <a name="you-need-to-restore-a-deleted-domain"></a>您必須還原已刪除的網域 

#### <a name="symptom"></a>徵狀
您的網域不再顯示於 Azure 入口網站中。

#### <a name="cause"></a>原因 
訂用帳戶的擁有者可能已意外刪除網域。

#### <a name="solution"></a>解決方案
如果網域遭到刪除的時間距離今天不到七天，網域的刪除程序可能尚未開始。 此時，您可以 Azure 入口網站中以同一個訂用帳戶再次購買相同的網域。  (請務必在 [搜尋] 方塊中輸入確切的功能變數名稱。 ) 此網域不會再向您收費。 如果網域在七天前遭到刪除，請聯絡 [Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 以取得還原網域的協助。

## <a name="domain-problems"></a>網域問題

### <a name="you-purchased-a-tlsssl-certificate-for-the-wrong-domain"></a>您為錯誤的網域購買了 TLS/SSL 憑證

#### <a name="symptom"></a>徵狀

您買到錯誤網域的 App Service 憑證。 您無法更新憑證以使用正確網域。

#### <a name="solution"></a>解決方案

刪除憑證，然後再購買新憑證。

如果使用錯誤網域的現行憑證處於「已發行」狀態，則您也必須支付該憑證的費用。 App Service 憑證無法退款，不過您可以連絡 [Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，看看是否有其他選擇。 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>App Service 憑證已更新，不過應用程式顯示舊憑證 

#### <a name="symptom"></a>徵狀

App Service 憑證已更新，不過使用 App Service 憑證的應用程式仍然使用舊憑證。 此外，您收到必須使用 HTTPS 通訊協定的警告。

#### <a name="cause"></a>原因 
App Service 在48小時內自動同步處理您的憑證。 當您輪替或更新憑證時，有時候應用程式仍會擷取舊憑證，而非更新後的憑證。 這是因為同步處理憑證資源的作業尚未執行。 按一下 [同步]。同步作業會自動更新 App Service 中憑證的主機名稱系結，而不會導致您的應用程式停止運作。

#### <a name="solution"></a>解決方案

您可以強制同步處理憑證：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [App Service 憑證]，然後選取憑證。
2. 選取 [ **重設金鑰和同步** 處理]，然後選取 [ **同步**]。同步需要一些時間才能完成。 
3. 當同步完成時，您會看見以下通知：「已成功將所有資源更新為最新憑證」。

### <a name="domain-verification-is-not-working"></a>網域驗證無法運作 

#### <a name="symptom"></a>徵狀 
App Service 憑證必須經過網域驗證才能使用。 當您選取 [驗證] 時，程序會失敗。

#### <a name="solution"></a>解決方案
新增 TXT 記錄來手動驗證網域：

1. 前往主控網域名稱的網域名稱服務 (DNS) 提供者。
1. 為您的網域新增一筆 TXT 記錄，並且使用 Azure 入口網站中顯示的網域權杖值。 

請稍候幾分鐘等待 DNS 傳播執行，然後選取 [重新整理] 按鈕來觸發驗證。 

或者，您可以使用 HTML 網頁方法，以手動方式驗證您的網域。 此方法可讓憑證授權單位確認憑證發行目標網域的網域擁有權。

1. 建立名為 {domain verification token}.html 的 HTML 檔案。 此檔案的內容應為網域驗證權杖的值。
1. 在主控網域的 Web 伺服器根目錄上傳此檔案。
1. 選取 [重新整理] 檢查憑證狀態。 驗證可能需要數分鐘才能完成。

例如，如果您為 azure.com 購買網域驗證權杖為 1234abcd 的標準憑證，驗證作業就會向 https://azure.com/1234abcd.html 發出 Web 要求，並且應傳回 1234abcd。 

> [!IMPORTANT]
> 憑證訂購必須在 15 天內完成網域驗證作業。 15 天過後，憑證授權單位就會拒絕憑證，您也不需要支付該憑證的費用。 在這種情況下，請刪除該憑證，然後再試一次。
>
> 

### <a name="you-cant-purchase-a-domain"></a>您無法購買網域

#### <a name="symptom"></a>徵狀
您無法在 Azure 入口網站中購買 App Service 網域。

#### <a name="cause-and-solution"></a>原因和解決方案

這個問題發生的原因如下：

- Azure 訂用帳戶沒有信用卡或信用卡無效。

    **解決方案**：為訂用帳戶新增有效的信用卡。

- 由於您不是訂用帳戶擁有者，因此沒有購買網域的權限。

    **解決方案**：[將擁有者角色](../role-based-access-control/role-assignments-portal.md)指派給您的帳戶。 或是連絡訂用帳戶管理員，以取得購買網域的權限。
- 您的訂用帳戶已達到購買網域的上限。 目前的限制為 20 個。

    **解決方案**：若要申請提高限制，請連絡 [Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- 您的 Azure 訂用帳戶類型不支援購買 App Service 網域。

    **解決方案**：將 Azure 訂用帳戶升級為其他訂用帳戶類型，如隨用隨付訂用帳戶。

### <a name="you-cant-add-a-host-name-to-an-app"></a>您無法將主機名稱新增至應用程式 

#### <a name="symptom"></a>徵狀

當您新增主機名稱時，程序無法驗證及確認網域。

#### <a name="cause"></a>原因 

這個問題發生的原因如下：

- 您無權新增主機名稱。

    **解決方案**：要求訂用帳戶管理員授與您新增主機名稱的權限。
- 您的網域擁有權無法驗證。

    **解決方案**：確認您的 CNAME 或 A 記錄設定正確。 若要將自訂網域對應至應用程式，請建立 CNAME 記錄或 A 記錄。 如果您想要使用根網域，必須使用 A 和 TXT 記錄：

    |記錄類型|Host|指向|
    |------|------|-----|
    |A|@|應用程式的 IP 位址|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>常見問題集

**當我購買我的網站時，我是否需要為我的網站設定我的自訂網域？**

當您從 Azure 入口網站購買網域時，會自動將 App Service 應用程式設定為使用該自訂網域。 您不必採取任何額外的步驟。 如需詳細資訊，請觀賞 [Azure App Service Self Help：在 Channel9 上新增自訂功能變數名稱](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) 。

**我可以使用 Azure 入口網站購買的網域來改為指向 Azure VM 嗎？**

是，您可以將網域指向 VM。 如需詳細資訊，請參閱[使用 Azure DNS 為 Azure 服務提供自訂網域設定](../dns/dns-custom-domain.md)。

**我的網域是由 GoDaddy 還是 Azure DNS 所裝載？**

App Service 網域會使用 GoDaddy 來註冊網域，並使用 Azure DNS 來裝載網域。 

**我已啟用自動更新，但仍會透過電子郵件收到網域的續約通知。我該怎麼做？**

如果您已啟用自動更新，就不需要採取任何動作。 系統會提供通知電子郵件通知您，網域即將到期，如果未啟用自動更新，則會手動更新。

**我會向我收取網域 Azure DNS 的費用嗎？**

網域購買的初始成本僅適用于網域註冊。 除了註冊成本之外，Azure DNS 也會根據您的使用量收取費用。 如需詳細資訊，請參閱 [Azure DNS 定價](https://azure.microsoft.com/pricing/details/dns/) 以取得詳細資料。

**我稍早從 Azure 入口網站購買網域，並想要從 GoDaddy 主機移至 Azure DNS 裝載。我該怎麼做？**

遷移至 Azure DNS 裝載並不是必要的。 如果您想要遷移至 Azure DNS，Azure 入口網站中的網域管理體驗會提供移至 Azure DNS 所需步驟的相關資訊。 如果網域是透過 App Service 購買的，則從 GoDaddy 裝載到 Azure DNS 的遷移程式相當完美。

**我想要從 App Service 網域購買我的網域，但我可以在 GoDaddy 而不是 Azure DNS 上裝載網域嗎？**

從2017年7月24日開始，在入口網站中購買的 App Service 網域會裝載于 Azure DNS。 如果您想要使用不同的主機服務提供者，您必須移至其網站以取得網域裝載解決方案。

**我必須支付網域的隱私權保護嗎？**

當您透過 Azure 入口網站購買網域時，可以選擇不額外的費用來新增隱私權。 這是透過 Azure App Service 購買網域的其中一個優點。

**如果我決定不再需要我的網域，我可以取回錢嗎？**

當您購買網域時，系統不會向您收取五天的費用，在這段期間，您可以決定不想要該網域。 如果您決定不想讓該網域在五天的期間內，則不會向您收費。  ( uk 網域是唯一的例外。 如果您購買的是 uk 網域，則會立即向您收費，而且無法退款。 ) 

**我可以在訂用帳戶中的另一個 Azure App Service 應用程式中使用網域嗎？**

是。 當您在 Azure 入口網站中存取自訂網域和 TLS 分頁時，會看到您已購買的網域。 您可以將應用程式設定為使用任何這些網域。

**我可以將網域從一個訂用帳戶轉移到另一個訂用帳戶嗎？**

您可以使用 [Get-azresource](/powershell/module/az.Resources/Move-azResource) PowerShell Cmdlet，將網域移至另一個訂用帳戶/資源群組。

**如果目前沒有 Azure App Service 應用程式，該如何管理我的自訂網域？**

即使您沒有 App Service Web 應用程式，也可以管理您的網域。 網域可用於 Azure 服務，例如虛擬機器、儲存體等等。如果您想要使用網域進行 App Service Web Apps，則必須包含不在免費 App Service 方案中的 Web 應用程式，才能將網域系結至您的 Web 應用程式。

**是否可以將具有自訂網域的 web 應用程式移至另一個訂用帳戶，或從 App Service 環境 v1 移至 V2？**

是，您可以跨訂用帳戶移動您的 web 應用程式。 遵循 [如何在 Azure 中移動資源](../azure-resource-manager/management/move-resource-group-and-subscription.md)的指導方針。 移動 web 應用程式時有一些限制。 如需詳細資訊，請參閱 [移動 App Service 資源的限制](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)。

移動 web 應用程式之後，[自訂網域] 設定中網域的主機名稱系結應維持不變。 設定主機名稱系結不需要額外的步驟。