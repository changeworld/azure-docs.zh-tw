---
title: 使用復原服務保存庫的診斷設定
description: 說明如何針對 Azure 備份使用舊的和新的診斷事件的文章
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 7abf8873aafeb996476d818376057bfd8732d906
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77583940"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>使用適用於復原服務保存庫的診斷設定

Azure 備份會傳送可收集的診斷事件，並用於分析、警示和報告。 

您可以透過 [Azure 入口網站] 來設定復原服務保存庫的診斷設定，方法是流覽至保存庫，然後按一下 [**診斷設定**] 功能表項目。 按一下 [ **+ 新增診斷設定**] 可讓您將一或多個診斷事件傳送至儲存體帳戶、事件中樞或 Log ANALYTICS （LA）工作區。

![診斷設定分頁](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>適用于 Azure 備份使用者的診斷事件

Azure 備份提供下列診斷事件，其中每一個都會在一組特定的備份相關成品上提供詳細資料：

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

[Azure 備份診斷事件的資料模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

這些事件的資料可以傳送至儲存體帳戶、LA 工作區或事件中樞。 如果您要將此資料傳送至 LA 工作區，您需要在 [**診斷設定**] 畫面中選取**資源特定**的切換（請參閱下列各節中的詳細資訊）。

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>搭配使用診斷設定與 Log Analytics （LA）

與 Azure Log Analytics 藍圖一致，Azure 備份現在可讓您將保存庫診斷資料傳送至專用的 LA 資料表以進行備份。 這些稱為[資源特有的資料表](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)。

若要將您的保存庫診斷資料傳送至 LA：

1.  流覽至您的保存庫，然後按一下 [**診斷設定**]。 按一下 [ **+ 新增診斷設定**]。
2.  為 [診斷] 設定指定名稱。
3.  勾選 [**傳送至 Log analytics** ] 方塊，然後選取 Log Analytics 工作區。
4.  選取切換中的 [資源] [**特定**]，並檢查下列六個事件： **CoreAzureBackup**、 **AddonAzureBackupAlerts**、 **AddonAzureBackupProtectedInstance**、 **AddonAzureBackupJobs**、 **AddonAzureBackupPolicy**和**AddonAzureBackupStorage**。
5.  按一下 [ **儲存**]。

![資源特定模式](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

一旦資料流程入 LA 工作區，就會在您的工作區中建立每個事件的專用資料表。 您可以直接查詢任何這些資料表，並視需要在這些資料表之間執行聯結或等位。

> [!IMPORTANT]
> 上述六個事件（也就是 CoreAzureBackup、AddonAzureBackupAlerts、AddonAzureBackupProtectedInstance、AddonAzureBackupJobs、AddonAzureBackupPolicy 和 AddonAzureBackupStorage）**僅**在資源特定模式中受到支援。 **請注意，如果您嘗試在 Azure 診斷模式中傳送這六個事件的資料，則不會有任何資料流程向 LA 工作區。**

## <a name="legacy-event"></a>舊版事件

傳統上，保存庫的所有備份相關診斷資料都已包含在名為 ' AzureBackupReport ' 的單一事件中。 上面所述的六個事件基本上是 AzureBackupReport 中包含的所有資料的分解。 

目前，如果使用者在此事件中有現有的自訂查詢，例如自訂記錄警示、自訂視覺效果等，我們就會繼續支援 AzureBackupReport 事件以提供回溯相容性。不過，我們建議您針對保存庫中的所有新診斷設定選擇新的事件，因為這可讓資料更容易在記錄查詢中使用、提供更好的架構和其結構的探索性，同時改善兩個內嵌的效能。延遲和查詢時間。 使用 Azure 診斷模式的支援最終會被淘汰，因此選擇新的事件可協助您避免在日後進行複雜的遷移。

您可以選擇為 AzureBackupReport 和六個新的事件建立個別的診斷設定，直到您將所有自訂查詢遷移成使用新資料表中的資料為止。 下圖顯示具有兩個診斷設定的保存庫範例。 第一個設定（名為**Setting1** ）會以 AzureDiagnostics 模式將 AzureBackupReport 事件的資料傳送至 LA 工作區。 第二個設定（名為**Setting2** ）會將六個新 Azure 備份事件的資料傳送至資源特定模式中的 LA 工作區。

![兩個設定](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> 只有 Azure 診斷模式**才**支援 AzureBackupReport 事件。 **請注意，如果您嘗試在資源特定模式下傳送此事件的資料，則不會有任何資料流程向 LA 工作區。**

> [!NOTE]
> 只有當使用者檢查 [**傳送至 Log Analytics**] 時，才會顯示 Azure 診斷/資源特定的切換。 若要將資料傳送至儲存體帳戶或事件中樞，使用者可以直接選取所需的目的地，並檢查任何所需的事件，而不需要任何額外的輸入。 同樣地，建議您不要選擇舊版的事件 AzureBackupReport，再繼續進行。

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>將 Azure Site Recovery 事件傳送至 Log Analytics 的使用者（LA）

Azure 備份和 Azure Site Recovery 事件會從相同的復原服務保存庫傳送。 由於 Azure Site Recovery 目前未上架到資源特定的資料表，因此想要將 Azure Site Recovery 事件傳送至 LA 的使用者，**只**會被導向使用 Azure 診斷模式（請參閱下圖）。 **選擇 Azure Site Recovery 事件的資源特定模式，將會導致所需的資料無法傳送至 LA 工作區**。

![Site Recovery 事件](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

總結上述的細微細節：

* 如果您已經使用 Azure 診斷設定了 LA 診斷，並已在其上撰寫自訂查詢，請將該設定保持不**變**，直到您將查詢遷移到使用新事件的資料為止。
* 如果您也想要上架到新的資料表（如建議），請建立**新**的診斷設定，選擇 [資源] [**特定**]，然後選取六個新事件（如上所指定）。
* 如果您目前正在將 Azure Site Recovery 事件傳送至 LA，**請勿**針對這些事件選擇資源特定模式，否則這些事件的資料將不會流入您的 LA 工作區。 相反地，請建立**額外的診斷設定**，選取 [ **Azure 診斷**]，然後選擇相關的 Azure Site Recovery 事件。

下圖顯示具有三個保存庫診斷設定的使用者範例。 第一個設定（名為**Setting1** ）會將 AzureBackupReport 事件的資料傳送至 AzureDiagnostics 模式的 LA 工作區。 第二個設定（名為**Setting2** ）會將六個新 Azure 備份事件的資料傳送至資源特定模式中的 LA 工作區。 第三個設定（名為**Setting3**）會以 Azure 診斷模式，將 Azure Site Recovery 事件的資料傳送至 LA 工作區。

![三個設定](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>後續步驟

[瞭解診斷事件的 Log Analytics 資料模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
