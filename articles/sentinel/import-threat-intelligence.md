---
title: 將威脅情報匯入 Azure Sentinel |Microsoft Docs
description: 使用 Azure Sentinel 中的威脅情報摘要來分析資料、偵測威脅，以及產生警示和事件。 使用活頁簿將威脅情報資訊視覺化。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2020
ms.author: yelevin
ms.openlocfilehash: e04d7fa1f319ca3969d8acdc0235e2838bb3a88d
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994774"
---
# <a name="import-threat-intelligence-into-azure-sentinel"></a>將威脅情報匯入 Azure Sentinel

## <a name="introduction-to-threat-intelligence"></a>威脅情報簡介

網路威脅情報 (CTI) 是描述已知現有或潛在威脅給系統和使用者的資訊。 這種類型的資訊採用許多形式，從詳述特定威脅執行者動機、基礎結構和技術的書面報表，到與網路威脅相關聯的 IP 位址、網域和檔案雜湊的特定觀察。 組織會使用 CTI 來提供重要的內容給不尋常的活動，讓安全性人員可以快速地採取行動來保護其人員和資產。 CTI 可以來自許多地方，例如開放原始碼資料摘要、威脅情報-共用群體、商業智慧摘要，以及在組織內的安全性調查過程中所收集的當地智慧。

在安全性資訊和事件管理 (SIEM) 的解決方案（例如 Azure Sentinel）中，最常使用的 CTI 形式是威脅指標，通常稱為入侵或 IoCs 的徵兆。 威脅指標是關聯觀察值（例如 Url、檔案雜湊或 IP 位址）與已知的威脅活動（例如網路釣魚、殭屍網路或惡意程式碼）相關聯的資料。 這種形式的威脅情報通常稱為策略性威脅情報，因為它可以套用到大規模的安全性產品和自動化，以保護和偵測組織的潛在威脅。 在 Azure Sentinel 中，您可以使用威脅指標來協助偵測在您的環境中觀察到的惡意活動，並將內容提供給安全性調查人員，以協助通知回應決策。

您可以透過下列活動，將威脅情報 (TI) 整合到 Azure Sentinel 中：

- 使用不同的 TI 平臺 **資料連線器** ，將 [威脅情報匯入](./connect-threat-intelligence.md) Azure Sentinel。
- 在 [ **記錄** ] 和 [Azure Sentinel 的新 **威脅情報** ] 區域中，查看及管理匯入的威脅情報。
- 使用內建的 **分析** 規則範本，利用您匯入的威脅情報來產生安全性警示和事件。
- 使用 **威脅情報活頁簿**，在 Azure Sentinel 中將威脅情報的重要資訊視覺化。

威脅情報也會在其他 Azure Sentinel 體驗（例如 **搜尋** 和 **筆記本**）內提供有用的內容，而且在本文未討論的情況下，這些經驗會在此絕佳的 blog 文章中解決，方法是在 [Sentinel 的 Jupyter 筆記本上 Ian Hellen](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239)，其中涵蓋了在筆記本內使用 CTI 的方式。

## <a name="azure-sentinel-data-connectors-for-threat-intelligence"></a>適用于威脅情報的 Azure Sentinel 資料連線器

就像 Azure Sentinel 中的所有其他事件資料一樣，威脅指標是使用資料連線器匯入的。 Azure Sentinel 有兩個資料連線器，專為威脅指標、 **威脅情報 TAXII** 和 **威脅情報平臺**提供。 您可以單獨使用資料連線器，或同時使用兩個連接器，視組織來源威脅指標的位置而定。 讓我們談談每個資料連線器。

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>使用威脅情報平臺資料連線器將威脅指標新增至 Azure Sentinel

許多組織都利用威脅情報平臺 (提示) 解決方案來匯總各種來源的威脅指標摘要、策展平臺內的資料，然後選擇要將哪些威脅指標套用至各種安全性解決方案，例如網路裝置、先進的威脅防護解決方案或 Siem （例如 Azure Sentinel）。 如果您的組織利用整合式提示解決方案，例如 MISP、Anomali ThreatStream、ThreatConnect 或 Palo Alto Networks 的 MineMeld， **威脅情報平臺資料連線器** 可讓您使用提示，將威脅指標匯入 Azure Sentinel。 因為連接器可搭配 Microsoft Graph 的 [安全性 TIINDICATORS API](https://docs.microsoft.com/graph/api/resources/tiindicator) 來完成這項作業，所以任何自訂威脅情報平臺也可以使用連接器來利用 tiIndicators API，將指標傳送至 Azure Sentinel (以及 Defender ATP) 等其他 Microsoft 安全性解決方案。

:::image type="content" source="media/import-threat-intelligence/threat-intel-import-path.png" alt-text="威脅情報匯入路徑":::

請遵循下列步驟，從您的整合式提示或自訂威脅情報解決方案將威脅指標匯入 Azure Sentinel：

1. 從您的 Azure Active Directory 取得應用程式識別碼和用戶端密碼

1. 將此資訊輸入至您的秘訣解決方案或自訂應用程式

1. 在 Azure Sentinel 中啟用威脅情報平臺資料連線器

以下是每個步驟的詳細探討。

#### <a name="obtain-an-application-id-and-client-secret-from-your-azure-active-directory"></a>從您的 Azure Active Directory 取得應用程式識別碼和用戶端密碼

無論您使用的是秘訣或自訂解決方案，tiIndicators API 都需要一些基本資訊來連接和傳送威脅指標。 您將需要取得的三項資訊包括：
- 應用程式 (用戶端) 識別碼
- 目錄 (租用戶) 識別碼
- 用戶端密碼

這項資訊一律是透過名為「 **應用程式註冊** 」的進程（包含下列三個步驟）從您的 Azure Active Directory：
- 使用 Azure Active Directory 註冊應用程式
- 指定應用程式連線到 Microsoft Graph tiIndicators API 以及傳送威脅指標所需的許可權
- 取得貴組織的同意，以將這些許可權授與此應用程式。  

**使用 Azure Active Directory 註冊應用程式**

1. 開啟 [Azure 入口網站](https://portal.azure.com/) ，然後流覽至 **Azure Active Directory** 服務。

1. 從功能表選取 [ **應用程式註冊** ]，然後選取 [ **新增註冊**]。

1. 選擇應用程式註冊的名稱，選取 [ **單一租** 使用者] 選項按鈕，然後選取 [ **註冊**]。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-register-application.png" alt-text="註冊應用程式":::

1. 從產生的畫面中，將 **應用程式 (用戶端) 識別碼** 和 **目錄 (租使用者) 識別碼** 值。 這些是您稍後需要用來設定秘訣或自訂解決方案，以將威脅指標傳送給 Azure Sentinel 的前兩個資訊片段。

**指定應用程式所需的許可權**

1. 開啟 [Azure 入口網站](https://portal.azure.com/) ，然後流覽至 **Azure Active Directory** 服務。

1. 從功能表選取 [ **應用程式註冊** ]，然後選取新註冊的應用程式。

1. 從功能表選取 [ **API 許可權** ]，然後按一下 [ **新增許可權** ] 按鈕。

1. 在 [ **選取 API** ] 頁面上，選取 [ **Microsoft Graph** ]，從 Microsoft Graph 許可權清單中選擇。

1. 當系統詢問 **您的應用程式需要何種許可權？** 選取 [ **應用程式許可權**]。 這是應用程式使用應用程式識別碼和應用程式秘密進行驗證所使用的許可權類型)  (API 金鑰。

1. 選取 [ **ThreatIndicators** ]，然後選取 [ **新增許可權** ]，將此許可權新增至您應用程式的許可權清單。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-1.png" alt-text="指定許可權":::

**取得貴組織的同意以授與這些許可權**

1. 若要授與同意，您需要 Azure Active Directory 全域管理員，才能在應用程式的 [API 許可權] 頁面上，選取 [ **授與您租使用者的系統管理員同意** ] 按鈕。 如果您的帳戶沒有全域管理員角色，此按鈕將無法使用，而且您必須向您的組織要求全域系統管理員，才能執行此步驟。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-2.png" alt-text="授與同意":::

1. 同意授與您的應用程式之後，您應該會在 [ **狀態**] 底下看到綠色核取記號。
 
現在您的應用程式已註冊且已授與許可權，您可以在清單中取得最後一件事，也就是您應用程式的用戶端密碼。

1. 開啟 [Azure 入口網站](https://portal.azure.com/) ，然後流覽至 **Azure Active Directory** 服務。

1. 從功能表選取 [ **應用程式註冊** ]，然後選取新註冊的應用程式。

1. 從功能表選取 [ **憑證] & 秘密** ，然後按一下 [ **新增用戶端密碼** ] 按鈕，為您的應用程式取得 (API 金鑰) 的秘密。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-client-secret.png" alt-text="取得用戶端密碼":::

1. 按一下 [ **新增** ] 按鈕，並 **務必複製用戶端密碼**，因為如果您離開此頁面，就無法再次取出此秘密。 當您設定提示或自訂解決方案時，將會需要此值。

#### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>將此資訊輸入至您的秘訣解決方案或自訂應用程式

您現在擁有設定秘訣或自訂解決方案，以將威脅指標傳送給 Azure Sentinel 所需的三個資訊片段。 
- 應用程式 (用戶端) 識別碼
- 目錄 (租用戶) 識別碼
- 用戶端密碼

視需要在您的整合式提示或自訂解決方案的設定中輸入這些值，威脅指標將會透過以 Azure Sentinel 為目標的 Microsoft Graph tiIndicators API 傳送。

#### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>在 Azure Sentinel 中啟用威脅情報平臺資料連線器

整合程式的最後一個步驟是在 Azure Sentinel 中啟用 **威脅情報平臺** 資料連線器。 這是將透過 Microsoft Graph tiIndicators API 從您的秘訣或自訂解決方案傳送的威脅指標匯入 Azure Sentinel 的步驟。 您組織的所有 Azure Sentinel 工作區都可使用這些指標。 請遵循下列步驟，為每個工作區啟用威脅情報平臺資料連線器：

1. 開啟 [Azure 入口網站](https://portal.azure.com/) ，然後流覽至 **Azure Sentinel** 服務。

1. 選擇您要匯入從提示或自訂解決方案傳送的威脅指示器的 **工作區** 。

1. 從功能表選取 [ **資料連線器** ]，從 [連接器資源庫] 選取 [ **威脅情報平臺** ]，然後按一下 [ **開啟連接器頁面** ] 按鈕。

1. 由於您已完成應用程式註冊並設定您的秘訣或自訂解決方案來傳送威脅指標，因此唯一剩下的步驟是按一下 [連線 **]** 按鈕。

在幾分鐘內，威脅指示器應開始流入此 Azure Sentinel 工作區。

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>使用威脅情報將威脅指標新增至 Azure Sentinel-TAXII 資料連線器

威脅情報傳輸最普遍採用的產業標準，是 [STIX 資料格式和 TAXII 通訊協定的組合](https://oasis-open.github.io/cti-documentation/)。 如果您的組織從支援目前 STIX/TAXII 版本 (2.0 或 2.1) 的解決方案中取得威脅指標，您可以使用 **威脅情報-TAXII** 資料連線器，將威脅指示器帶入 Azure Sentinel。 威脅情報-TAXII data connector 可讓 Azure Sentinel 中的內建 TAXII 用戶端，從 TAXII 2.x 伺服器匯入威脅情報。

:::image type="content" source="media/import-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="TAXII 匯入路徑":::
 
依照下列步驟，將 STIX 格式的威脅指標匯入至來自 TAXII 伺服器的 Azure Sentinel：

1. 取得 TAXII 伺服器 API 的根目錄和集合識別碼

1. 在 Azure Sentinel 中啟用威脅情報-TAXII 資料連線器

現在讓我們看看每個步驟的詳細資訊。

#### <a name="obtain-the-taxii-server-api-root-and-collection-id"></a>取得 TAXII 伺服器 API 的根目錄和集合識別碼

TAXII 2.x 伺服器通告 API 根目錄，也就是裝載威脅情報集合的 Url。 您可以透過裝載 TAXII 伺服器的威脅情報提供者的檔頁面，來取得 API 根。 不過，有時候唯一通告的資訊是稱為探索端點的 URL。 如果是這種情況，就很容易使用探索端點來尋找 API 根。 如果您已經知道您想要使用的 TAXII 伺服器 API 根和集合識別碼，請隨意跳到下一節， **在 Azure Sentinel 中啟用威脅情報-TAXII 資料連線器**。

讓我們來看看如何使用簡單的命令列公用程式（稱為「 [捲曲](https://en.wikipedia.org/wiki/CURL)」）（在 Windows 和大部分的 Linux 發行版本中提供），以探索 API 根目錄，並只從探索端點開始流覽 TAXII 伺服器的集合。 在此範例中，我們將使用 [Anomali Limo](https://www.anomali.com/community/limo) ThreatStream TAXII 2.0 伺服器的探索端點。

1.  在瀏覽器中，流覽至 [THREATSTREAM TAXII 2.0 伺服器探索端點](https://limo.anomali.com/taxii) ，以取得 API 根目錄。 使用使用者和密碼進行驗證 `guest` 。

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  使用來自上一個步驟的捲曲公用程式和 API 根 (https://limo.anomali.com/api/v1/taxii2/feeds/) 來流覽 Api 根目錄上裝載的集合識別碼清單

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

您現在已擁有將 Azure Sentinel 連接到 Anomali Limo 所提供的一或多個 TAXII 伺服器集合所需的所有資訊。

| **API 根目錄** (https://limo.anomali.com/api/v1/taxii2/feeds/) | 集合識別碼 |
| ------------------------------------------------------------ | ------------: |
| **網路釣魚水箱**                                               | 107           |
| **Abuse.ch 勒索軟體 Ip**                                  | 135           |
| **Abuse.ch 勒索軟體網域**                              | 136           |
| **DShield 掃描 Ip**                                     | 150           |
| **惡意程式碼網域清單-Hotlist**                            | 200           |
| **Blutmagie TOR 節點**                                      | 209           |
| **新興的威脅 C&C Server**                              |  31           |
| **Lehigh Malwaredomains**                                    |  33           |
| **犯罪**                                               |  41           |
| **新興威脅-遭盜用**                           |  68           |
|

#### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>在 Azure Sentinel 中啟用威脅情報-TAXII 資料連線器

若要從 TAXII 伺服器將威脅指示器匯入 Azure Sentinel，請遵循下列步驟：

1. 開啟 [Azure 入口網站](https://portal.azure.com/) ，然後流覽至 **Azure Sentinel** 服務。

1. 選擇您要從 TAXII 伺服器匯入威脅指示器的 **工作區** 。

1. 從功能表選取 [ **資料連線器** ]，從 [連接器資源庫] 選取 [ **威脅情報-TAXII** ]，然後按一下 [ **開啟連接器頁面** ] 按鈕。

1. 輸入此 TAXII 伺服器集合的 **名稱** 、[ **API 根目錄 URL**]、[ **集合識別碼**]、[必要 **時 (]**) 和 [需要的 **密碼** (]) ，然後按一下 [ **新增** ] 按鈕。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-configure-taxii-servers.png" alt-text="設定 TAXII 伺服器":::
 
您應該會收到確認已成功建立 TAXII 伺服器的連線，而且您可以視需要重複上述步驟 (4) ，以從相同或不同的 TAXII 伺服器連線至多個集合。

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>查看 Azure Sentinel 中的威脅指示器

既然您已使用 **威脅情報平臺** 和/或 **威脅情報 TAXII** 資料連線器成功將威脅指標匯入 Azure Sentinel，您就可以在 **ThreatIntelligenceIndicator** 資料表的 **記錄** 檔中，查看所有 Azure Sentinel 事件資料的儲存位置。 此資料表是其他 Azure Sentinel 功能（例如分析和活頁簿）所執行之查詢的基礎。 以下是在 **ThreatIntelligenceIndicator** 資料表中尋找和查看威脅指標的方式。

1. 開啟 [Azure 入口網站](https://portal.azure.com/) ，然後流覽至 **Azure Sentinel** 服務。

1. 使用威脅情報資料連線器，選擇您已匯入威脅指標的 **工作區** 。

1. 從 [Azure Sentinel] 功能表的 [**一般**] 區段中選取 [**記錄**]。

1. **ThreatIntelligenceIndicator**資料表位於**Azure Sentinel**群組底下。

1. 選取 [ **預覽資料** ] 圖示 (資料表名稱旁邊的眼睛) ，然後選取 [ **在查詢編輯器中查看** ] 按鈕以執行會顯示此資料表記錄的查詢。

結果看起來應該類似下面所示的範例威脅指示器：

:::image type="content" source="media/import-threat-intelligence/threat-intel-sample-query.png" alt-text="範例查詢資料":::
 
## <a name="manage-your-threat-indicators-in-the-new-threat-intelligence-area-of-azure-sentinel"></a>在 Azure Sentinel 的新威脅情報區域中管理您的威脅指示器

有了新的 **威脅情報** 區域（可從 [Azure Sentinel] 功能表存取），您也可以在不寫入 **記錄** 查詢的情況下，查看、排序、篩選和搜尋已匯入的威脅指示器。 這個新的領域也可讓您直接在 Azure Sentinel 介面內建立威脅指標，以及執行常見的威脅情報系統管理工作，例如指標標記，以及建立與安全性調查相關的新指標。
讓我們來看看兩個最常見的工作，建立新的威脅指標和標記指標以方便群組和參考。

1. 開啟 [Azure 入口網站](https://portal.azure.com/) ，然後流覽至 **Azure Sentinel** 服務。

1. 使用威脅情報資料連線器，選擇您已匯入威脅指標的 **工作區** 。

1. 從 [Azure Sentinel] 功能表的 [威脅管理] 區段中選取 [ **威脅情報** ]。

1. 選取頁面頂端功能表中的 [ **加入新** 的] 按鈕。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-add-new-indicator.png" alt-text="新增威脅指標" lightbox="media/import-threat-intelligence/threat-intel-add-new-indicator.png":::

1. 選擇指標類型，然後在 **新的指標** 面板上完成標示紅色星號 ( * ) 的必要欄位。

1. 選取 [套用]。 指標會新增至 [指標] 方格，而且也會傳送至 **記錄**檔中的 ThreatIntelligenceIndicator 資料表。

標記威脅指標是將它們群組在一起以使其更容易尋找的簡單方式。 一般來說，您可能會將標記套用至與特定事件相關的指標，或套用至代表特定已知動作專案或知名攻擊活動之威脅的指標。 您可以個別標記威脅指標，或多重選取指標，然後一次標記這些指標。 以下顯示以事件識別碼標記多個指標的範例。 由於標記為自由格式，建議的作法是為威脅指標標記建立標準命名慣例。 您可以將多個標記套用到每個指標。

:::image type="content" source="media/import-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="將標籤套用至威脅指標" lightbox="media/import-threat-intelligence/threat-intel-tagging-indicators.png":::

## <a name="analytics-puts-your-threat-indicators-to-work-detecting-potential-threats"></a>分析可讓您的威脅指示器偵測潛在威脅

您已將威脅指示器送入 Azure Sentinel;您已瞭解如何查看和管理它們;現在就來看看它們可以為您做些什麼。 SIEM 解決方案（例如 Azure Sentinel）中威脅指標最重要的使用案例是 power analytics 規則。  這些以指標為基礎的規則會比較您的資料來源中的原始事件與您的威脅指標，以偵測您組織中的安全性威脅。 在 Azure Sentinel **分析**中，您可以建立定期執行的分析規則，並產生安全性警示。 規則是由查詢所驅動，以及決定應執行規則的頻率、何種查詢結果應該產生安全性警示，以及產生警示時要觸發的任何自動化回應。

雖然您可以隨時從頭開始建立新的分析規則，Azure Sentinel 提供一組由 Microsoft 安全性工程師所建立的內建規則範本，您可以依原樣使用或修改以符合您的需求。 您可以立即識別使用威脅指標的規則範本，因為它們的標題開頭為 "**TI map**..."。 所有這些規則範本的運作方式都相同，唯一的差別在於使用哪種類型的威脅指標 (網域、電子郵件、檔案雜湊、IP 位址或 URL) ，以及要比對的事件種類。 每個範本都會列出規則運作所需的必要資料來源，因此您可以一目了然地查看是否已在 Azure Sentinel 中匯入必要的事件。

讓我們看看其中一個規則範本，並逐步解說如何啟用和設定規則，以使用您已匯入 Azure Sentinel 的威脅指標來產生安全性警示。 在此範例中，我們將使用稱為 **TI MAP IP 實體**的規則範本來 AzureActivity。 這項規則會比對所有的 IP 位址類型威脅指標與您的所有 Azure 活動事件。 找到相符專案時，將會產生 **警示** ，以及由安全性作業小組調查的對應 **事件** 。 只有當您已啟用其中一或兩個 **威脅情報** 資料連線器 (匯入威脅指標) 和 **azure 活動** 資料連線器 (以匯入您的 azure 訂用帳戶層級事件) 時，此分析規則才會順利運作。

1. 開啟 [Azure 入口網站](https://portal.azure.com/) ，然後流覽至 **Azure Sentinel** 服務。

1. 使用**Azure 活動**資料連線器，選擇您使用**威脅情報**資料連線器和 azure 活動資料匯入威脅指標的**工作區**。

1. 從 [Azure Sentinel] 功能表的 [設定 **] 區段中選取 [** **分析**]。

1. 選取 [ **規則範本** ] 索引標籤，以查看可用分析規則範本的清單。

1. 流覽至標題為 [ **TI 對應 IP] 實體以 AzureActivity** 的規則，並確定您已連接所有必要的資料來源，如下所示。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-required-data-sources.png" alt-text="必要的資料來源":::

1. 選取此規則，然後選取 [ **建立規則** ] 按鈕。 這會開啟嚮導來設定規則。 完成這裡的設定，然後選取 [ **下一步：設定規則邏輯] >** 按鈕。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-create-analytics-rule.png" alt-text="建立分析規則":::

1. Wizard 的規則邏輯部分包含：
    - 將在規則中使用的查詢。

    - 實體對應，告訴 Azure Sentinel 如何辨識帳戶、IP 位址和 Url 等實體，讓 **事件** 和 **調查** 瞭解如何使用此規則所產生之任何安全性警示中的資料。

    - 要執行此規則的排程。

    - 產生安全性警示之前所需的查詢結果數目。

    範本中的預設設定為：
    - 每小時執行一次。

    - 將 **ThreatIntelligenceIndicator** 資料表中的任何 ip 位址威脅指標，與 **AzureActivity** 資料表的過去一小時內所找到的任何 ip 位址相符。

    - 如果查詢結果大於零，則產生安全性警示，表示找到任何相符的專案。

您可以保留預設設定，或變更這些設定以符合您的需求。 當您完成時，請選取 [ **下一步：自動回應 >] ** 按鈕

1. 當此分析規則產生安全性警示時，嚮導的這個步驟可讓您設定任何您想要觸發的自動化。 Azure Sentinel 中的自動化是使用工作 **手冊**（由 Azure Logic Apps 提供技術支援）來完成。 若要深入瞭解，請參閱本 [教學課程：在 Azure Sentinel 中設定自動威脅回應](./tutorial-respond-threats-playbook.md)。 在此範例中，我們只需要選取 [ **下一步：審核 >] ** 按鈕即可繼續。

1. 最後一個步驟會驗證規則中的設定。 當您準備好啟用規則時，請選取 [ **建立** ] 按鈕，然後完成。

現在您已啟用分析規則，您可以在 Azure Sentinel 的 [**分析**] 區段的 [作用中**規則**] 索引標籤中找到已啟用的規則。 您可以從該處編輯、啟用、停用、重複或刪除使用中的規則。 新規則會在啟用時立即執行，然後在其定義的排程上執行。

根據預設設定，每次規則依排程執行時，找到的任何結果都會產生安全性警示。 Azure Sentinel 中的安全性警示可在 Azure Sentinel 的 [**記錄**] 區段中，于**Azure Sentinel**群組底下的 [ **SecurityAlert** ] 資料表中查看。

在 Azure Sentinel 中，從分析規則產生的警示也會產生安全性事件，可在 [Azure Sentinel] 功能表上的 [**威脅管理**] 下的**事件**中找到。 事件是您的安全性作業小組將分級和調查的內容，以判斷適當的回應動作。 您可以在本教學課程中找到詳細資訊 [：使用 Azure Sentinel 調查事件](./tutorial-investigate-cases.md)。

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>活頁簿可提供您威脅情報的深入解析

最後，您可以使用 Azure Sentinel 活頁簿，以視覺化方式呈現有關您在 Azure Sentinel 中威脅情報的重要資訊，而且可以根據您的業務需求輕鬆地自訂活頁簿。
讓我們逐步解說如何尋找 Azure Sentinel 中提供的威脅情報活頁簿，同時也會示範如何對活頁簿進行編輯以進行自訂。

1. 開啟 [Azure 入口網站](https://portal.azure.com/) ，然後流覽至 **Azure Sentinel** 服務。

1. 使用威脅情報資料連線器，選擇您已匯入威脅指標的 **工作區** 。

1. 從 [Azure Sentinel] 功能表的 [**威脅管理**] 區段中，選取 [活頁**簿**]。

1. 流覽至標題為 [ **威脅情報** ] 的活頁簿，並確認您有 **ThreatIntelligenceIndicator** 資料表中的資料，如下所示。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-verify-data.png" alt-text="驗證資料":::
 
1. 選取 [ **儲存** ] 按鈕，然後選擇要儲存活頁簿的 Azure 位置。 如果您要以任何方式修改活頁簿，並儲存變更，則需要此步驟。

1. 現在，選取 [ **View saved] （已儲存** 的活頁簿）按鈕開啟活頁簿，以進行流覽和編輯。

1. 您現在應該會看到範本所提供的預設圖表。 現在讓我們對其中一個圖表進行一些變更。 選取頁面頂端的 [ **編輯** ] 按鈕，進入活頁簿的編輯模式。

1. 讓我們依威脅類型新增威脅指示器的新圖表。 滾動至頁面底部，然後選取 [加入查詢]。

1. 將下列文字新增至 [ **Log Analytics 工作區記錄查詢** ] 文字方塊：
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. 在 [ **視覺效果** ] 下拉式清單中，選取 [ **橫條圖**]。

1. 選取 [ **完成編輯** ] 按鈕。 您已為您的活頁簿建立新的圖表。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-bar-chart.png" alt-text="橫條圖":::

活頁簿提供功能強大的互動式儀表板，可讓您深入瞭解 Azure Sentinel 的所有層面。 您可以使用活頁簿來完成許多工作，雖然提供的範本是很好的起點，但是您可能會想要深入探索和自訂這些範本，或是建立新的儀表板，結合許多不同的資料來源，讓您能夠以獨特的方式將資料視覺化。 由於 Azure Sentinel 活頁簿是以 Azure 監視器活頁簿為基礎，因此已經有廣泛的可用檔，還有更多範本。 這篇文章將說明如何 [使用 Azure 監視器活頁簿建立互動式報表](../azure-monitor/platform/workbooks-overview.md)，這是很好的起點。 

[GitHub 上](https://github.com/microsoft/Application-Insights-Workbooks)也有豐富的 Azure 監視器活頁簿，您可以在其中下載其他範本，並提供您自己的範本。

## <a name="next-steps"></a>下一步
在本檔中，您已瞭解 Azure Sentinel 的威脅情報功能，以及新的威脅情報 blade。 如需使用 Azure Sentinel 的威脅情報功能的實用指導方針，請參閱下列文章：

- [將威脅情報資料連線](./connect-threat-intelligence.md) 至 Azure Sentinel。
- 在 Azure Sentinel 中建立 [內建](./tutorial-detect-threats-built-in.md) 或 [自訂](./tutorial-detect-threats-custom.md) 警示，以及 [調查](./tutorial-investigate-cases.md) 事件。