---
title: 診斷 Azure Key Vault 上的私人連結設定問題
description: 解決 Key Vault 的常見私用連結問題，並深入探討設定
author: msfcolombo
ms.author: fcolombo
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: c4873bded750186f072dd39ddcb8d78941848586
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289363"
---
# <a name="diagnose-private-links-configuration-issues-on-azure-key-vault"></a>診斷 Azure Key Vault 上的私人連結設定問題

## <a name="introduction"></a>簡介

本文可協助使用者診斷和修正涉及 Key Vault 和私用連結功能的問題。 本指南可協助您瞭解設定層面，例如取得第一次運作的私人連結，或修正私用連結因某些變更而停止運作的情況。

如果您不熟悉這項功能，請參閱 [整合 Key Vault 與 Azure Private Link](private-link-service.md)。

### <a name="problems-covered-by-this-article"></a>本文所涵蓋的問題

- 您的 DNS 查詢仍然會傳回金鑰保存庫的公用 IP 位址，而不是您預期使用私用連結功能的私人 IP 位址。
- 使用私用連結的指定用戶端所提出的所有要求，都會因為超時或網路錯誤而失敗，而且問題不是間歇性的。
- 金鑰保存庫具有私人 IP 位址，但要求仍會收到 `403` `ForbiddenByFirewall` 內部錯誤碼的回應。
- 您正在使用私人連結，但您的金鑰保存庫仍接受來自公用網際網路的要求。
- 您的金鑰保存庫有兩個私人端點。 使用其中一個的要求會正常運作，但使用其他要求的要求卻失敗。
- 您有另一個使用私用連結的訂用帳戶、金鑰保存庫或虛擬網路。 您想要建立新的類似部署，但無法取得私人連結來運作。

### <a name="problems-not-covered-by-this-article"></a>本文未涵蓋的問題

- 有間歇性的連線問題。 在指定的用戶端中，您會看到某些要求正在運作，而且有些無法運作。 *間歇性問題通常不是私用連結設定中的問題所造成;它們是網路或用戶端超載的符號。*
- 您所使用的 Azure 產品支援 BYOK (攜帶您自己的金鑰) 、CMK (客戶管理的金鑰) ，或存取儲存在金鑰保存庫中的秘密。 當您在金鑰保存庫設定中啟用防火牆時，該產品無法存取您的金鑰保存庫。 *查看特定產品的檔。請確定它明確指出支援啟用防火牆的金鑰保存庫。如有需要，請聯絡該特定產品的支援。*

### <a name="how-to-read-this-article"></a>如何閱讀這篇文章

如果您不熟悉私用連結或正在評估複雜的部署，建議您閱讀整篇文章。 否則，請隨意選擇可對您遇到的問題更有意義的區段。

現在就開始吧！

## <a name="1-confirm-that-you-own-the-client-connection"></a>1. 確認您擁有用戶端連接

### <a name="confirm-that-your-client-runs-at-the-virtual-network"></a>確認您的用戶端是在虛擬網路上執行

本指南旨在協助您修正從應用程式程式碼產生的金鑰保存庫連接。 範例包括在 Azure 虛擬機器中執行的應用程式和腳本、Azure Service Fabric 叢集、Azure App Service Azure Kubernetes Service (AKS) ，以及類似其他專案。 本指南也適用于在 Azure 入口網站 web 基底使用者介面中執行的存取，而瀏覽器會直接存取您的金鑰保存庫。

根據私用連結的定義，應用程式、腳本或入口網站必須在連接到 [私人端點資源](../../private-link/private-endpoint-overview.md) 部署所在之虛擬網路的電腦、叢集或環境上執行。

如果應用程式、腳本或入口網站在任意網際網路連線的網路上執行，本指南並不適用，而且可能無法使用私人連結。 這項限制也適用于在 Azure Cloud Shell 中執行的命令，因為它們會在隨選提供的遠端 Azure 電腦中執行，而不是在使用者瀏覽器上執行。

### <a name="if-you-use-a-managed-solution-refer-to-specific-documentation"></a>如果您使用受控解決方案，請參閱特定檔

本指南不適用於 Microsoft 所管理的解決方案，其中金鑰保存庫是由獨立于客戶虛擬網路的 Azure 產品存取。 這類案例的範例有 Azure 儲存體或針對待用加密設定的 Azure SQL、Azure 事件中樞使用客戶提供的金鑰來加密資料、Azure Data Factory 存取儲存在金鑰保存庫中的服務認證、Azure Pipelines 從金鑰保存庫取得秘密，以及其他類似的案例。 在這些情況下， *您必須檢查產品是否支援啟用防火牆的金鑰保存庫* 。 這項支援通常是使用 Key Vault 防火牆的「 [信任的服務](overview-vnet-service-endpoints.md#trusted-services) 」功能來執行。 不過，許多產品都不包含在受信任的服務清單中，原因有很多。 在此情況下，會觸及產品特定的支援。

少數 Azure 產品支援 *vnet 插入* 的概念。 簡單來說，產品會將網路裝置新增至客戶虛擬網路，讓它能夠傳送要求，就像是部署至虛擬網路一樣。 [Azure Databricks](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)有一個值得注意的範例。 這類產品可以使用私用連結對金鑰保存庫提出要求，而這項疑難排解指南可能有所説明。

## <a name="2-confirm-that-the-connection-is-approved-and-succeeded"></a>2. 確認連接已通過核准且成功

下列步驟會驗證私人端點連線是否已通過核准且成功：

1. 開啟 Azure 入口網站，然後開啟您的金鑰保存庫資源。
2. 在左側功能表中，選取 [ **網路** ]。
3. 按一下 [ **私人端點連接** ] 索引標籤。這會顯示所有私人端點連線及其各自的狀態。 如果沒有連線或虛擬網路的連線已遺失，您就必須建立新的私人端點。 稍後將會討論。
4. 仍在 **私人端點** 連線中，找出您要診斷的連線，並確認 [線上狀態] 已 **通過核准** ，且 [布建狀態] 已 **成功** 。
    - 如果連接處於「擱置中」狀態，您可能只需要核准它。
    - 如果連接「已拒絕」、「失敗」、「錯誤」、「已中斷連線」或其他狀態，則您必須建立新的私人端點資源才能生效。

最好先刪除不正確連接，以保持清除。

## <a name="3-confirm-that-the-key-vault-firewall-is-properly-configured"></a>3. 確認金鑰保存庫防火牆已正確設定

>[!IMPORTANT]
> 變更防火牆設定可能會移除仍未使用私人連結之合法用戶端的存取權。 請確定您知道防火牆設定中每項變更的含意。

重要的概念是，私用連結 *功能只會* 在已關閉的虛擬網路中存取您的金鑰保存庫，以防止資料遭到外泄。 它不會 *移除* 任何現有的存取權。 若要有效地封鎖來自公用網際網路的存取，您必須明確啟用金鑰保存庫防火牆：

1. 開啟 Azure 入口網站，然後開啟您的金鑰保存庫資源。
2. 在左側功能表中，選取 [ **網路** ]。
3. 確定已選取頂端的 [ **防火牆與虛擬網路** ] 索引標籤。
4. 確定已選取 [ **私人端點] 和 [選取的網路** ] 選項。 如果您找到 **所有網路** ，請選取，這會說明外部用戶端仍然能夠存取金鑰保存庫的原因。

下列語句也適用于防火牆設定：

- Private links 功能不需要在金鑰保存庫防火牆設定中指定任何「虛擬網路」。 使用金鑰保存庫之私人 IP 位址的所有要求 (請參閱下一節) 必須正常運作，即使沒有在金鑰保存庫防火牆設定中指定虛擬網路也一樣。
- Private links 功能不需要在金鑰保存庫防火牆設定中指定任何 IP 位址。 同樣地，即使防火牆設定中未指定任何 IP 位址，使用金鑰保存庫之私人 IP 位址的所有要求都必須正常運作。

如果您使用 private links，在 key vault 防火牆中指定虛擬網路或 IP 位址的唯一動機為：

- 您有一個混合式系統，其中某些用戶端使用私用連結，有些使用服務端點，有些使用公用 IP 位址。
- 您正在轉換為 private links。 在此情況下，一旦您確認所有用戶端都使用私人連結，您應該從金鑰保存庫防火牆設定移除虛擬網路和 IP 位址。

## <a name="4-make-sure-the-key-vault-has-a-private-ip-address"></a>4. 確認金鑰保存庫具有私人 IP 位址

### <a name="difference-between-private-and-public-ip-addresses"></a>私人和公用 IP 位址之間的差異

私人 IP 位址一律具有下列其中一種格式：

- 4.x. x. x. x. x. x. x：範例： `10.1.2.3` ， `10.56.34.12` 。
- >172.16.. x 至 172.32. x：範例： `172.20.1.1` ， `172.31.67.89` 。
- 192.168.. `192.168.0.1` x：範例： `192.168.100.7`

某些 IP 位址和範圍會保留：

- 224. x. x. x. x. x. x：多播
- 255.255.255.255：廣播
- 127. x. x. x. x. x. x：回送
- 169.254.. x：連結-本機
- 168.63.129.16：內部 DNS

所有其他的 IP 位址都是公用的。

當您流覽入口網站或執行顯示 IP 位址的命令時，請確定您可以識別該 IP 位址是私人、公用或保留的。 若要讓私人連結運作，金鑰保存庫主機名稱必須解析為屬於虛擬網路位址空間的私人 IP 位址。

### <a name="find-the-key-vault-private-ip-address-in-the-virtual-network"></a>在虛擬網路中尋找金鑰保存庫的私人 IP 位址

您將需要診斷主機名稱解析，並且必須知道已啟用私人連結的金鑰保存庫確切私人 IP 位址。 若要尋找該位址，請遵循此程式：

1. 開啟 Azure 入口網站，然後開啟您的金鑰保存庫資源。
2. 在左側功能表中，選取 [ **網路** ]。
3. 按一下 [ **私人端點連接** ] 索引標籤。這會顯示所有私人端點連線及其各自的狀態。
4. 找出您正在診斷的帳戶，並確認「線上狀態」已 **核准** ，且布建狀態為「 **成功** 」。 如果您看不到這個內容，請返回本檔的上一節。
5. 當您找到正確的專案時，請按一下 [ **私人端點** ] 資料行中的連結。 這會開啟私人端點資源。
6. [總覽] 頁面可能會顯示一個稱為 **自訂 DNS 設定** 的區段。 確認只有一個符合金鑰保存庫主機名稱的專案。 該專案會顯示金鑰保存庫的私人 IP 位址。
7. 您也可以按一下 [ **網路介面** ] 的連結，確認私人 IP 位址與上一個步驟中所顯示的相同。 網路介面是代表金鑰保存庫的虛擬裝置。

IP 位址是 *在相同虛擬網路中* 執行的 vm 和其他裝置將用來連線到金鑰保存庫的位址。 請記下 IP 位址，或讓瀏覽器索引標籤保持開啟，而且不會在您進行進一步調查時觸碰。

>[!NOTE]
> 如果您的金鑰保存庫有多個私人端點，則會有多個私人 IP 位址。 只有當您有多個虛擬網路存取相同的金鑰保存庫，且每個虛擬網路都是透過自己的私人端點 (私人端點屬於單一虛擬網路) 時，這項功能才會很有用。 請務必診斷正確虛擬網路的問題，並在上述程式中選取正確的私人端點連線。 此外， **請勿** 在相同的虛擬網路中針對相同的 Key Vault 建立多個私人端點。 這是不必要的，而且是混淆的來源。

## <a name="5-validate-the-dns-resolution"></a>5. 驗證 DNS 解析

DNS 解析是將金鑰保存庫主機名稱 (範例： `fabrikam.vault.azure.net`) 至 IP 位址的程式， (範例： `10.1.2.3`) 。 下列各小節顯示每個案例中 DNS 解析的預期結果。

### <a name="key-vaults-without-private-link"></a>沒有私用連結的金鑰保存庫

本節旨在供學習之用。 當金鑰保存庫沒有已核准狀態的私人端點連線時，解析主機名稱會產生類似下面的結果：

Windows：

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  52.168.109.101
    Aliases:  fabrikam.vault.azure.net
              data-prod-eus.vaultcore.azure.net
              data-prod-eus-region.vaultcore.azure.net

Linux：

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
    data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
    data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101

您可以看到名稱解析為公用 IP 位址，但沒有 `privatelink` 別名。 別名稍後會加以說明，現在請不要擔心。

無論電腦是連線到虛擬網路，或是具有網際網路連線的任意電腦，預期都會有上述結果。 發生這種情況是因為金鑰保存庫沒有處於已核准狀態的私人端點連線，因此金鑰保存庫不需要支援私用連結。

### <a name="key-vault-with-private-link-resolving-from-arbitrary-internet-machine"></a>從任意網際網路電腦解析私用連結的金鑰保存庫

當金鑰保存庫有一或多個私人端點連線處於已核准狀態，而且您從連線到網際網路的任意電腦解析主機名稱時 (電腦 *未* 連線至私人端點所在的虛擬網路) ，您應該會發現：

Windows：

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  52.168.109.101
    Aliases:  fabrikam.vault.azure.net
              fabrikam.privatelink.vaultcore.azure.net
              data-prod-eus.vaultcore.azure.net
              data-prod-eus-region.vaultcore.azure.net

Linux：

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
    fabrikam.privatelink.vaultcore.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
    data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
    data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101

與上一個案例的顯著差異在於，有一個具有值的新別名 `{vaultname}.privatelink.vaultcore.azure.net` 。 這表示金鑰保存庫資料平面已準備好接受來自私用連結的要求。

這並不表示從虛擬網路 *外部* 的電腦執行的要求 (，就像您剛剛使用的電腦一樣) 將會使用私人連結，而不是。 您可以看到，從主機名稱仍然解析為公用 IP 位址的事實中。 只有 *連線到虛擬網路的* 機器可以使用 private links。 接下來將會有更多相關資訊。

如果您沒有看到 `privatelink` 別名，表示金鑰保存庫的狀態為零的私人端點連線 `Approved` 。 請在重試之前返回 [至此區段](#2-confirm-that-the-connection-is-approved-and-succeeded) 。

### <a name="key-vault-with-private-link-resolving-from-virtual-network"></a>從虛擬網路解析私用連結的金鑰保存庫

當金鑰保存庫有一或多個私人端點連線處於已核准狀態，而且您從連線到私人端點建立所在之虛擬網路的電腦解析主機名稱時，這會是預期的回應：

Windows：

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  10.1.2.3
    Aliases:  fabrikam.vault.azure.net
              fabrikam.privatelink.vaultcore.azure.net

Linux：

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
    fabrikam.privatelink.vaultcore.azure.net has address 10.1.2.3

有兩個值得注意的差異。 首先，名稱會解析為私人 IP 位址。 這必須是我們在本文 [對應章節](#find-the-key-vault-private-ip-address-in-the-virtual-network) 中找到的 IP 位址。 其次，它後面沒有其他別名 `privatelink` 。 發生這種情況是因為虛擬網路 DNS 伺服器會 *攔截* 別名鏈，並直接從名稱傳回私人 IP 位址 `fabrikam.privatelink.vaultcore.azure.net` 。 該專案實際上是 `A` 私人 DNS 區域中的一筆記錄。 接下來將會有更多相關資訊。

>[!NOTE]
> 上述結果只會發生在連線到已建立私人端點之虛擬網路的虛擬機器上。 如果您沒有部署在包含私人端點的虛擬網路中的 VM，請部署一個 VM，並從遠端連線到該 VM，然後執行 `nslookup` 命令 (Windows) 或 `host` (Linux) 的命令。

如果您在連線到已建立私人端點的虛擬網路的虛擬機器上執行這些命令，且 **未** 顯示金鑰保存庫的私人 IP 位址，下一節可能有助於修正此問題。

## <a name="6-validate-the-private-dns-zone"></a>6. 驗證私人 DNS 區域

如果 DNS 解析無法如上一節所述運作，您的私人 DNS 區域可能會有問題，而且本節可能有所説明。 如果 DNS 解析顯示正確的金鑰保存庫私人 IP 位址，則您的私人 DNS 區域可能是正確的。 您可以略過這整個區段。

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>確認必要的私人 DNS 區域資源存在

您的 Azure 訂用帳戶必須具有具有此確切名稱的 [私人 DNS 區域](../../dns/private-dns-privatednszone.md) 資源：

    privatelink.vaultcore.azure.net

您可以前往入口網站中的 [訂用帳戶] 頁面，然後選取左側功能表上的 [資源]，來檢查此資源是否存在。 資源名稱必須是 `privatelink.vaultcore.azure.net` ，而且資源類型必須是 **私人 DNS 區域** 。

當您使用一般程式建立私人端點時，通常會自動建立此資源。 但在某些情況下，不會自動建立此資源，您必須手動進行。 此資源也可能已被意外刪除。

如果您沒有此資源，請在您的訂用帳戶中建立新的私人 DNS 區域資源。 請記住，此名稱必須完全 `privatelink.vaultcore.azure.net` 不包含空格或其他點。 如果您指定錯誤的名稱，本文中所述的名稱解析將無法運作。 如需有關如何建立此資源的詳細資訊，請參閱 [使用 Azure 入口網站建立 Azure 私人 DNS 區域](../../dns/private-dns-getstarted-portal.md)。 如果您遵循該頁面，您可以略過建立虛擬網路，因為此時您應該已經有虛擬網路。 您也可以略過虛擬機器的驗證程式。

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>確認私人 DNS 區域已連結至虛擬網路

有私人 DNS 區域並沒有足夠的空間。 它也必須連結至包含私人端點的虛擬網路。 如果私人 DNS 區域未連結到正確的虛擬網路，則該虛擬網路中的任何 DNS 解析將會忽略私人 DNS 區域。

開啟私人 DNS 區域資源，然後按一下左側功能表中的 [ **虛擬網路連結** ] 選項。 這會顯示連結的清單，每個都有您訂用帳戶的虛擬網路輸入名稱。 您必須在此列出包含私人端點資源的虛擬網路。 如果不存在，請加以新增。 如需詳細步驟，請參閱 [連結虛擬網路](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)。 您可以取消核取 [啟用自動註冊]，這項功能與私人連結無關。

一旦將私人 DNS 區域連結至虛擬網路，來自虛擬網路的 DNS 要求就會在私人 DNS 區域中尋找名稱。 在連接到建立私人端點的虛擬網路之虛擬機器上執行的正確位址解析時，就必須這樣做。

>[!NOTE]
> 如果您只是儲存連結，這可能需要一些時間才會生效，即使入口網站指出作業已完成。 您也可能需要將用來測試 DNS 解析的 VM 重新開機。

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-record"></a>確認私人 DNS 區域包含正確的 A 記錄

使用入口網站，以名稱開啟私人 DNS 區域 `privatelink.vaultcore.azure.net` 。 [總覽] 頁面會顯示所有記錄。 根據預設，將會有一個具有名稱 `@` 和類型的記錄 `SOA` ，表示 [授權單位啟動]。 請勿觸及。

若要讓金鑰保存庫名稱解析正常運作，必須有 `A` 具有簡單保存庫名稱的記錄，但不含尾碼或點。 例如，如果主機名稱是 `fabrikam.vault.azure.net` ，則必須有名稱為 `A` 的記錄 `fabrikam` ，而不含任何尾碼或點。

此外， `A` (IP 位址) 的記錄值必須是 [金鑰保存庫的私人 IP 位址](#find-the-key-vault-private-ip-address-in-the-virtual-network)。 如果您找到 `A` 記錄，但它包含錯誤的 ip 位址，您必須移除錯誤的 ip 位址，並新增一個。 建議您移除整個 `A` 記錄，並新增一個記錄。

>[!NOTE]
> 每當您移除或修改 `A` 記錄時，機器仍可解析為舊的 IP 位址，因為 TTL (存留時間) 值可能尚未過期。 建議您一律將 TTL 值指定為小於60秒 (一分鐘) ，而且不超過600秒 (10 分鐘的) 。 如果您指定的值太大，您的用戶端可能會花太多時間來復原中斷。

### <a name="dns-resolution-for-more-than-one-virtual-network"></a>一個以上虛擬網路的 DNS 解析

如果有多個虛擬網路，且每個虛擬網路都有自己的私人端點資源參考相同的金鑰保存庫，則金鑰保存庫主機名稱需要解析為不同的私人 IP 位址，視網路而定。 這表示也需要多個私人 DNS 區域，每個都連結至不同的虛擬網路，並在記錄中使用不同的 IP 位址 `A` 。

在更先進的案例中，虛擬網路可能已啟用對等互連。 在此情況下，只有一個虛擬網路需要私人端點資源，但兩者都需要連結至私人 DNS 區域資源。 這是本檔未直接涵蓋的案例。

### <a name="understand-that-you-have-control-over-dns-resolution"></a>瞭解您可以掌控 DNS 解析

如上一 [節](#key-vault-with-private-link-resolving-from-arbitrary-internet-machine)所述，具有私用連結的金鑰保存庫 `{vaultname}.privatelink.vaultcore.azure.net` 在其 *公開* 註冊中具有別名。 虛擬網路所使用的 DNS 伺服器會使用公開註冊，但它會檢查每個別名是否有 *私人* 註冊，如果找到的話，則會停止在公開註冊時定義的下列別名。

此邏輯表示如果虛擬網路連結到具有名稱的私人 DNS 區域 `privatelink.vaultcore.azure.net` ，且金鑰保存庫的公用 dns 註冊有別名 `fabrikam.privatelink.vaultcore.azure.net` (請注意，金鑰保存庫主機名稱尾碼會與私人 dns 區功能變數名稱稱完全相符) ，然後 DNS 查詢會 `A` `fabrikam` *在私人 dns 區域中* 尋找具有名稱的記錄。 如果 `A` 找到記錄，就會在 DNS 查詢中傳回其 IP 位址，且不會在公用 DNS 註冊中執行任何進一步的查閱。

您可以看到，名稱解析是在您的控制之下。 此設計的原理後會有期是：

- 您可能會有一個複雜的案例，包括自訂 DNS 伺服器，以及與內部部署網路整合。 在此情況下，您需要控制名稱如何轉譯為 IP 位址。
- 您可能需要存取沒有私用連結的金鑰保存庫。 在這種情況下，從虛擬網路解析主機名稱必須傳回公用 IP 位址，這是因為沒有私用連結的金鑰保存庫 `privatelink` 在名稱註冊中沒有別名。

## <a name="7-validate-that-requests-to-key-vault-use-private-link"></a>7. 驗證金鑰保存庫的要求使用 private link

### <a name="query-the-healthstatus-endpoint-of-the-key-vault"></a>查詢 `/healthstatus` 金鑰保存庫的端點

您的金鑰保存庫 `/healthstatus` 會提供可用於診斷的端點。 回應標頭包含來源 IP 位址，如金鑰保存庫服務所見。 您可以使用下列命令來呼叫該端點 ( **記得使用您的金鑰保存庫主機名稱** ) ：

Windows (PowerShell) ：

    PS C:\> $(Invoke-WebRequest -UseBasicParsing -Uri https://fabrikam.vault.azure.net/healthstatus).Headers

    Key                           Value
    ---                           -----
    Pragma                        no-cache
    x-ms-request-id               3729ddde-eb6d-4060-af2b-aac08661d2ec
    x-ms-keyvault-service-version 1.2.27.0
    x-ms-keyvault-network-info    addr=10.4.5.6;act_addr_fam=InterNetworkV6;
    Strict-Transport-Security     max-age=31536000;includeSubDomains
    Content-Length                4
    Cache-Control                 no-cache
    Content-Type                  application/json; charset=utf-8

Linux 或最新版本的 Windows 10，其中包括 `curl` ：

    joe@MyUbuntu:~$ curl -i https://fabrikam.vault.azure.net/healthstatus
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Pragma: no-cache
    Content-Type: application/json; charset=utf-8
    x-ms-request-id: 6c090c46-0a1c-48ab-b740-3442ce17e75e
    x-ms-keyvault-service-version: 1.2.27.0
    x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
    Strict-Transport-Security: max-age=31536000;includeSubDomains
    Content-Length: 4

如果您未取得類似的輸出，或發生網路錯誤，這表示您的金鑰保存庫無法透過您在範例)  (中指定的主機名稱來存取 `fabrikam.vault.azure.net` 。 可能是主機名稱未解析為正確的 IP 位址，或您在傳輸層有連線問題。 這可能是因為路由問題、封裝捨棄及其他原因所致。 您必須進一步調查。

回應必須包含標頭 `x-ms-keyvault-network-info` ：

    x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;

`addr`標頭中的欄位會 `x-ms-keyvault-network-info` 顯示要求來源的 IP 位址。 此 IP 位址可以是下列其中一項：

- 執行要求的電腦的私人 IP 位址。 這表示要求正在使用私用連結或服務端點。 這是私用連結的預期結果。
- 某些其他私人 IP 位址， *不* 是來自執行要求的電腦。 這表示某些自訂路由是有效的。 它仍然表示要求正在使用私人連結或服務端點。
- 某些公用 IP 位址。 這表示要求正在透過閘道 (NAT) 裝置路由傳送到網際網路。 這表示要求未使用私用連結，而某些問題需要修正。 這種情況的常見原因是 1) 私人端點未處於核准和成功狀態;和 2) 主機名稱未解析為金鑰保存庫的私人 IP 位址。 本文包含這兩種案例的疑難排解動作。

>[!NOTE]
> 如果要求 `/healthstatus` 正常運作，但 `x-ms-keyvault-network-info` 缺少標頭，則金鑰保存庫可能不會提供端點。 因為上述命令也會驗證 HTTPS 憑證，所以遺漏的標頭可能是篡改的符號。

### <a name="query-the-key-vault-ip-address-directly"></a>直接查詢金鑰保存庫 IP 位址

>[!IMPORTANT]
> 存取未使用 HTTPS 憑證驗證的金鑰保存庫很危險，而且只能用於學慣用途。 實際執行程式碼絕不能存取金鑰保存庫，而不需要進行此用戶端驗證。 即使您只是要診斷問題，如果您經常在金鑰保存庫的要求中停用 HTTPS 憑證驗證，則可能會受到篡改嘗試，而不會顯示。

如果您已安裝最新版的 PowerShell，您可以使用 `-SkipCertificateCheck` 略過 HTTPS 憑證檢查，然後您可以直接將 [金鑰保存庫 IP 位址](#find-the-key-vault-private-ip-address-in-the-virtual-network) 設為目標：

    PS C:\> $(Invoke-WebRequest -SkipCertificateCheck -Uri https://10.1.2.3/healthstatus).Headers

如果您使用的是 `curl` ，您可以使用引數來執行相同動作 `-k` ：

    joe@MyUbuntu:~$ curl -i -k https://10.1.2.3/healthstatus

回應必須與上一節中的相同，這表示它必須包含 `x-ms-keyvault-network-info` 具有相同值的標頭。 `/healthstatus`如果您使用金鑰保存庫主機名稱或 IP 位址，端點就不會在意。

如果您看到 `x-ms-keyvault-network-info` 使用金鑰保存庫主機名稱傳回要求的一個值，並使用 IP 位址針對要求傳回另一個值，則每個要求的目標是不同的端點。 請參閱 `addr` 上一節中的欄位說明 `x-ms-keyvault-network-info` ，以決定哪一種情況是錯誤的，而且需要修正。

## <a name="8-other-changes-and-customizations-that-cause-impact"></a>8. 造成影響的其他變更和自訂

下列專案是非徹底的調查動作。 他們會告訴您要在哪裡尋找其他問題，但您必須具備 advanced network 知識，才能修正這些案例中的問題。

### <a name="diagnose-custom-dns-servers-at-virtual-network"></a>診斷虛擬網路的自訂 DNS 伺服器

在入口網站中，開啟虛擬網路資源。 在左側功能表中，開啟 [ **DNS 伺服器** ]。 如果您使用「自訂」，則 DNS 解析可能不會如本檔中所述。 您必須診斷 DNS 伺服器解析金鑰保存庫主機名稱的方式。

如果您使用預設的 Azure 提供的 DNS 伺服器，這整份檔都適用。

### <a name="diagnose-hosts-overriding-or-custom-dns-servers-at-virtual-machine"></a>診斷主機在虛擬機器上覆寫或自訂 DNS 伺服器

許多作業系統都可讓您設定每個主機名稱的明確固定 IP 位址，通常是藉由變更檔案 `hosts` 。 它們也可以允許覆寫 DNS 伺服器。 如果您使用上述其中一種情況，請繼續進行系統專屬的診斷選項。

### <a name="promiscuous-proxies-fiddler-etc"></a>混合 proxy (Fiddler 等 ) 

除非有明確注明，否則本文中的診斷選項只適用于環境中沒有任何混合的 proxy。 雖然這些 proxy 通常是以獨佔方式安裝在正在診斷的電腦上 (Fiddler 是最常見的範例) ，但是 advanced administrator 可能會覆寫 (Ca 的根憑證授權單位) ，並在提供網路中多部電腦的閘道裝置上安裝混合 proxy。 這些 proxy 會大幅影響安全性和可靠性。 Microsoft 不支援使用這類產品的設定。

### <a name="other-things-that-may-affect-connectivity"></a>可能會影響連線能力的其他專案

這是一份不完整的專案清單，可在先進或複雜的案例中找到：

- 防火牆設定，也就是連線到虛擬網路的 Azure 防火牆，或在虛擬網路或電腦中部署的自訂防火牆解決方案。
- 網路對等互連，可能會影響使用的 DNS 伺服器，以及流量的路由方式。
- 自訂閘道 (NAT) 解決方案，這可能會影響流量的路由，包括來自 DNS 查詢的流量。