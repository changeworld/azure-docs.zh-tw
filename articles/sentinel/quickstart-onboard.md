---
title: 快速入門：在 Azure Sentinel 中上線
description: 在本快速入門中，您可以先啟用 Sentinel，然後連接資料來源，以深入了解如何讓 Azure Sentinel 上線。
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 10/14/2020
ms.custom: references_regions
ms.openlocfilehash: 21e0cfd56a37304d1ec333f8713894504282541f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660656"
---
# <a name="quickstart-on-board-azure-sentinel"></a>快速入門：使 Azure Sentinel 上線

在本快速入門中，您將了解如何使 Azure Sentinel 上線。 

若要讓 Azure Sentinel 上線，必須先啟用 Azure Sentinel，然後連線到您的資料來源。 Azure Sentinel 隨附多種適用於 Microsoft 解決方案的連接器，現成可用且提供即時整合，包括 Microsoft 365 Defender (前稱 Microsoft 威脅防護) 解決方案、Microsoft 365 來源 (包括 Office 365)、Azure AD、Microsoft Defender for Identity (前稱 Azure ATP)、Microsoft Cloud App Security 以及來自 Azure 資訊安全中心的 Azure Defender 警示等等。 此外，還有適用於非 Microsoft 解決方案的內建連接器，用於連線至更廣泛的安全性生態系統。 您也可以使用常見事件格式 (CEF) (Syslog 或 REST API) 來連線資料來源與 Azure Sentinel。 

連線您的資料來源之後，請從建立的活頁簿 (以您的資料為基礎進行深入解析) 資源庫中選擇。 這些活頁簿可以根據您的需求輕鬆地進行自訂。

>[!IMPORTANT] 
> 如需使用 Azure Sentinel 時所產生費用的相關資訊，請參閱 [Azure Sentinel 定價](https://azure.microsoft.com/pricing/details/azure-sentinel/)。

## <a name="global-prerequisites"></a>全域必要條件

- 如果您沒有使用中的 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- Log Analytics 工作區。 了解如何[建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。 如需 Log Analytics 工作區的詳細資訊，請參閱[設計您的 Azure 監視器記錄部署](../azure-monitor/platform/design-logs-deployment.md)。

- 若要啟用 Azure Sentinel，您需要 Azure Sentinel 工作區所在之訂用帳戶的參與者權限。 
- 若要使用 Azure Sentinel，您需要工作區所屬資源群組的參與者或讀者權限。
- 連線特定資料來源可能需要其他權限。
- Azure Sentinel 是付費服務。 如需價格相關資訊，請參閱[關於 Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058)。

### <a name="geographical-availability-and-data-residency"></a>地理可用性與資料落地

- Azure Sentinel 可以在任何 [Log Analytics 的 GA 區域](https://azure.microsoft.com/global-infrastructure/services/?products=monitor)的工作區上執行，中國和德國 (主權) 區域除外。 

- Azure Sentinel 所產生的資料 (例如事件、書簽和分析規則) 可能包含源自客戶 Log Analytics 工作區的一些客戶資料。 根據工作區所在的地理位置，此 Azure Sentinel 產生的資料會儲存在下表所列的地理位置中：

    | 工作區地理位置 | Azure Sentinel 產生的資料地理位置 |
    | --- | --- |
    | 美國<br>印度<br>巴西<br>非洲<br>南韓 | 美國 |
    | 歐洲<br>法國<br>瑞士 | 歐洲 |
    | 澳大利亞 | 澳大利亞 |
    | United Kingdom | United Kingdom |
    | Canada | Canada |
    | 日本 | 日本 |
    |

## <a name="enable-azure-sentinel"></a>啟用 Azure Sentinel<a name="enable"></a>

1. 登入 Azure 入口網站。 請確定已選取 Azure Sentinel 建立所在的訂用帳戶。

1. 搜尋並選取 [Azure Sentinel]。

   ![服務搜尋](./media/quickstart-onboard/search-product.png)

1. 選取 [新增]。

1. 選取您想要使用的工作區，或建立一個新的工作區。 您可以在一個以上的工作區上執行 Azure Sentinel，但資料會隔離到單一工作區。

   ![選擇工作區](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Azure 資訊安全中心所建立的預設工作區不會出現在清單中；您無法在其上安裝 Azure Sentinel。
   >

   >[!IMPORTANT]
   >
   > - 在工作區部署之後，Azure Sentinel **目前即不支援** 將該工作區移至其他資源群組或訂用帳戶。 
   >
   >   如果您已移動工作區，請停用 **Analytics** 下的所有作用中規則，然後在五分鐘後重新啟用。 不過在大部分的情況下，這種做法應該是有效的，但請注意此方法不受支援，而且您需自行承擔風險。

1. 選取 [新增 Azure Sentinel]。

## <a name="connect-data-sources"></a>連線資料來源

Azure Sentinel 藉由連線到服務並將事件和記錄轉送至 Azure Sentinel，從服務和應用程式擷取資料。 針對實體和虛擬機器，您可以安裝 Log Analytics 代理程式，該代理程式會收集記錄然後轉送至 Azure Sentinel。 對於防火牆和 Proxy，Azure Sentinel 會在 Linux Syslog 伺服器上安裝 Log Analytics 代理程式，該代理程式會從中收集記錄檔並將其轉送至 Azure Sentinel。 
 
1. 從主功能表中，選取 [資料連接器]。 這會開啟資料連接器資源庫。

1. 此資源庫是您可連線的所有資料來源清單。 選取資料來源，然後選取 [開啟連接器頁面] 按鈕。

1. 連接器頁面會顯示設定連接器的相關指示，以及任何可能需要的其他指示。<br>
例如，若您選取 [Azure Active Directory] 資料來源，其可讓您將記錄從 Azure AD 串流至 Azure Sentinel，則可選取您要取得的記錄類型 (登入記錄及/或稽核記錄)。 <br> 依照安裝指示或[參閱相關的連線指南](connect-data-sources.md)以取得詳細資訊。 如需資料連接器的詳細資訊，請參閱[連線 Microsoft 服務](connect-data-sources.md)。

1. 連接器頁面上的 [後續步驟] 索引標籤會顯示相關的內建活頁簿、範例查詢，以及資料連接器隨附的分析規則範本。 您可以依現況使用範本或加以修改 - 無論哪種方式，您都可以立即取得資料的相關見解。 <br>

與資料來源連線之後，您的資料就會開始串流至 Azure Sentinel，並準備好讓您開始使用。 您可以在[內建活頁簿](quickstart-get-visibility.md)中檢視記錄，並且開始在 Log Analytics 中建置查詢以[調查資料](tutorial-investigate-cases.md)。

## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何將資料來源上線和連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- 從[常見的事件格式設備](connect-common-event-format.md)將資料串流至 Azure Sentinel。