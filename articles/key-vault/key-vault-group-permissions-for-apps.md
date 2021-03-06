---
title: さまざまなアプリケーションに Azure のキー コンテナーへのアクセス許可を付与する | Microsoft Docs
description: さまざまなアプリケーションにキー コンテナーへのアクセス許可を付与する方法を説明します。
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: ambapat
ms.openlocfilehash: ddeaf184138bd48d324799ddb45248b0a0ee8eeb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>さまざまなアプリケーションにキー コンテナーへのアクセス許可を付与する

## <a name="q-i-have-several-over-16-applications-that-need-to-access-a-key-vault-since-key-vault-only-allows-16-access-control-entries-how-can-i-achieve-that"></a>Q: キー コンテナーにアクセスする必要があるアプリケーションがいくつか (17 個以上) あります。 Key Vault では最大 16 個のアクセス制御エントリまでしか認められていませんが、どうすればよいでしょうか。

Key Vault のアクセス制御ポリシーでは、エントリは 16 個までしかサポートされません。 ただし、Azure Active Directory セキュリティ グループを作成することができます。 関連するすべてのサービス プリンシパルをこのセキュリティ グループに追加し、そのセキュリティ グループに Key Vault へのアクセスを許可してください。

前提条件は次のとおりです。
* [Azure Active Directory V2 PowerShell モジュールのインストール](https://www.powershellgallery.com/packages/AzureAD)。
* [Azure PowerShell のインストール](/powershell/azure/overview)。
* 次のコマンドを実行するには、Azure Active Directory テナント内でグループを作成または編集するためのアクセス許可が必要です。 アクセス許可を持っていない場合は、Azure Active Directory 管理者に連絡する必要があります。

PowerShell で次のコマンドを実行します。

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId -PermissionsToKeys all –PermissionsToSecrets all –PermissionsToCertificates all 
 
# Of course you can adjust the permissions as required 
```

アプリケーションのグループに異なるアクセス許可セットを付与する必要がある場合は、そのようなアプリケーション用に別の Azure Active Directory セキュリティ グループを作成します。

## <a name="next-steps"></a>次の手順

[キー コンテナーをセキュリティ保護する](key-vault-secure-your-key-vault.md)方法について確認します。
