---
title: Configure ADDS LDAP with extended groups for Azure NetApp Files NFS volume access | Microsoft Docs
description: Describes the considerations and steps for enabling LDAP with extended groups when you create an NFS volume by using Azure NetApp Files.  
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''

ms.assetid:
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.topic: how-to
ms.date: 11/11/2021
ms.author: anfdocs
---
# Configure ADDS LDAP with extended groups for NFS volume access

When you [create an NFS volume](azure-netapp-files-create-volumes.md), you have the option to enable the LDAP with extended groups feature (the **LDAP** option) for the volume. This feature enables Active Directory LDAP users and extended groups (up to 1024 groups) to access files and directories in the volume. You can use the LDAP with extended groups feature with both NFSv4.1 and NFSv3 volumes. 

This article explains the considerations and steps for enabling LDAP with extended groups when you create an NFS volume.  

## Considerations

* You can enable the LDAP with extended groups feature only during volume creation. This feature cannot be retroactively enabled on existing volumes.  

* LDAP with extended groups is supported only with Active Directory Domain Services (ADDS) or Azure Active Directory Domain services (AADDS). OpenLDAP or other third-party LDAP directory services are not supported. 

* LDAP over TLS must *not* be enabled if you are using Azure Active Directory Domain Services (AADDS).  

* You cannot modify the LDAP option setting (enabled or disabled) after you have created the volume.  

* The following table describes the Time to Live (TTL) settings for the LDAP cache. You need to wait until the cache is refreshed before trying to access a file or directory through a client. Otherwise, an access or permission denied message appears on the client. 

    |     Error condition    |     Resolution    |
    |-|-|
    | Cache |  Default Timeout |
    | Group membership list  | 24-hour TTL  |
    | Unix groups  | 24-hour TTL, 1-minute negative TTL  |
    | Unix users  | 24-hour TTL, 1-minute negative TTL  |

    Caches have a specific timeout period called *Time to Live*. After the timeout period, entries age out so that stale entries do not linger. The *negative TTL* value is where a lookup that has failed resides to help avoid performance issues due to LDAP queries for objects that might not exist.”        

## Steps

1. The LDAP with extended groups feature is currently in preview. Before using this feature for the first time, you need to register the feature:  

    1. Register the feature:   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```

    2. Check the status of the feature registration: 

        > [!NOTE]
        > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is `Registered` before continuing.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```
        
    You can also use [Azure CLI commands](/cli/azure/feature) `az feature register` and `az feature show` to register the feature and display the registration status. 

2. LDAP volumes require an Active Directory configuration for LDAP server settings. Follow instructions in [Requirements for Active Directory connections](create-active-directory-connections.md#requirements-for-active-directory-connections) and [Create an Active Directory connection](create-active-directory-connections.md#create-an-active-directory-connection) to configure Active Directory connections on the Azure portal.  

    > [!NOTE]
    > Ensure that you have configured the Active Directory connection settings. A machine account will be created in the organizational unit (OU) that is specified in the Active Directory connection settings. The settings are used by the LDAP client to authenticate with your Active Directory.

3. Ensure that the Active Directory LDAP server is up and running on the Active Directory. 

4. LDAP NFS users need to have certain POSIX attributes on the LDAP server. Set the attributes for LDAP users and LDAP groups as follows: 

    * Required attributes for LDAP users:   
        `uid: Alice`,  
        `uidNumber: 139`,  
        `gidNumber: 555`,  
        `objectClass: user, posixAccount`
    * Required attributes for LDAP groups:   
        `objectClass: group, posixGroup`,  
        `gidNumber: 555`

    The values specified for `objectClass` are separate entries. For example, in Multi-valued String Editor, `objectClass` would have separate values (`user` and `posixAccount`) specified as follows for LDAP users:   

    ![Screenshot of Multi-valued String Editor that shows multiple values specified for Object Class.](../media/azure-netapp-files/multi-valued-string-editor.png) 

    You can manage POSIX attributes by using the Active Directory Users and Computers MMC snap-in. The following example shows the Active Directory Attribute Editor. See [Access Active Directory Attribute Editor](create-volumes-dual-protocol.md#access-active-directory-attribute-editor) for details.  

    ![Active Directory Attribute Editor](../media/azure-netapp-files/active-directory-attribute-editor.png) 

5. If you want to configure an LDAP-integrated NFSv4.1 Linux client, see [Configure an NFS client for Azure NetApp Files](configure-nfs-clients.md).

6. If your LDAP-enabled volumes use NFSv4.1, follow instructions in [Configure NFSv4.1 domain](azure-netapp-files-configure-nfsv41-domain.md#configure-nfsv41-domain) to configure the `/etc/idmapd.conf` file.

    You need to set `Domain` in `/etc/idmapd.conf` to the domain that is configured in the Active Directory Connection on your NetApp account. For instance, if `contoso.com` is the configured domain in the NetApp account, then set `Domain = contoso.com`.

    Then you need to restart the `rpcbind` service on your host or reboot the host. 

7.	Follow steps in [Create an NFS volume for Azure NetApp Files](azure-netapp-files-create-volumes.md) to create an NFS volume. During the volume creation process, under the **Protocol** tab, enable the **LDAP** option.   

    ![Screenshot that shows Create a Volume page with LDAP option.](../media/azure-netapp-files/create-nfs-ldap.png)  

8. Optional - You can enable local NFS client users not present on the Windows LDAP server to access an NFS volume that has LDAP with extended groups enabled. To do so, enable the **Allow local NFS users with LDAP** option as follows:
    1. Click **Active Directory connections**.  On an existing Active Directory connection, click the context menu (the three dots `…`), and select **Edit**.  
    2. On the **Edit Active Directory settings** window that appears, select the **Allow local NFS users with LDAP** option.  

    ![Screenshot that shows the Allow local NFS users with LDAP option](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  

## Next steps  

* [Create an NFS volume for Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Create and manage Active Directory connections](create-active-directory-connections.md)
* [Configure NFSv4.1 domain](azure-netapp-files-configure-nfsv41-domain.md#configure-nfsv41-domain)
* [Troubleshoot volume errors for Azure NetApp Files](troubleshoot-volumes.md)
