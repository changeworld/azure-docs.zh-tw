---
title: 設定 Azure 區塊鏈服務的交易節點
description: 如何設定 Azure 區塊鏈服務的交易節點
ms.date: 11/20/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: b3a4650977f1ad1a7a6967daa162adc8d2ef7bff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530381"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>設定 Azure 區塊鏈服務的交易節點

交易節點會透過公用端點，用來將區塊鏈交易傳送至 Azure 區塊鏈服務。 預設交易節點包含在區塊鏈上註冊之乙太坊帳戶的私密金鑰，因此無法刪除。

若要查看預設交易節點詳細資料：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至您的 Azure 區塊鏈服務成員。 選取 [ **交易節點**]。

    ![選取預設交易節點](./media/configure-transaction-nodes/nodes.png)

    總覽詳細資料包含公用端點位址和公開金鑰。

## <a name="create-transaction-node"></a>建立交易節點

您最多可以將9個額外的交易節點新增至您的區塊鏈成員，總共10個交易節點。 藉由新增交易節點，您可以增加擴充性或分散負載。 例如，您可能會有不同用戶端應用程式的交易節點端點。

若要加入交易節點：

1. 在 Azure 入口網站中，流覽至您的 Azure 區塊鏈服務成員，然後選取 [ **交易節點 > 新增**]。
1. 完成新交易節點的設定。

    ![新增交易節點](./media/configure-transaction-nodes/add-node.png)

    | 設定 | 描述 |
    |---------|-------------|
    | 名稱 | 交易節點名稱。 該名稱會用來建立交易節點端點的 DNS 位址。 例如： `newnode-myblockchainmember.blockchain.azure.com` 。 節點名稱在建立後即無法變更。 |
    | 密碼 | 設定強式密碼。 使用密碼可透過基本驗證來存取交易節點端點。

1. 選取 [建立]****。

    佈建一個新的交易節點大約需要 10 分鐘。 額外的交易節點會產生成本。 如需成本的詳細資訊，請參閱 [Azure 定價](https://aka.ms/ABSPricing)。

## <a name="endpoints"></a>端點

交易節點具有唯一的 DNS 名稱和公用端點。

若要查看交易節點的端點詳細資料：

1. 在 Azure 入口網站中，流覽至其中一個 Azure 區塊鏈服務成員交易節點，然後選取 **[總覽**]。

    ![螢幕擷取畫面會顯示區塊鏈成員的交易節點總覽。](./media/configure-transaction-nodes/endpoints.png)

交易節點端點是安全的，且需要驗證。 您可以使用 Azure AD authentication、HTTPS 基本驗證，以及透過 HTTPS 的存取金鑰或透過 TLS 的 Websocket，來連接到交易端點。

### <a name="azure-active-directory-access-control"></a>Azure Active Directory 存取控制

Azure 區塊鏈服務的交易節點端點支援 Azure Active Directory (Azure AD) 驗證。 您可以將 Azure AD 使用者、群組和服務主體存取權授與您的端點。

若要將 Azure AD 存取控制授與您的端點：

1. 在 Azure 入口網站中，流覽至您的 Azure 區塊鏈服務成員，然後選取 [ **交易節點] > 存取控制 (IAM) > 新增 > 新增角色指派**]。
1. 為使用者、群組或服務主體建立新的角色指派， (應用程式角色) 。

    ![新增 IAM 角色](./media/configure-transaction-nodes/add-role.png)

    | 設定 | 動作 |
    |---------|-------------|
    | 角色 | 選取 [ **擁有**者]、[ **參與者**] 或 [ **讀者**]。
    | 存取權指派對象 | 選取 **Azure AD 使用者、群組或服務主體**。
    | Select | 搜尋您想要新增的使用者、群組或服務主體。

1. 選取 [ **儲存** ] 以新增角色指派。

如需有關 Azure AD 存取控制的詳細資訊，請參閱 [使用 RBAC 和 Azure 入口網站管理 Azure 資源的存取權](../../role-based-access-control/role-assignments-portal.md)

如需如何使用 Azure AD authentication 連接的詳細資訊，請參閱 [使用 AAD 驗證連接到您的節點](configure-aad.md)。

### <a name="basic-authentication"></a>基本驗證

若是 HTTPS 基本驗證，則會在要求的 HTTPS 標頭中，將使用者名稱和密碼認證傳遞給端點。

您可以在 Azure 入口網站中查看交易節點的基本驗證端點詳細資料。 流覽至其中一個 Azure 區塊鏈服務成員交易節點，並在 [設定] 中選取 [ **基本驗證** ]。

![基本驗證](./media/configure-transaction-nodes/basic.png)

使用者名稱是您的節點名稱，而且無法變更。

若要使用 URL，請以布 \<password\> 建節點時所設定的密碼取代。 您可以藉由選取 [ **重設密碼**] 來更新密碼。

### <a name="access-keys"></a>便捷鍵

針對存取金鑰驗證，存取金鑰包含在端點 URL 中。 布建交易節點時，會產生兩個存取金鑰。 您可以使用這兩個存取金鑰進行驗證。 您可以使用兩個金鑰來變更和旋轉金鑰。

您可以查看交易節點的存取金鑰詳細資料，並複製包含存取金鑰的端點位址。 流覽至其中一個 Azure 區塊鏈服務成員交易節點，然後選取 [設定] 中的 [ **存取金鑰** ]。

### <a name="firewall-rules"></a>防火牆規則

防火牆規則可讓您限制可嘗試向交易節點進行驗證的 IP 位址。  如果未針對交易節點設定任何防火牆規則，則任何合作物件都無法存取它。  

若要查看交易節點的防火牆規則，請流覽至其中一個 Azure 區塊鏈服務成員交易節點，然後選取 [設定] 中的 [ **防火牆規則** ]。

您可以在 [ **防火牆規則** ] 方格中輸入規則名稱、起始 ip 位址和結束 ip 位址，以新增防火牆規則。

![防火牆規則](./media/configure-transaction-nodes/firewall-rules.png)

若要啟用：

* **單一 IP 位址：** 為開始和結束 IP 位址設定相同的 IP 位址。
* **IP 位址範圍：** 設定開始和結束 IP 位址範圍。 例如，從10.221.34.0 開始並結束于10.221.34.255 的範圍會啟用整個10.221.34.xxx 子網。
* **允許所有 IP 位址：** 將起始 IP 位址設定為0.0.0.0，並將結束 IP 位址設定為255.255.255.255。

## <a name="connection-strings"></a>連接字串

您可以針對基本驗證或使用存取金鑰，提供交易節點的連接字串語法。 提供透過 HTTPS 和 Websocket 的連接字串，包括存取金鑰。

您可以查看交易節點的連接字串，並複製端點位址。 流覽至其中一個 Azure 區塊鏈服務成員交易節點，並在 [設定] 中選取 [ **連接字串** ]。

![連接字串](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>範例程式碼

提供的範例程式碼可讓您透過 Web3、Nethereum、Web3js 和 Truffle，快速地連接到您的交易節點。

您可以查看交易節點的範例連接程式碼，並將其複製以搭配熱門的開發人員工具使用。 移至其中一個 Azure 區塊鏈服務成員交易節點，然後選取 [設定] 中的 **範例程式碼** 。

選擇 [Web3]、[Nethereum]、[Truffle] 或 [Web3j] 索引標籤，以查看您想要使用的程式碼範例。

![範例程式碼](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure CLI 設定交易節點](manage-cli.md)
