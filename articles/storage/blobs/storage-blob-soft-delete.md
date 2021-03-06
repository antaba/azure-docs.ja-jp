---
title: Azure Storage Blob の論理的な削除 (プレビュー) | Microsoft Docs
description: Azure Storage では、BLOB オブジェクトの論理的な削除 (プレビュー) が提供されるようになり、アプリケーションまたは他のストレージ アカウント ユーザーによってデータが誤って変更または削除されたときに、いっそう簡単にデータを復旧できるようになりました。
services: storage
author: MichaelHauss
manager: vamshik
ms.service: storage
ms.topic: article
ms.date: 03/21/2018
ms.author: mihauss
ms.openlocfilehash: 0e728f9f9754d76d893b12309bb52201d772efbf
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2018
---
# <a name="soft-delete-for-azure-storage-blobs-preview"></a>Azure Storage Blob の論理的な削除 (プレビュー)

## <a name="overview"></a>概要

Azure Storage では、BLOB オブジェクトの論理的な削除 (プレビュー) が提供されるようになり、アプリケーションまたは他のストレージ アカウント ユーザーによってデータが誤って変更または削除されたときに、いっそう簡単にデータを復旧できるようになりました。

## <a name="how-does-it-work"></a>それはどのように機能しますか?

論理的な削除機能を有効にすると、BLOB または BLOB のスナップショットを保存し、それらが削除されたときに復旧することができます。 この保護は、上書きの結果として消去された BLOB データにまで拡張されます。

削除されたデータは、完全に消去されるのではなく、論理的に削除された状態に移行します。 論理的な削除が有効になっている状態で、データを上書きすると、上書きされたデータの状態を保存するために、論理的に削除されたスナップショットが生成されます。 論理的に削除されたオブジェクトは、明示的に一覧表示されない限り表示されません。
論理的に削除されたデータが完全に有効期限切れになって復旧できなくなるまでの時間を構成することができます。

論理的な削除には下位互換性があります。この機能が提供する保護を利用するために、アプリケーションを変更する必要はありません。 ただし、[データ復旧](#recovery)には新しい **Undelete Blob** API が導入されています。

> [!NOTE]
> パブリック プレビュー期間中は、スナップショットのある BLOB に対して BLOB 層の設定を呼び出すことはできません。
論理的な削除では、上書きからデータを保護するためにスナップショットが生成されます。 現在、スナップショットがあっても BLOB 層を設定できるようにする作業が行われています。

### <a name="configuration-settings"></a>構成設定

新しいアカウントを作成したとき、論理的な削除は既定で無効に設定されます。 既存のストレージ アカウントについても、論理的な削除の既定の設定はオフです。 ストレージ アカウントの有効期間中であればいつでも、機能のオンとオフを切り替えることができます。

以前に機能を有効にしたときに論理的に削除されたデータが保存されている場合は、機能が無効になっていても、論理的に削除されたデータにアクセスして復旧できます。 論理的な削除を有効にするときは、リテンション期間も構成する必要があります。

リテンション期間は、論理的に削除されたデータが保存されていて復旧に利用できる期間を示します。 明示的に削除された BLOB および BLOB のスナップショットの場合は、データが削除された時点でリテンション期間のクロックが開始します。 データ上書き時に論理的な削除機能によって生成される論理的に削除されたスナップショットの場合は、スナップショットが生成された時点でクロックが開始します。 現在、論理的に削除されたデータを保持できる期間は 1 ～ 365 日です。

論理的な削除のリテンション期間はいつでも変更できます。 更新されたリテンション期間は、それ以降に削除されたデータにのみ適用されます。 以前に削除されたデータは、データが削除されたときに構成されていたリテンション期間に基づいて期限が切れます。

### <a name="saving-deleted-data"></a>削除されたデータの保存

論理的な削除は、BLOB または BLOB のスナップショットが削除または上書きされる多くの場合に、データを保持します。

**Put Blob**、**Put Block**、**Put Block List**、または **Copy Blob** を使って BLOB が上書きされると、書き込み操作前の BLOB の状態のスナップショットが自動的に生成されます。 このスナップショットは論理的に削除されたスナップショットであり、論理的に削除されたオブジェクトが明示的に一覧表示されない限り表示されません。 論理的に削除されたオブジェクトを一覧表示する方法については、「[復旧](#recovery)」セクションをご覧ください。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

"*論理的に削除されたデータはグレーで、アクティブなデータは青で示されています。上にあるデータほど書き込まれたのが古いデータです。B0 が B1 で上書きされると、B0 の論理的に削除されたスナップショットが生成されます。B1 が B2 で上書きされると、B1 の論理的に削除されたスナップショットが生成されます。*"

> [!NOTE]
> 論理的な削除がコピー操作の上書き保護を提供するのは、コピー先 BLOB のアカウントで機能が有効になっている場合だけです。

> [!NOTE]
> アーカイブ層の BLOB に対しては、論理的な削除の上書き保護は提供されません。 アーカイブ内の BLOB が任意の層の新しい BLOB で上書きされる場合、上書きされた BLOB は完全に有効期限が切れます。

スナップショットで **Delete Blob** が呼び出されると、そのスナップショットは論理的に削除されたものとしてマークされます。 新しいスナップショットは生成されません。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

"*論理的に削除されたデータはグレーで、アクティブなデータは青で示されています。上にあるデータほど書き込まれたのが古いデータです。**Snapshot Blob** が呼び出されると、B0 はスナップショットになり、B1 はアクティブな状態の BLOB です。B0 スナップショットが削除されると、論理的に削除済みとしてマークされます。*"

ベース BLOB (それ自体がスナップショットではないすべての BLOB) で **Delete Blob** を呼び出すと、その BLOB は論理的に削除済みとしてマークされます。 以前の動作との整合性のため、アクティブなスナップショットがある BLOB で **Delete Blob** を呼び出すと、エラーが返されます。 論理的に削除されたスナップショットのある BLOB で **Delete Blob** を呼び出すと、エラーは返されません。 論理的な削除が有効になっているときでも、BLOB とそのすべてのスナップショットを 1 回の操作で削除することができます。 これを行うと、ベース BLOB とスナップショットが論理的に削除済みとしてマークされます。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

"*論理的に削除されたデータはグレーで、アクティブなデータは青で示されています。上にあるデータほど書き込まれたのが古いデータです。ここで、**Delete Blob** を呼び出して、B2 とそれに関連付けられているすべてのスナップショットを削除します。アクティブな BLOB である B2 および関連付けられているすべてのスナップショットが、論理的に削除済みとしてマークされます。*"

> [!NOTE]
> 論理的に削除された BLOB が上書きされると、書き込み操作前の BLOB の状態の論理的に削除されたスナップショットが、自動的に生成されます。 新しい BLOB は、上書きされた BLOB の階層を継承します。

コンテナーまたはアカウントによる削除の場合、または BLOB メタデータおよび BLOB プロパティの上書きの場合は、論理的な削除はデータを保存しません。 誤った削除からストレージ アカウントを保護するには、Azure Resource Manager を使ってロックを設定できます。 詳しくは、Azure Resource Manager の記事「[リソースのロックによる予期せぬ変更の防止](../../azure-resource-manager/resource-group-lock-resources.md)」をご覧ください。

次の表では、論理的な削除を有効にしたときに想定される動作の詳細を示します。

| REST API の操作                                                                                                | リソースの種類                 | [説明]                                                                                                 | 動作の変更点                                                                                                                                                                                                                                                                                                                                                   |
|-------------------------------------------------------------------------------------------------------------------|-------------------------------|-------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [削除](/rest/api/storagerp/StorageAccounts/Delete)              | アカウント                       | ストレージ アカウントおよびそれに含まれるすべてのコンテナーと BLOB を削除します。                           | 変更はありません。 削除されたアカウントのコンテナーと BLOB は復旧できません。                                                                                                                                                                                                                                                                                          |
| [Delete Container](/rest/api/storageservices/fileservices/delete-container)       | コンテナー                     | コンテナーおよびそれに含まれるすべての BLOB を削除します。                                                | 変更はありません。 削除されたコンテナーの BLOB は復旧できません。                                                                                                                                                                                                                                                                                                       |
| [Put Blob](/rest/api/storageservices/fileservices/put-blob)                       | ブロック BLOB、追加 BLOB、ページ BLOB | コンテナー内で新しい BLOB を作成するか、既存の BLOB を置き換えます                                          | 既存の BLOB の置き換えに使った場合、呼び出し前の BLOB の状態のスナップショットが自動的に生成されます。 これは、以前に論理的に削除された BLOB が同じ種類 (ブロック、追加、ページ) の BLOB によって置き換えられる場合にのみ、以前に論理的に削除された BLOB にも適用されます。 異なる種類の BLOB で置き換えられる場合は、既存の論理的に削除されたすべてのデータが完全に期限切れになります。 |
| [Delete Blob](/rest/api/storageservices/fileservices/delete-blob)                 | ブロック BLOB、追加 BLOB、ページ BLOB | BLOB または BLOB のスナップショットを削除としてマークします。 BLOB またはスナップショットは、後でガベージ コレクションの間に削除されます。 | BLOB のスナップショットの削除に使った場合、そのスナップショットは論理的に削除済みとしてマークされます。 BLOB の削除に使った場合、その BLOB は論理的に削除済みとしてマークされます。                                                                                                                                                                                                                           |
| [Copy Blob](/rest/api/storageservices/fileservices/copy-blob)                     | ブロック BLOB、追加 BLOB、ページ BLOB | コピー元の BLOB を、同じストレージ アカウントまたは別のストレージ アカウントのコピー先 BLOB にコピーします。       | 既存の BLOB の置き換えに使った場合、呼び出し前の BLOB の状態のスナップショットが自動的に生成されます。 これは、以前に論理的に削除された BLOB が同じ種類 (ブロック、追加、ページ) の BLOB によって置き換えられる場合にのみ、以前に論理的に削除された BLOB にも適用されます。 異なる種類の BLOB で置き換えられる場合は、既存の論理的に削除されたすべてのデータが完全に期限切れになります。 |
| [Put Block](/rest/api/storageservices/put-block)                                  | ブロック BLOB                   | ブロック BLOB の一部としてコミットされる新しいブロックを作成します。                                                | アクティブな BLOB にブロックをコミットするために使う場合、変更はありません。 論理的に削除された BLOB にブロックをコミットするために使う場合、新しい BLOB が作成され、論理的に削除された BLOB の状態をキャプチャするためにスナップショットが自動的に生成されます。                                                                                                                      |
| [Put Block List](/rest/api/storageservices/fileservices/put-block-list)           | ブロック BLOB                   | ブロック BLOB を構成するブロック ID のセットを指定することにより、BLOB をコミットします。                             | 既存の BLOB の置き換えに使った場合、呼び出し前の BLOB の状態のスナップショットが自動的に生成されます。 これは、以前に論理的に削除された BLOB がブロック BLOB である場合にのみ、以前に論理的に削除された BLOB にも適用されます。 異なる種類の BLOB で置き換えられる場合は、既存の論理的に削除されたすべてのデータが完全に期限切れになります。                                                |
| [Put Page](/rest/api/storageservices/fileservices/put-page)                       | ページ BLOB                    | ページの範囲をページ BLOB に書き込みます。                                                                     | 変更はありません。 この操作を使って上書きまたは消去されるページ BLOB のデータは保存されず、復旧できません。                                                                                                                                                                                                                                                   |
| [Append Block](/rest/api/storageservices/fileservices/append-block)               | 追加 BLOB                  | 追加 BLOB の末尾にデータのブロックを書き込みます                                                         | 変更はありません。                                                                                                                                                                                                                                                                                                                                                           |
| [Set Blob Properties](/rest/api/storageservices/fileservices/set-blob-properties) | ブロック BLOB、追加 BLOB、ページ BLOB | BLOB に対して定義されているシステム プロパティの値を設定します。                                                       | 変更はありません。 上書きされた BLOB のプロパティは復旧できません。                                                                                                                                                                                                                                                                                                          |
| [Set Blob Metadata](/rest/api/storageservices/fileservices/set-blob-metadata)     | ブロック BLOB、追加 BLOB、ページ BLOB | 1 つまたは複数の名前と値のペアとして指定された BLOB のユーザー定義メタデータを設定します。                          | 変更はありません。 上書きされた BLOB のメタデータは復旧できません。                                                                                                                                                                                                                                                                                                             |

"Put Page" を呼び出してページ BLOB の範囲を上書きまたはクリアしても、スナップショットは自動的に生成されないということに、注意する必要があります。 仮想マシンのディスクは、ページ BLOB を利用しており、**Put Page** を使ってデータを書き込みます。

### <a name="recovery"></a>復旧

削除されたデータの復旧を容易にするため、新しい "Undelete Blob" API が導入されました。 論理的に削除されたベース BLOB に対して削除取り消し API を呼び出すと、その BLOB およびそれに関連付けられているすべての論理的に削除されたスナップショットが、アクティブとして復元されます。 アクティブなベース BLOB に対して削除取り消し API を呼び出すと、それに関連付けられているすべての論理的に削除されたスナップショットが、アクティブとして復元されます。 アクティブとして復元されるスナップショットは、ユーザー生成のスナップショットと同じように処理されます。ベース BLOB を上書きすることはありません。

特定の論理的に削除されたスナップショットに BLOB を復元するには、ベース BLOB に対して **Undelete Blob** を呼び出します。 その後、アクティブになった BLOB にスナップショットをコピーできます。 新しい BLOB にスナップショットをコピーすることもできます。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

"*論理的に削除されたデータはグレーで、アクティブなデータは青で示されています。上にあるデータほど書き込まれたのが古いデータです。この例では、BLOB B に対して **Undelete Blob** が呼び出されており、それにより、ベース BLOB B1 と、それに関連付けられているすべてのスナップショット (ここでは B0 のみ) がアクティブとして復元されます。2 番目のステップでは、B0 がベース BLOB にコピーされます。このコピー操作では、B1 の論理的に削除されたスナップショットが生成されます。*"

論理的に削除された BLOB および BLOB のスナップショットを表示するには、**List Blobs** に削除されたデータを含めます。 論理的に削除されたベース BLOB だけを表示することも、論理的に削除された BLOB のスナップショットを含めることもできます。 すべての論理的に削除されたデータについて、データが削除された日時と、データが完全に期限切れになるまでの日数を表示できます。

### <a name="example"></a>例

次に示すのは、論理的な削除が有効になっているときに、"HelloWorld" という名前の BLOB のアップロード、上書き、スナップショット作成、削除、復元を行う .NET スクリプトのコンソール出力です。

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

この出力を生成したアプリケーションについては、「[次のステップ](#Next steps)」セクションをご覧ください。

## <a name="pricing-and-billing"></a>価格と課金

論理的に削除されたデータはすべて、アクティブなデータと同じレートで課金されます。 構成されているリテンション期間の後で完全に削除されたデータについては請求されません。 スナップショットとその課金方法について詳しくは、「[スナップショットの課金方法について](storage-blob-snapshots.md)」をご覧ください。

スナップショットの自動生成に関するトランザクションには課金されません。 **Undelete Blob** のトランザクションは、"書き込み操作" のレートで課金されます。

一般的な Azure Blob Storage の価格について詳しくは、[Azure Blob Storage の料金に関するページ](https://azure.microsoft.com/pricing/details/storage/blobs/)をご覧ください。

論理的な削除を初めて有効にするときは、リテンション期間を短くして、この機能が請求に及ぼす影響をよく理解することをお勧めします。

## <a name="quickstart"></a>クイック スタート

### <a name="azure-portal"></a>Azure ポータル
論理的な削除を有効にするには、**[Blob service]** の **[論理的な削除]** オプションに移動します。 **[有効]** をクリックして、論理的に削除されたデータを保持する日数を入力します。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

論理的に削除された BLOB を表示するには、**[Show deleted blobs]\(削除された BLOB を表示する\)** チェック ボックスをオンにします。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

特定の BLOB の論理的に削除されたスナップショットを表示するには、BLOB を選んで、**[スナップショットの表示]** をクリックします。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

**[Show deleted snapshots]\(削除されたスナップショットを表示する\)** チェック ボックスがオンになっていることを確認します。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

論理的に削除された BLOB またはスナップショットをクリックしたら、新しい BLOB のプロパティを確認します。 プロパティでは、オブジェクトが削除された日時と、BLOB または BLOB のスナップショットが完全に期限切れになるまでの残り日数が示されます。 論理的に削除されたオブジェクトがスナップショットではない場合は、削除を取り消すこともできます。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

BLOB の削除を取り消すと、関連付けられているすべてのスナップショットの削除も取り消されることに注意してください。 アクティブな BLOB の論理的に削除されたスナップショットの削除を取り消すには、BLOB をクリックして、**[Undelete all snapshots]\(すべてのスナップショットの削除を取り消す\)** を選びます。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

BLOB のスナップショットの削除を取り消した後は、**[レベル上げ]** をクリックしてスナップショットをベース BLOB にコピーすることで、BLOB をスナップショットに復元できます。

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

### <a name="powershell"></a>PowerShell
論理的な削除を有効にするには、BLOB クライアントのサービスのプロパティを更新します。 次の例では、サブスクリプションのアカウントのサブセットで論理的な削除を有効にしています。

```powershell
Set-AzureRmContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzureRMStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzureStorageDeleteRetentionPolicy -RetentionDays 7
```

誤って削除された BLOB を復旧するには、それらの BLOB で Undelete を呼び出します。 アクティブな BLOB と論理的に削除された BLOB の両方で **Undelete Blob** を呼び出すと、関連付けられているすべての論理的に削除されたスナップショットがアクティブとして復元されることに注意してください。 次の例では、コンテナー内にあるすべての論理的に削除された BLOB とアクティブな BLOB で Undelete を呼び出しています。
```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzureStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
### <a name="python-client-library"></a>Python クライアント ライブラリ

論理的な削除を有効にするには、BLOB クライアントのサービスのプロパティを更新します。

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(delete_retention_policy = DeleteRetentionPolicy(enabled = True, days = 7))
```

### <a name="net-client-library"></a>.NET クライアント ライブラリ

論理的な削除を有効にするには、BLOB クライアントのサービスのプロパティを更新します。

```csharp
// Get the blob client’s service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client’s service property settings
blobClient.SetServiceProperties(serviceProperties);
```

誤って削除された BLOB を復旧するには、それらの BLOB で Undelete を呼び出します。
アクティブな BLOB と論理的に削除された BLOB の両方で **Undelete Blob** を呼び出すと、関連付けられているすべての論理的に削除されたスナップショットがアクティブとして復元されることに注意してください。 次の例では、コンテナー内にあるすべての論理的に削除された BLOB とアクティブな BLOB で Undelete を呼び出しています。

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

BLOB の特定のバージョンに復元するには、最初に BLOB で Undelete を呼び出した後、目的のスナップショットを BLOB にコピーします。 次の例では、ブロック BLOB を最後に生成されたスナップショットに復旧しています。

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

## <a name="should-i-use-soft-delete"></a>論理的な削除を使用する必要があるかどうか

アプリケーションまたは別のストレージ アカウントのユーザーによってデータが誤って変更または削除される可能性がある場合は、論理的な削除を有効にすることをお勧めします。
論理的な削除はデータ保護戦略の一部であり、不注意によるデータの損失を防ぐことができます。

## <a name="faq"></a>FAQ

**論理的な削除を使用できるのはどのストレージの種類ですか?**

現時点では、論理的な削除を使うことができるのは BLOB (オブジェクト) ストレージだけです。

**すべてのストレージ アカウントの種類で論理的な削除を使用できますか?**

はい。論理的な削除は、BLOB ストレージ アカウントだけでなく汎用ストレージ アカウントの BLOB でも利用できます。 これは、Standard と Premium 両方のアカウントに適用されます。 論理的な削除は、管理ディスクでは利用できません。

**すべてのストレージ層で論理的な削除を使用できますか?**

はい。論理的な削除は、ホット、クール、アーカイブを含むすべてのストレージ層で使うことができます。 ただし、アーカイブ層の BLOB に対しては、論理的な削除の上書き保護は提供されません。

**Premium ストレージ アカウントには BLOB ごとのスナップショットに 100 個の制限があります。論理的に削除されたスナップショットは、この制限にカウントされますか?**

いいえ。論理的に削除されたスナップショットは、この制限にはカウントされません。

**既存のストレージ アカウントで論理的な削除を有効にすることができますか?**

はい。論理的な削除は、既存と新規の両方のストレージ アカウントに対して構成できます。

**論理的な削除を有効にして、アカウント全体またはコンテナー全体を削除した場合、関連付けられているすべての BLOB が保存されますか?**

いいえ。アカウント全体またはコンテナー全体を削除した場合は、関連付けられているすべての BLOB が完全に削除されます。 偶発的な削除からストレージ アカウントを保護する方法については、Azure Resource Manager の記事「[リソースのロックによる予期せぬ変更の防止](../../azure-resource-manager/resource-group-lock-resources.md)」をご覧ください。

**削除されたデータの容量メトリックを見ることはできますか?**

論理的に削除されたデータは、ストレージ アカウントの合計容量の一部として含まれます。 ストレージ容量の監視と追跡について詳しくは、「[Storage Analytics](../common/storage-analytics.md)」をご覧ください。

**論理的な削除を無効にした場合でも、論理的に削除されたデータにアクセスできますか?**

はい。論理的な削除が無効になっていても、期限が切れていなければ、論理的に削除されたデータにアクセスしてそれを復旧することができます。

**BLOB の論理的に削除されたスナップショットを読み取ってコピーできますか?**

はい。ただし、最初に BLOB で Undelete を呼び出す必要があります。

**すべての BLOB の種類で論理的な削除を使用できますか?**

はい。論理的な削除は、ブロック BLOB、追加 BLOB、ページ BLOB で利用できます。

**論理的な削除は、仮想マシンのディスクに対して使用できますか?**

論理的な削除は、Premium と Standard 両方の非管理ディスクに利用できます。 論理的な削除は、**Delete Blob**、**Put Blob**、**Put Block List**、**Put Block**、**Copy Blob** によって削除されたデータの復旧にのみ役立ちます。 **Put Page** を呼び出すことで上書きされたデータは復旧できません。

**論理的な削除を使うには、既存のアプリケーションを変更する必要がありますか?**

論理的な削除は、お使いの API のバージョンに関係なく利用できます。 ただし、論理的に削除された BLOB および BLOB のスナップショットを一覧表示および復旧するには、[ストレージ サービス REST API](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) の 2017-07-29 以降のバージョンを使う必要があります。 この機能を使っているかどうかにかかわらず、一般に、最新バージョンを常に使うことをお勧めします。

## <a name="next-steps"></a>次の手順

* [.NET サンプル コード](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)

* [Blob service の REST API](/rest/api/storageservices/fileservices/blob-service-rest-api)

* [Azure Storage のレプリケーション](../common/storage-redundancy.md)

* [RA-GRS を使用した高可用性アプリケーションの設計](../common/storage-designing-ha-apps-with-ragrs.md)

* [Azure Storage の停止が発生した場合の対処方法](../common/storage-disaster-recovery-guidance.md)
