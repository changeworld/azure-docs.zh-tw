---
title: Azure 區塊鏈服務安全性
description: Azure 區塊鏈服務資料存取和安全性概念
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 989d9f2afad30517a85185878d694c0b6640e987
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80879593"
---
# <a name="azure-blockchain-service-security"></a>Azure 區塊鏈服務安全性

Azure 區塊鏈服務會使用數個 Azure 功能來確保您的資料安全且可供使用。 使用隔離、加密和驗證來保護資料。

## <a name="isolation"></a>隔離

Azure 區塊鏈服務資源會在私人虛擬網路中隔離。 每個交易和驗證節點都是虛擬機器 (VM) 。 一個虛擬網路中的 Vm 無法直接與不同虛擬網路中的 Vm 通訊。 隔離可確保虛擬網路內的通訊保持私密。 如需 Azure 虛擬網路隔離的詳細資訊，請參閱 [Azure 公用雲端中的隔離](../../security/fundamentals/isolation-choices.md#networking-isolation)。

![VNET 圖表](./media/data-security/vnet.png)

## <a name="encryption"></a>加密

使用者資料會儲存在 Azure 儲存體中。 使用者資料會在動態和待用時加密，以獲得安全性和機密性。 如需詳細資訊，請參閱： [Azure 儲存體安全性指南](../../storage/blobs/security-recommendations.md)。

## <a name="authentication"></a>驗證

您可以透過 RPC 端點將交易傳送至區塊鏈節點。 用戶端會使用反向 proxy 伺服器與交易節點通訊，以處理使用者驗證並透過 TLS 加密資料。

![驗證圖表](./media/data-security/authentication.png)

RPC 存取的驗證模式有三種。

### <a name="basic-authentication"></a>基本驗證

基本驗證使用包含使用者名稱和密碼的 HTTP 驗證標頭。 使用者名稱是區塊鏈節點的名稱。 密碼是在布建成員或節點期間設定。 您可以使用 Azure 入口網站或 CLI 來變更密碼。

### <a name="access-keys"></a>便捷鍵

存取金鑰使用包含在端點 URL 中的隨機產生字串。 有兩個存取金鑰有助於啟用金鑰輪替。 您可以從 Azure 入口網站和 CLI 重新產生金鑰。

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) 使用以宣告為基礎的驗證機制，其中使用者會透過 Azure AD 使用 Azure AD 使用者認證進行驗證。 Azure AD 提供雲端式身分識別管理，並可讓客戶在整個企業中使用單一身分識別，並存取雲端上的應用程式。 Azure 區塊鏈服務與 Azure AD 啟用識別碼同盟、單一登入和多重要素驗證整合。 您可以為組織中的使用者、群組和應用程式角色指派區塊鏈成員和節點存取權。

您可以在 [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases)上取得 Azure AD 用戶端 proxy。 用戶端 proxy 會將使用者導向 Azure AD 登入頁面，並在驗證成功時取得持有人權杖。 之後，使用者會將乙太坊用戶端應用程式（例如 Geth 或 Truffle）連接至用戶端 proxy 的端點。 最後，提交交易時，用戶端 proxy 會在 HTTP 標頭中插入持有人權杖，而反向 proxy 會使用 OAuth 通訊協定來驗證權杖。

## <a name="keys-and-ethereum-accounts"></a>金鑰和乙太坊帳戶

布建 Azure 區塊鏈服務成員時，會產生乙太坊帳戶和公開和私密金鑰組。 私密金鑰用來將交易傳送至區塊鏈。 乙太坊帳戶是公開金鑰雜湊的最後20個位元組。 乙太坊帳戶也稱為錢包。

私用和公開金鑰組會儲存為 JSON 格式的 keyfile。 私密金鑰會使用建立區塊鏈總帳服務時輸入的密碼進行加密。

私密金鑰用來數位簽署交易。 在私用區塊鏈中，私密金鑰所簽署的智慧型合約代表簽署者的身分識別。 若要驗證簽章的有效性，接收者可以比較簽署者的公開金鑰與簽章所計算的位址。

Constellation 索引鍵可用來唯一識別仲裁節點。 Constellation 索引鍵是在節點布建時產生，並且在仲裁的私用交易的 >privatefor 參數中指定。

## <a name="next-steps"></a>後續步驟

請參閱 [如何設定 Azure 區塊鏈服務的 Azure Active Directory 存取](configure-aad.md)。
