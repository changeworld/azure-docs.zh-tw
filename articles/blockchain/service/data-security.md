---
title: Azure 區塊鏈服務安全性
description: Azure 區塊鏈服務資料存取和安全概念
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 989d9f2afad30517a85185878d694c0b6640e987
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879593"
---
# <a name="azure-blockchain-service-security"></a>Azure 區塊鏈服務安全性

Azure 區塊鏈服務使用多個 Azure 功能來保證數據的安全性和可用性。 數據通過隔離、加密和身份驗證進行保護。

## <a name="isolation"></a>隔離

Azure 區塊鏈服務資源在專用虛擬網路中隔離。 每個事務和驗證節點都是虛擬機器 (VM)。 一個虛擬網路中的 VM 無法直接與不同虛擬網路中的 VM 通訊。 隔離可確保虛擬網路中的通信保持私密性。 有關 Azure 虛擬網路隔離的詳細資訊,請參閱[Azure 公共雲中的隔離](../../security/fundamentals/isolation-choices.md#networking-isolation)。

![VNET 圖](./media/data-security/vnet.png)

## <a name="encryption"></a>加密

使用者數據存儲在 Azure 儲存中。 用戶數據在移動和靜態中加密,以增強安全性和機密性。 有關詳細資訊,請參閱[:Azure 儲存安全指南](../../storage/blobs/security-recommendations.md)。

## <a name="authentication"></a>驗證

事務可以通過 RPC 終結點發送到區塊鏈節點。 用戶端使用反向代理伺服器與事務節點通信,該伺服器處理使用者身份驗證並加密通過 TLS 的數據。

![驗證圖](./media/data-security/authentication.png)

RPC 訪問有三種身份驗證模式。

### <a name="basic-authentication"></a>基本驗證

基本身份驗證使用包含使用者名稱和密碼的 HTTP 身份驗證標頭。 使用者名是區塊鏈節點的名稱。 密碼是在成員或節點的預配期間設置的。 可以使用 Azure 門戶或 CLI 更改密碼。

### <a name="access-keys"></a>存取金鑰

存取鍵使用終結點網址 中包含的隨機生成的字串。 兩個訪問密鑰有助於啟用密鑰輪換。 可以從 Azure 門戶和 CLI 重新生成密鑰。

### <a name="azure-active-directory"></a>Azure Active Directory

Azure 活動目錄 (Azure AD) 使用基於聲明的身份驗證機制,其中使用者由 Azure AD 使用 Azure AD 使用者認證進行身份驗證。 Azure AD 提供基於雲的標識管理,並允許客戶在整個企業中使用單個標識並造訪雲上的應用程式。 Azure 區塊鏈服務與 Azure AD 支援 ID 聯合、單一登錄和多重身份驗證集成。 您可以為組織中的使用者、組和應用程式角色分配區塊鏈成員和節點訪問。

Azure AD 客戶端代理在[GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases)上可用。 用戶端代理將使用者定向到 Azure AD 登錄頁,並在成功身份驗證後獲取無記名權杖。 隨後,使用者將以來者用戶端應用程式(如 Geth 或 Truffle)連接到用戶端代理的終結點。 最後,在提交事務時,用戶端代理在 HTTP 標頭中注入無記名令牌,反向代理使用 OAuth 協議驗證權杖。

## <a name="keys-and-ethereum-accounts"></a>鑰匙和以地庭帳戶

預配 Azure 區塊鏈服務成員時,將生成以天坊帳戶以及公鑰和私鑰對。 私鑰用於將事務發送到區塊鏈。 以地庭帳戶是公鑰哈希的最後 20 個字節。 以子坊帳戶也稱為錢包。

私密金鑰和公開金鑰對以 JSON 格式儲存為金鑰檔。 私鑰使用創建區塊鏈分類賬服務時輸入的密碼進行加密。

私密金鑰用於對事務進行數位簽章。 在私有區塊鏈中,由私鑰簽署的智能合約代表簽名者的身份。 為了驗證簽名的有效性,接收方可以將簽名器的公鑰與從簽名計算的地址進行比較。

星座鍵用於唯一標識仲裁節點。 星座密鑰在節點預配時生成,並在 Quorum 中專用事務的私有 For 參數中指定。

## <a name="next-steps"></a>後續步驟

請參考[如何為 Azure 區塊鏈服務設定 Azure 活動目錄存取](configure-aad.md)。
