# HelloID-Conn-SA-Full-Exchange-Online-DistributionGroupDelete

| :information_source: Information |
| :------------------------------- |
| This repository contains the connector and configuration code only. The implementer is responsible for acquiring the connection details such as organization name, application ID, certificate, etc. You might need to coordinate with the client's application manager before implementing this connector. |

## Description
HelloID-Conn-SA-Full-Exchange-Online-DistributionGroupDelete is a template designed for use with HelloID Service Automation (SA) Delegated Forms. It can be imported into HelloID and customized according to your requirements.

By using this delegated form, you can locate and delete a distribution group or a mail-enabled security group in Exchange Online. The form workflow includes:
 1. Enter a search value
 2. Retrieve and select the target distribution group or mail-enabled security group from a grid
 3. Submit the form to delete the selected group in Exchange Online

Notes shown in the form:
- Retrieving data typically takes ~10 seconds

## Getting started
### Requirements

#### App Registration & Certificate Setup

Before implementing this connector, configure a Microsoft Entra ID App Registration and certificate-based authentication for Exchange Online PowerShell. During the setup process, create a new App Registration in the Entra portal, generate a certificate, and configure app-only access for Exchange Online.

Follow the official Microsoft documentation for creating an App Registration and setting up certificate-based authentication:
- [App-only authentication with certificate (Exchange Online)](https://learn.microsoft.com/en-us/powershell/exchange/app-only-auth-powershell-v2?view=exchange-ps#set-up-app-only-authentication)

#### HelloID-specific configuration

Once you have completed the Microsoft setup and followed their best practices, configure the following HelloID-specific requirements.

- **Exchange Online RBAC (Application):**
  - Grant the application sufficient permissions to manage distribution groups by adding the service principal to an appropriate Exchange role group (e.g., Recipient Management or Organization Management), or an equivalent role that permits deleting distribution groups.
- **Certificate:**
  - Upload the public key file (.cer) in Entra ID
  - Provide the certificate as a Base64 string and its password in HelloID

### Connection settings

The following user-defined variables are used by the connector.

| Setting                          | Description                             | Mandatory |
| -------------------------------- | --------------------------------------- | --------- |
| EntraIdOrganization              | Entra tenant domain/organization (e.g., contoso.onmicrosoft.com) | Yes |
| EntraIdAppId                     | Entra application (client) ID           | Yes |
| EntraIdCertificateBase64String   | Entra certificate Base64 string         | Yes |
| EntraIdCertificatePassword       | Entra certificate password              | Yes |

## Remarks

- **Lookup data source:**
  - The form includes a lookup that retrieves distribution groups using `Get-DistributionGroup` filtered by `Alias`, `DisplayName`, or `Name`. It supports filtering by group type (Distribution Group or Mail-enabled Security Group).
- **Grid selection:**
  - The grid displays `Name`, `PrimarySmtpAddress`, and `Id` with single-row selection.
- **Deletion action:**
  - On submit, the delegated form executes `Remove-DistributionGroup` against the selected group (`Id`).
  - Success and error outcomes are sent to HelloID Audit logging.
- **Performance notes:**
  - Lookup/deletion typically completes in ~10 seconds; actual times may vary.
- **Duplicate import:**
  - When importing a duplicate form, resource names can be suffixed automatically, as configured in the script.

## Development resources

### API endpoints

This connector uses Exchange Online PowerShell (EXO) cmdlets via the `ExchangeOnlineManagement` module:

| Cmdlet/Operation         | Description                                  |
|--------------------------|----------------------------------------------|
| Connect-ExchangeOnline   | Establish EXO session using app-only auth    |
| Get-DistributionGroup    | Retrieve and filter distribution groups      |
| Remove-DistributionGroup | Delete the selected distribution group       |
| Disconnect-ExchangeOnline| Close EXO session                            |

### API documentation

- Exchange Online PowerShell overview: https://learn.microsoft.com/powershell/exchange/exchange-online-powershell
- Connect-ExchangeOnline: https://learn.microsoft.com/powershell/module/exchange/connect-exchangeonline
- Get-DistributionGroup: https://learn.microsoft.com/powershell/module/exchange/get-distributiongroup
- Remove-DistributionGroup: https://learn.microsoft.com/powershell/module/exchange/remove-distributiongroup
- Disconnect-ExchangeOnline: https://learn.microsoft.com/powershell/module/exchange/disconnect-exchangeonline

## Getting help
> :bulb: **Tip:**  
> For more information on Delegated Forms, please refer to our documentation pages: https://docs.helloid.com/en/service-automation/delegated-forms.html

## HelloID docs
The official HelloID documentation can be found at: https://docs.helloid.com/