---
title: 用於 B2B 集成的羅塞塔網消息
description: 將 Azure 邏輯應用中的羅塞塔網消息與企業集成包交換
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 2cc2ac08b9624c1d1d9bee9ce91a7c91189d7f2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792412"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>在 Azure 邏輯應用中為 B2B 企業集成交換羅塞塔網消息

[羅塞塔網](https://resources.gs1us.org)是一個非營利性財團，已經建立了共用商務資訊的標準流程。 這些標準通常用於供應鏈流程，在半導體、電子和物流行業廣泛分佈。 羅塞塔網聯盟創建和維護合作夥伴介面流程 （PPs），為所有羅塞塔網消息交換提供通用業務流程定義。 RosettaNet 基於 XML，定義了消息準則、業務流程介面以及公司之間通信的實現框架。

在[Azure 邏輯應用中](../logic-apps/logic-apps-overview.md)，羅塞塔網連接器可説明您創建支援羅塞塔網標準的集成解決方案。 該連接器基於羅塞塔網實施框架 （RNIF） 版本 2.0.01。 RNIF 是一個開放的網路應用程式框架，使業務合作夥伴能夠協作運行羅塞塔網 PIP。 此框架定義郵件結構、確認需求、多用途 Internet 郵件擴展 （MIME） 編碼和數位簽章。

具體而言，連接器提供以下功能：

* 編碼或接收羅塞塔網消息。
* 解碼或發送羅塞塔網消息。
* 等待故障通知的回應和生成。

對於這些功能，連接器支援由 RNIF 2.0.01 定義的所有 PIP。 與合作夥伴的通信可以是同步的，也可以是非同步。

## <a name="rosettanet-concepts"></a>羅塞塔網概念

以下是羅塞塔網規範獨有的一些概念和術語，在構建基於羅塞塔網的集成時非常重要：

* **匹 普**

  羅塞塔網組織創建和維護合作夥伴介面流程 （PIP），為所有羅塞塔網消息交換提供通用業務流程定義。 每個 PIP 規格都提供了文件類型定義 (DTD) 檔案以及訊息指導方針文件。 DTD 檔案定義服務內容訊息結構。 訊息指導方針文件是可以閱讀的 HTML 檔案，其中指定元素階層條件約束。 這些檔共同提供了業務流程的完整定義。

   PIP 按高級業務功能、群集、子功能或細分進行分類。 例如，"3A4"是採購訂單的 PIP，而"3"是訂單管理功能，"3A"是報價&訂單輸入子功能。 有關詳細資訊，請參閱[羅塞塔網網站](https://resources.gs1us.org)。

* **動作**

  作為 PIP 的一部分，操作消息是合作夥伴之間交換的業務消息。

* **訊號**

   作為 PIP 的一部分，信號消息是回應操作消息而發送的確認。

* **單一操作和雙重操作**

  對於單操作 PIP，唯一的回應是確認信號消息。 對於雙操作 PIP，發源器除了單操作消息流外，還接收回應訊息和回復，並帶有確認。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 用於存儲協定和其他 B2B 工件的[集成帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 此集成帳戶必須與 Azure 訂閱關聯。

* 在集成帳戶中定義並配置在 **"業務標識**"下的"DUNS"限定詞的至少兩個[合作夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)

* 在集成帳戶中發送或接收羅塞塔網消息所需的 PIP 進程配置。 流程配置存儲所有 PIP 配置特徵。 然後，您可以在與合作夥伴創建協定時引用此配置。 要在集成帳戶中創建 PIP 流程配置，請參閱[添加 PIP 流程配置](#add-pip)。

* 用於加密、解密或簽名上載到集成帳戶的郵件的可選[證書](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 僅當您使用簽名或加密時，才需要證書。

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>添加 PIP 流程配置

要將 PIP 流程配置添加到集成帳戶，請按照以下步驟操作：

1. 在[Azure 門戶](https://portal.azure.com)中，查找並打開集成帳戶。

1. 在 **"概述"** 窗格中，選擇**羅塞塔網 PIP**磁貼。

   ![選擇羅塞塔網磁貼](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. 在**羅塞塔網 PIP**下，選擇 **"添加**"。 提供您的 PIP 詳細資訊。

   ![添加羅塞塔網 PIP 詳細資訊](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **名稱** | 是 | 您的 PIP 名稱 |
   | **PIP 代碼** | 是 | PIP 三位數代碼。 有關詳細資訊，請參閱[羅塞塔網 PIP](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips)。 |
   | **PIP 版本** | 是 | PIP 版本號，根據您選擇的 PIP 代碼提供 |
   ||||

   有關這些 PIP 屬性的詳細資訊，請訪問[羅塞塔網網站](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)。

1. 完成後，選擇 **"確定**"，這將創建 PIP 配置。

1. 要查看或編輯流程配置，請選擇 PIP，然後選擇 **"編輯為 JSON**"。

   所有流程配置設置均來自 PIP 的規範。 邏輯應用使用這些屬性最常用的值的預設值填充大多數設置。

   ![編輯羅塞塔網 PIP 配置](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. 確認這些設置與相應的 PIP 規範中的值相對應，並滿足您的業務需求。 如有必要，請更新 JSON 中的值並保存這些更改。

## <a name="create-rosettanet-agreement"></a>創建羅塞塔網協定

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並開啟您的整合帳戶 (若它尚未開啟)。

1. 在 **"概述"** 窗格中，選擇"**協定**"磁貼。

   ![選擇協定磁貼](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. 在 [合約]**** 之下，選擇 [新增]****。 提供您的協定詳細資訊。

   ![添加協定詳細資訊](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **名稱** | 是 | 協定名稱 |
   | **協定類型** | 是 | 選擇**羅塞塔網**。 |
   | **主控夥伴** | 是 | 合約需要主機合作夥伴和來賓合作夥伴。 主機夥伴代表設定合約的組織。 |
   | **主控身分識別** | 是 | 主控夥伴的識別碼 |
   | **來賓夥伴** | 是 | 合約需要主機合作夥伴和來賓合作夥伴。 來賓夥伴代表與主控夥伴有生意往來的組織。 |
   | **來賓身分識別** | 是 | 來賓合作夥伴的識別碼 |
   | **接收設定** | 不定 | 這些屬性適用于主機合作夥伴收到的所有消息 |
   | **傳送設定** | 不定 | 這些屬性適用于主機合作夥伴發送的所有郵件 |  
   | **羅塞塔網 PIP 參考** | 是 | 協定的 PIP 引用。 所有羅塞塔網消息都需要 PIP 配置。 |
   ||||

1. 要設置接收來自來賓合作夥伴傳入消息的協定，請選擇"**接收設置**"。

   ![接收設置](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. 要啟用傳入消息的簽名或加密，請在 **"消息"** 下，選擇 **"消息"或**分別**加密消息**。

      | 屬性 | 必要 | 描述 |
      |----------|----------|-------------|
      | **訊息應該簽署** | 否 | 使用所選證書對傳入郵件進行簽名。 |
      | **憑證** | 是，如果已啟用簽名 | 用於簽名的證書 |
      | **啟用訊息加密** | 否 | 使用所選證書加密傳入消息。 |
      | **憑證** | 是，如果啟用了加密 | 用於加密的證書 |
      ||||

   1. 在每次選擇下，選擇以前添加到集成帳戶的相應[證書](./logic-apps-enterprise-integration-certificates.md)，用於簽名或加密。

1. 要設置向來賓合作夥伴發送消息的協定，請選擇"**發送設置**"。

   ![發送設置](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. 要啟用傳出消息的簽名或加密，請在 **"消息"** 下分別選擇 **"啟用消息簽名**"或 **"啟用郵件加密**"。 在每次選擇下，選擇以前添加到集成帳戶的相應演算法和[證書](./logic-apps-enterprise-integration-certificates.md)，以用於簽名或加密。

      | 屬性 | 必要 | 描述 |
      |----------|----------|-------------|
      | **啟用訊息簽署** | 否 | 使用選定的簽名演算法和證書對傳出消息進行簽名。 |
      | **簽署演算法** | 是，如果已啟用簽名 | 基於所選證書要使用的簽名演算法 |
      | **憑證** | 是，如果已啟用簽名 | 用於簽名的證書 |
      | **啟用訊息加密** | 否 | 使用選定的加密演算法和證書加密傳出。 |
      | **加密演算法** | 是，如果啟用了加密 | 基於所選證書要使用的加密演算法 |
      | **憑證** | 是，如果啟用了加密 | 用於加密的證書 |
      ||||

   1. 在**端點**下，指定用於發送操作消息和確認所需的 URL。

      | 屬性 | 必要 | 描述 |
      |----------|----------|-------------|
      | **動作 URL** |  是 | 用於發送操作消息的 URL。 URL 是同步消息和非同步消息的必要欄位。 |
      | **確認 URL** | 是 | 用於發送確認訊息的 URL。 URL 是非同步消息的必要欄位。 |
      ||||

1. 要為合作夥伴設置與羅塞塔網 PIP 參考的約定，請選擇**羅塞塔網 PIP 參考。** 在**PIP 名稱**下，選擇以前創建的 PIP 的名稱。

   ![PIP 參考](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   您的選擇填充剩餘的屬性，這些屬性基於您在集成帳戶中設置的 PIP。 如有必要，可以更改 PIP**角色**。

   ![選定的 PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

完成這些步驟後，即可發送或接收羅塞塔網消息。

## <a name="rosettanet-templates"></a>羅塞塔網範本

為了加快開發和推薦整合模式，可以使用邏輯應用範本解碼和編碼羅塞塔網消息。 創建邏輯應用時，可以從邏輯應用設計器中的範本庫中選擇。 您還可以在 Azure 邏輯應用的[GitHub 存儲庫](https://github.com/Azure/logicapps)中找到這些範本。

![羅塞塔網範本](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>接收或解碼羅塞塔網消息

1. [創建空白邏輯應用](quickstart-create-first-logic-app-workflow.md)。

1. [將集成帳戶連結到](logic-apps-enterprise-integration-create-integration-account.md#link-account)邏輯應用。

1. 在添加解碼 RosettaNet 消息的操作之前，必須添加啟動邏輯應用的觸發器，如請求觸發器。

1. 添加觸發器後，選擇 **"新建步驟**"。

   ![添加請求觸發器](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 在搜索框中，輸入"羅塞塔內特"，然後選擇此操作：**羅塞塔網解碼**

   ![查找並選擇"羅塞塔網解碼"操作](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. 提供操作屬性的資訊：

   ![提供操作詳細資訊](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **消息** | 是 | 要解碼的羅塞塔網消息  |
   | **標頭** | 是 | 提供版本（RNIF 版本）和回應類型的 HTTP 標頭，指示合作夥伴之間的通信類型，並且可以是同步或非同步 |
   | **角色** | 是 | 主機合作夥伴在 PIP 中的角色 |
   ||||

   從羅塞塔網解碼操作中，輸出和其他屬性包括**出站信號**，您可以選擇對該信號進行編碼並返回到合作夥伴，或對該輸出執行任何其他操作。

## <a name="send-or-encode-rosettanet-messages"></a>發送或編碼羅塞塔網消息

1. [創建空白邏輯應用](quickstart-create-first-logic-app-workflow.md)。

1. [將集成帳戶連結到](logic-apps-enterprise-integration-create-integration-account.md#link-account)邏輯應用。

1. 在添加對羅塞塔網消息進行編碼的操作之前，必須添加用於啟動邏輯應用的觸發器，如請求觸發器。

1. 添加觸發器後，選擇 **"新建步驟**"。

   ![添加請求觸發器](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 在搜索框中，輸入"羅塞塔內特"，然後選擇此操作：**羅塞塔網編碼**

   ![查找並選擇"羅塞塔網編碼"操作](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. 提供操作屬性的資訊：

   ![提供操作詳細資訊](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **消息** | 是 | 要編碼的羅塞塔網消息  |
   | **主機合作夥伴** | 是 | 主機合作夥伴名稱 |
   | **賓客合作夥伴** | 是 | 來賓合作夥伴名稱 |
   | **PIP 代碼** | 是 | PIP 代碼 |
   | **PIP 版本** | 是 | PIP 版本 |  
   | **PIP 實例標識** | 是 | 此 PIP 消息的唯一識別碼 |  
   | **訊息類型** | 是 | 要編碼的消息的類型 |  
   | **角色** | 是 | 主機合作夥伴的角色 |
   ||||

   編碼的消息現在可以發送給合作夥伴了。

1. 要發送編碼消息，此示例使用**HTTP**操作，該操作重命名為"HTTP - 向合作夥伴發送編碼消息"。

   ![用於發送羅塞塔網消息的 HTTP 操作](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   根據羅塞塔網標準，只有當 PIP 定義的所有步驟都完成時，業務交易才被視為已完成。

1. 主機向合作夥伴發送編碼消息後，主機將等待信號和確認。 要完成此任務，請添加**羅塞塔Net等待回應**操作。

   ![添加"羅塞塔網等待回應"操作](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   用於等待的持續時間和重試次數基於集成帳戶中的 PIP 配置。 如果未收到回應，此操作將生成失敗通知。 要處理重試，請始終將 **"編碼"** 和"**等待回應**操作"放在 **"直到**"迴圈中。

   ![直到迴圈與羅塞塔網操作](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>後續步驟

* 了解驗證和轉換方式，以及[企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)中的其他訊息作業
* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
