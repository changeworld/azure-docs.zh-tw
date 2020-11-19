---
title: 啟用 Azure Functions 的私人網站存取
description: 了解如何設定 Azure Functions 的 Azure 虛擬網路私人網站存取。
author: craigshoemaker
ms.author: cshoe
ms.service: azure-functions
ms.topic: tutorial
ms.date: 06/17/2020
ms.openlocfilehash: e367e4f2a704d8c718551fb031164520b3ff5bb3
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579125"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>教學課程：建立 Azure Functions 私人網站存取

本教學課程說明如何使用 Azure Functions 來啟用[私人網站存取](./functions-networking-options.md#private-endpoint-connections)。 使用私人網站存取，您可以要求只從特定的虛擬網路觸發您的函式程式碼。

當函式應用程式的存取權必須限制為特定的虛擬網路時，私人網站存取會很有用。 例如，函式應用程式可能僅適用於特定組織的員工，或位於指定虛擬網路 (例如另一個 Azure Function、Azure 虛擬機器或 AKS 叢集) 內的服務。

如果函式應用程式需要存取虛擬網路中的 Azure 資源，或透過[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)連線，則需要[虛擬網路整合](./functions-create-vnet.md)。

在本教學課程中，您將了解如何設定函式應用程式的私人網站存取：

> [!div class="checklist"]
> * 建立虛擬機器
> * 建立 Azure Bastion 服務
> * 建立 Azure Functions 應用程式
> * 設定虛擬網路服務端點
> * 建立並部署 Azure 函式
> * 從外部和虛擬網路內叫用函式

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="topology"></a>拓撲

下圖顯示要建立的解決方案架構：

![私人網站存取解決方案的高階架構圖](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>必要條件

在本教學課程中，您必須了解 IP 定址和子網路設定。 您可以從 [本文](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)開始，其內容涵蓋了定址和子網路設定的基本概念。 您也可以從線上取得更多文章和影片。

## <a name="sign-in-to-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

本教學課程的第一個步驟是在虛擬網路內建立新的虛擬機器。  一旦您將虛擬機器的存取權限制為只能從虛擬網路存取，系統就會使用虛擬機器來存取您的函式。

1. 選取 [建立資源] 按鈕。

1. 在搜尋欄位中，輸入 **Windows Server**，然後在搜尋結果中選取 [Windows Server]。

1. 從 Windows Server 選項清單中選取 [Windows Server 2019 Datacenter]，然後按 [建立] 按鈕。

1. 在 [基本資訊] 索引標籤中，使用影像下方表格中指定的 VM 設定：

    >[!div class="mx-imgBorder"]
    >![新 Windows VM 的基本資訊索引標籤](./media/functions-create-private-site-access/create-vm-3.png)

    | 設定      | 建議的值  | 描述      |
    | ------------ | ---------------- | ---------------- |
    | _訂用帳戶_ | 您的訂用帳戶 | 建立您資源所在位置的訂用帳戶。 |
    | [_資源群組_](../azure-resource-manager/management/overview.md) | myResourceGroup | 選擇資源群組，以包含本教學課程中的所有資源。  使用相同的資源群組可讓您在結束此教學課程時能更輕鬆地清除資源。 |
    | _虛擬機器名稱_ | myVM | VM 名稱在資源群組中必須是唯一的 |
    | [_區域_](https://azure.microsoft.com/regions/) | (美國) 美國中北部 | 選擇您附近或接近要存取之函式附近的區域。 |
    | _公用輸入連接埠_ | None | 選取 [無] 以確保沒有從網際網路到 VM 的輸入連線能力。 VM 的遠端存取會透過 Azure Bastion 服務來設定。 |

1. 選擇 [網路] 索引標籤，然後選取 [建立新的] 來設定新的虛擬網路。

    >[!div class="mx-imgBorder"]
    >![此螢幕擷取畫面顯示 [網路] 索引標籤，其中 [新建] 動作已在 [虛擬網路] 區段中反白顯示。](./media/functions-create-private-site-access/create-vm-networking.png)

1. 在 _建立虛擬網路_ 中，使用影像下方表格中的設定：

    >[!div class="mx-imgBorder"]
    >![為新 VM 建立新的虛擬網路](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | 設定      | 建議的值  | 描述      |
    | ------------ | ---------------- | ---------------- |
    | _名稱_ | myResourceGroup-vnet | 您可以使用為虛擬網路產生的預設名稱。 |
    | _位址範圍_ | 10.10.0.0/16 | 在虛擬網路中使用單一位址範圍。 |
    | _子網路名稱_ | 教學課程 | 子網路的名稱。 |
    | _位址範圍_ (子網路) | 10.10.1.0/24 | 子網路大小會定義可新增至子網路的介面數目。 VM 會使用此子網路。 /24 子網路會提供 254 個主機位址。 |

1. 選取 [確定] 以建立虛擬網路。
1. 回到 [網路] 索引標籤，確定已在 [公用 IP] 中選取 [無]。
1. 選擇 [管理] 索引標籤，然後在 [診斷儲存體帳戶] 中，選擇 [建立新的] 來建立新的儲存體帳戶。
1. 保留 _身分識別_、_自動關閉_，以及 _備份_ 區段的預設值。
1. 選取 [檢閱 + 建立]。 驗證完成時，選取 [建立]。 VM 建立程序需要幾分鐘完成。

## <a name="configure-azure-bastion"></a>設定 Azure Bastion

[Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) 是完全受控的 Azure 服務，可直接從 Azure 入口網站提供虛擬機器安全的 RDP 和 SSH 存取權。 使用 Azure Bastion 服務，就不需要設定與 RDP 存取相關的網路設定。

1. 在入口網站中，選擇資源群組檢視頂端的 [新增]。
1. 在搜尋欄位中，輸入 **Bastion**。
1. 在搜尋結果中選取 [Bastion]。
1. 選取 [建立] 開始建立新 Azure Bastion資源的流程。 您會在 [虛擬網路] 區段中發現錯誤訊息，因為還未建立 AzureBastionSubnet 子網路。 子網路會在後續步驟中建立。 使用影像下方表格中的設定：

    >[!div class="mx-imgBorder"]
    >![開始建立 Azure Bastion](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | 設定      | 建議的值  | 描述      |
    | ------------ | ---------------- | ---------------- |
    | _名稱_ | myBastion | 新 Bastion 資源的名稱 |
    | _區域_ | 美國中北部 | 選擇與您接近的[區域](https://azure.microsoft.com/regions/)，或選擇與函式將會存取之其他服務接近的區域。 |
    | _虛擬網路_ | myResourceGroup-vnet | 將在其中建立 Bastion 資源的虛擬網路 |
    | _子網路_ | AzureBastionSubnet | 虛擬網路中將部署新 Bastion 主機資源的子網路。 您必須使用 **AzureBastionSubnet** 名稱值來建立子網路。 此值可讓 Azure 知道要將 Bastion 資源部署到哪一個子網路。 您必須使用至少 **/27** 或更大的子網路 (/27、/26 等等)。 |

    > [!NOTE]
    > 如需建立 Azure Bastion 資源的詳細逐步指南，請參閱[建立 Azure Bastion 主機](../bastion/bastion-create-host-portal.md)教學課程。

1. 建立 Azure 可在其中佈建 Azure Bastion 主機的子網路。 選擇 [管理子網路設定] 會開啟新的窗格，您可以在其中定義新的子網路。  選擇 [+ 子網路] 以建立新的子網路。
1. 子網路的名稱必須是 **AzureBastionSubnet** 且子網路前置詞至少必須是 **/27**。  選取 [確定] 以建立子網路。

    >[!div class="mx-imgBorder"]
    >![建立 Azure Bastion 主機的子網路](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

1. 在 [建立 Bastion] 頁面上，從可用的子網路清單中選取新建立的 [AzureBastionSubnet]。

    >[!div class="mx-imgBorder"]
    >![建立具有特定子網路的 Azure Bastion 主機](./media/functions-create-private-site-access/create-bastion-basics-2.png)

1. 選取 [檢閱和建立]。 驗證完成時，選取 [建立]。 建立 Azure Bastion 資源需要幾分鐘的時間。

## <a name="create-an-azure-functions-app"></a>建立 Azure Functions 應用程式

下一個步驟是使用[取用方案](functions-scale.md#consumption-plan)在 Azure 中建立函式應用程式。 您稍後會在本教學課程中將函式程式碼部署到此資源。

1. 在入口網站中，選擇資源群組檢視頂端的 [新增]。
1. 選取 [計算 > 函式應用程式]
1. 在 [基本資訊] 區段中，使用下表中指定的函式應用程式設定。

    | 設定      | 建議的值  | 描述      |
    | ------------ | ---------------- | ---------------- |
    | _資源群組_ | myResourceGroup | 選擇資源群組，以包含本教學課程中的所有資源。  讓函式應用程式和 VM 使用相同的資源群組可讓您在結束此教學課程時能更輕鬆地清除資源。 |
    | _函式應用程式名稱_ | 全域唯一的名稱 | 用以識別新函式應用程式的名稱。 有效字元為 a-z (區分大小寫)、0-9 以及 -。 |
    | _Publish_ | 程式碼 | 發佈程式碼檔案或 Docker 容器的選項。 |
    | _執行階段堆疊_ | 慣用語言 | 選擇支援您慣用函式程式設計語言的執行階段。 |
    | _區域_ | 美國中北部 | 選擇與您接近的[區域](https://azure.microsoft.com/regions/)，或選擇與函式將會存取之其他服務接近的區域。 |

    選取頁面底部的 [下一步:裝載 >] 按鈕。
1. 在 [裝載] 區段中，選取適當的 [儲存體帳戶]、[作業系統]和 [方案]，如下表所述。

    | 設定      | 建議的值  | 描述      |
    | ------------ | ---------------- | ---------------- |
    | _儲存體帳戶_ | 全域唯一的名稱 | 建立您函式應用程式使用的儲存體帳戶。 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。 您也可以使用現有帳戶，條件是必須符合[儲存體帳戶需求](./functions-scale.md#storage-account-requirements)。 |
    | _作業系統_ | 慣用的作業系統 | 系統會根據您的執行階段堆疊選項預先選取作業系統，但您可以視需要變更設定。 |
    | _規劃_ | 耗用量 | [主控方案](./functions-scale.md) 會指示函式應用程式的調整方式，以及每個執行個體可用的資源。 |
1. 選取 [檢閱 + 建立]，以檢閱應用程式組態選項。
1. 選取 [建立] 以佈建並部署函式應用程式。

## <a name="configure-access-restrictions"></a>設定存取限制

下一個步驟是設定[存取限制](../app-service/app-service-ip-restrictions.md)，確保只有虛擬網路上的資源可以叫用函式。

在函式應用程式與指定的虛擬網路之間建立 Azure 虛擬網路[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)，即可啟用[私人網站](functions-networking-options.md#private-endpoint-connections)存取。 存取限制是透過服務端點來執行。 服務端點會確保只有來自指定虛擬網路內的流量可以存取指定的資源。 在此情況下，指定的資源是 Azure 函式。

1. 在函式應用程式內，選取 [設定] 區段標頭底下的 [網路] 連結。
1. [網路] 頁面是設定 Azure Front Door、Azure CDN 和存取限制的起點。
1. 選取 [設定存取限制] 以設定私人網站存取權。
1. 在 _存取限制_ 頁面上，您只會看到預設限制。 預設不會對函式應用程式的存取施加任何限制。  選取 [新增規則]，以建立私人網站存取限制組態。
1. 在 [新增存取限制] 窗格中，為新規則提供 [名稱]、[優先順序] 和 [描述]。
1. 從 [類型] 下拉式方塊中選取 [虛擬網路]、選取先前建立的虛擬網路，然後選取 [教學課程] 子網路。 
    > [!NOTE]
    > 可能需要幾分鐘的時間來啟用服務端點。
1. _存取限制_ 頁面現在會顯示有新的限制。 可能需要幾秒鐘的時間，[端點狀態] 才會從 [已停用] 變更為 [佈建中] 再變更為 [已啟用]。

    >[!IMPORTANT]
    > 每個函式應用程式都有一個[進階工具 (Kudu) 網站](../app-service/app-service-ip-restrictions.md#restrict-access-to-an-scm-site)，可用來管理函式應用程式部署。 此網站可從 URL 存取，例如：`<FUNCTION_APP_NAME>.scm.azurewebsites.net`。 在 Kudu 網站上啟用存取限制可防止從本機開發人員的工作站部署專案程式碼，然後虛擬網路內需要有代理程式才能執行部署。

## <a name="access-the-functions-app"></a>存取函式應用程式

1. 回到先前建立的函式應用程式。  在 [概觀] 區段中，複製 URL。

    >[!div class="mx-imgBorder"]
    >![取得函式應用程式 URL](./media/functions-create-private-site-access/access-function-overview.png)

    如果嘗試從虛擬網路外的電腦立即存取函式應用程式，您會收到 HTTP 403 頁面指出禁止存取。
1. 請返回資源群組，然後選取先前建立的虛擬機器。 若要從 VM 存取此網站，您必須透過 Azure Bastion 服務連線到 VM。
1. 選取 [連線]，然後選擇 [Bastion]。
1. 提供所需的使用者名稱和密碼來登入虛擬機器。
1. 選取 [連接]。 新的瀏覽器視窗隨即出現，讓您能夠與虛擬機器進行互動。
VM 會透過虛擬網路存取網站，因此您可以從 VM 上的網頁瀏覽器存取網站。  雖然只能從指定的虛擬網路中存取網站，但仍會有公用 DNS 項目。

## <a name="create-a-function"></a>建立函式

本教學課程的下一個步驟是建立可透過 HTTP 觸發的 Azure 函式。 透過 HTTP GET 或 POST 叫用函式應該會產生「Hello，{name}」的回應。  

1. 遵循下列其中一個快速入門，建立和部署您的 Azure Functions 應用程式。

    * [Visual Studio Code](./functions-create-first-function-vs-code.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [命令列](./functions-create-first-azure-function-azure-cli.md)
    * [Maven (Java)](./create-first-function-cli-java.md?tabs=bash,browser)

1. 發佈 Azure Functions 專案時，請選擇您稍早在本教學課程中建立的函式應用程式資源。
1. 確認已部署函式。

    >[!div class="mx-imgBorder"]
    >![函式清單中的已部署函式](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>直接叫用函式

1. 為了測試函式的存取權，您必須複製函式 URL。 選取已部署的函式，然後選取 [取得函式 URL]。 接著按一下 [複製] 按鈕，將 URL 複製到剪貼簿。

    >[!div class="mx-imgBorder"]
    >![複製函式 URL](./media/functions-create-private-site-access/get-function-url.png)

1. 在網頁瀏覽器中貼上 URL。 如果嘗試從虛擬網路外的電腦立即存取函式應用程式，您會收到 HTTP 403 回應指出禁止存取應用程式。

## <a name="invoke-the-function-from-the-virtual-network"></a>從虛擬網路叫用函式

您可順利在虛擬網路上設定的 VM 上透過網頁瀏覽器存取函式 (使用 Azure Bastion 服務)！

>[!div class="mx-imgBorder"]
>![透過 Azure Bastion 存取 Azure 函式](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>後續步驟


> [!div class="nextstepaction"]
> [深入了解 Azure Functions 中的網路功能選項](./functions-networking-options.md)
