---
title: 連接到 IBM MQ 伺服器
description: 使用 Azure 或本地 IBM MQ 伺服器和 Azure 邏輯應用程式傳送和檢索郵件
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 03/31/2020
tags: connectors
ms.openlocfilehash: 737c5b90b216156ca08346f4a64fd0b421ad6c19
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410171"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>從 Azure 邏輯套用連線到 IBM MQ 伺服器

IBM MQ 連接器發送和檢索存儲在本地或 Azure 中的 IBM MQ 伺服器中的郵件。 此連接器包含透過 TCP/IP 網路與遠端 IBM MQ Server 通訊的 Microsoft MQ 用戶端。 本文提供了使用 MQ 連接器的入門指南。 您可以首先瀏覽佇列上的單一訊息,然後嘗試其他操作。

IBM MQ 連接器包括這些操作,但不提供觸發器:

- 瀏覽單個郵件而不從IBM MQ伺服器中刪除郵件。
- 瀏覽一批郵件,而不從 IBM MQ 伺服器中刪除郵件。
- 接收單一訊息並從 IBM MQ 伺服器中刪除該郵件。
- 接收一批消息並從IBM MQ伺服器中刪除郵件。
- 向 IBM MQ 伺服器發送一條消息。

以下是官方支援的 IBM WebSphere MQ 版本:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

## <a name="prerequisites"></a>Prerequisites

* 如果您使用的是本地 MQ 伺服器,請在網路中的伺服器安裝[本地資料閘道](../logic-apps/logic-apps-gateway-install.md)。 安裝本地資料閘道的伺服器還必須安裝 .NET Framework 4.6,以便 MQ 連接器正常工作。

  安裝完閘道後,還必須在 Azure 中為本地數據閘道創建資源。 有關詳細資訊,請參考[設定資料閘道連線](../logic-apps/logic-apps-gateway-connection.md)。

  如果 MQ 伺服器在 Azure 中是公開的或可用的,則不必使用數據閘道。

* 要添加 MQ 操作的邏輯應用。 此邏輯應用必須使用與本地數據閘道連接相同的位置,並且必須已具有啟動工作流的觸發器。

  MQ 連接器沒有任何觸發器,因此必須首先向邏輯應用添加觸發器。 例如,可以使用"定期"觸發器。 如果您是邏輯應用的新增功能,請嘗試這個[快速入門建立第一個邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>建立 MQ 連線

如果在新增 MQ 操作時尚未建立 MQ 連線,系統將提示您建立連接,例如:

![提供連線資訊](media/connectors-create-api-mq/connection-properties.png)

1. 如果要連接到本地 MQ 伺服器,請選擇 **「透過本地資料閘道連線**」。

1. 提供 MQ 伺服器的連接資訊。

   * 針對 [伺服器]****，您可以輸入 MQ Server 的名稱，或輸入 IP 位址，後面接著冒號和連接埠號碼。

   * 要使用安全套接字層 (SSL),請選擇啟用**SSL?**

     MQ 連接器目前僅支援伺服器身份驗證,不支援用戶端身份驗證。 有關詳細資訊,請參閱[連接和身份驗證問題](#connection-problems)。

1. 在**閘道**部分,按照以下步驟操作:

   1. 在**訂閱**清單中,選擇與 Azure 閘道資源關聯的 Azure 訂閱。

   1. 連線**閘道**清單中,選擇要使用的 Azure 閘道資源。

1. 當您完成時，選取 [建立]****。

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>連線與身份驗證問題

當您的邏輯應用嘗試連接到本地 MQ 伺服器時,您可能會收到此錯誤:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* 如果您直接在 Azure 中使用 MQ 連接器,則 MQ 伺服器需要使用由受信任的[證書頒發機構](https://www.ssl.com/faqs/what-is-a-certificate-authority/)頒發的證書。

* 如果您使用的是本地資料閘道,請試著盡可能使用信任的[憑證授權機構頒發的憑證](https://www.ssl.com/faqs/what-is-a-certificate-authority/)。 但是,如果無法使用此選項,則可以使用自簽名證書,該證書不是由受信任的[證書頒發機構](https://www.ssl.com/faqs/what-is-a-certificate-authority/)頒發的,並且被認為不太安全。

  要安裝伺服器的自簽名證書,可以使用**Windows 認證管理員**(certmgr.msc) 工具。 對於此方案,在運行本地數據閘道服務的本地電腦上,需要在**受信任的根證書頒發機構**級別的**本地電腦**憑證儲存中安裝證書。

  1. 在運行本地資料閘道服務的電腦上,打開開始功能表,查找並選擇 **「管理使用者證書**」。。

  1. 開啟 Windows 認證管理員工具後,轉到 **「證書 - 本地電腦** >  **受信任的根憑證頒發機構」** 資料夾,然後安裝證書。

     > [!IMPORTANT]
     > 請確保在**憑證 -本地電腦** > **受信任的根憑證頒發機構**儲存中安裝證書。

* MQ 伺服器要求您定義要用於 SSL 連接的密碼規範。 但是,.NET 中的 SsLStream 不允許指定密碼規格的順序。 要解決此限制,可以更改 MQ 伺服器設定,以匹配連接器在 SSL 協商中發送的套件中的第一個密碼規範。

  嘗試連接時,MQ 伺服器會記錄一個事件消息,指示連接失敗,因為另一端使用了不正確的密碼規範。 事件消息包含清單中首先出現的密碼規範。 更新通道配置中的密碼規範,以匹配事件消息中的密碼規範。

## <a name="browse-single-message"></a>瀏覽單條訊息

1. 在邏輯應用中,在觸發器或其他操作下,選擇 **「新建步驟**」。。

1. 在搜尋框中,輸入`mq`,然後選擇 **「瀏覽郵件**」操作。

   ![選擇「瀏覽郵件」操作](media/connectors-create-api-mq/browse-message.png)

1. 如果尚未建立 MQ 連線,系統會將提示您[建立此連線](#create-connection)。

1. 建立連線後,設定 **「瀏覽」訊息**操作的屬性:

   | 屬性 | 描述 |
   |----------|-------------|
   | **佇列** | 如果與連接中指定的佇列不同,請指定該佇列。 |
   | **訊息 Id,****關聯 Id**,**群組 Id**和其他屬性 | 瀏覽基於不同 MQ 訊息屬性的訊息 |
   | **包括資訊** | 在輸出中包含其他訊息資訊,請選擇**true**。 要省略輸出中的其他訊息資訊,請選擇**false**。 |
   | **超時** | 輸入一個值以確定等待消息以空佇列到達的時間。 如果未輸入任何內容，就會取出佇列中的第一個訊息，且不會花費時間等候要顯示的訊息。 |
   |||

   例如：

   !["瀏覽訊息'操作屬性](media/connectors-create-api-mq/browse-message-properties.png)

1. 完成後,在設計器工具列上,選擇 **「保存**」。。 要測試應用,請選擇"**運行**"。

   執行完成後,設計器將顯示工作流步驟及其狀態,以便您可以查看輸出。

1. 要查看有關每個步驟的詳細資訊,請單擊步驟的標題列。 要檢視有關步驟輸出的詳細資訊,請選擇「**顯示原始輸出**」 。

   ![瀏覽訊息輸出](media/connectors-create-api-mq/browse-message-output.png)

   下面是一些範例原始輸出:

   ![瀏覽訊息原始輸出](media/connectors-create-api-mq/browse-message-raw-output.png)

1. 如果將 **「包含資訊」** 設定為**true,** 將顯示其他輸出:

   ![瀏覽訊息包含資訊](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>瀏覽多個訊息

**"流覽訊息**"操作包括**一個 BatchSize**選項,用於指示要從佇列返回的消息數。 如果**BatchSize**沒有值,則返回所有消息。 傳回的輸出是陣列訊息。

1. 按照前面的步驟操作,但請改為添加 **「瀏覽訊息」** 操作。

1. 如果尚未建立 MQ 連線,系統會將提示您[建立此連線](#create-connection)。 否則,默認情況下,將使用第一個以前配置的連接。 要建立新連接,請選擇 **「更改連接**」 。 或者,選擇其他連接。

1. 提供操作的資訊。

1. 保存並運行邏輯應用。

   邏輯應用完成執行後,下面是 **「瀏覽訊息」** 操作中的一些範例輸出:

   ![範例「瀏覽訊息」輸出](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>接收單條訊息

[接收訊息]**** 動作具有與 [瀏覽訊息]**** 動作相同的輸入和輸出。 使用 **「接收」消息**時,將從佇列中刪除該消息。

## <a name="receive-multiple-messages"></a>接收多個訊息

[接收訊息]**** 動作具有與 [瀏覽訊息]**** 動作相同的輸入和輸出。 使用 **「接收消息」** 時,將從佇列中刪除消息。

> [!NOTE]
> 在沒有任何消息的佇列上運行瀏覽或接收操作時,該操作將使用此輸出失敗:
>
> ![MQ"無訊息"錯誤](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>傳送訊息

1. 按照前面的步驟操作,但請改為添加 **「發送消息**」操作。

1. 如果尚未建立 MQ 連線,系統會將提示您[建立此連線](#create-connection)。 否則,默認情況下,將使用第一個以前配置的連接。 要建立新連接,請選擇 **「更改連接**」 。 或者,選擇其他連接。

1. 提供操作的資訊。 對於**訊息型**態 ,請選擇有效的訊息型態:**資料圖**,**請回覆**或**要求**

   ![寄件送出訊息操作的屬性](media/connectors-create-api-mq/send-message-properties.png)

1. 保存並運行邏輯應用。

   邏輯應用完成執行後,下面是 **「發送訊息」** 操作中的一些範例輸出:

   ![範例「傳送訊息」輸出](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>連接器參考

有關操作和限制的技術詳細資訊(由連接器的 Swagger 描述)描述,請檢視連接器的[參考頁](/connectors/mq/)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
