---
title: 動的グループと Azure Active Directory B2B コラボレーション | Microsoft Docs
description: Azure Active Directory B2B コラボレーションを Azure AD の動的グループと共に使用する方法について説明します。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 12/14/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 751502c2be84e9454c507f09a47b609d003ce2c5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33927853"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>動的グループと Azure Active Directory B2B コラボレーション

## <a name="what-are-dynamic-groups"></a>動的グループとは
Azure Active Directory (Azure AD) のセキュリティ グループ メンバーシップの動的な構成は、[Azure Portal](https://portal.azure.com) で利用できます。 管理者は、ユーザー属性 (userType、部門、国など) に基づいて、Azure AD で作成されたグループのメンバーを設定するためのルールを指定できます。 メンバーを属性に基づいて自動的にセキュリティ グループに追加したり、セキュリティ グループから削除したりすることができます。 これらのグループを使用すると、アプリケーションやクラウド リソース (SharePoint サイト、ドキュメント) へのアクセスを付与したり、メンバーにライセンスを割り当てたりすることができます。 動的なグループの詳細については、「[Azure Active Directory の専用グループ](active-directory-accessmanagement-dedicated-groups.md)」を参照してください。

動的グループを作成および使用するには、適切な [Azure AD Premium P1 または P2 ライセンス](https://azure.microsoft.com/pricing/details/active-directory/)が必要です。 詳細については「[Azure Active Directory で動的グループ メンバーシップの属性ベースのルールを作成する](active-directory-groups-dynamic-membership-azure-portal.md)」の記事を参照してください。

## <a name="what-are-the-built-in-dynamic-groups"></a>組み込みの動的グループとは
**[すべてのユーザー]** 動的グループを使用すると、テナント管理者は、テナントのすべてのユーザーを含むグループをワンクリックで作成できます。 既定では、**[すべてのユーザー]** グループには、メンバーとゲストを含む、ディレクトリ内のすべてのユーザーが含まれます。
新しい Azure Active Directory 管理ポータルの [グループ設定] ビューで、**[すべてのユーザー]** グループを有効にすることができます。

![[" すべてのユーザー" グループの有効化] を [はい] に設定](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>[すべてのユーザー] 動的グループの強化
既定では、**[すべてのユーザー]** グループには B2B コラボレーション (ゲスト) ユーザーも含まれます。 ゲスト ユーザーを削除するルールを使用して、**[すべてのユーザー]** グループをさらに保護することができます。 次の図は、ゲストを除外するように変更された **[すべてのユーザー]** グループを示しています。

![ユーザーの種類がゲストと等しくない場合のルール](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

ゲスト ユーザーにポリシー (Azure AD の条件付きアクセス ポリシーなど) を適用できるように、ゲスト ユーザーのみが含まれる新しい動的グループを作成すると便利な場合もあります。
グループは次のようになります。

![ユーザーの種類がゲストと等しい場合のルール](media/active-directory-b2b-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>次の手順

- [B2B コラボレーション ユーザーのプロパティ](active-directory-b2b-user-properties.md)
- [B2B コラボレーション ユーザーのロールへの追加](active-directory-b2b-add-guest-to-role.md)
- [B2B コラボレーション ユーザーの条件付きアクセス](active-directory-b2b-mfa-instructions.md)

