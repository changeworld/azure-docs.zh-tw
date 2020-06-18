---
title: 傳送和接收 B2B 的 X12 訊息
description: 透過採用 Enterprise Integration Pack 的 Azure Logic Apps，交換適用於 B2B 企業整合案例的 X12 訊息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 9398b40763e8226cedf788f9cefbf5ed28cd649d
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83739527"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>在採用 Enterprise Integration Pack 的 Azure Logic Apps 中交換適用於 B2B 企業整合的 X12 訊息

若要使用 Azure Logic Apps 中的 X12 訊息，您可使用 X12 連接器，其提供用來管理 X12 通訊的觸發程序和動作。 如需有關 EDIFACT 訊息的詳細資訊，請參閱[交換 EDIFACT 訊息](logic-apps-enterprise-integration-edifact.md) (機器翻譯)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您要在其中使用 X12 連接器的邏輯應用程式，以及啟動邏輯應用程式工作流程的觸發程序。 X12 連接器僅提供動作，而非觸發程序。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 與 Azure 訂用帳戶建立關聯的[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，且已連結到要使用 X12 連接器的邏輯應用程式。 邏輯應用程式與整合帳戶必須位於相同位置或相同的 Azure 區域中。

* 使用 X12 身分識別辨識符號，在整合帳戶中至少定義兩名[交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)。

* 用於已新增至整合帳戶 XML 驗證的[結構描述](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 如果您使用的是健康保險便利和責任法案 (HIPAA) 結構描述，請參閱 [HIPAA 結構描述](#hipaa-schemas)。

* 在使用 X12 連接器前，您必須先在交易夥伴間建立 X12 [合約](../logic-apps/logic-apps-enterprise-integration-agreements.md) ，並將該合約儲存在整合帳戶中。 如果您使用的是健康保險便利和責任法案 (HIPAA) 結構描述，則必須將 `schemaReferences` 區段新增至合約。 如需詳細資訊，請參閱 [HIPAA 結構描述](#hipaa-schemas)。

<a name="receive-settings"></a>

## <a name="receive-settings"></a>接收設定

在設定合約屬性後，即可設定此合約如何識別及處理透過此合約從夥伴接收的輸入訊息。

1. 在 [新增] 之下，選取 [接收設定]。

1. 根據您與其交換訊息之夥伴所簽署的合約，設定這些屬性。 **接收設定**會整理為下列各區段：

   * [識別碼](#inbound-identifiers)
   * [通知](#inbound-acknowledgement)
   * [結構描述](#inbound-schemas)
   * [信封](#inbound-envelopes)
   * [控制編號](#inbound-control-numbers)
   * [Validations](#inbound-validations)
   * [內部設定](#inbound-internal-settings)

   如需屬性說明，請參閱本節中的資料表。

1. 完成後，請務必選取 [確定] 以儲存設定。

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>接收設定 - 識別碼

![輸入訊息的識別碼屬性](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| 屬性 | 描述 |
|----------|-------------|
| **ISA1 (授權辨識符號)** | 您想要使用的授權辨識符號值。 預設值為 [00 - 沒有任何授權資訊]。 <p>**注意**：如果您選取其他值，請指定 [ISA2] 屬性的值。 |
| **ISA2** | 當 [ISA1] 屬性不為 [00 - 沒有任何授權資訊] 時，所使用的授權資訊值。 此屬性值至少需包含一個英數字元 (最多 10 個)。 |
| **ISA3 (安全性辨識符號)** | 您想要使用的安全性辨識符號值。 預設值為 [00 - 沒有任何安全性資訊]。 <p>**注意**：如果您選取其他值，請指定 [ISA4] 屬性的值。 |
| **ISA4** | 當 [ISA3] 屬性不為 [00 - 沒有任何安全性資訊] 時，所使用的安全性資訊值。 此屬性值至少需包含一個英數字元 (最多 10 個)。 |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>接收設定 - 通知

![輸入訊息的通知](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| 屬性 | 描述 |
|----------|-------------|
| **預期 TA1** | 將技術通知 (TA1) 傳回給交換傳送者。 |
| **預期 FA** | 將功能通知 (FA) 傳回給交換傳送者。 <p>針對 [FA 版本] 屬性，請根據結構描述版本，選取 997 或 999 通知。 <p>若要針對接受的交易集在功能通知中啟用產生 AK2 迴圈，請選取 [包含 AK2/IK2 迴圈]。 |
||||

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>接收設定 - 結構描述

![輸入訊息的結構描述](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

在此區段，請在[整合帳戶](../logic-apps/logic-apps-enterprise-integration-accounts.md)中，為每個交易類型 (ST01) 和傳送者應用程式 (GS02) 選取[結構描述](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 EDI 接收管線會比對輸入訊息中 ST01 與 GS02 的值與在此區段所設定值，以及比對輸入訊息的結構描述與在此區段所設定結構描述來解譯輸入訊息。 在完成每個列時，即會自動出現一個新的空白列。

| 屬性 | 描述 |
|----------|-------------|
| **版本** | 結構描述的 X12 版本 |
| **交易類型 (ST01)** | 即交易類型 |
| **傳送者應用程式 (GS02)** | 即傳送者應用程式 |
| **結構描述** | 您要使用的結構描述檔案 |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>接收設定 - 信封

![分隔符號，用於輸入訊息的交易集](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| 屬性 | 描述 |
|----------|-------------|
| **ISA11 使用方式** | 要用於交易集中的分隔符號︰ <p>- **標準識別項**︰使用句號 (.) 作為小數點標記，而不是 EDI 接收管線中內送文件的小數點標記。 <p>- **重複分隔符號**︰指定重複出現簡單資料項目或重複資料結構的分隔符號。 例如，插入號 (^) 通常會做為重複分隔符號。 針對 HIPAA 結構描述，您只能使用插入號。 |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>接收設定 - 控制編號

![處理輸入訊息的控制編號重複項目](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| 屬性 | 描述 |
|----------|-------------|
| **不允許交換控制編號重複項目** | 封鎖重複的交換。 針對已接收的交換控制編號檢查交換控制編號 (ISA13)。 如果偵測到相符項目，EDI 接收管線就不會處理交換。 <p><p>若要指定每次執行檢查所間隔天數的值，請為 [檢查 ISA13 是否重複的天數間隔] 屬性輸入值。 |
| **不允許群組控制編號重複項目** | 封鎖具有重複群組控制編號的交換。 |
| **不允許交易集控制編號重複項目** | 封鎖具有重複交易集控制編號的交換。 |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>接收設定 - 驗證

![輸入訊息的驗證](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

[預設] 列會顯示用於 EDI 訊息類型的驗證規則。 如果想要定義不同的規則，請選取要將該規則設定為 **true** 的核取方塊。 在完成每個列時，即會自動出現一個新的空白列。

| 屬性 | 描述 |
|----------|-------------|
| **訊息類型** | EDI 訊息類型 |
| **EDI 驗證** | 依照結構描述的 EDI 屬性、長度限制、空白資料元素和尾端分隔符號定義，在資料類型上執行 EDI 驗證。 |
| **擴充驗證** | 如果資料類型不是 EDI，則在資料項目要求時才進行驗證，且允許重複、列舉和資料項目長度驗證 (最小或最大)。 |
| **允許前置/尾端零** | 保留任何額外的前置或尾端零及空格字元。 請勿移除這些字元。 |
| **修剪前置/尾端零** | 移除任何前置或尾端零及空格字元。 |
| **尾端分隔符號原則** | 產生尾端分隔符號。 <p>- **不允許**：禁止在輸入交換中使用尾端分隔符號。 如果交換具有尾端分隔符號，則會被宣告為無效。 <p>- **選用**：接受包含 (或不含) 尾端分隔符號的交換。 <p>- **強制**：輸入交換必須具有尾端分隔符號。 |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>接收設定 - 內部設定

![輸入訊息的內部設定](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| 屬性 | 描述 |
|----------|-------------|
| **將隱含的小數格式 Nn 轉換為 10 進制數值** | 將使用 "Nn" 格式指定的 EDI 數字轉換為 10 進制數值。 |
| **如果允許尾端分隔符號，則建立空白 XML 標籤** | 讓交換傳送者在尾端分隔符號包含空白 XML 標籤。 |
| **將交換分割為交易集 - 發生錯誤時暫停交易集** | 套用適當的信封至交易集，將交換中每個交易集剖析為個別的 XML 文件。 只暫停驗證失敗的交易。 |
| **將交換分割為交易集 - 發生錯誤時暫停交換** | 套用適當的信封，將交換中每個交易集剖析為個別的 XML 文件。 如果交換中有一或多個交易集無法通過驗證，則暫停整個交換。 |
| **保留交換 - 發生錯誤時暫停交易集** | 讓交換維持不變，並建立整個批次交換的 XML 文件。 只暫停未通過驗證的交易集，但繼續處理所有其他交易集。 |
| **保留交換 - 發生錯誤時暫停交換** |讓交換維持不變，建立整個批次交換的 XML 文件。 如果交換中有一或多個交易集無法通過驗證，則會暫停整個交換。 |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>傳送設定

在設定合約屬性後，即可設定此合約如何識別及處理透過此合約傳送給夥伴的輸出訊息。

1. 在 [新增] 之下，選取 [傳送設定]。

1. 根據您與其交換訊息之夥伴所簽署的合約，設定這些屬性。 如需屬性說明，請參閱本節中的資料表。

   **傳送設定**會整理為下列各區段：

   * [識別碼](#outbound-identifiers)
   * [通知](#outbound-acknowledgement)
   * [結構描述](#outbound-schemas)
   * [信封](#outbound-envelopes)
   * [控制版本號碼](#outbound-control-version-number)
   * [控制編號](#outbound-control-numbers)
   * [字元集和分隔符號](#outbound-character-sets-separators)
   * [驗證](#outbound-validation)

1. 完成後，請務必選取 [確定] 以儲存設定。

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>傳送設定 - 識別碼

![輸出訊息的識別碼屬性](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| 屬性 | 描述 |
|----------|-------------|
| **ISA1 (授權辨識符號)** | 您想要使用的授權辨識符號值。 預設值為 [00 - 沒有任何授權資訊]。 <p>**注意**：如果您選取其他值，請指定 [ISA2] 屬性的值。 |
| **ISA2** | 當 [ISA1] 屬性不為 [00 - 沒有任何授權資訊] 時，所使用的授權資訊值。 此屬性值至少需包含一個英數字元 (最多 10 個)。 |
| **ISA3 (安全性辨識符號)** | 您想要使用的安全性辨識符號值。 預設值為 [00 - 沒有任何安全性資訊]。 <p>**注意**：如果您選取其他值，請指定 [ISA4] 屬性的值。 |
| **ISA4** | 當 [ISA3] 屬性不為 [00 - 沒有任何安全性資訊] 時，所使用的安全性資訊值。 此屬性值至少需包含一個英數字元 (最多 10 個)。 |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>傳送設定 - 通知

![輸出訊息的通知屬性](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| 屬性 | 描述 |
|----------|-------------|
| **預期 TA1** | 將技術通知 (TA1) 傳回給交換傳送者。 <p>此設定指定傳送訊息的主控夥伴向合約中來賓合作夥伴要求通知。 主控夥伴根據合約的接收設定而預期會有這些通知。 |
| **預期 FA** | 將功能通知 (FA) 傳回給交換傳送者。 針對 [FA 版本] 屬性，請根據結構描述版本，選取 997 或 999 通知。 <p>此設定指定傳送訊息的主控夥伴向合約中來賓夥伴要求通知。 主控夥伴根據合約的接收設定而預期會有這些通知。 |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>傳送設定 - 結構描述

![輸出訊息的結構描述](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

針對此區段，請在[整合帳戶](../logic-apps/logic-apps-enterprise-integration-accounts.md)中，為每個交易類型 (ST01) 選取[結構描述](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 在完成每個列時，即會自動出現一個新的空白列。

| 屬性 | 描述 |
|----------|-------------|
| **版本** | 結構描述的 X12 版本 |
| **交易類型 (ST01)** | 結構描述的交易類型 |
| **結構描述** | 您要使用的結構描述檔案。 如果您先選取結構描述，則會自動設定版本和交易類型。 |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>傳送設定 - 信封

![要在交易集中用於輸出訊息的分隔符號](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| 屬性 | 描述 |
|----------|-------------|
| **ISA11 使用方式** | 要用於交易集中的分隔符號︰ <p>- **標準識別項**︰使用句號 (.) 作為小數點標記，而不是 EDI 傳送管線中輸出文件的小數點標記。 <p>- **重複分隔符號**︰指定重複出現簡單資料項目或重複資料結構的分隔符號。 例如，插入號 (^) 通常會做為重複分隔符號。 針對 HIPAA 結構描述，您只能使用插入號。 |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>傳送設定 - 控制版本號碼

![輸出訊息的控制版本號碼](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

針對此區段，請在[整合帳戶](../logic-apps/logic-apps-enterprise-integration-accounts.md)中，為每個交換選取[結構描述](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 在完成每個列時，即會自動出現一個新的空白列。

| 屬性 | 描述 |
|----------|-------------|
| **控制版本號碼 (ISA12)** | X12 標準的版本 |
| **使用狀況指示符號 (ISA15)** | 交換的內容，可為 [測試] 資料、[資訊] 資料，或 [生產] 資料 |
| **結構描述** | 要使用的結構描述，用於產生 X12 編碼交換的 GS 和 ST 區段，並將這些區段傳送至 EDI 傳送管線。 |
| **GS1** | (選擇性) 選取功能程式碼。 |
| **GS2** | (選擇性) 指定應用程式傳送者。 |
| **GS3** | (選擇性) 指定應用程式接收者。 |
| **GS4** | (選擇性) 選取 **CCYYMMDD** 或 **YYMMDD**。 |
| **GS5** | (選擇性) 選取 **HHMM**、**HHMMSS** 或 **HHMMSSdd**。 |
| **GS7** | (選擇性) 選取負責單位的值。 |
| **GS8** | (選擇性) 指定結構描述文件版本。 |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>傳送設定 - 控制編號

![輸出訊息的控制編號](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| 屬性 | 描述 |
|----------|-------------|
| **交換控制編號 (ISA13)** | 交換控制編號的值範圍，最小值為 1，最大值為 999999999 |
| **群組控制編號 (GS06)** | 群組控制編號的值範圍，最小值為 1，最大值為 999999999 |
| **交易集控制編號 (ST02)** | 交易集控制編號的值範圍，最小值為 1，最大值為 999999999 <p>- **首碼**：(選擇性) 英數字元值 <br>- **尾碼**：(選擇性) 英數字元值 |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>傳送設定 - 字元集和分隔符號

![輸出訊息中訊息類型的分隔符號](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

[預設] 列會顯示作為訊息結構描述分隔符號的字元集。 如果不想要使用 [預設] 字元集，則可為每個訊息類型輸入一組不同的分隔符號。 在完成每個列時，即會自動出現一個新的空白列。

> [!TIP]
> 若要提供特殊字元值，請將合約編輯為 JSON，並為特殊字元提供 ASCII 值。

| 屬性 | 描述 |
|----------|-------------|
| **要使用的字元集** | X12 字元集，可為 [基本]、[擴充]，或 [UTF8]。 |
| **結構描述** | 您要使用的結構描述。 選取結構描述後，根據以下分隔符號描述來選取想要使用的字元集。 |
| **輸入類型** | 字元集的輸入類型 |
| **元件分隔符號** | 分隔複合資料項目的單一字元 |
| **資料項目分隔符號** | 分隔複合資料項目內簡單資料項目的單一字元 |
| **取代字元分隔符號** | 產生輸出 X12 訊息時，可取代承載資料中所有分隔符號字元的取代字元 |
| **區段結束字元** | 指出 EDI 區段結尾的單一字元 |
| **尾碼** | 與區段識別碼一起使用的字元。 如果指定尾碼，則區段結束字元資料項目即可為空白。 如果區段結束字元留白，則必須指定尾碼。 |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>傳送設定 - 驗證

![輸出訊息的驗證屬性](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

[預設] 列會顯示用於 EDI 訊息類型的驗證規則。 如果想要定義不同的規則，請選取要將該規則設定為 **true** 的核取方塊。 在完成每個列時，即會自動出現一個新的空白列。

| 屬性 | 描述 |
|----------|-------------|
| **訊息類型** | EDI 訊息類型 |
| **EDI 驗證** | 依照結構描述的 EDI 屬性、長度限制、空白資料元素和尾端分隔符號定義，在資料類型上執行 EDI 驗證。 |
| **擴充驗證** | 如果資料類型不是 EDI，則在資料項目要求時才進行驗證，且允許重複、列舉和資料項目長度驗證 (最小或最大)。 |
| **允許前置/尾端零** | 保留任何額外的前置或尾端零及空格字元。 請勿移除這些字元。 |
| **修剪前置/尾端零** | 移除任何前置或尾端零及空格字元。 |
| **尾端分隔符號原則** | 產生尾端分隔符號。 <p>- **不允許**：禁止在輸出交換中使用尾端分隔符號。 如果交換具有尾端分隔符號，則會被宣告為無效。 <p>- **選用**：傳送包含 (或不含) 尾端分隔符號的交換。 <p>- **強制**：輸出交換必須具有尾端分隔符號。 |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>HIPAA 結構描述和訊息類型

當使用 HIPAA 結構描述和 277 或 837 訊息類型時，您需要執行一些額外的步驟。 此訊息類型的[文件版本號碼 (GS8)](#outbound-control-version-number) 包含超過 9 個字元，例如 "005010X222A1"。 此外，某些文件版本號碼會對應到變體訊息類型。 如果您未在結構描述與合約中參考正確的訊息類型，則會出現下列錯誤訊息：

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

下表列出受影響的訊息類型、變體，以及對應至這些訊息類型的文件版本號碼：

| 訊息類型或變體 |  描述 | 文件版本號碼 (GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | 醫療保健資訊狀態通知 | 005010X212 |
| 837_I | 醫療保健索賠機構 | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | 醫療保健索賠牙科 | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | 醫療保健索賠職業 | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

當使用這些文件版本號碼時，您也需要停用 EDI 驗證，因為這些驗證會導致字元長度無效而引發錯誤。

若要指定這些文件版本號碼和訊息類型，請遵循下列步驟：

1. 在 HIPAA 架構中，將目前訊息類型取代為想要使用的文件版本號碼其變體訊息類型。

   例如，假設想要使用訊息類型為 `837` 的文件版本號碼 `005010X222A1`。 在結構描述中，以 `"X12_00501_837_P"` 值取代每個 `"X12_00501_837"` 值。

   若要更新結構描述，請遵循下列步驟：

   1. 在 Azure 入口網站中，前往整合帳戶。 尋找並下載結構描述。 取代訊息類型並重新命名結構描述檔案，然後將修改過的結構描述上傳至整合帳戶。 如需詳細資訊，請參閱[編輯結構描述](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas)。

   1. 在 [合約訊息設定] 中，選取修改過的結構描述。

1. 在合約的 `schemaReferences` 物件中，新增另一個項目，以指定與文件版本號碼相符的變體訊息類型。

   例如，假設想要使用訊息類型為 `837` 的文件版本號碼 `005010X222A1`。 合約具有 `schemaReferences` 區段，其中包含下列屬性和值：

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   在此 `schemaReferences` 區段中，新增具有下列值的另一個項目：

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   完成上述步驟後， `schemaReferences` 區段看起來會像這樣：

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

1. 在 [合約訊息設定] 中，針對各個訊息類型類型清除 [EDI 驗證] 核取方塊 (如果正在使用 [預設] 值，則會清除所有訊息類型的核取方塊)，以停用 EDI 驗證。

   ![停用所有訊息類型或各個訊息類型的驗證](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>連接器參考

如需有關此連接器的其他技術詳細資料 (例如連接器 Swagger 檔案中所述的動作和限制)，請參閱[連接器的參考頁面](https://docs.microsoft.com/connectors/x12/)。

> [!NOTE]
> 針對[整合服務環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中的邏輯應用程式，此連接器其 ISE 標籤版本使用 [ISE 的 B2B 訊息限制](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits)。

## <a name="next-steps"></a>後續步驟

* 深入了解其他[邏輯應用程式的連接器](../connectors/apis-list.md)
