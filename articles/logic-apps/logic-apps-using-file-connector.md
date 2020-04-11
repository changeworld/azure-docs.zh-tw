---
title: 連線到本地端檔案系統
description: 在 Azure Logic Apps 中透過內部部署資料閘道自動執行工作和工作流程，以利用檔案系統連接器來連線到內部部署檔案系統
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: ab17137f162b893b54942d870b07a36f87d1b71d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115080"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>透過 Azure Logic Apps 連線到內部部署檔案系統

使用 Azure 邏輯套用與檔案系統連接器,您可以建立自動工作與工作流,在本地端檔案分享建立與管理檔案,例如:

- 建立、取得、附加更新及刪除檔案。
- 列出資料夾或根資料夾中的檔案。
- 取得檔案內容與中繼資料。

本文說明如何如此範例案例所述來連線到內部部署檔案系統：將已上傳至 Dropbox 的檔案複製到檔案共用，然後傳送電子郵件。 若要安全地連線並存取內部部署系統，邏輯應用程式可使用[內部部署資料閘道](../logic-apps/logic-apps-gateway-connection.md)。 如果您是邏輯應用的新功能,請查看[什麼是 Azure 邏輯應用?](../logic-apps/logic-apps-overview.md) 有關特定連接器的技術資訊,請參考[檔案系統連線器參考](/connectors/filesystem/)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您必須先[安裝及設定內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md)，才可以將邏輯應用程式連線到內部部署系統 (例如檔案系統伺服器)。 如此一來，您可以在從邏輯應用程式建立檔案系統連線時，指定使用您的閘道安裝。

* [一個Dropbox帳戶](https://www.dropbox.com/),你可以免費註冊。 在邏輯應用和 Dropbox 帳戶之間創建連接時,您的帳戶憑據是必需的。

* 存取具有要使用的檔案系統的電腦。 例如,如果將數據閘道安裝到與文件系統相同的電腦上,則需要該電腦的帳戶憑據。

* Logic Apps 支援的任何電子郵件提供者 (例如 Office 365 Outlook、Outlook.com 或 Gmail) 所提供的電子郵件帳戶。 對於其他提供者，請[檢閱這裡的連接器清單](https://docs.microsoft.com/connectors/)。 此邏輯應用程式會使用 Office 365 Outlook 帳戶。 如果您使用另一個電子郵件帳戶，則整體步驟相同，但您的 UI 可能稍有不同。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。 針對此範例，您需要空白的邏輯應用程式。

## <a name="add-trigger"></a>新增觸發程序

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 在搜尋方塊中，輸入 "dropbox" 作為篩選條件。 從觸發程序清單中選取此觸發程序：**建立檔案時**

   ![選取 Dropbox 觸發程序](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. 使用您的 Dropbox 帳戶認證登入，並授與 Azure Logic Apps 存取您的 Dropbox 資料的權限。

1. 為您的觸發程序提供必要的資訊。

   ![Dropbox 觸發程序](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>新增動作

1. 在觸發程序下方，選擇 [下一個步驟]****。 在搜尋方塊中，輸入「檔案系統」作為篩選條件。 從操作清單中選擇此操作:**建立檔案**

   ![尋找檔案系統連接器](media/logic-apps-using-file-connector/find-file-system-action.png)

1. 如果您尚未與檔案系統建立連線，系統就會提示您建立連線。

   ![建立連線](media/logic-apps-using-file-connector/file-system-connection.png)

   | 屬性 | 必要 | 值 | 描述 |
   | -------- | -------- | ----- | ----------- |
   | **連線名稱** | 是 | <*連線名稱*> | 您想要的連線名稱 |
   | **根資料夾** | 是 | <*根資料夾名稱*> | 您檔案系統的根資料夾，例如，如果您已安裝內部部署的資料閘道，則為內部部署資料閘道安裝所在電腦上的本機資料夾，或電腦可以存取的網路共用資料夾。 <p>例如： `\\PublicShare\\DropboxFiles` <p>根資料夾是主要的父資料夾，會作為所有檔案相關動作的相對路徑。 |
   | **驗證類型** | 否 | <*auth 型*> | 檔案系統使用的身份驗證類型 **:Windows** |
   | **使用者名稱** | 是 | <*domain*網域\\*username*使用者<名稱>> | 您的檔案系統所在電腦的使用者名稱 |
   | **密碼** | 是 | <*您的密碼*> | 您的檔案系統所在電腦的密碼 |
   | **閘道** | 是 | <*已安裝閘道名稱*> | 先前所安裝閘道的名稱 |
   |||||

1. 完成之後，請選擇 [建立]****。

   Logic Apps 會設定並測試連線，以確定連線運作正常。 如果已正確設定連線，就會針對您先前選取的動作顯示選項。

1. 在 [建立檔案]**** 動作中，提供詳細資料，以將檔案從 Dropbox 複製到內部部署檔案共用的根資料夾。 若要新增先前步驟的輸出，請在方塊內按一下，並且在動態內容清單出現時，從可用的欄位進行選取。

   ![建立檔案動作](media/logic-apps-using-file-connector/create-file-filled.png)

1. 現在，新增可傳送電子郵件的 Outlook 動作，讓相關使用者了解新的檔案。 輸入電子郵件的收件者、標題和本文。 為了進行測試，您可以使用自己的電子郵件地址。

   ![傳送電子郵件動作](media/logic-apps-using-file-connector/send-email.png)

1. 儲存您的邏輯應用程式。 將檔案上傳至 Dropbox，以測試應用程式。

   您的邏輯應用程式應該將檔案複製到您的內部部署檔案共用，並將有關所複製檔案的電子郵件傳送給收件者。

## <a name="connector-reference"></a>連接器參考

有關此連線器的更多技術詳細資訊,例如連接器的 Swagger 檔所述的觸發器、操作和限制,請參閱[連接器的參考頁](https://docs.microsoft.com/connectors/fileconnector/)。

> [!NOTE]
> 對於[整合服務環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)的邏輯應用,此連線器的 ISE 標籤版本使用[ISE 訊息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>後續步驟

* 了解如何[連線至內部部署資料](../logic-apps/logic-apps-gateway-connection.md) 
* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
