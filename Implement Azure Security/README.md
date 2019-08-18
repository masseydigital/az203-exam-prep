# Implement Azure Security (10-15%)

## Implement Authentication

### Implement Authentication by using certificates, forms-based authentication, or tokens

### Implement multi-factor or Windows authentication using Azure AD

### Implement OAuth2 authentication 

### Implement Managed Service Identity (MSI) / Service Principal Authentication

## Implement Access Control

### Implement CBAC (Claims-Based Access Control) authorization

### Implement RBAC (Role-Based Access Control) authorization

### Create shared access signatures

## Implement Secure Data Solutions

### Encrypt and Decrypt data at rest and in transit
_Transport Encryption_ helps protection application data while it's in transit from users to your servers.  Azure Application Gateway is one way that we can encrypt data in transit with Azure.

_Application Gateway_ is an application delivery controller.  It provides features such as load balancing HTTP traffic, web application firewall, and support for SSL encryption of your data.

There are many steps to retrieving/sending encrypted traffic with the Application Gateway:
1) Frontend Port and Listener: Traffic enters the gateway through a front end port.  A listener is the first thing that your traffic meets when entering the gateway through a port.  It's set-up to listen for a specific host name, and specific port on a specific ip address.  The listener can use an ssl certificate to decrypt traffic.  It then forwards traffic to a back-end pool.
2) Backend Pool: The backend pool contains your application servers.  These servers might be virtual machines, a virtual machine scale set, or applications running on Azure App service.  Incoming requests are load balanced across the pool, and recrypts the traffic before forwarding it to pool.

**If you are using Azure App Service to host the backend application, you don't need to install the certificates in Application Gateway to connect to the backend pool.**

You can add an authentication certificate to Application Gateway by using the az network application-gateway auth-cert create command in the Azure CLI.

```powershell
az network application-gateway root-cert create \
      --resource-group <resource group name> \
      --gateway-name <application gateway name> \
      --name <certificate name> \
      --cert-file <path to trusted CA certificate>
```

Application Gateway uses a _rule_ to specify how to direct the messages that it receives on its incoming port to the servers in the backend pool.  If the servers are using SSL, you must configure the rule to indicate.
1) That the servers expect traffic through the HTTPS protocol.
2) Which certificate to use to encrypt the traffic and authenticate the connection to a server.

To define an HTTP setting, you use the az network application-gateway http-settings create command in the Azure CLI.

```powershell
az network application-gateway http-settings create \
    --resource-group <resource group name> \
    --gateway-name <application gateway name> \
    --name <HTTPS settings name> \
    --port 443 \
    --protocol Https \
```

You can set-up the backend pool for the application gateway with the following command:

```powershell
az network application-gateway address-pool create \
  --resource-group [Sandbox resource group] \
  --gateway-name gw-shipping \
  --name ap-backend \
  --servers $privateip
```

You can set the backend pool to use the ssl certificate settings with the following command:

```powershell
export rgID="$(az group show --name [Sandbox resource group] --query id --output tsv)"

az network application-gateway http-settings update \
    --resource-group [Sandbox resource group] \
    --gateway-name gw-shipping \
    --name https-settings \
    --set trustedRootCertificates='[{"id": "'$rgID'/providers/Microsoft.Network/applicationGateways/gw-shipping/trustedRootCertificates/shipping-root-cert"}]'s
```

You can create the frontend port with the following command:

```powershell
az network application-gateway frontend-port create \
    --resource-group <resource group name> \
    --gateway-name <application gateway name>  \
    --name <port name>
    --port 443
```

You can create an ssl certificate in the PFX format with the following command:

```powershell
az network application-gateway ssl-cert create \
   --resource-group <resource group name> \
   --gateway-name <application gateway name> \
   --name <ssl certificate name> \
   --cert-file <SSL certificate file (PFX)> \
   --cert-password <password for certificate file>
```

You can then create the listener that receives requests from the frontend port and decrypts them by using this certificate:

```powershell
az network application-gateway http-listener create \
    --resource-group <resource group name> \
    --gateway-name <application gateway name> \
    --name <listener name> \
    --frontend-port <frontend port name> \
    --ssl-cert <ssl certificate name>
```

Finally, you can create a rule which directs messages received through the listener to the servers in the backend pool:

```powershell
az network application-gateway rule create \
    --resource-group <resource group name> \
    --gateway-name <application gateway name> \
    --name <rule name> \
    --address-pool <backend pool> \
    --http-listener <listener name> \
    --http-settings <HTTPS settings name> \
    --rule-type Basic
```

### Create, Read, Update, and Delete keys, secrets, and certificates by using the KeyVault API
Azure Key Vault is a _secret store_: a centralized cloud service for storing application secrets.  Key Vault helps you control your applications' secretes by keeping them in a single central location and providing secure access, permissions control, and access logging.

Secret access and vault management is accomplished through REST Api, which is also supported by all of the Azure management tools as well as client libraries available for many popular languages.  Every vault has a unique URL where its API is hosted.  Azure Key Vault also supports certificates and keys.

**Best practice is to create a vault for each environment of your application (i.e development, test, production).**

Azure Key Vault's API uses Azure Active Directory to authenticate users and applications.  Vault access policies are based on actions, and are applied across an entire vault.  All actions performed on a vault require authentication and authorization - there is no way to grant anonymous access.

To create a key vault in the Cloud Shell, use the following command:

```powershell
az keyvault create \
    --resource-group [resource-group] \
    --name <your-unique-vault-name>
```

To add a secret to the vault, use the following command:

```powershell
az keyvault secret set \
    --name [password-name] \
    --value [password-value] \
    --vault-name <your-unique-vault-name>
```

To start using Azure Key Vault with web applications, we must register the app with Azure Active Directory - this creates an identity for the app.  Apps and users authenticate to Key Vault using an Azure Active Directory authentication token.  Getting a token from Azure Active Directory requires a secret or certificate, because anyone with a token could use the application identity to access all of the secrets in the vault.

_Managed Identities_ for Azure resources is an Azure feature that your app can use to access Key Vault and other Azure services without having to manage even a single secret outside of the vault.  Managed Identities for Azure resources also registers your app in Azure Active Directory, adn will delete the registration if you delete the web app or disable its managed identity.

The official Key Vault client for .Net Core is the KeyVaultClient class in Microsoft.Azure.KeyVault NuGet package.  You can use ASP.Net Core's AddAzureKeyVault method to load all of the secrets from a vault into the Configuration API at startup.

Azure key vault url's use the following format:

```csharp
// Build the current set of configuration to load values from
                    // JSON files and environment variables, including VaultName.
                    var builtConfig = config.Build();

                    // Use VaultName from the configuration to create the full vault URL.
                    var vaultUrl = $"https://{builtConfig["VaultName"]}.vault.azure.net/";

                    // Load all secrets from the vault into configuration. This will automatically
                    // authenticate to the vault using a managed identity. If a managed identity
                    // is not available, it will check if Visual Studio and/or the Azure CLI are
                    // installed locally and see if they are configured with credentials that can
                    // access the vault.
                    config.AddAzureKeyVault(vaultUrl);
```

To add your KeyVault to your webapp, define the VaultName to the configuration

```powershell
az webapp config appsettings set \
    --resource-group [subscription name] \
    --name <your-unique-app-name> \
    --settings 'VaultName=<your-unique-vault-name>'
```

To enable managed identity for your web app, use the following command:

```powershell
az webapp identity assign \
    --resource-group [subscription name] \
    --name <your-unique-app-name>
```

Finally, set the policy for the webapp to use AzureKeyVault with the principalId value copied from the previous step.

```powershell
az keyvault set-policy \
    --secret-permissions get list \
    --name <your-unique-vault-name> \
    --object-id <your-managed-identity-principleid>
```