---
title: User management enhancements - Azure Active Directory | Microsoft Docs
description: Describes how Azure Active Directory enables user search, filtering, and more information about your users.
services: active-directory
documentationcenter: ''
author: curtand
manager: karenhoran
editor: ''

ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 01/03/2022
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro

ms.collection: M365-identity-device-management
---

# User management enhancements in Azure Active Directory

This article describes how to use the user management enhancements in the Azure Active Directory (Azure AD) portal. The **All users** and **Deleted users** pages have been updated to provide more information and make it easier to find users.

Enhancements include:

- More visible user properties including object ID, directory sync status, creation type, and identity issuer
- Search allows substring search and combined search of names, emails, and object IDs
- Enhanced filtering by user type (member, guest, none), directory sync status, creation type, company name, and domain name
- Sorting capabilities on properties like name and user principal name
- Total users count that updates with searches or filters

> [!NOTE]
> These enhancements are not currently available for Azure AD B2C tenants.

## User properties enhanced

We’ve made some changes to the columns available on the **All users** and **Deleted users** pages. In addition to the existing columns we provide for managing your list of users, we've added a few more columns.

### All users page

The following are the displayed user properties on the **All users** page:

- Name: The display name of the user.
- User principal name: The user principal name (UPN) of the user.
- User Type: Member, guest, none.
- Creation time: The date and time the user was created.
- Job title: The job title of the user.
- Department: The department the user works in.
- Directory synced: Indicates whether the user is synced from an on-premises directory.
- Identity issuer: The issuers of the identity used to sign into a user account.
- Object ID: The object ID of the user.
- Creation type: Indicates how the user account was created.
- Company name: The company name which the user is associated.
- Invitation state: The status of the invitation for a guest user.
- Mail: The email of the user.

![new user properties displayed on All users and Deleted users pages](./media/users-search-enhanced/user-properties.png)

### Deleted users page

The **Deleted users** page includes all the columns that are available on the **All users** page, and a few additional columns, namely:

- Deletion date: The date the user was first deleted from the organization (the user is restorable).
- Permanent deletion date: The date after which the process of permanently deleting the user from the organization automatically begins.
- Original user principal name: The original UPN of the user before their object ID was added as a prefix to their deleted UPN.

> [!NOTE]
> Deletion dates are displayed in Coordinated Universal Time ‎(UTC)‎.

Some columns are displayed by default. To add other columns, select **Columns** on the page, select the column names you’d like to add, and select **OK** to save your preferences.

### Identity issuers

Select an entry in the **Identity issuer** column for any user to view additional details about the issuer including the sign-in type and the issuer assigned ID. The entries in the **Identity issuer** column can be multi-valued. If there are multiple issuers of the user's identity, you'll see the word Multiple in the **Identity issuer** column on **All users** and **Deleted users** pages, and the details pane list all issuers.

> [!NOTE]
> The **Source** column is replaced by multiple columns including **Creation type**, **Directory synced**, and **Identity issuer** for more granular filtering.

## User list search

When you enter a search string, the search now uses "starts with" and substring search to match names, emails, or object IDs in a single search. You can enter any of these attributes into the search box, and the search automatically looks across all these properties to return any matching results. The substring search is performed only on whole words. You can perform the same search on both the **All users** and **Deleted users** pages.

## User list filtering

Filtering capabilities have been enhanced to provide more filtering options for the **All users** and **Deleted users** pages. You can now filter by multiple properties simultaneously, and can filter by more properties.

### Filtering All users list

The following are the filterable properties on the **All users** page:

- User type: Member, guest, none
- Directory synced status: Yes, no
- Creation type: Invitation, Email verified, Local account
- Creation time: Last 7, 14, 30, 90, 360 or >360 days ago
- Job title: Enter a job title
- Department: Enter a department name
- Group: Search for a group
- Invitation state – Pending acceptance, Accepted
- Domain name: Enter a domain name
- Company name: Enter a company name
- Administrative unit: Select this option to restrict the scope of the users you view to a single administrative unit. For more information, see [Administrative units management preview](../roles/administrative-units.md).

### Filtering Deleted users list

The **Deleted users** page has additional filters not in the **All users** page. The following are the filterable properties on the **Deleted users** page:

- User type: Member, guest, none
- Directory synced status: Yes, no
- Creation type: Invitation, Email verified, Local account
- Creation time: Last 7, 14, 30, 90, 360 or > 360 days ago
- Job title: Enter a job title
- Department: Enter a department name
- Invitation state: Pending acceptance, Accepted
- Deletion date: Last 7, 14, or 30 days
- Domain name: Enter a domain name
- Company name: Enter a company name
- Permanent deletion date: Last 7, 14, or 30 days

## User list sorting

You can now sort by name and user principal name in the **All users** and **Deleted users** pages. You can also sort by deletion date in the **Deleted Users** list.

## User list counts

You can view the total number of users in the **All users** and **Deleted users** pages. As you search or filter the lists, the count is updated to reflect the total number of users found.

![Illustration of user list counts on the All users page](./media/users-search-enhanced/user-list-sorting.png)

## Frequently Asked Questions (FAQ)

Question | Answer
-------- | ------
Why is the deleted user still displayed when the permanent deletion date has passed? | The permanent deletion date is displayed in the UTC time zone, so this may not match your current time zone. Also, this date is the earliest date after which the user will be permanently deleted from the organization, so it may still be processing. Permanently deleted users will automatically be removed from the list.
What happen to the bulk capabilities for users and guests? | The bulk operations are all still available for users and guests, including bulk create, bulk invite, bulk delete, and download users. We’ve just merged them into a menu called **Bulk operations**. You can find the **Bulk operations** options at the top of the **All users** page.
What happened to the Source column? | The **Source** column has been replaced with other columns that provide similar information, while allowing you to filter on those values independently. Examples include **Creation type**, **Directory synced** and **Identity issuer**.
What happened to the User Name column? | The **User Name** column is still there, but it’s been renamed to **User Principal Name**. This  better reflects the information contained in that column. You’ll also notice that the full User Principal Name is now displayed for B2B guests. This matches what you’d get in MS Graph.  

## Next steps

User operations

- [Add or change profile information](../fundamentals/active-directory-users-profile-azure-portal.md)
- [Add or delete users](../fundamentals/add-users-azure-active-directory.md)

Bulk operations

- [Download list of users](users-bulk-download.md)
- [Bulk add users](users-bulk-add.md)
- [Bulk delete users](users-bulk-delete.md)
- [Bulk restore users](users-bulk-restore.md)
