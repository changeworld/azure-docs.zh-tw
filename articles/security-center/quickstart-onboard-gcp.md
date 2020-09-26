---
title: 將您的 GCP 帳戶連接到 Azure 資訊安全中心
description: 從 Azure 資訊安全中心監視您的 GCP 資源
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: c4020bc5e96ff35fa2d04a872216e43abbba5323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328304"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>將您的 GCP 帳戶連接到 Azure 資訊安全中心

雲端工作負載通常會跨越多個雲端平臺，因此，雲端安全性服務必須相同。

Azure 資訊安全中心保護 Azure 中的工作負載、Amazon Web Services (AWS) 和 Google Cloud Platform (GCP) 。

將您的 GCP 帳戶上架到「安全中心」、整合 GCP 安全性命令與 Azure 資訊安全中心。 因此，「安全性中心」可讓您在這兩個雲端環境中提供可見度和保護，以提供：

- 偵測安全性錯誤配置
- 顯示安全性中心建議和 GCP 安全性命令中心結果的單一視圖
- 將您的 GCP 資源整合至安全中心的安全分數計算
- 將根據 CIS 標準的 GCP 安全性命令中心建議整合至資訊安全中心的法規合規性儀表板

在下方的螢幕擷取畫面中，您可以看到 GCP 專案顯示在 [總覽] 儀表板中。

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="3個 GCP 專案列在資訊安全中心的總覽儀表板上" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|預覽|
|定價：|需要 [適用于伺服器的 Azure Defender](defender-for-servers-introduction.md)|
|必要的角色和許可權：|相關 Azure 訂用帳戶的**擁有**者或**參與者**|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![否](./media/icons/no-icon.png) 全國/主權 (US Gov、中國 Gov、其他 Gov) |
|||

## <a name="connect-your-gcp-account"></a>連接 GCP 帳戶

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>步驟 1： 使用安全性健康情況分析來設定 GCP Security 命令中心

針對組織中的所有 GCP 專案，您也必須：

1. 使用[GCP 檔中的這些指示](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup)來設定**GCP Security 命令中心**。
1. 使用[GCP 檔中的這些指示](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics)來啟用**安全性健康情況分析**。
1. 確認有資料流程向安全性命令中心。

連接您的 GCP 環境以進行安全性設定的指示遵循 Google 對於取用安全性設定建議的建議。 此整合會利用 Google Security 命令中心，而且會耗用可能會影響您帳單的其他資源。

當您第一次啟用安全性健康情況分析時，可能需要數小時的時間才能使用資料。


### <a name="step-2-enable-gcp-security-command-center-api"></a>步驟 2： 啟用 GCP 安全性命令中心 API

1. 從 Google 的 **Cloud CONSOLE API 程式庫**中，選取您要連接 Azure 資訊安全中心的專案。
1. 在 API 程式庫中，尋找並選取 [ **安全性命令中心 API**]。
1. 在 API 的頁面上，選取 [ **啟用**]。

深入瞭解 [安全性命令中心 API](https://cloud.google.com/security-command-center/docs/reference/rest/)。


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>步驟 3： 建立專用的服務帳戶以進行安全性設定整合

1. 在 **GCP 主控台**中，選取您要連接到「安全性中心」的專案。
1. 在 **導覽功能表**的 [ **IAM & 管理** 選項] 下，選取 [ **服務帳戶**]。
1. 選取 [ **建立服務帳戶**]。
1. 輸入帳戶名稱，然後選取 [ **建立**]。
1. 將 **角色** 指定為「 **安全性中心」系統管理檢視器**，然後選取 [ **繼續**]。
1. [ **授與使用者存取此服務帳戶** ] 區段是選擇性的。 選取 [完成]。
1. 複製已建立之服務帳戶的 **電子郵件值** ，並加以儲存以供稍後使用。
1. 在**導覽功能表**的 [ **iam & 管理**選項] 下，選取 [ **iam** ]
    1. 切換至 [組織] 層級。
    1. 選取 [ **新增**]。
    1. 在 [ **新成員** ] 欄位中，貼上您先前複製的 **電子郵件值** 。
    1. 將角色指定為「 **安全性中心」管理檢視器** ，然後選取 [儲存]。
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="設定相關的 GCP 許可權":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>步驟 4： 建立專用服務帳戶的私密金鑰
1. 切換至專案層級。
1. 在 **導覽功能表**的 [ **IAM & 管理** 選項] 下，選取 [ **服務帳戶**]。
1. 開啟專用服務帳戶，然後選取 [編輯]。
1. 在 [ **金鑰** ] 區段中，選取 [新增 **金鑰** ]，然後 **建立新的金鑰**。
1. 在 [建立私密金鑰] 畫面中，選取 [ **JSON**]，然後選取 [ **建立**]。
1. 儲存此 JSON 檔案以供稍後使用。


### <a name="step-5-connect-gcp-to-security-center"></a>步驟 5。 將 GCP 連線到安全性中心 
1. 在 [安全性中心] 功能表中，選取 [ **雲端連接器**]。
1. 選取 [新增 GCP 帳戶]。
1. 在 [上架] 頁面中，執行下列動作，然後選取 **[下一步]**。
    1. 驗證選擇的訂用帳戶。
    1. 在 [ **顯示名稱** ] 欄位中，輸入連接器的顯示名稱。
    1. 在 [ **組織識別碼** ] 欄位中，輸入您組織的識別碼。 如果您不知道，請參閱 [建立與管理組織](https://cloud.google.com/resource-manager/docs/creating-managing-organization)。
    1. 在 [ **私密金鑰** 檔案] 方塊中，流覽至您在步驟4下載的 JSON 檔案 [。為專用的服務帳戶建立私密金鑰](#step-4-create-a-private-key-for-the-dedicated-service-account)。


### <a name="step-6-confirmation"></a>步驟 6. 確認

當連接器已成功建立，且 GCP 安全性命令中心已正確設定：

- GCP CIS 標準會顯示在安全性中心的 [法規合規性] 儀表板中。
- 您 GCP 資源的安全性建議會出現在「上線完成」之後的「安全性中心」入口網站和法規合規性儀表板5-10 分鐘內：   :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="在 [安全性中心的建議] 頁面中 GCP 資源和建議":::


## <a name="monitoring-your-gcp-resources"></a>監視您的 GCP 資源

如上所示，Azure 資訊安全中心的安全性建議頁面會將您的 GCP 資源連同您的 Azure 和 AWS 資源一起顯示，以進行真正的多重雲端觀點。

若要依資源類型來查看資源的所有作用中建議，請使用 [安全性中心的資產清查] 頁面，並篩選至您感興趣的 GCP 資源類型：

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="顯示 GCP 選項的資產清查頁面的資源類型篩選"::: 


## <a name="next-steps"></a>後續步驟

連接您的 GCP 帳戶是 Azure 資訊安全中心中提供的多雲端體驗的一部分。 如需相關資訊，請參閱下列頁面：

- [將您的 AWS 帳戶連接到 Azure 資訊安全中心](quickstart-onboard-aws.md)
