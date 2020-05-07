---
title: 傳送和接收 B2B 的 X12 訊息
description: 使用 Azure Logic Apps 搭配企業整合套件來交換 B2B 企業整合案例的 X12 訊息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 8ec20e03544ba54b83130ae41244dcdb186252d0
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82613007"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>在採用 Enterprise Integration Pack 的 Azure Logic Apps 中交換適用於 B2B 企業整合的 X12 訊息

若要在 Azure Logic Apps 中使用 X12 訊息，您可以使用 X12 連接器，它會提供用來管理 X12 通訊的觸發程式和動作。 如需有關 EDIFACT 訊息的詳細資訊，請參閱[EXCHANGE edifact 訊息](logic-apps-enterprise-integration-edifact.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您想要使用 X12 連接器的邏輯應用程式，以及啟動邏輯應用程式工作流程的觸發程式。 X12 連接器只提供動作，而非觸發程式。 如果您還不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 與您的 Azure 訂用帳戶相關聯的[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，並連結至您打算使用 X12 連接器的邏輯應用程式。 您的邏輯應用程式和整合帳戶必須存在於相同的位置或 Azure 區域中。

* 至少有兩個您已在整合帳戶中使用 X12 識別辨識符號定義的[交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)。

* 您已新增至整合帳戶的 XML 驗證所使用的[架構](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 如果您使用的是健康保險流通與責任法案（HIPAA）架構，請參閱[HIPAA 架構](#hipaa-schemas)。

* 您必須先建立交易夥伴之間的 X12[合約](../logic-apps/logic-apps-enterprise-integration-agreements.md)，並將該合約儲存在您的整合帳戶中，才可以使用 X12 連接器。 如果您使用的是健康保險流通與責任法案（HIPAA）架構，則需要在您的`schemaReferences`合約中新增一節。 如需詳細資訊，請參閱[HIPAA 架構](#hipaa-schemas)。

<a name="receive-settings"></a>

## <a name="receive-settings"></a>接收設定

設定協定屬性之後，您可以設定此合約如何識別及處理您透過此合約從合作夥伴收到的輸入訊息。

1. 在 [新增]**** 之下，選取 [接收設定]****。

1. 根據您與其交換訊息之夥伴所簽署的合約，設定這些屬性。 **接收設定**會組織成下列各節：

   * [識別碼](#inbound-identifiers)
   * [通知](#inbound-acknowledgement)
   * [結構描述](#inbound-schemas)
   * [信封](#inbound-envelopes)
   * [控制編號](#inbound-control-numbers)
   * [Validations](#inbound-validations)
   * [內部設定](#inbound-internal-settings)

   如需屬性說明，請參閱本節中的資料表。

1. 當您完成時，請務必選取 **[確定]** 來儲存您的設定。

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>接收設定-識別碼

![輸入訊息的識別碼屬性](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| 屬性 | 描述 |
|----------|-------------|
| **ISA1 (授權辨識符號)** | 您想要使用的授權辨識符號值。 預設值為 [ **00-沒有授權資訊**]。 <p>**注意**：如果您選取其他值，請指定**ISA2**屬性的值。 |
| **ISA2** | 當**ISA1**屬性不是00時，所要使用的授權資訊值 **（不存在授權資訊）**。 此屬性值必須至少有一個英數位元，最多10個字元。 |
| **ISA3 (安全性辨識符號)** | 您想要使用的安全性辨識符號值。 預設值為 [ **00-沒有安全性資訊**]。 <p>**注意**：如果您選取其他值，請指定**ISA4**屬性的值。 |
| **ISA4** | 當**ISA3**屬性不是**00-沒有安全性資訊**時，所要使用的安全性資訊值。 此屬性值必須至少有一個英數位元，最多10個字元。 |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>接收設定-通知

![輸入訊息的確認](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| 屬性 | 描述 |
|----------|-------------|
| **預期的 TA1** | 將技術通知 (TA1) 傳回給交換傳送者。 |
| **預期的 FA** | 將功能通知 (FA) 傳回給交換傳送者。 <p>針對 [ **FA 版本**] 屬性，根據架構版本，選取997或999通知。 <p>若要在功能通知中針對已接受的交易集啟用產生 AK2 迴圈，請選取 [**包含 AK2/IK2 迴圈**]。 |
||||

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>接收設定-架構

![輸入訊息的架構](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

在此區段中，請針對每個交易類型（ST01）和傳送者應用程式（GS02），從您的[整合帳戶](../logic-apps/logic-apps-enterprise-integration-accounts.md)中選取[架構](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 EDI 接收管線會將您在此區段中設定的值和架構與內送訊息中的 ST01 和 GS02 值，以及傳入訊息的架構進行比對，以反組譯傳入訊息。 當您完成每個資料列之後，就會自動顯示新的空白資料列。

| 屬性 | 描述 |
|----------|-------------|
| **版本** | 架構的 X12 版本 |
| **交易類型 (ST01)** | 交易類型 |
| **傳送者應用程式 (GS02)** | 寄件者應用程式 |
| **Schema** | 您想要使用的架構檔案 |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>接收設定-信封

![用於輸入訊息之交易集的分隔符號](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| 屬性 | 描述 |
|----------|-------------|
| **ISA11 使用方法** | 要在交易集中使用的分隔符號： <p>- **標準識別碼**：使用句號（.）作為十進位標記法，而不是 EDI 接收管線中傳入檔的十進位標記法。 <p>- **重複分隔符號**：指定重複出現的簡單資料元素或重複資料結構的分隔符號。 例如，插入號 (^) 通常會做為重複分隔符號。 針對 HIPAA 結構描述，您只能使用插入號。 |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>接收設定-控制編號

![處理輸入訊息的控制編號重複](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| 屬性 | 描述 |
|----------|-------------|
| **不允許交換控制編號重複** | 封鎖重複的交換。 檢查已接收之交換控制編號的交換控制編號（ISA13）。 如果偵測到相符的，EDI 接收管線就不會處理交換。 <p><p>若要指定執行檢查的天數，請輸入 [**檢查重複的 ISA13 間隔（天）** ] 屬性的值。 |
| **不允許群組控制編號重複** | 封鎖有重複群組控制編號的交換。 |
| **不允許交易集控制編號重複** | 封鎖具有重複交易集控制編號的交換。 |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>接收設定-驗證

![驗證輸入訊息](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

**預設**的資料列會顯示用於 EDI 訊息類型的驗證規則。 如果您想要定義不同的規則，請選取您要將規則設定為**true**的每個方塊。 當您完成每個資料列之後，就會自動顯示新的空白資料列。

| 屬性 | 描述 |
|----------|-------------|
| **訊息類型** | EDI 訊息類型 |
| **EDI 驗證** | 依照結構描述的 EDI 屬性、長度限制、空白資料元素和尾端分隔符號定義，在資料類型上執行 EDI 驗證。 |
| **擴充驗證** | 如果資料類型不是 EDI，則會在資料元素需求上進行驗證，並允許進行重複、列舉和資料元素長度驗證（最小或最大）。 |
| **允許前置/尾端零** | 保留任何額外的前置或尾端零和空白字元。 請勿移除這些字元。 |
| **修剪前置/尾端零** | 移除任何開頭或尾端的零和空白字元。 |
| **尾端分隔符號原則** | 產生尾端分隔符號。 <p>- **不允許**：禁止輸入交換中的尾端分隔符號和分隔符號。 如果交換具有尾端分隔符號，則會被宣告為無效。 <p>- **選擇性**：接受包含或不含尾端分隔符號的交換。 <p>- **強制**：輸入交換必須有尾端分隔符號和分隔符號。 |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>接收設定-內部設定

![輸入訊息的內部設定](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| 屬性 | 描述 |
|----------|-------------|
| **將隱含的小數格式 Nn 轉換為10進制數值** | 將以 "Nn" 格式指定的 EDI 數位轉換成以10為基底的數值。 |
| **如果允許尾端分隔符號，則建立空的 XML 標籤** | 讓交換傳送者為尾端分隔符號包含空的 XML 標記。 |
| **將交換分割為交易集 - 發生錯誤時暫停交易集** | 藉由將適當的信封套用至交易集，將交換中的每個交易集剖析為個別的 XML 檔。 僅暫停驗證失敗的交易。 |
| **將交換分割為交易集 - 發生錯誤時暫停交換** | 藉由套用適當的信封，將交換中的每個交易集剖析為個別的 XML 檔。 如果交換中有一或多個交易集無法通過驗證，則暫停整個交換。 |
| **保留交換 - 發生錯誤時暫停交易集** | 將交換保持不變，並建立整個批次交換的 XML 檔。 只暫停驗證失敗的交易集，但繼續處理所有其他交易集。 |
| **保留交換 - 發生錯誤時暫停交換** |讓交換維持不變，建立整個批次交換的 XML 文件。 如果交換中有一或多個交易集無法通過驗證，則會暫停整個交換。 |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>傳送設定

設定協定屬性之後，您可以設定此合約如何識別及處理您透過此合約傳送給夥伴的輸出訊息。

1. 在 [新增]**** 之下，選取 [傳送設定]****。

1. 根據您與其交換訊息之夥伴所簽署的合約，設定這些屬性。 如需屬性說明，請參閱本節中的資料表。

   **傳送設定**會組織成下列各節：

   * [識別碼](#outbound-identifiers)
   * [通知](#outbound-acknowledgement)
   * [結構描述](#outbound-schemas)
   * [信封](#outbound-envelopes)
   * [控制版本號碼](#outbound-control-version-number)
   * [控制編號](#outbound-control-numbers)
   * [字元集和分隔符號](#outbound-character-sets-separators)
   * [驗證](#outbound-validation)

1. 當您完成時，請務必選取 **[確定]** 來儲存您的設定。

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>傳送設定-識別碼

![輸出訊息的識別碼屬性](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| 屬性 | 描述 |
|----------|-------------|
| **ISA1 (授權辨識符號)** | 您想要使用的授權辨識符號值。 預設值為 [ **00-沒有授權資訊**]。 <p>**注意**：如果您選取其他值，請指定**ISA2**屬性的值。 |
| **ISA2** | 當**ISA1**屬性不是00時，所要使用的授權資訊值 **（不存在授權資訊）**。 此屬性值必須至少有一個英數位元，最多10個字元。 |
| **ISA3 (安全性辨識符號)** | 您想要使用的安全性辨識符號值。 預設值為 [ **00-沒有安全性資訊**]。 <p>**注意**：如果您選取其他值，請指定**ISA4**屬性的值。 |
| **ISA4** | 當**ISA3**屬性不是**00-沒有安全性資訊**時，所要使用的安全性資訊值。 此屬性值必須至少有一個英數位元，最多10個字元。 |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>傳送設定-通知

![輸出訊息的通知屬性](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| 屬性 | 描述 |
|----------|-------------|
| **預期的 TA1** | 將技術通知 (TA1) 傳回給交換傳送者。 <p>此設定指定傳送訊息的主夥伴會向合約中的來賓夥伴要求通知。 根據合約的 [接收設定]，主機合作夥伴應該會收到這些通知。 |
| **預期的 FA** | 將功能通知 (FA) 傳回給交換傳送者。 針對 [ **FA 版本**] 屬性，根據架構版本，選取997或999通知。 <p>這項設定會指定傳送訊息的主夥伴在合約中要求來自來賓夥伴的確認。 根據合約的 [接收設定]，主機合作夥伴應該會收到這些通知。 |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>傳送設定-架構

![輸出訊息的架構](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

在此區段中，請從您的[整合帳戶](../logic-apps/logic-apps-enterprise-integration-accounts.md)中，為每個交易類型（ST01）選取一個[架構](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 當您完成每個資料列之後，就會自動顯示新的空白資料列。

| 屬性 | 描述 |
|----------|-------------|
| **版本** | 架構的 X12 版本 |
| **交易類型 (ST01)** | 架構的交易類型 |
| **Schema** | 您想要使用的架構檔案。 如果您先選取架構，則會自動設定版本和交易類型。 |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>傳送設定-信封

![要用於輸出訊息的交易集中的分隔符號](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| 屬性 | 描述 |
|----------|-------------|
| **ISA11 使用方法** | 要在交易集中使用的分隔符號： <p>- **標準識別碼**：在 EDI 傳送管線中，使用句號（.）作為十進位標記法，而不是輸出檔案的十進位標記法。 <p>- **重複分隔符號**：指定重複出現的簡單資料元素或重複資料結構的分隔符號。 例如，插入號 (^) 通常會做為重複分隔符號。 針對 HIPAA 結構描述，您只能使用插入號。 |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>傳送設定-控制版本號碼

![輸出訊息的控制版本號碼](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

在此區段中，請從您的[整合帳戶](../logic-apps/logic-apps-enterprise-integration-accounts.md)中選取每個交換的[架構](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 當您完成每個資料列之後，就會自動顯示新的空白資料列。

| 屬性 | 描述 |
|----------|-------------|
| **控制版本編號 (ISA12)** | X12 標準的版本 |
| **使用狀況指示符號 (ISA15)** | 交換的內容，也就是**測試**資料、**資訊**資料或**實際**執行資料 |
| **Schema** | 要用來針對傳送至 EDI 傳送管線的 X12 編碼交換產生 GS 和 ST 區段的架構。 |
| **GS1** | （選擇性）選取功能程式碼。 |
| **GS2** | （選擇性）指定應用程式寄件者。 |
| **GS3** | （選擇性）指定應用程式接收者。 |
| **GS4** | （選擇性）選取 [ **CCYYMMDD** ] 或 [ **YYMMDD**]。 |
| **GS5** | 選擇性，選取 [ **HHMM**]、[ **HHMMSS**] 或 [ **HHMMSSdd**]。 |
| **GS7** | （選擇性）選取負責機關的值。 |
| **GS8** | 選擇性，指定架構檔版本。 |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>傳送設定-控制編號

![輸出訊息的控制編號](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| 屬性 | 描述 |
|----------|-------------|
| **交換控制編號 (ISA13)** | 交換控制編號的值範圍，其最小值為1，最大值為999999999 |
| **群組控制編號 (GS06)** | 群組控制編號的值範圍，其最小值為1，最大值為999999999 |
| **交易集控制編號 (ST02)** | 交易集控制編號的值範圍，其最小值為1，最大值為999999999 <p>- **Prefix**：選擇性，英數位元值 <br>- **尾碼**：選擇性，英數位元值 |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>傳送設定-字元集和分隔符號

![輸出訊息中訊息類型的分隔符號](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

**預設**的資料列會顯示用來做為訊息架構分隔符號的字元集。 如果您不想要使用**預設**字元集，可以針對每種訊息類型輸入一組不同的分隔符號。 當您完成每個資料列之後，就會自動顯示新的空白資料列。

> [!TIP]
> 若要提供特殊字元值，請將合約編輯為 JSON，並為特殊字元提供 ASCII 值。

| 屬性 | 描述 |
|----------|-------------|
| **要使用的字元集** | X12 字元集，也就是**基本**、**擴充**或**UTF8**。 |
| **Schema** | 您想要使用的架構。 選取架構之後，請根據下面的分隔符號描述，選取您想要使用的字元集。 |
| **輸入類型** | 字元集的輸入類型 |
| **元件分隔符號** | 分隔複合資料元素的單一字元 |
| **資料元素分隔符號** | 分隔複合資料中簡單資料元素的單一字元 |
| **取代字元分隔符號** | 當產生輸出 X12 訊息時，取代裝載資料中所有分隔字元的取代字元 |
| **區段結束字元** | 單一字元，表示 EDI 區段的結尾 |
| **尾碼** | 要與區段識別碼一起使用的字元。 如果您指定尾碼，區段結束字元資料元素可以是空的。 如果區段結束字元為空白，您就必須指定尾碼。 |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>傳送設定-驗證

![輸出訊息的驗證屬性](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

**預設**的資料列會顯示用於 EDI 訊息類型的驗證規則。 如果您想要定義不同的規則，請選取您要將規則設定為**true**的每個方塊。 當您完成每個資料列之後，就會自動顯示新的空白資料列。

| 屬性 | 描述 |
|----------|-------------|
| **訊息類型** | EDI 訊息類型 |
| **EDI 驗證** | 依照結構描述的 EDI 屬性、長度限制、空白資料元素和尾端分隔符號定義，在資料類型上執行 EDI 驗證。 |
| **擴充驗證** | 如果資料類型不是 EDI，則會在資料元素需求上進行驗證，並允許進行重複、列舉和資料元素長度驗證（最小或最大）。 |
| **允許前置/尾端零** | 保留任何額外的前置或尾端零和空白字元。 請勿移除這些字元。 |
| **修剪前置/尾端零** | 移除任何開頭或尾端的零和空白字元。 |
| **尾端分隔符號原則** | 產生尾端分隔符號。 <p>- **不允許**：禁止輸出交換中的尾端分隔符號和分隔符號。 如果交換具有尾端分隔符號，則會被宣告為無效。 <p>- **選擇性**：傳送具有或不含尾端分隔符號的交換。 <p>- **必要**：輸出交換必須有尾端分隔符號。 |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>HIPAA 架構和訊息類型

當您使用 HIPAA 架構和277或837訊息類型時，您需要執行一些額外的步驟。 這些訊息類型的[檔版本號碼（GS8）](#outbound-control-version-number)有9個以上的字元，例如 "005010X222A1"。 此外，某些檔版本號碼會對應到 variant 訊息類型。 如果您未在架構中參考正確的訊息類型，且您的合約中出現下列錯誤訊息：

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

下表列出受影響的訊息類型、任何變體，以及對應至這些訊息類型的檔版本號碼：

| 訊息類型或變體 |  描述 | 檔版本號碼（GS8） |
|-------------------------|--------------|-------------------------------|
| 277 | 醫療保健資訊狀態通知 | 005010X212 |
| 837_I | 醫療保健索賠牙科 | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | 醫療保健索賠機構 | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | 醫療保健索賠專業人員 | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

當您使用這些檔版本號碼時，您也需要停用 EDI 驗證，因為它們會導致字元長度不正確錯誤。

若要指定這些檔版本號碼和訊息類型，請遵循下列步驟：

1. 在您的 HIPAA 架構中，將目前的訊息類型取代為您要使用之檔版本號碼的 variant 訊息類型。

   例如，假設您想要使用具有`005010X222A1` `837`訊息類型的檔版本號碼。 在您的架構中， `"X12_00501_837"`改為以`"X12_00501_837_P"`值取代每個值。

   若要更新您的架構，請遵循下列步驟：

   1. 在 Azure 入口網站中，移至您的整合帳戶。 尋找並下載您的架構。 取代訊息類型並重新命名架構檔案，然後將修訂過的架構上傳至您的整合帳戶。 如需詳細資訊，請參閱[編輯架構](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas)。

   1. 在 [您是合約的訊息設定] 中，選取修改過的架構。

1. 在您的`schemaReferences`合約物件中，加入另一個專案，以指定與您的檔版本號碼相符的 variant 訊息類型。

   例如，假設您想要使用`005010X222A1` `837`訊息類型的檔版本號碼。 您的合約具有`schemaReferences`下列屬性和值的區段：

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   在`schemaReferences`本節中，新增具有下列值的另一個專案：

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   當您完成時，您`schemaReferences`的區段看起來會像這樣：

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      },
      {
         "messageId": "837_P",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837_P"
      }
   ]
   ```

1. 在合約的訊息設定中，如果您使用**預設**值，請清除每個訊息類型或所有訊息類型的 [ **edi 驗證**] 核取方塊，以停用 edi 驗證。

   ![停用所有訊息類型或每種訊息類型的驗證](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>連接器參考

如需此連接器的其他技術詳細資料，例如連接器的 Swagger 檔案所述的動作和限制，請參閱[連接器的參考頁面](https://docs.microsoft.com/connectors/x12/)。

> [!NOTE]
> 對於[整合服務環境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的邏輯應用程式，此連接器的 ise 標記版本會使用[適用于 ISE 的 B2B 訊息限制](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits)。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Logic Apps 的其他連接器](../connectors/apis-list.md)