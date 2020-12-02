---
title: 將您的 AWS 帳戶連線至 Azure 資訊安全中心
description: 從 Azure 資訊安全中心監視您的 AWS 資源
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 7aa65cb8e37ce48a59c276fdf2631f75397d3236
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122503"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>將您的 AWS 帳戶連線到 Azure 資訊安全中心

由於雲端工作負載通常需要跨越多個雲端平台，因此雲端安全性服務必須執行相同動作。

Azure 資訊安全中心會保護 Azure、Amazon Web Services (AWS) 和 Google Cloud Platform (GCP) 中的工作負載。

將您的 AWS 帳戶上線至資訊安全中心，即可整合 AWS 安全性中樞和 Azure 資訊安全中心。 因此，資訊安全中心會在這兩個雲端環境間提供可見度和保護，以支援：

- 自動佈建代理程式 (資訊安全中心會使用 [Azure Arc](../azure-arc/servers/overview.md) 將 Log Analytics 代理程式部署至您的 AWS 執行個體)
- 原則管理
- 弱點管理
- 內嵌端點偵測和回應 (EDR)
- 安全性設定錯誤的偵測
- 顯示資訊安全中心建議和 AWS 安全性中樞結果的單一檢視
- 將您的 AWS 資源合併到資訊安全中心的安全分數計算中
- AWS 資源的法規合規性評量

在下面的螢幕擷取畫面中，您可以看到 AWS 帳戶顯示在資訊安全中心的 [概觀] 儀表板中。

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="資訊安全中心的概觀儀表板上所列的 3 個 GCP 專案" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|預覽<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|定價：|需要[適用於伺服器的 Azure Defender](defender-for-servers-introduction.md)|
|必要的角色和權限：|相關 Azure 訂用帳戶上的 **擁有者** 或 **參與者**|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![否](./media/icons/no-icon.png) 國家/地區/主權 (US Gov、中國 Gov、其他 Gov)|
|||



## <a name="connect-your-aws-account"></a>連接 AWS 帳戶

### <a name="step-1-set-up-aws-security-hub"></a>步驟 1： 設定 AWS 安全性中樞：

1. 若要檢視多個區域的安全性建議，請對每個相關區域重複執行下列步驟。

    > [!IMPORTANT]
    > 如果您使用 AWS 主要帳戶，請重複下列三個步驟，在所有相關區域間設定主要帳戶和所有連線的成員帳戶

    1. 啟用 [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html)。
    1. 啟用 [AWS 安全性中樞](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html)。
    1. 確認有資料流向安全性中樞。

        當您第一次啟用安全性中樞時，資料可能需要幾個小時才可供使用。

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>步驟 2： 在 AWS 中設定資訊安全中心的驗證

有兩種方式可讓資訊安全中心向 AWS 進行驗證：

- **建立資訊安全中心的 IAM 角色** - 這是最安全的方法，建議您使用
- **資訊安全中心的 AWS 使用者** - 如果未啟用 IAM，這將是較不安全的選項

#### <a name="create-an-iam-role-for-security-center"></a>建立資訊安全中心的 IAM 角色
1. 在 Amazon Web Services 主控台的 [安全性、身分識別與合規性] 底下，選取 [IAM]。
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="AWS 服務":::

1. 選取 [角色] 和 [建立角色]。
1. 選取 [另一個 AWS 帳戶]。
1. 輸入下列詳細資料：

    - **帳戶識別碼** - 輸入 Microsoft 帳戶識別碼 (**158177204117**)，如資訊安全中心的 AWS 連接器頁面中所示。
    - **需要外部識別碼** - 應選取
    - **外部識別碼** - 輸入訂用帳戶識別碼，如資訊安全中心的 AWS 連接器頁面中所示 

1. 選取 [下一步] 。
1. 在 [連結權限原則] 區段中，選取下列原則：

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. 選擇性地新增標籤。 為使用者新增標籤並不會影響連線。
1. 選取 [下一步] 。

1. 在 [角色] 清單中，選擇您建立的角色

1. 儲存 Amazon Resource Name (ARN) 以供後續使用。 

#### <a name="create-an-aws-user-for-security-center"></a>建立資訊安全中心的 AWS 使用者 
1. 開啟 [使用者] 索引標籤，然後選取 [新增使用者]。
1. 在 [詳細資料] 步驟中，輸入資訊安全中心的使用者名稱，並確實選取 [程式設計存取] 作為 [AWS 存取類型]。 
1. 選取 [下一步：權限]。
1. 選取 [直接連結現有的原則]，並套用下列原則：
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. 完成時，選取 [下一步:標籤]。 選擇性地新增標籤。 為使用者新增標籤並不會影響連線。
1. 選取 [檢閱]。
1. 儲存自動產生的 **存取金鑰識別碼** 和 **秘密存取金鑰** CSV 檔案，以供後續使用。
1. 檢閱摘要，然後按一下 [建立使用者]。


### <a name="step-3-configure-the-ssm-agent"></a>步驟 3： 設定 SSM 代理程式

需要 AWS Systems Manager，才能將各項 AWS 資源間的工作自動化。 如果您的 EC2 執行個體沒有 SSM 代理程式，請依照 Amazon 的相關指示：

- [在 Windows 執行個體上安裝和設定 SSM 代理程式](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [在 Amazon EC2 Linux 執行個體上安裝和設定 SSM 代理程式](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-complete-azure-arc-prerequisites"></a>步驟 4： 完成 Azure Arc 必要條件
1. 請確定適當的 [Azure 資源提供者](../azure-arc/servers/agent-overview.md#register-azure-resource-providers)：
    - Microsoft.HybridCompute
    - Microsoft.GuestConfiguration

1. 建立服務主體以進行大規模上線。 以您要用於上線之訂用帳戶的 **擁有者** 身分，依照 [建立服務主體以進行大規模上線](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) 中的說明，建立用於 Azure Arc 上線的服務主體。


### <a name="step-5-connect-aws-to-security-center"></a>步驟 5。 將 AWS 連線至資訊安全中心

1. 從資訊安全中心的功能表中，選取 [多重雲端連接器]。
1. 選取 [新增 AWS 帳戶]。
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="資訊安全中心的 [多重雲端連接器] 頁面上的 [新增 AWS 帳戶] 按鈕":::
1. 設定 [AWS 驗證] 索引標籤中的選項：
    1. 輸入連接器的 [顯示名稱]。
    1. 確認訂用帳戶正確無誤。 這是將包含連接器和 AWS 安全性中樞建議的訂用帳戶。
    1. 根據您在[步驟 2. 在 AWS 中設定資訊安全中心的驗證](#step-2-set-up-authentication-for-security-center-in-aws)中選擇的驗證選項：
        - 選取 [假設角色]，然後從 [為資訊安全中心建立 IAM 角色](#create-an-iam-role-for-security-center) 貼上 ARN。
            :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="在 Azure 入口網站中，將 ARN 檔案貼入 AWS 連線精靈的相關欄位":::

            或者

        - 選取 [認證]，並貼上您在 [建立資訊安全中心的 AWS 使用者](#create-an-aws-user-for-security-center)中儲存的 .csv 檔案所包含的 **存取金鑰** 和 **秘密金鑰**。
1. 選取 [下一步] 。
1. 設定 [Azure Arc 設定] 索引標籤中的選項：

    資訊安全中心會探索已連線 AWS 帳戶中的 EC2 執行個體，並使用 SSM 將其上線至 Azure Arc。 

    > [!TIP]
    > 如需支援的作業系統清單，請參閱常見問題中的 [我的 EC2 執行個體支援哪些作業系統？](#what-operating-systems-for-my-ec2-instances-are-supported)。

    1. 在選取的訂用帳戶中，選取探索到的 AWS EC2 將會上線到的 [資源群組] 和 [Azure 區域]。
    1. 輸入 Azure Arc 的 [服務主體識別碼] 和 [服務主體用戶端密碼]，如[建立服務主體以進行大規模上線](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)所說明
    1. 如果機器是透過 Proxy 伺服器連線至網際網路，請指定供機器用來與 Proxy 伺服器通訊的 Proxy 伺服器 IP 位址或名稱與連接埠號碼。 請輸入 ```http://<proxyURL>:<proxyport>``` 格式的值
    1. 選取 [檢閱 + 建立]。

        檢閱摘要資訊

        [標籤] 區段會列出所有會針對每個上線的 EC2 自動建立的 Azure 標籤，及其本身的相關詳細資料，以便在 Azure 中辨識該標籤。 

        您可以在[使用標籤來組織 Azure 資源和管理階層](../azure-resource-manager/management/tag-resources.md)中深入了解 Azure 標籤。

### <a name="step-7-confirmation"></a>步驟 7： 確認

在成功建立連接器並正確設定 AWS 安全性中樞之後：

- 資訊安全中心會掃描環境中的 AWS EC2 執行個體，並將其上線至 Azure Arc，以安裝 Log Analytics 代理程式，並提供威脅防護和安全性建議。 
- ASC 服務每 6 小時會掃描一次新的 AWS EC2 執行個體，並根據設定加以上線。
- AWS CIS 標準會顯示在資訊安全中心的法規合規性儀表板中。
- 若已啟用安全性中樞原則，則在上線完成 5-10 分鐘後，資訊安全中心入口網站和法規合規性儀表板將會顯示建議。
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="資訊安全中心建議頁面中的 AWS 資源和建議":::



## <a name="monitoring-your-aws-resources"></a>監視您的 AWS 資源

如上所示，Azure 資訊安全中心的安全性建議頁面會顯示您的 AWS 資源以及 Azure 和 GCP 資源，而提供真正的多重雲端檢視。

若要依資源類型檢視資源的所有作用中建議，請使用資訊安全中心的資產清查頁面，並篩選到您所需的 AWS 資源類型：

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="資產清查頁面的資源類型篩選器，顯示 AWS 選項"::: 


## <a name="aws-in-security-center-faq"></a>資訊安全中心中的 AWS 常見問題集

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>EC2 執行個體支援哪些作業系統？

支援將 AWS 機器自動上線至 Azure Arc 的作業系統

- Ubuntu 16.04 - 依預設會預先安裝 SSM 代理程式
- Ubuntu 18.04 - 依預設會預先安裝 SSM 代理程式
- Windows 伺服器 - 依預設會預先安裝 SSM 代理程式
- CentOS Linux 7 – SSM 應手動安裝或個別上線
- SUSE Linux Enterprise Server (SLES) 15 (x64) - SSM 應手動安裝或個別上線
- Red Hat Enterprise Linux (RHEL) 7 (x64) - SSM 應手動安裝或個別上線


## <a name="next-steps"></a>後續步驟

連接 AWS 帳戶是 Azure 資訊安全中心提供的多重雲端體驗之一。 如需相關資訊，請參閱下列頁面：

- [將您的 GCP 帳戶連線到 Azure 資訊安全中心](quickstart-onboard-gcp.md)
