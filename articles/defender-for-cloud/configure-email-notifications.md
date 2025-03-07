---
title: Configure email notifications for Microsoft Defender for Cloud alerts
description: Learn how to fine-tune the Microsoft Defender for Cloud security alert emails.
ms.topic: quickstart
ms.date: 11/09/2021
ms.custom: mode-other
---
# Configure email notifications for security alerts 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Security alerts need to reach the right people in your organization. By default, Microsoft Defender for Cloud emails subscription owners whenever a high-severity alert is triggered for their subscription. This page explains how to customize these notifications.

Use Defender for Cloud's **Email notifications** settings page to define preferences for notification emails including:

- ***who* should be notified** - Emails can be sent to select individuals or to anyone with a specified Azure role for a subscription. 
- ***what* they should be notified about** - Modify the severity levels for which Defender for Cloud should send out notifications.

To avoid alert fatigue, Defender for Cloud limits the volume of outgoing mails. For each subscription, Defender for Cloud sends:

- a maximum of one email per **6 hours** (4 emails per day) for **high-severity** alerts
- a maximum of one email per **12 hours** (2 emails per day) for **medium-severity** alerts
- a maximum of one email per **24 hours** for **low-severity** alerts

:::image type="content" source="./media/configure-email-notifications/email-notification-settings.png" alt-text="Configuring the details of the contact who will receive emails about security alerts." :::
 
## Availability

|Aspect|Details|
|----|:----|
|Release state:|General availability (GA)|
|Pricing:|Free|
|Required roles and permissions:|**Security Admin**<br>**Subscription Owner** |
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Commercial clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||


## Customize the security alerts email notifications via the portal<a name="email"></a>
You can send email notifications to individuals or to all users with specific Azure roles.

1. From Defender for Cloud's **Environment settings** area, select the relevant subscription, and open **Email notifications**.

1. Define the recipients for your notifications with one or both of these options:

    - From the dropdown list, select from the available roles.
    - Enter specific email addresses separated by commas. There's no limit to the number of email addresses that you can enter.

1. To apply the security contact information to your subscription, select **Save**.

## Customize the alerts email notifications through the API
You can also manage your email notifications through the supplied REST API. For full details see the [SecurityContacts API documentation](/rest/api/securitycenter/securitycontacts).

This is an example request body for the PUT request when creating a security contact configuration:

URI: `https://management.azure.com/subscriptions/<SubscriptionId>/providers/Microsoft.Security/securityContacts/default?api-version=2020-01-01-preview`

```json
{
    "properties": {
        "emails": admin@contoso.com;admin2@contoso.com,
        "notificationsByRole": {
            "state": "On",
            "roles": ["AccountAdmin", "Owner"]
        },
        "alertNotifications": {
            "state": "On",
            "minimalSeverity": "Medium"
        },
        "phone": ""
    }
}
```


## See also
To learn more about security alerts, see the following pages:

- [Security alerts - a reference guide](alerts-reference.md)--Learn about the security alerts you might see in Microsoft Defender for Cloud's Threat Protection module
- [Manage and respond to security alerts in Microsoft Defender for Cloud](managing-and-responding-alerts.md)--Learn how to manage and respond to security alerts
- [Workflow automation](workflow-automation.md)--Automate responses to alerts with custom notification logic
