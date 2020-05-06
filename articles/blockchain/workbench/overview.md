---
title: Azure Blockchain Workbench 預覽概觀
description: Azure Blockchain Workbench 預覽及其功能的概觀。
ms.date: 09/05/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: 162626ba2303ab04bd2963a5add61c556615ebec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "79203639"
---
# <a name="what-is-azure-blockchain-workbench"></a>什麼是 Azure Blockchain Workbench？

Azure Blockchain Workbench 預覽是 Azure 服務和功能的集合，目的是要協助您建立及部署區塊鏈應用程式，以與其他組織共用商務程序和資料。 Azure Blockchain Workbench 能為建置區塊鏈應用程式提供基礎結構 Scaffolding，讓開發人員可以專注於建立商務邏輯和智慧型合約上。 它也可以透過整合數個 Azure 服務和功能來協助自動化一般開發工作，讓使用者能更輕鬆地建立區塊鏈應用程式。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="create-blockchain-applications"></a>建立區塊鏈應用程式

透過使用 Blockchain Workbench，您可以使用設定與撰寫智慧型合約程式碼，來定義區塊鏈應用程式。 您可以啟動區塊鏈應用程式開發，並專注於定義合約與撰寫商務邏輯，而非建置 Scaffolding 和設定支援服務上。

## <a name="manage-applications-and-users"></a>管理應用程式與使用者

Azure Blockchain Workbench 提供用於管理區塊鏈應用程式和使用者的 Web 應用程式和 REST API。 Blockchain Workbench 系統管理員可以管理應用程式存取，並指派應用程式角色給您的使用者。 Azure AD 使用者會自動對應到應用程式中的成員。

## <a name="integrate-blockchain-with-applications"></a>整合區塊鏈與應用程式

您可以使用 Blockchain Workbench REST API 和訊息型 API 來與現有系統整合。 這些 API 會提供介面，以允許取代或使用多個分散式總帳技術、儲存體和資料庫供應項目。

Blockchain Workbench 可以轉換傳送至其訊息型 API 的訊息，以建置該區塊鏈原生 API 所預期格式的交易。  Workbench 可以簽署交易，並將交易路由傳送至適當的區塊鏈。 

Workbench 會自動將事件傳遞至服務匯流排和事件方格，以向下游取用者傳送訊息。 開發人員可以與這些訊息傳送系統之一進行整合，以推動交易並查看結果。

## <a name="deploy-a-blockchain-network"></a>部署區塊鏈網路

Azure Blockchain Workbench 會使用 Azure Resource Manager 解決方案範本，將混合區塊鏈網路設定簡化為預先設定的解決方案。 該範本會提供能部署執行混合區塊鏈所需全部元件的簡化部署。 Blockchain Workbench 目前支援 Ethereum。

## <a name="use-active-directory"></a>使用 Active Directory

利用現有的區塊鏈通訊協定，區塊鏈身分識別會以網路上位址的形式來表示。 Azure Blockchain Workbench 能透過將區塊鏈身分識別與 Active Directory 身分識別建立關聯，來對區塊鏈身分識別進行抽象化處理，這能使搭配 Active Directory 身分識別建置企業應用程式變得更為簡單。

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>將鏈結上的資料同步處理至鏈結外的儲存體

Azure Blockchain Workbench 能透過將區塊鏈上的資料自動同步處理至鏈結外的儲存體，來使分析區塊鏈事件和資料變得更加容易。 與其直接從區塊鏈擷取資料，您可以查詢鏈結外的資料庫系統，例如 SQL Server。 進行資料分析工作的使用者並不需要具備區塊鏈專業知識。

## <a name="support-and-feedback"></a>支援與意見反應

如需 Azure 區塊鏈服務消息，請瀏覽 [Azure 區塊鏈服務部落格](https://azure.microsoft.com/blog/topics/blockchain/)，即時掌握 Azure 區塊鏈服務工程小組提供的區塊鏈服務供應項目和最新資訊。

若要提供產品意見反應或要求新功能，請透過 [Azure 區塊鏈服務的意見反應論壇](https://aka.ms/blockchainuservoice)，張貼票選想法。

### <a name="community-support"></a>社群支援

洽詢 Microsoft 工程師和 Azure 區塊鏈服務社群專家。

* [Azure 區塊鏈服務 MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain)
* [Microsoft 技術社群](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 架構](architecture.md)
