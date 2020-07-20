---
title: Azure Batch 中的作業和工作
description: 從開發觀點了解作業和工作，以及如何在 Azure Batch 工作流程中使用。
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 5120b76f34e81c2ceeba88767a656b5ee0d40c2f
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955364"
---
# <a name="jobs-and-tasks-in-azure-batch"></a>Azure Batch 中的作業和工作

在 Azure Batch 中，「工作」代表計算單位。 「作業」是這些工作的集合。 以下說明作業和工作的詳細資訊，以及其在 Azure Batch 工作流程中的使用方式。

## <a name="jobs"></a>工作

作業是工作的集合。 作業可管理其工作在集區中的計算節點上執行計算的方式。

作業會指定工作執行所在的[集區](nodes-and-pools.md#pools)。 您可以為每個作業建立新的集區，或將集區使用於許多工作。 您可以針對與作業排程相關聯的每項作業建立集區，或針對與作業排程相關聯的所有作業建立集區。

### <a name="job-priority"></a>作業優先順序

您可以將選擇性的作業優先順序指派給您所建立的作業。 Batch 服務會使用作業的優先順序值，以決定帳戶內的作業排程順序 (這不會與 [排程的作業](#scheduled-jobs)混淆)。 優先順序值可以介於 -1000 到 1000，-1000 表示最低優先順序，1000 表示最高優先順序。 若要更新作業的屬性，呼叫[更新作業的屬性](/rest/api/batchservice/job/update)作業 (Batch REST) 或藉由修改 [CloudJob.Priority](/dotnet/api/microsoft.azure.batch.cloudjob) 屬性 (Batch .NET)。

在相同的帳戶內，較高優先順序的作業具有比低優先順序作業更高的排程優先順序。 一個帳戶中具有較高優先順序值的作業，其排程優先順序並不高於不同帳戶中較低優先順序值的另一項作業。 已在執行中的較低優先順序作業中的工作不會被優先佔用。

不同集區的作業排程是獨立的。 在不同的集區之間，即使作業的優先順序較高，如果其相關聯的集區缺少閒置的節點，並不保證此作業會優先排程。 在相同的集區上，相同優先順序等級的作業有相同的排程機會。

### <a name="job-constraints"></a>作業條件約束

您可以使用作業條件約束，為作業指定特定的限制：

- 您可以設定 **最大時鐘時間**，因此如果作業的執行時間超過指定的最大時鐘時間，則會終止此作業和所有相關聯的工作。
- 您可以指定**工作重試次數上限**作為條件約束，包括要「一律」重試工作還是「決不」重試工作。 重試工作表示如果工作失敗，就會重新排入佇列以再次執行。

### <a name="job-manager-tasks-and-automatic-termination"></a>作業管理員工作及自動終止

用戶端應用程式可以在作業中新增工作，或者您可以指定 [作業管理員工作](#job-manager-task)。 作業管理員工作包含為作業建立必要工作所需的資訊，而作業管理員工作會在集區內的其中一個計算節點上執行。 Batch 會特別處理作業管理員工作，此工作會在作業建立後立即排入佇列，且在失敗時會重新啟動。 由[作業排程](#scheduled-jobs)建立的作業需要有作業管理員工作，因為其為在作業具現化之前唯一可定義工作的方法。

根據預設，作業內的所有工作都完成時，作業仍會保持作用中狀態。 您可以變更此行為，讓作業在其中的所有工作完成時自動終止。 將作業的 **onAllTasksComplete** 屬性 (在 Batch .NET 中為 [OnAllTasksComplete](/dotnet/api/microsoft.azure.batch.cloudjob)) 設定為 terminatejob，以在作業的所有工作處於已完成狀態時，自動終止該作業。

Batch 服務會將「沒有」工作的作業視為其所有工作都已完成。 因此，這個選項最常搭配 [作業管理員工作](#job-manager-task)使用。 如果您想要使用自動作業終止，而不透過作業管理員，您一開始就應該將新作業的 **onAllTasksComplete** 屬性設定為 noaction，而只在您完成將工作新增至作業之後，將它設定為 terminatejob。

### <a name="scheduled-jobs"></a>Scheduled jobs

[作業排程](/rest/api/batchservice/jobschedule)可讓您在 Batch 服務內建立週期性作業。 作業排程會指定何時要執行作業，並且包含要執行之作業的規格。 您可以指定排程的持續時間 (排程的有效時間和生效時間)，以及在排程期間建立作業的頻率。

## <a name="tasks"></a>工作

工作是與作業相關聯的計算單位。 工作是在節點上執行。 工作會指派給節點以便執行，或排入佇列直到節點變成可用為止。 簡言之，工作會在計算節點上執行一或多個程式或指令碼，以執行您需要完成的工作。

建立工作時，您可以指定︰

- 工作的 **命令列**。 這是可在計算節點上執行應用程式或指令碼的命令列。

    請務必注意，命令列並非在 Shell 底下執行。 因此，它無法以原生方式利用 Shell 功能，例如[環境變數](#environment-settings-for-tasks)擴充功能 (這包括 `PATH`)。 若要利用這類功能，您必須在命令列中叫用此 Shell，例如，藉由在 Windows 節點上啟動 `cmd.exe` 或在 Linux 上啟動 `/bin/sh`︰

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    如果您的工作需要執行不在節點的 `PATH` 中的應用程式或指令碼，或參考環境變數，請在工作命令列中明確地叫用 Shell。
- **資源檔** 。 在工作的命令列執行之前，這些檔案會自動從 Azure 儲存體帳戶中的 Blob 儲存體複製到節點。 如需詳細資訊，請參閱[啟動工作](#start-task)及[檔案和目錄](files-and-directories.md)。
- 應用程式所需的 **環境變數** 。 如需詳細資訊，請參閱[工作的環境設定](#environment-settings-for-tasks)。
- 執行工作所應根據的 **條件約束** 。 例如，條件約束包括允許執行工作的時間上限、失敗的工作應該重試的次數上限，以及檔案保留在工作的工作目錄中的時間上限。
- **Application packages** 。 [應用程式封裝](batch-application-packages.md) 會提供您的工作執行之應用程式的簡化部署和版本控制。 在共用集區的環境中，工作層級的應用程式封裝特別有用：不同的作業會在一個集區上執行，而某項作業完成時並不會刪除該集區。 如果您的作業擁有的工作少於集區中的節點，工作應用程式套件可以減少資料傳輸，因為您的應用程式只會部署至執行工作的節點。
- Docker 中樞或私人登錄中的**容器映像**參考和其他設定，用來建立 Docker 容器，工作會在其中的節點上執行。 如果集區是使用容器設定進行設定，您只能指定此資訊。

> [!NOTE]
> 工作的最長存留期 (從它新增至作業到完成時) 為 180 天。 已完成的工作會保留 7 天；無法存取未在最長存留期內完成之工作的資料。

除了您定義在節點上執行計算的工作以外，Batch 服務還提供數個特殊工作：

- [啟動工作](#start-task)
- [作業管理員工作](#job-manager-task)
- [作業準備和作業釋放工作](#job-preparation-and-release-tasks)
- [多重執行個體工作](#multi-instance-task)
- [作業相依性](#task-dependencies)

### <a name="start-task"></a>啟動工作

藉由建立啟動工作與集區的關聯，您可以準備其節點的作業環境。 例如，您可以執行下列動作：安裝您的工作執行的應用程式或啟動背景程序。 啟動工作會在節點每次啟動時執行，只要其保留在集區中即可。 這包括當節點第一次新增至集區，以及重新開機或重新安裝映像時。

啟動工作的主要優點，是其中可包含設定計算節點和安裝工作執行所需的應用程式時必須用到的所有資訊。 因此，增加集區中的節點數目如同指定新目標節點計數一樣簡單。 啟動工作會為 Batch 服務提供設定新節點，並讓其準備好接受工作所需的資訊。

如同任何 Azure Batch 工作，除了要執行的命令列以外，您還可以指定 [Azure 儲存體](../storage/index.yml)中的資源檔清單。 Batch 服務會先從 Azure 儲存體將資源檔案複製到節點，然後再執行命令列。 對於集區啟動工作，檔案清單通常包含工作應用程式與其相依項目。

但啟動工作也可以包含在計算節點上執行的所有工作將會使用的參考資料。 例如，啟動工作的命令列可執行 `robocopy` 作業，將應用程式檔案 (已指定為資源檔案並下載至節點) 從啟動工作的[工作目錄](files-and-directories.md)複製到**共用**資料夾，然後執行 MSI 或 `setup.exe`。

通常 Batch 服務最好能夠等待開始工作完成，然後再考慮將工作指派給節點，但您可以設定此行為。

如果計算節點上的啟動工作失敗，則會更新節點的狀態以反映失敗，且不會指派任何工作給該節點。 如果從儲存體複製啟動工作的資源檔案時發生問題，或由其命令列執行的程序傳回非零的結束程式碼，啟動工作可能會失敗。

如果您新增或更新現有集區的啟動工作，您必須重新啟動其計算節點，啟動工作才會套用至節點。

>[!NOTE]
> Batch 會限制啟動工作的總大小，包括資源檔案和環境變數。 如果您需要減少啟動工作的大小，您可以使用下列其中一個方法：
>
> 1. 您可以使用應用程式套件，將應用程式或資料分散於 Batch 集區中的每個節點。 如需應用程式套件的詳細資訊，請參閱[使用 Batch 應用程式套件將應用程式部署至計算節點](batch-application-packages.md)。
> 2. 您可以手動建立已壓縮的封存檔，其中包含您的應用程式檔案。 將已壓縮的封存檔以 blob 形式上傳至 Azure 儲存體。 指定已壓縮的封存檔作為您啟動工作的資源檔。 執行啟動工作的命令列之前，請從命令列將封存檔解壓縮。 
>
>    若要將封存檔解壓縮，您可以使用您選擇的封存工具。 您必須包含用來將封存檔解壓縮為啟動工作之資源檔的工具。

### <a name="job-manager-task"></a>作業管理員工作

您通常會使用作業管理員工作來控制和/或監視作業執行。 例如，作業管理員工作通常會用來建立及提交作業的工作、決定其他要執行的工作，以及判斷工作何時完成。

但作業管理員的工作並不限定於這些活動。 其為功能完備的工作，可執行作業所需的任何動作。 比方說，作業管理員工作可以下載指定為參數的檔案、分析該檔案的內容，並根據這些內容提交其他工作。

作業管理員工作會在所有其他工作之前啟動。 它可提供下列功能：

- 建立作業時由 Batch 服務自動提交為工作。
- 排程在作業中的其他工作之前執行。
- 縮小集區時，相關聯的節點最後才會從集區中移除。
- 此終止可能完全取決於作業中的所有工作終止。
- 需要重新啟動時，作業管理員工作有最高的優先順序。 如果找不到閒置的節點，Batch 服務可能會終止集區中正在執行的其中另一個工作，以便挪出空間供作業管理員工作執行。
- 一個作業中的作業管理員工作的優先順序不會高於其他作業的工作。 不同作業之間，只注重作業層級優先順序。

### <a name="job-preparation-and-release-tasks"></a>作業準備和作業釋放工作

Batch 會提供作業準備工作，以進行前置作業的執行設定，以及後置作業的維護或清除。

在執行任何其他作業工作之前，會在排定要執行工作的所有計算節點上執行作業準備工作。 例如，您可以使用作業準備工作來複製所有工作所共用、但對作業是唯一的資料。

作業完成後，系統會對集區中至少執行了一個工作的每個節點上執行作業釋放工作。 例如，您可以使用作業釋放工作來刪除作業準備工作所複製的資料，或是壓縮並上傳診斷記錄資料。

作業準備和釋放工作可讓您指定要在叫用工作時執行的命令列。 它們會提供許多功能，例如檔案下載、提升權限的執行、自訂環境變數、最大執行持續時間、重試計數、檔案保留時間。

如需關於作業準備和釋放工作的詳細資訊，請參閱 [在 Azure Batch 計算節點上執行準備和完成的工作](batch-job-prep-release.md)。

### <a name="multi-instance-task"></a>多重執行個體工作

[多重執行個體工作](batch-mpi.md) 是設定為同時在多個計算節點上執行的工作。 您可以利用多重執行個體工作來啟用高效能計算案例，例如需要一組配置在一起以處理單一工作負載 (像是訊息傳遞介面 (MPI)) 的計算節點。

如需在 Batch 中使用 Batch .NET 程式庫執行 MPI 作業的詳細討論，請參閱 [在 Azure Batch 中使用多個執行個體的工作執行訊息傳遞介面 (MPI) 應用程式](batch-mpi.md)。

### <a name="task-dependencies"></a>作業相依性

[工作相依性](batch-task-dependencies.md)正如其名，可讓您在執行某個工作之前，指定該工作相依於其他工作。 此功能提供下列情況的支援：「下游」工作取用「上游」工作的輸出，或當上游工作執行下游工作所需的某種初始化時。

若要使用這項功能，您必須先在 Batch 作業上[啟用工作相依性](batch-task-dependencies.md#enable-task-dependencies
)。 然後，針對每個相依於另一個工作 (或其他許多工作) 的工作，指定該工作相依的工作。

利用工作相依性，您可以設定如下所示的案例︰

- taskB 相依於 taskA (直到 taskA 完成，才會開始執行 taskB)。
- taskC 同時相依於 taskA 和 taskB。
- taskD 在執行前相依於某個範圍的工作，例如工作 1 至 10。

如需詳細資訊，請查看 [Azure Batch 中的工作相依性](batch-task-dependencies.md)和 [azure-batch-samples](https://github.com/Azure-Samples/azure-batch-samples) GitHub 存放庫中的 [TaskDependencies](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) 程式碼範例。

### <a name="environment-settings-for-tasks"></a>工作的環境設定

Batch 服務所執行的每個工作都可以存取在計算節點上設定的環境變數。 這包括 Batch 服務所定義 ([服務定義的](./batch-compute-node-environment-variables.md)) 的環境變數，以及您可以為工作定義的自訂環境變數。 工作所執行的應用程式和指令碼，可以在執行期間存取這些環境變數。

您可以填入這些實體的「環境設定」  屬性，以在工作或作業層級設定自訂環境變數。 例如，請參閱[將工作新增至作業](/rest/api/batchservice/task/add?)作業 (Batch REST API) 或 Batch .NET 中的 [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) 和 [CloudJob.CommonEnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudjob) 屬性。

您的用戶端應用程式或服務可藉由使用[取得工作的相關資訊](/rest/api/batchservice/task/get)作業 (Batch REST) 或存取 [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) 屬性 (Batch .NET)，取得工作的環境變數 (服務定義和自訂)。 在計算節點上執行的程序可以在節點上存取這些和其他環境變數，例如，藉由使用熟悉的 `%VARIABLE_NAME%` (Windows) 或 `$VARIABLE_NAME` (Linux) 語法。

您可以在[計算節點環境變數](batch-compute-node-environment-variables.md)中找到所有服務定義的環境變數完整清單。

## <a name="next-steps"></a>後續步驟

- 了解[檔案和目錄](files-and-directories.md)。
