---
title: 配置 Azure 區塊鏈服務事務節點
description: 如何配置 Azure 區塊鏈服務事務節點
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 4a9a4f660dd171e65b600ec4cd66714ca476b091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252255"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>配置 Azure 區塊鏈服務事務節點

事務節點用於通過公共終結點將區塊鏈事務發送到 Azure 區塊鏈服務。 預設事務節點包含在區塊鏈上註冊的以地庭帳戶的私密金鑰，因此無法刪除。

要查看預設事務節點詳細資訊：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 瀏覽至您的 Azure 區塊鏈服務成員。 選擇**事務節點**。

    ![選取預設交易節點](./media/configure-transaction-nodes/nodes.png)

    概述詳細資訊包括公共終結點位址和公開金鑰。

## <a name="create-transaction-node"></a>建立交易節點

您最多可以將 9 個附加交易節點添加到區塊鏈成員中，總共 10 個事務節點。 通過添加事務節點，可以增加可伸縮性或分配負載。 例如，您可以為不同的用戶端應用程式提供事務節點終結點。

要添加事務節點：

1. 在 Azure 門戶中，導航到 Azure 區塊鏈服務成員，然後**選擇事務節點>添加**。
1. 完成新事務節點的設置。

    ![添加事務節點](./media/configure-transaction-nodes/add-node.png)

    | 設定 | 描述 |
    |---------|-------------|
    | 名稱 | 交易節點名稱。 該名稱會用來建立交易節點端點的 DNS 位址。 例如： `newnode-myblockchainmember.blockchain.azure.com` 。 創建節點名稱後，無法更改節點名稱。 |
    | 密碼 | 設置強式密碼。 使用密碼使用基本驗證訪問事務節點終結點。

1. 選取 [建立]****。

    佈建一個新的交易節點大約需要 10 分鐘。 其他事務節點會產生成本。 有關成本的詳細資訊，請參閱[Azure 定價](https://aka.ms/ABSPricing)。

## <a name="endpoints"></a>端點

事務節點具有唯一的 DNS 名稱和公共終結點。

要查看事務節點的終結點詳細資訊：

1. 在 Azure 門戶中，導航到 Azure 區塊鏈服務成員事務節點之一，然後選擇 **"概述**"。

    ![端點](./media/configure-transaction-nodes/endpoints.png)

事務節點終結點是安全的，需要身份驗證。 您可以使用 Azure AD 身份驗證、HTTPS 基本驗證以及通過 HTTPS 或 Web通訊端通過 SSL 訪問金鑰連接到事務終結點。

### <a name="azure-active-directory-access-control"></a>Azure 活動目錄存取控制

Azure 區塊鏈服務事務節點終結點支援 Azure 活動目錄 （Azure AD） 身份驗證。 您可以授予 Azure AD 使用者、組和服務主體對終結點的存取權限。

要向終結點授予 Azure AD 存取控制，請執行：

1. 在 Azure 門戶中，導航到 Azure 區塊鏈服務成員，然後選擇**事務節點>存取控制 （IAM） >添加>添加角色指派**。
1. 為使用者、組或服務主體（應用程式角色）創建新的角色指派。

    ![添加 IAM 角色](./media/configure-transaction-nodes/add-role.png)

    | 設定 | 動作 |
    |---------|-------------|
    | 角色 | 選擇**擁有者**、**參與者**或**讀取器**。
    | 存取權指派對象 | 選擇**Azure AD 使用者、組或服務主體**。
    | 選取 | 搜索要添加的使用者、組或服務主體。

1. 選擇 **"保存"** 以添加角色指派。

有關 Azure AD 訪問控制項的詳細資訊，請參閱[使用 RBAC 和 Azure 門戶管理對 Azure 資源的訪問](../../role-based-access-control/role-assignments-portal.md)

有關如何使用 Azure AD 身份驗證進行連接的詳細資訊，請參閱[使用 AAD 身份驗證連接到節點](configure-aad.md)。

### <a name="basic-authentication"></a>基本驗證

對於 HTTPS 基本驗證，使用者名和密碼憑據在請求的 HTTPS 標頭中傳遞給終結點。

您可以在 Azure 門戶中查看事務節點的基本驗證終結點詳細資訊。 導航到 Azure 區塊鏈服務成員事務節點之一，並在設置中選擇 **"基本驗證**"。

![基本驗證](./media/configure-transaction-nodes/basic.png)

使用者名是節點的名稱，無法更改。

要使用 URL，請將\<密碼\>替換為預配節點時設置的密碼。 您可以通過選擇 **"重置密碼"** 來更新密碼。

### <a name="access-keys"></a>存取金鑰

對於訪問金鑰身份驗證，訪問金鑰包含在終結點 URL 中。 預配事務節點時，將生成兩個訪問金鑰。 任一訪問金鑰都可用於身份驗證。 兩個鍵使您能夠更改和旋轉鍵。

您可以查看事務節點的訪問金鑰詳細資訊，並複製包含訪問金鑰的終結點位址。 導航到 Azure 區塊鏈服務成員事務節點之一，並在設置中選擇**訪問金鑰**。

### <a name="firewall-rules"></a>防火牆規則

防火牆規則使您能夠限制可以嘗試向事務節點進行身份驗證的 IP 位址。  如果未為事務節點配置防火牆規則，則任何一方都無法訪問該防火牆規則。  

要查看事務節點的防火牆規則，請導航到 Azure 區塊鏈服務成員事務節點之一，並在設置中選擇**防火牆規則**。

您可以通過在防火牆規則網格中輸入規則名稱、啟動 IP 位址和結束 IP 位址來添加**防火牆規則**。

![防火牆規則](./media/configure-transaction-nodes/firewall-rules.png)

要啟用：

* **單個 IP 位址：** 為啟動和結束 IP 位址配置相同的 IP 位址。
* **IP 位址範圍：** 配置啟動和結束 IP 位址範圍。 例如，從 10.221.34.0 開始到 10.221.34.255 結束的範圍將啟用整個10.221.34.xxx子網。
* **允許所有 IP 位址：** 將起始 IP 位址配置為 0.0.0.0，將結束 IP 位址配置為 255.255.255.255。

## <a name="connection-strings"></a>連接字串

為事務節點提供連接字串語法以進行基本驗證或使用訪問金鑰。 提供連接字串，包括 HTTPS 和 WebSocket 的訪問金鑰。

您可以查看事務節點的連接字串和複製終結點位址。 導航到 Azure 區塊鏈服務成員事務節點之一，並在設置中選擇**連接字串**。

![連接字串](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>範例程式碼

提供示例代碼可快速啟用通過 Web3、Nethereum、Web3js 和 Truffle 連接到事務節點。

您可以查看事務節點的示例連接代碼並將其複製到常用開發人員工具中。 轉到 Azure 區塊鏈服務成員事務節點之一，並在設置中選擇**示例代碼**。

選擇 Web3、內蒂圖姆、特魯弗爾或 Web3j 選項卡以查看要使用的代碼示例。

![範例程式碼](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure CLI 配置事務節點](manage-cli.md)
