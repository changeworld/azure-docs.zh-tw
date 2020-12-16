---
title: Azure 資訊安全中心中的警示驗證 |Microsoft Docs
description: 瞭解如何驗證已正確設定您的安全性警示 Azure 資訊安全中心
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2020
ms.author: memildin
ms.openlocfilehash: 598c13b0434a364e73471b53c82663b94fb42f4e
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560096"
---
# <a name="alert-validation-in-azure-security-center"></a>Azure 資訊安全中心中的警示驗證
這份文件可協助您了解如何驗證您的系統是否已針對 Azure 資訊安全中心警示正確設定。

## <a name="what-are-security-alerts"></a>什麼是安全性警示：
警示是當資訊安全中心偵測到您資源受到威脅時，所產生的通知。 它會排定優先順序並列出警示，以及快速調查問題所需的資訊。 資訊安全中心也提供如何修復攻擊的建議。
如需詳細資訊，請參閱資訊 [安全中心的安全性警示](security-center-alerts-overview.md) ，以及 [管理和回應安全性警示](security-center-managing-and-responding-alerts.md)


## <a name="generate-sample-azure-defender-alerts"></a>產生 Azure Defender 的範例警示

如果您使用 [ [管理及回應 Azure 資訊安全中心中的安全性警示](security-center-managing-and-responding-alerts.md)] 中所述的新預覽警示體驗，您可以在 Azure 入口網站的 [安全性警示] 頁面中，按幾下滑鼠來建立範例警示。

使用範例警示：

- 評估 Azure Defender 的價值和功能
- 驗證您針對安全性警示所做的任何設定 (例如 SIEM 整合、工作流程自動化，以及電子郵件通知) 

> [!NOTE]
> 此程式需要新的 (預覽) [ **安全性警示** ] 頁面頂端的橫幅提供的警示體驗。
>
> :::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="連結至新的預覽警示體驗的橫幅":::

若要建立範例警示：

1. 從 [警示] 頁面上的工具列中，選取 [ **建立範例警示**]。 
1. 選取訂用帳戶。
1. 選取您要查看其警示的相關 Azure Defender 方案。 
1. 選取 [ **建立範例警示**]。

    :::image type="content" source="media/security-center-alert-validation/create-sample-alerts-procedures.png" alt-text="在 Azure 資訊安全中心中建立範例警示的步驟":::
    
    此時會出現通知，讓您知道正在建立範例警示：

    :::image type="content" source="media/security-center-alert-validation/notification-sample-alerts-creation.png" alt-text="產生範例警示的通知。":::

    幾分鐘後，警示就會出現在 [安全性警示] 頁面中。 它們也會出現在您已設定的其他地方，以接收 Azure 資訊安全中心的安全性警示 (連線 Siem、電子郵件通知等) 。

    :::image type="content" source="media/security-center-alert-validation/sample-alerts.png" alt-text="安全性警示清單中的範例警示":::

    > [!TIP]
    > 警示適用于模擬的資源。

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>在您的 Azure Vm (Windows) 上模擬警示 <a name="validate-windows"></a>

在您的電腦上安裝資訊安全中心代理程式之後，請在您想要成為警示受攻擊資源的電腦上，遵循下列步驟：

1. 將可執行檔 (例如 **calc.exe**) 複製到電腦的桌面或其他方便的目錄，並將它重新命名為 **ASC_AlertTest_662jfi039N.exe**。
1. 開啟命令提示字元，並使用引數 (只) 假引數名稱的引數來執行此檔案，例如： ```ASC_AlertTest_662jfi039N.exe -foo```
1. 等待 5 到 10 分鐘，然後開啟安全性中心警示。 應該會出現警示。

> [!NOTE]
> 查看 Windows 的這項測試警示時，請確定 **已啟用欄位引數審核** **。** 如果為 **false**，則您必須啟用命令列引數的審核。 若要將其啟用，請使用下列命令：
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>在您的 Azure Vm (Linux) 上模擬警示 <a name="validate-linux"></a>

在您的電腦上安裝資訊安全中心代理程式之後，請在您想要成為警示受攻擊資源的電腦上，遵循下列步驟：
1. 將可執行檔案複製到方便存取的位置，並將它重新命名為 **./asc_alerttest_662jfi039n**，例如：

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. 開啟命令提示字元，然後執行此檔案：

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. 等待 5 到 10 分鐘，然後開啟安全性中心警示。 應該會出現警示。


## <a name="simulate-alerts-on-kubernetes"></a>模擬 Kubernetes 上的警示 <a name="validate-kubernetes"></a>

如果您已整合 Azure Kubernetes Service 與安全性中心，您可以使用下列 kubectl 命令來測試您的警示是否正常運作：

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

如需有關如何防禦 Kubernetes 節點和叢集的詳細資訊，請參閱 [Azure Defender For Kubernetes 簡介](defender-for-kubernetes-introduction.md)

## <a name="next-steps"></a>後續步驟
本文介紹警示驗證程序。 現在，您已熟悉此驗證，請嘗試下列文章︰

* [驗證 Azure 資訊安全中心的 Azure Key Vault 威脅偵測](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [管理和回應 Azure 資訊安全中心中的安全性警示](security-center-managing-and-responding-alerts.md) -瞭解如何管理警示，以及回應安全中心的安全性事件。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) - 了解如何監視 Azure 資源的健全狀況。
* [瞭解 Azure 資訊安全中心中的安全性警示](./security-center-alerts-overview.md) -瞭解不同類型的安全性警示。