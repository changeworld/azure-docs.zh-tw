---
title: 將您的 AWS 帳戶連接到 Azure 資訊安全中心
description: 從 Azure 資訊安全中心監視您的 AWS 資源
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ee1b8fe6ed97f3b71cda418cce9e432f7c045447
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934726"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>將您的 AWS 帳戶連接到 Azure 資訊安全中心

雲端工作負載通常會跨越多個雲端平臺，因此，雲端安全性服務必須相同。

Azure 資訊安全中心保護 Azure 中的工作負載、Amazon Web Services (AWS) 和 Google Cloud Platform (GCP) 。

將您的 AWS 帳戶上架到「安全中心」，整合 AWS 安全性中樞與 Azure 資訊安全中心。 因此，「安全性中心」可讓您在這兩個雲端環境中提供可見度和保護，以提供：

-  (安全中心的自動代理程式布建會使用 [Azure Arc](../azure-arc/servers/overview.md) ，將 Log Analytics 代理程式部署到您的 AWS 實例) 
- 原則管理
- 弱點管理
- 內嵌端點偵測和回應 (EDR) 
- 偵測安全性錯誤配置
- 顯示安全性中心建議和 AWS 安全性中樞結果的單一視圖
- 將您的 AWS 資源整合至安全中心的安全分數計算
- AWS 資源的法規合規性評估

在下方的螢幕擷取畫面中，您可以看到 AWS 帳戶顯示在安全性中心的總覽儀表板中。

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="3個 GCP 專案列在資訊安全中心的總覽儀表板上" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|預覽|
|定價：|需要 [適用于伺服器的 Azure Defender](defender-for-servers-introduction.md)|
|必要的角色和許可權：|相關 Azure 訂用帳戶的**擁有**者或**參與者**|
|雲端：|![Yes](./media/icons/yes-icon.png) 商業雲端<br>![No](./media/icons/no-icon.png) 全國/主權 (US Gov、中國 Gov、其他 Gov) |
|||



## <a name="connect-your-aws-account"></a>連接 AWS 帳戶

### <a name="step-1-set-up-aws-security-hub"></a>步驟 1： 設定 AWS 安全性中樞：

1. 若要查看多個區域的安全性建議，請針對每個相關區域重複執行下列步驟。

    > [!IMPORTANT]
    > 如果您使用 AWS 主帳戶，請重複下列三個步驟，以在所有相關區域中設定主帳戶和所有連線的成員帳戶。

    1. 啟用 [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html)。
    1. 啟用 [AWS 安全性中樞](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html)。
    1. 確認有資料流程向安全性中樞。

        當您第一次啟用安全性中樞時，可能需要數小時的時間才能使用資料。

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>步驟 2： 在 AWS 中設定適用于安全性中心的驗證

有兩種方式可讓安全中心向 AWS 進行驗證：

- **建立安全性中心的 IAM 角色** -這是最安全的方法，建議使用
- **AWS Security Center 的使用者** -如果您沒有啟用 IAM，則較不安全的選項

#### <a name="create-an-iam-role-for-security-center"></a>為安全性中心建立 IAM 角色
1. 從 Amazon Web Services 主控台的 [ **安全性]、[識別 & 合規性**] 下，選取 [ **IAM**]。
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="AWS 服務":::

1. 選取 [ **角色** ] 並 **建立角色**。
1. 選取 **另一個 AWS 帳戶**。
1. 輸入下列詳細資料：

    - **帳戶識別碼** -輸入 MICROSOFT 帳戶識別碼 (**158177204117**) ，如「安全性中心」的 [AWS 連接器] 頁面所示。
    - **需要外部識別碼** -應選取
    - **外部識別碼** -輸入訂用帳戶識別碼，如 [安全性中心] 中的 [AWS 連接器] 頁面所示 

1. 選取 [下一步]  。
1. 在 [ **附加許可權原則** ] 區段中，選取下列原則：

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. 選擇性地新增標記。 將標記新增至使用者不會影響連接。
1. 選取 [下一步]  。

1. 在 [角色] 清單中，選擇您建立的角色

1. 將 Amazon 資源名稱儲存 (ARN) 以供稍後之用。 

#### <a name="create-an-aws-user-for-security-center"></a>為安全性中心建立 AWS 使用者 
1. 開啟 [ **使用者** ] 索引標籤，然後選取 [ **新增使用者**]。
1. 在 [ **詳細資料** ] 步驟中，輸入 [資訊安全中心] 的使用者名稱，並確定您選取 [以程式 **設計方式存取** AWS] 存取類型。 
1. 選取 [下一步：權限]。
1. 選取 [ **直接附加現有的原則** ]，並套用下列原則：
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. 完成時，選取 [下一步:標籤]。 選擇性地新增標記。 將標記新增至使用者不會影響連接。
1. 選取 [ **審核**]。
1. 儲存自動產生的 **存取金鑰識別碼** 和 **密碼存取金鑰** CSV 檔案，以供稍後使用。
1. 查看摘要，然後按一下 [ **建立使用者**]。


### <a name="step-3-configure-the-ssm-agent"></a>步驟 3： 設定 SSM 代理程式

您必須要有 AWS Systems Manager，才能將整個 AWS 資源的工作自動化。 如果您的 EC2 實例沒有 SSM 代理程式，請遵循 Amazon 的相關指示：

- [在 Windows 實例上安裝和設定 SSM 代理程式](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [在 Amazon EC2 Linux 實例上安裝及設定 SSM 代理程式](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-create-a-service-principal-for-onboarding-at-scale"></a>步驟 4： 建立服務主體以進行大規模上線

以您想要用來登入的訂用帳戶**擁有**者身分，建立 Azure Arc 上線的服務主體，如[建立大規模上線的服務主體](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)中所述


### <a name="step-5-connect-aws-to-security-center"></a>步驟 5。 將 AWS 連線到安全性中心

1. 在 [安全性中心] 功能表中，選取 [ **多個雲端連接器**]。
1. 選取 [ **新增 AWS 帳戶**]。
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="資訊安全中心的多重雲端連接器頁面上的 [新增 AWS 帳戶] 按鈕":::
1. 在 [ **AWS 驗證** ] 索引標籤中設定選項：
    1. 輸入連接器的 **顯示名稱** 。
    1. 確認訂用帳戶正確無誤。 這是將包含連接器和 AWS 安全性中樞建議的訂用帳戶。
    1. 取決於您在步驟2中選擇的驗證選項 [。在 AWS 中設定適用于安全性中心的驗證](#step-2-set-up-authentication-for-security-center-in-aws)：
        - 在:::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Azure 入口網站中，選取 ["::: **假設角色**] 並貼上 ARN，從 [[建立安全性中心的 IAM 角色](#create-an-iam-role-for-security-center)] 中，將 ARN 檔案貼到 [AWS 連線] 的相關欄位

            或者

        - 選取 [**認證**]，然後貼上您在 [[為安全性中心建立 AWS 使用者](#create-an-aws-user-for-security-center)] 中儲存的 .csv 檔案中的**存取金鑰**和**秘密金鑰**。
1. 選取 [下一步]  。
1. 設定 [ **Azure Arc** 設定] 索引標籤中的選項：

    「安全性中心」會探索已連線 AWS 帳戶中的 EC2 實例，並使用 SSM 將其上架至 Azure Arc。 

    > [!TIP]
    > 支援的作業系統清單位於下列常見問題中。

    1. 選取所選訂用帳戶中所探索到的 AWS EC2s 將上線的 **資源群組** 和 **Azure 區域** 。
    1. 輸入 Azure Arc 的 **服務主體識別碼** 和 **服務主體用戶端密碼** ，如下所述 [：建立大規模上架的服務主體](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)
    1. 如果電腦是透過 proxy 伺服器連線到網際網路，請指定 proxy 伺服器的 IP 位址，或電腦用來與 proxy 伺服器通訊的名稱和埠號碼。 以格式輸入值 ```http://<proxyURL>:<proxyport>```
    1. 選取 [檢閱 + 建立]。

        查看摘要資訊

        [標籤] 區段會列出所有將為每個上線 EC2 自動建立的 Azure 標籤，其中包含其專屬的相關詳細資料，以便在 Azure 中輕鬆辨識。 

        深入瞭解使用標記的 Azure 標記 [，以組織您的 azure 資源和管理](../azure-resource-manager/management/tag-resources.md)階層。

### <a name="step-7-confirmation"></a>步驟 7： 確認

當連接器成功建立，且已正確設定 AWS 安全性中樞時：

- 「安全性中心」會掃描環境中的 AWS EC2 實例，並將其上架到 Azure Arc，以便安裝 Log Analytics 代理程式，並提供威脅防護和安全性建議。 
- ASC 服務會每隔6小時掃描新的 AWS EC2 實例，並根據設定加以將上線。
- AWS CIS 標準會顯示在安全性中心的 [法規合規性] 儀表板中。
- 如果啟用了安全性中樞原則，建議將會出現在 [上線] 入口網站中，以及在上架完成之後的 [法規合規性儀表板] 5-10 分鐘。
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="在 Security Center 的建議頁面中 AWS 資源和建議":::



## <a name="monitoring-your-aws-resources"></a>監視您的 AWS 資源

如上所示，Azure 資訊安全中心的安全性建議頁面會將您的 AWS 資源連同您的 Azure 和 GCP 資源一起顯示，以進行真正的多重雲端觀點。

若要依資源類型來查看資源的所有作用中建議，請使用 [安全性中心的資產清查] 頁面，並篩選至您感興趣的 AWS 資源類型：

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="顯示 AWS 選項的資產清查頁面的資源類型篩選"::: 


## <a name="aws-in-security-center-faq"></a>AWS in Security Center 常見問題

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>我的 EC2 實例支援哪些作業系統？

針對 AWS 機器自動上線 Azure Arc 的支援 OS

- Ubuntu 16.04-依預設會預先安裝 SSM 代理程式
- Ubuntu 18.04-依預設會預先安裝 SSM 代理程式
- Windows server-依預設會預先安裝 SSM 代理程式
- CentOS Linux 7 – SSM 應手動安裝，或將其上架
- SUSE Linux Enterprise Server (SLES) 15 (x64) -SSM 應手動安裝或分別上線
- Red Hat Enterprise Linux (RHEL) 7 (x64) -SSM 應手動安裝或分別上線


## <a name="next-steps"></a>下一步

連接您的 AWS 帳戶是 Azure 資訊安全中心中提供的多雲端體驗的一部分。 如需相關資訊，請參閱下列頁面：

- [將您的 GCP 帳戶連接到 Azure 資訊安全中心](quickstart-onboard-gcp.md)
