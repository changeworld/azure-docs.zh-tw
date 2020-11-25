---
title: Azure Blockchain Workbench 架構
description: Azure Blockchain Workbench 預覽架構及其元件的總覽。
ms.date: 09/05/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 1fff70ef2eeb1dc27d33769fd48fe5923f56717b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021600"
---
# <a name="azure-blockchain-workbench-architecture"></a>Azure Blockchain Workbench 架構

Azure Blockchain Workbench Preview 提供使用數個 Azure 元件的解決方案，以簡化應用程式開發的區塊鏈。 您可以使用 Azure Marketplace 中的解決方案範本來部署 Blockchain Workbench。 範本可讓您挑選要部署的模組和元件，包括區塊鏈堆疊、用戶端應用程式類型及 IoT 整合支援。 Blockchain Workbench 一經部署，即可提供 Web 應用程式、iOS 應用程式和 Android 應用程式的存取權。

![Blockchain Workbench 架構](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>身分識別和驗證

協會可以使用 Blockchain Workbench，透過 Azure Active Directory (Azure AD) 讓其所有企業身分識別產生同盟。 Workbench 會使用 Azure AD 中所儲存的企業身分識別，為鏈結身分識別產生新的使用者帳戶。 身分識別對應除了可讓用戶端 API 和應用程式的登入接受驗證，還會使用組織的驗證原則。 Workbench 也能讓您將企業身分識別關聯至指定智慧合約中的特定角色。 此外，Workbench 也會提供機制供您識別這些角色可在何時採取哪些動作。

Blockchain Workbench 部署完成後，使用者可透過用戶端應用程式、以 REST 為基礎的用戶端 API 或傳訊 API 來與 Blockchain Workbench 互動。 不論是哪種方式，您都必須透過 Azure Active Directory (Azure AD) 或裝置專屬認證讓互動接受驗證。

使用者可對參與者傳送電子郵件邀請 (傳送到其電子郵件地址)，以便讓其身分識別與協會的 Azure AD 產生同盟。 這些使用者可在登入時使用名稱、密碼和原則來進行驗證。 例如，其組織的雙因素驗證。

Azure AD 可用來管理所有可以存取 Blockchain Workbench 的使用者。 每個與智慧合約連線的裝置也都會與 Azure AD 產生關聯。

Azure AD 也可用來將使用者指派至特殊的系統管理員群組。 與系統管理員群組相關聯的使用者會獲得 Blockchain Workbench 內權限和動作的存取權，包括部署合約及賦予使用者存取合約的權限。 此群組之外的使用者則無法存取系統管理員動作。

## <a name="client-applications"></a>用戶端應用程式

Workbench 會為 Web 和行動裝置 (iOS、Android) 提供自動產生的用戶端應用程式，以供用來驗證、測試及檢視區塊鏈應用程式。 系統會根據智慧合約中繼資料動態產生適用於任何使用案例的應用程式介面。 用戶端應用程式會對 Blockchain Workbench 所產生的完整區塊鏈應用程式提供與使用者互動的前端。 用戶端應用程式會透過 Azure Active Directory (Azure AD) 驗證使用者，然後針對智慧合約的商務內容提供量身打造的使用者體驗。 使用者體驗能夠依照獲得授權的個人建立新的智慧合約執行個體，然後在智慧合約所代表的商務程序中，於適當時間點執行特定類型的交易。

在 Web 應用程式中，獲得授權的使用者可以存取系統管理員主控台。 主控台會在 Azure AD 的系統管理員群組中提供給使用者使用，並且會提供下列功能的存取權：

* 針對常見案例部署 Microsoft 所提供的智慧合約。 例如，資產傳輸案例。
* 上傳並部署他們自己的智慧合約。
* 在特定角色的內容中對使用者指派智慧合約的存取權。

如需詳細資訊，請參閱 [GitHub 上的 Azure Blockchain Workbench 範例用戶端應用程式](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile)。

## <a name="gateway-service-api"></a>閘道服務 API

Blockchain Workbench 包含以 REST 為基礎的閘道服務 API。 在寫入至區塊鏈時，API 會產生訊息並傳遞至事件代理人。 當 API 要求資料時，查詢會傳送至離鏈資料庫。 資料庫包含連鎖資料的複本，以及提供支援的智慧型合約內容和設定資訊的中繼資料。 查詢會以合約中繼資料所通知的格式，從鏈結關閉複本傳回所需的資料。

開發人員可以存取閘道服務 API 來建置或整合區塊鏈解決方案，而不必依賴 Blockchain Workbench 用戶端應用程式來進行。

> [!NOTE]
> 為了對 API 的存取啟用驗證，會在 Azure Active Directory 中註冊兩個用戶端應用程式。 Azure Active Directory 必須對每個應用程式類型 (原生和 Web) 使用不同的應用程式註冊。

## <a name="message-broker-for-incoming-messages"></a>內送郵件的訊息代理人

想要將訊息直接傳送給 Blockchain Workbench 的開發人員，可以直接將訊息傳送給服務匯流排。 例如，訊息 API 可用於系統對系統的整合或 IoT 裝置。

## <a name="message-broker-for-downstream-consumers"></a>下游取用者的訊息代理人

在應用程式的生命週期內會發生各種事件。 這些事件可能是由閘道 API 或在總帳上觸發。 事件通知可以起始以事件為基礎的下游程式碼。

Blockchain Workbench 會自動部署兩種類型的事件消費者。 其中一種消費者會由區塊鏈事件觸發來填入鏈外 SQL 存放區。 另一種消費者則是用來針對與文件上傳和儲存相關之 API 所產生的事件，擷取其中繼資料。

## <a name="message-consumers"></a>訊息取用者

 訊息取用者會從服務匯流排取得訊息。 訊息取用者的基礎事件模型可讓您擴充其他服務和系統。 例如，您可以新增用來填入 CosmosDB 的支援，或使用 Azure 串流分析來評估訊息。 下列幾節會說明 Blockchain Workbench 中所包含的訊息取用者。

### <a name="distributed-ledger-consumer"></a>分散式總帳取用者

分散式總帳技術 (DLT) 訊息包含要寫入至區塊鏈的交易中繼資料。 取用者會擷取訊息，然後將資料推送至交易產生器、簽署者和路由器。

### <a name="database-consumer"></a>資料庫取用者

資料庫取用者會從服務匯流排取得訊息，並將資料推送至附加的資料庫，例如 Azure SQL Database 中的資料庫。

### <a name="storage-consumer"></a>儲存體取用者

儲存體取用者會從服務匯流排取得訊息，然後將資料推送至連結的儲存體。 例如，將雜湊的文件儲存在 Azure 儲存體。

## <a name="transaction-builder-and-signer"></a>交易產生器和簽署者

如果輸入訊息代理人上的訊息需要寫入至區塊鏈，將會由 DLT 取用者負責處理。 DLT 取用者是一項服務，其會擷取訊息 (其中包含所要執行交易的中繼資料)，然後將資訊傳送至「交易產生器和簽署者」。 「交易產生器和簽署者」會將以資料為基礎的區塊鏈交易與所需的 區塊鏈目的地組合在一起。 一經組合，便會簽署交易。 私密金鑰會儲存在 Azure Key Vault。

 Blockchain Workbench 會從 Key Vault 擷取適當的私密金鑰，並在 Key Vault 外簽署交易。 一經簽署，交易就會傳送至交易路由器和總帳。

## <a name="transaction-routers-and-ledgers"></a>交易路由器和總帳

交易路由器和總帳會取得已經簽署的交易，並將它們路由傳送至適當的區塊鏈。 目前，Blockchain Workbench 支援以 Ethereum 作為其目標區塊鏈。

## <a name="dlt-watcher"></a>DLT 監看員

分散式總帳技術 (DLT) 監看員會監視連結至 Blockchain Workbench 的區塊鏈上所發生的事件。
事件會反映個人和系統的相關資訊。 例如，建立新的合約執行個體、執行交易和變更狀態。 系統會擷取事件並傳送至輸出訊息代理人，以供下游取用者取用。

例如，SQL 取用者會監視事件、取用事件，然後以包含的值填入資料庫。 進行複製可在鏈結關閉存放區中重新建立鏈結資料的複本。

## <a name="azure-sql-database"></a>Azure SQL Database

附加至 Blockchain Workbench 的資料庫會儲存合約定義、設定中繼資料，以及儲存在區塊鏈中的 SQL 可存取資料複本。 直接存取資料庫，即可輕鬆地對此資料進行查詢、視覺化或分析。 開發人員和其他使用者可以使用資料庫來進行報告、分析或其他以資料為中心的整合。 例如，使用者可以使用 Power BI 將交易資料視覺化。

這個鏈結關閉儲存體可讓企業組織在 SQL 中 (而不是在區塊鏈總帳中) 查詢資料。 此外，藉由對不限定於區塊鏈技術堆疊的標準結構描述標準化，鏈結關閉儲存體可跨專案、案例和組織重複使用報告和其他成品。

## <a name="azure-storage"></a>Azure 儲存體

Azure 儲存體可用來儲存合約以及與合約相關聯的中繼資料。

從採購單和提貨單、到新聞和醫學影像所使用的圖片，再到源自 Continuum 的影片 (包括警用隨身攝影機和強檔電影)，文件在許多以區塊鏈為中心的案例中都扮演一定角色。 文件不適合直接放在區塊鏈上。

Blockchain Workbench 能夠使用區塊鏈商務邏輯來新增文件或其他媒體內容。 文件或媒體內容的雜湊會儲存在區塊鏈中，實際的文件或媒體內容則會儲存在 Azure 儲存體中。 相關聯的交易資訊會傳遞至輸入訊息代理人、加以封裝、簽署，並路由傳送至區塊鏈。 此程序會觸發事件，這些事件會透過輸出訊息代理人來共用。 SQL DB 會取用這項資訊，並將其傳送至 DB 以便稍後進行查詢。 下游系統也可取用這些事件以採取適當行動。

## <a name="monitoring"></a>監視

Workbench 使用 Application Insights 和 Azure 監視器來提供應用程式記錄。 Application Insights 可用來儲存 Blockchain Workbench 中所記錄的所有資訊，並且會包含錯誤、警告和成功的作業。 Application Insights 可供開發人員用來對 Blockchain Workbench 的問題進行偵錯。 

Azure 監視器可提供有關區塊鏈網路健康情況的資訊。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [部署 Azure Blockchain Workbench](./deploy.md)
