---
title: 管理 Confluent 雲端-Azure 合作夥伴解決方案
description: 本文說明如何在 Azure 入口網站上管理 Confluent 雲端。 如何設定單一登入、刪除 Confluent 組織，以及取得支援。
ms.service: partner-services
ms.topic: conceptual
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 2d13c183f0b3891fa92b5e2a6534acbf8102e032
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253392"
---
# <a name="manage-the-confluent-cloud-resource"></a>管理 Confluent 雲端資源

本文說明如何在 Azure 上管理 Apache Kafka for Confluent Cloud 的實例。 它會顯示如何設定單一登入 (SSO) 、刪除 Confluent 組織，以及建立支援要求。

## <a name="single-sign-on"></a>單一登入

若要為您的組織執行 SSO，您的租使用者系統管理員可以匯入資源庫應用程式。 此為選用步驟。 如需匯入應用程式的資訊，請參閱 [快速入門：將應用程式新增至 Azure Active Directory (Azure AD) 租使用者](../../active-directory/manage-apps/add-application-portal.md)。 當租使用者系統管理員匯入應用程式時，使用者不需要明確同意允許 Confluent Cloud 入口網站的存取權。

若要啟用 SSO，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至您的 Confluent Cloud 資源實例的 **總覽** 。
1. 選取要 **在 Confluent Cloud 上管理** 的連結。

   :::image type="content" source="media/sso-link.png" alt-text="Confluent portal 單一登入。":::

1. 如果租使用者系統管理員未匯入資源庫應用程式以進行 SSO 同意，請授與許可權和同意。 只有當您第一次存取在 **Confluent Cloud 上管理** 的連結時，才需要執行此步驟。
1. 選擇單一登入 Confluent Cloud 入口網站的 Azure AD 帳戶。
1. 提供同意之後，系統會將您重新導向至 Confluent Cloud 入口網站。

## <a name="set-up-cluster"></a>設定叢集

如需設定叢集的詳細資訊，請參閱 [在 Confluent 雲端 Confluent 檔中建立](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)叢集。

## <a name="delete-confluent-organization"></a>刪除 Confluent 組織

當您不再需要 Confluent 雲端資源時，請刪除 Azure 和 Confluent Cloud 中的資源。

若要刪除 Azure 中的資源：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 **所有資源**，並依 Confluent 雲端資源或 **資源類型** _Confluent 組織_**的名稱進行篩選**。 或者，在 Azure 入口網站中，搜尋資源名稱。
1. 在資源的 **總覽** 中選取 [ **刪除**]。

    :::image type="content" source="media/delete-resources-icon.png" alt-text="資源刪除圖示。":::

1. 若要確認刪除，請輸入資源名稱，然後選取 [ **刪除**]。

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="提示確認刪除資源。":::

若要刪除 Confluent Cloud 上的資源，請參閱 [Confluent 雲端環境的檔-Confluent 檔](https://docs.confluent.io/current/cloud/using/environments.html) 和 [Confluent Cloud 基本概念-Confluent 檔](https://docs.confluent.io/current/cloud/using/cloud-basics.html)。

叢集中的叢集和所有資料都會永久刪除。 如果您的合約包含資料保留子句，Confluent 會將您的資料保留在 [服務條款 Confluent 檔](https://www.confluent.io/confluent-cloud-tos)中指定的時間週期內。

您需支付在刪除叢集時按比例計算的使用量。 永久刪除您的叢集之後，Confluent 會傳送電子郵件確認給您。

## <a name="get-support"></a>取得支援

若要提交支援要求至 Confluent，請聯絡 [Confluent 支援](https://support.confluent.io) 人員，或透過入口網站提交要求，如下所示。

> [!NOTE]
> 若為第一次使用者，請先重設您的密碼，然後再登入 Confluent 支援入口網站。 如果您沒有 Confluent Cloud 的帳戶，請傳送電子郵件給以 `cloud-support@confluent.io` 取得進一步協助。

若要從您的資源提交要求，請遵循下列步驟：

1. 在 [Azure 入口網站中，選取您的 Confluent 組織。
1. 從畫面左側的功能表中，選取 [ **新增支援要求**]。
1. 若要建立支援要求，請選取 **Confluent 入口網站** 的連結。

    :::image type="content" source="media/support-request.png" alt-text="建立支援要求。":::

## <a name="next-steps"></a>後續步驟

如需疑難排解的協助，請參閱 [針對 Confluent 雲端解決方案的 Apache Kafka 進行疑難排解](troubleshoot.md)。
