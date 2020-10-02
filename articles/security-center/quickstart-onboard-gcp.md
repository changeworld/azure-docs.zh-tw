---
title: 將您的 GCP 帳戶連線至 Azure 資訊安全中心
description: 從 Azure 資訊安全中心監視您的 GCP 資源
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 71f1de7b4ff265a5740181a2bb2032f33a83abe3
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448989"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>將您的 GCP 帳戶連線到 Azure 資訊安全中心

由於雲端工作負載通常需要跨越多個雲端平台，因此雲端安全性服務必須執行相同動作。

Azure 資訊安全中心會保護 Azure、Amazon Web Services (AWS) 和 Google Cloud Platform (GCP) 中的工作負載。

將您的 GCP 帳戶上線至資訊安全中心，即可整合 GCP 安全性命令和 Azure 資訊安全中心。 因此，資訊安全中心會在這兩個雲端環境間提供可見度和保護，以支援：

- 安全性設定錯誤的偵測
- 顯示資訊安全中心建議和 GCP 安全性命令中心結果的單一檢視
- 將您的 GCP 資源合併到資訊安全中心的安全分數計算中
- 根據 CIS 標準，將 GCP 安全性命令中心建議合併到資訊安全中心的法規合規性儀表板中

在下面的螢幕擷取畫面中，您可以看到 GCP 專案顯示在資訊安全中心的 [概觀] 儀表板中。

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="資訊安全中心的概觀儀表板上所列的 3 個 GCP 專案" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|預覽|
|定價：|需要[適用於伺服器的 Azure Defender](defender-for-servers-introduction.md)|
|必要的角色和權限：|相關 Azure 訂用帳戶上的**擁有者**或**參與者**|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![否](./media/icons/no-icon.png) 國家/地區/主權 (US Gov、中國 Gov、其他 Gov)|
|||

## <a name="connect-your-gcp-account"></a>連接 GCP 帳戶

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>步驟 1： 使用安全性健康情況分析設定 GCP 安全性命令中心

對於組織中的所有 GCP 專案，您也必須：

1. 使用 [GCP 文件中的指示](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup)，設定 **GCP 安全性命令中心**。
1. 使用 [GCP 文件中的指示](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics)，啟用**安全性健康情況分析**。
1. 確認有資料流向安全性命令中心。

連接您的 GCP 環境以進行安全性設定的指示，會遵循 Google 在使用安全性設定方面的建議。 此整合會運用 Google 安全性命令中心，且會使用可能影響到計費的其他資源。

當您第一次啟用安全性健康情況分析時，資料可能需要幾個小時才可供使用。


### <a name="step-2-enable-gcp-security-command-center-api"></a>步驟 2： 啟用 GCP 安全性命令中心 API

1. 在 Google 的 [雲端主控台 API 程式庫] 中，選取要連線至 Azure 資訊安全中心的專案。
1. 在 API 程式庫中，尋找並選取 [安全性命令中心 API]。
1. 在 API 的頁面上，選取 [啟用]。

深入了解[安全性命令中心 API](https://cloud.google.com/security-command-center/docs/reference/rest/)。


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>步驟 3： 建立安全性設定整合的專用服務帳戶

1. 在 [GCP 主控台] 中，選取要連線至資訊安全中心的專案。
1. 在 [導覽] 功能表中的 [IAM 和管理] 選項底下，選取 [服務帳戶]。
1. 選取 [建立服務帳戶]。
1. 輸入帳戶名稱，然後選取 [建立]。
1. 將 [角色] 指定為 [資訊安全中心管理檢視器]，然後選取 [繼續]。
1. [為使用者授與此服務帳戶的存取權] 區段是選擇性的。 選取 [完成]。
1. 複製已建立之服務帳戶的 [電子郵件值]，並加以儲存供後續使用。
1. 在 [導覽] 功能表中的 [IAM 和管理] 選項底下，選取 [IAM]
    1. 切換至組織層級。
    1. 選取 [新增]。
    1. 在 [新增成員] 欄位中，貼上您先前複製的 [電子郵件值]。
    1. 將 [角色] 指定為 [資訊安全中心管理檢視器]，然後選取 [儲存]。
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="資訊安全中心的概觀儀表板上所列的 3 個 GCP 專案":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>步驟 4： 建立專用服務帳戶的私密金鑰
1. 切換至專案層級。
1. 在 [導覽] 功能表中的 [IAM 和管理] 選項底下，選取 [服務帳戶]。
1. 開啟專用服務帳戶，然後選取 [編輯]。
1. 在 [金鑰] 區段中選取 [新增金鑰]，然後選取 [建立新的金鑰]。
1. 在 [建立私密金鑰] 畫面中選取 [JSON]，然後選取 [建立]。
1. 儲存此 JSON 檔案以供後續使用。


### <a name="step-5-connect-gcp-to-security-center"></a>步驟 5。 將 GCP 連線至資訊安全中心 
1. 從資訊安全中心的功能表中，選取 [雲端連接器]。
1. 選取 [新增 GCP 帳戶]。
1. 在 [上線] 頁面中執行下列動作，然後選取 [下一步]。
    1. 驗證選擇的訂用帳戶。
    1. 在 [顯示名稱] 欄位中，輸入連接器的顯示名稱。
    1. 在 [組織識別碼] 欄位中，輸入您的組織識別碼。 如果您不知道該識別碼，請參閱[建立和管理組織](https://cloud.google.com/resource-manager/docs/creating-managing-organization)。
    1. 在 [私密金鑰] 檔案方塊中，瀏覽至您在[步驟 4. 建立專用服務帳戶的私密金鑰](#step-4-create-a-private-key-for-the-dedicated-service-account)中下載的 JSON 檔案。


### <a name="step-6-confirmation"></a>步驟 6. 確認

在成功建立連接器並正確設定 GCP 安全性命令中心後：

- GCP CIS 標準會顯示在資訊安全中心的法規合規性儀表板中。
- 上線完成 5-10 分鐘後，資訊安全中心入口網站和法規合規性儀表板將會顯示 GCP 資源的安全性建議： :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="資訊安全中心的概觀儀表板上所列的 3 個 GCP 專案":::


## <a name="monitoring-your-gcp-resources"></a>監視您的 GCP 資源

如上所示，Azure 資訊安全中心的安全性建議頁面會顯示您的 GCP 資源以及 Azure 和 AWS 資源，而提供真正的多重雲端檢視。

若要依資源類型檢視資源的所有作用中建議，請使用資訊安全中心的資產清查頁面，並篩選到您所需的 GCP 資源類型：

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="資訊安全中心的概觀儀表板上所列的 3 個 GCP 專案"::: 


## <a name="next-steps"></a>後續步驟

連接 GCP 帳戶是 Azure 資訊安全中心提供的多重雲端體驗之一。 如需相關資訊，請參閱下列頁面：

- [將您的 AWS 帳戶連線到 Azure 資訊安全中心](quickstart-onboard-aws.md)
