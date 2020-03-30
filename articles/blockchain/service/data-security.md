---
title: Azure 區塊鏈服務安全性
description: Azure 區塊鏈服務資料訪問和安全概念
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 06bf4e0fa4037b07505a4f816fc7af56c14576d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982234"
---
# <a name="azure-blockchain-service-security"></a>Azure 區塊鏈服務安全性

Azure 區塊鏈服務使用多個 Azure 功能來保證資料的安全性和可用性。 資料通過隔離、加密和身份驗證進行保護。

## <a name="isolation"></a>隔離

Azure 區塊鏈服務資源在專用虛擬網路中隔離。 每個事務和驗證節點都是虛擬機器 （VM）。 一個虛擬網路中的 VM 無法直接與不同虛擬網路中的 VM 通信。 隔離可確保虛擬網路中的通信保持私密性。 有關 Azure 虛擬網路隔離的詳細資訊，請參閱[Azure 公共雲中的隔離](../../security/fundamentals/isolation-choices.md#networking-isolation)。

![VNET 圖](./media/data-security/vnet.png)

## <a name="encryption"></a>加密

使用者資料存儲在 Azure 存儲中。 使用者資料在移動和靜態中加密，以增強安全性和機密性。 有關詳細資訊，請參閱[：Azure 存儲安全指南](../../storage/blobs/security-recommendations.md)。

## <a name="authentication"></a>驗證

事務可以通過 RPC 終結點發送到區塊鏈節點。 用戶端使用反向代理伺服器與事務節點通信，該伺服器處理使用者身份驗證並通過 SSL 加密資料。

![身份驗證圖](./media/data-security/authentication.png)

RPC 訪問有三種身份驗證模式。

### <a name="basic-authentication"></a>基本驗證

基本驗證使用包含使用者名和密碼的 HTTP 身份驗證標頭。 使用者名是區塊鏈節點的名稱。 密碼是在成員或節點的預配期間設置的。 可以使用 Azure 門戶或 CLI 更改密碼。

### <a name="access-keys"></a>存取金鑰

便捷鍵使用終結點 URL 中包含的隨機生成的字串。 兩個訪問金鑰有助於啟用金鑰輪換。 可以從 Azure 門戶和 CLI 重新生成金鑰。

### <a name="azure-active-directory"></a>Azure Active Directory

Azure 活動目錄 （Azure AD） 使用基於聲明的身份驗證機制，其中使用者由 Azure AD 使用 Azure AD 使用者憑據進行身份驗證。 Azure AD 提供基於雲的標識管理，並允許客戶在整個企業中使用單個標識並訪問雲上的應用程式。 Azure 區塊鏈服務與 Azure AD 支援 ID 聯合、單一登入和多重要素驗證集成。 您可以為組織中的使用者、組和應用程式角色分配區塊鏈成員和節點訪問。

Azure AD 用戶端代理在[GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases)上可用。 用戶端代理將使用者定向到 Azure AD 登錄頁，並在成功身份驗證後獲取無記名權杖。 隨後，使用者將以來者用戶端應用程式（如 Geth 或 Truffle）連接到用戶端代理的終結點。 最後，在提交事務時，用戶端代理在 HTTP 標頭中注入無記名權杖，反向代理使用 OAuth 協定驗證權杖。

## <a name="keys-and-ethereum-accounts"></a>鑰匙和以地庭帳戶

預配 Azure 區塊鏈服務成員時，將生成以天坊帳戶以及公開金鑰和私密金鑰對。 私密金鑰用於將事務發送到區塊鏈。 以地庭帳戶是公開金鑰雜湊的最後 20 個位元組。 以子坊帳戶也稱為錢包。

私密金鑰和公開金鑰對以 JSON 格式存儲為金鑰檔。 私密金鑰使用創建區塊鏈分類帳服務時輸入的密碼進行加密。

私密金鑰用於對事務進行數位簽章。 在私有區塊鏈中，由私密金鑰簽署的智慧合約代表簽名者的身份。 為了驗證簽名的有效性，接收方可以將簽名器的公開金鑰與從簽名計算的位址進行比較。

星座鍵用於唯一標識仲裁節點。 星座金鑰在節點預配時生成，並在 Quorum 中專用事務的私有 For 參數中指定。

## <a name="next-steps"></a>後續步驟

請參閱[如何為 Azure 區塊鏈服務配置 Azure 活動目錄訪問](configure-aad.md)。
