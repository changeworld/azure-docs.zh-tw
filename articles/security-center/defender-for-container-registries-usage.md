---
title: 如何使用適用于容器登錄的 Azure Defender
description: 瞭解如何使用適用于容器登錄的 Azure Defender 來掃描登錄中的映射
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 18fa1b14bb20bbb2805eee2ec663d1316f787950
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936573"
---
# <a name="use-azure-defender-for-container-registries-to-scan-your-images-for-vulnerabilities"></a>使用適用于容器登錄的 Azure Defender 來掃描映射是否有弱點

此頁面說明如何使用內建的弱點掃描器來掃描儲存在 Azure Resource Manager 型 Azure Container Registry 中的容器映射。

啟用 **container registry 的 Azure Defender** 時，您推送至登錄的任何映射都會立即掃描。 此外，也會掃描過去30天內提取的任何影像。 

當掃描器將弱點回報給資訊安全中心時，資訊安全中心會將結果和相關資訊顯示為建議。 此外，這些結果還包含相關的資訊，例如補救步驟、相關 Cve、CVSS 分數等等。 您可以針對一或多個訂用帳戶或特定登錄，查看已識別的弱點。

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|正式上市 (GA)|
|定價：|**適用于 container registry 的 Azure Defender**會依[定價頁面上的](security-center-pricing.md)顯示方式計費|
|支援的登錄和映射：|![是 ](./media/icons/yes-icon.png) 可從公用網際網路存取並提供 shell 存取的 Linux 託管 ACR 登錄。<br>![沒有任何 ](./media/icons/no-icon.png) Windows 主控的 ACR 登錄。<br>![無「私人」登錄 ](./media/icons/no-icon.png) -「安全性中心」需要您的登錄才能從公用網際網路存取。 「安全性中心」目前無法連接或掃描登錄，其存取權受限於防火牆、服務端點或私人端點（例如 Azure Private Link）。<br>![沒有任何 ](./media/icons/no-icon.png) 超級極簡映射（例如 [Docker 臨時](https://hub.docker.com/_/scratch/) 影像）或 "Distroless" 映射（只包含應用程式和其執行時間相依性，沒有套件管理員、shell 或 OS）。|
|必要的角色和許可權：|**安全性讀取者** 和 [Azure Container Registry 讀者角色](https://docs.microsoft.com/azure/container-registry/container-registry-roles)|
|雲端：|![Yes](./media/icons/yes-icon.png) 商業雲端<br>![No](./media/icons/no-icon.png) 全國/主權 (US Gov、中國 Gov、其他 Gov) |
|||


## <a name="identify-vulnerabilities-in-images-in-azure-container-registries"></a>識別 Azure container registry 映射中的弱點 

1. 若要啟用以 Azure Resource Manager 為基礎 Azure Container Registry 中所儲存映射的弱點掃描：

    1. 為您的訂用帳戶啟用 **適用于容器登錄的 Azure Defender** 。

        安全性中心現在已準備好在您的登錄中掃描映射。

        >[!NOTE]
        > 這項功能是依每個映射收費。

1. 映射掃描會在每次推送或匯入時觸發，以及在過去30天內提取映射。 

    當掃描完成時 (通常是在大約2分鐘之後，但最多可能會有15分鐘的) ，結果會以安全性中心建議的形式提供。

1. [查看並補救結果，如下所述](#view-and-remediate-findings)。

## <a name="identify-vulnerabilities-in-images-in-other-container-registries"></a>識別其他容器登錄中映射的弱點 

1. 使用 ACR 工具，從 Docker Hub 或 Microsoft Container Registry 將映射帶入您的登錄。  匯入完成時，Azure Defender 會掃描匯入的映射。 

    深入瞭解[如何將容器映射匯入至容器](../container-registry/container-registry-import-images.md)登錄

    當掃描完成時 (通常是在大約2分鐘之後，但最多可能會有15分鐘的) ，結果會以安全性中心建議的形式提供。

1. [查看並補救結果，如下所述](#view-and-remediate-findings)。


## <a name="view-and-remediate-findings"></a>查看及補救結果

1. 若要查看結果，請移至 [ **建議** ] 頁面。 如果發現問題，您將會看到**Azure Container Registry 映射中應補救**的建議弱點

    ![補救問題的建議 ](media/monitor-container-security/acr-finding.png)

1. 選取建議。 

    [建議詳細資料] 頁面隨即開啟，並提供其他資訊。 這項資訊包括具有易受攻擊的映射的登錄清單 ( 「受影響的資源」 ) 和補救步驟。 

1. 選取特定的登錄，以查看裡面有易受攻擊的存放庫的存放庫。

    ![選取登錄](media/monitor-container-security/acr-finding-select-registry.png)

    [登錄詳細資料] 頁面隨即開啟，其中包含受影響的存放庫清單。

1. 選取特定的存放庫，以查看裡面有易受攻擊的映射的存放庫。

    ![選取存放庫](media/monitor-container-security/acr-finding-select-repository.png)

    [存放庫詳細資料] 頁面隨即開啟。 它會列出易受攻擊的映射，並評估結果的嚴重性。

1. 選取特定的映射以查看弱點。

    ![選取影像](media/monitor-container-security/acr-finding-select-image.png)

    所選映射的結果清單隨即開啟。

    ![結果清單](media/monitor-container-security/acr-findings.png)

1. 若要深入瞭解尋找，請選取 [尋找]。 

    [結果詳細資料] 窗格隨即開啟。

    [![結果詳細資料窗格](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    此窗格包含問題的詳細描述，以及外部資源的連結，以協助減輕威脅。

1. 依照此窗格的 [補救] 區段中的步驟執行。

1. 當您採取補救安全性問題所需的步驟時，請更換您登錄中的映射：

    1. 推送更新的映射。 這將會觸發掃描。 
    
    1. 請查看建議頁面，以取得建議「應補救 Azure Container Registry 映射中的弱點」。 
    
        如果仍出現建議，且您已處理的影像仍出現在易受攻擊的映射清單中，請再次檢查補救步驟。

    1. 當您確定已推送、掃描並不再出現建議中的已更新映射時，請從您的登錄中刪除「舊」易受攻擊的映射。


## <a name="disable-specific-findings-preview"></a>停用特定的結果 (預覽) 

如果您的組織需要忽略尋找，而不是補救它，您可以選擇性地停用它。 停用的結果不會影響您的安全分數或產生不必要的雜訊。

當尋找符合您在停用規則中定義的準則時，不會出現在結果清單中。 一般案例包括：

- 停用低於中等嚴重性的結果
- 停用非可修補的結果
- 停用低於6.5 的 CVSS 分數的結果
- 使用安全性檢查或類別中的特定文字停用結果 (例如 "RedHat"、"CentOS Security Update for sudo" ) 

> [!IMPORTANT]
> 若要建立規則，您需要在 Azure 原則中編輯原則的許可權。
>
> 在 Azure 原則中深入瞭解 [RBAC 許可權](../governance/policy/overview.md#rbac-permissions-in-azure-policy)。

您可以使用下列任一準則： 

- 尋找識別碼 
- 類別
- 安全性檢查 
- CVSS v3 分數
- 嚴重性 
- 可修補狀態 

若要建立規則：

1. 在 **應補救 Azure Container Registry 映射中弱點**的 [建議詳細資料] 頁面上，選取 [ **停用規則**]。
1. 選取相關的範圍。
1. 定義準則。
1. 選取 [套用 **規則**]。

    :::image type="content" source="./media/defender-for-container-registries-usage/new-disable-rule-for-registry-finding.png" alt-text="針對登錄上的 VA 結果建立停用規則":::

1. 若要查看、覆寫或刪除規則： 
    1. 選取 [ **停用規則**]。
    1. 從範圍清單中，使用作用中規則的訂閱會顯示為已套用的 **規則**。
        :::image type="content" source="./media/remediate-vulnerability-findings-vm/modify-rule.png" alt-text="修改或刪除現有的規則":::
    1. 若要查看或刪除規則，請選取省略號功能表 ( ) ]。


## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [深入瞭解 Azure Defender](azure-defender.md)