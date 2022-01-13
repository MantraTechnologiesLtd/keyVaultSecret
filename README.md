# Using Managed Service Identity to access Azure keyVault secrets

This is example of creating and accessing keyVault secret. Example shows how to access secrets using .net api

##	First create a vault
Open Azure portal and log in – click on the “All services” menu item on the left hand side, and search for “key vault” – this should filter the options so you have a screen like the one below.

![kv-01](https://user-images.githubusercontent.com/83211588/149376693-556b9a08-421a-4568-8ef9-4bc1c539f186.png)

Once you’ve got the Key Vaults option, click on it to see a screen like the one below which will list the Key Vaults in your subscription. To create a new vault, click on the “Create” button, highlighted in the document below.

![kv-02](https://user-images.githubusercontent.com/83211588/149376702-053cad05-92cf-468d-bfc2-ad0313a7eef9.png)

You will be presented a new page where you can enter information about your new vault.

![kv-03](https://user-images.githubusercontent.com/83211588/149376707-a5ce75bb-83f2-4375-aca3-79ded5888bc3.png)

![kv-04](https://user-images.githubusercontent.com/83211588/149376712-68e4113b-73f7-4f55-b9dc-2aa126327fc9.png)

![kv-05](https://user-images.githubusercontent.com/83211588/149376719-afff57b1-dc3f-4851-ba25-91132fd1ba45.png)

![kv-07](https://user-images.githubusercontent.com/83211588/149376760-b9aac901-ac87-43a9-881b-5ca6c32df07f.png)

I clicked on the Create button at the bottom of the screen, and the portal presents a toast at the top right to say my vault is in the process of being created.

![kv-08](https://user-images.githubusercontent.com/83211588/149376768-30dd3f15-d20e-45d3-8e72-b9b520993153.png)

Once key vault is created you will be notified and on that screen click on go to resource.

![kv-09](https://user-images.githubusercontent.com/83211588/149376776-82d3f42b-5114-4690-9639-67b0b48393e5.png)

![kv-10](https://user-images.githubusercontent.com/83211588/149376783-16f1e840-f5ec-4d19-8371-4a3500cf122b.png)


##	Add a secret to your vault

![kv-11](https://user-images.githubusercontent.com/83211588/149376792-ef9e1227-0d5d-44bf-8f60-67bc1ffb82ff.png)
![kv-12](https://user-images.githubusercontent.com/83211588/149376799-1443ef4e-de9a-4043-9339-20c5a1ec421b.png)
![kv-13](https://user-images.githubusercontent.com/83211588/149376807-84d5b029-11f8-4124-82d6-f06f2afdc31d.png)

##	Create and Secure your app service using Managed Service Identity

![kv-14](https://user-images.githubusercontent.com/83211588/149376814-3f88728e-3d0d-4d31-8e37-f6eb2f149c9d.png)
![kv-15](https://user-images.githubusercontent.com/83211588/149376823-1e1cc792-1ecf-4a61-9687-89f834c51343.png)
![kv-16](https://user-images.githubusercontent.com/83211588/149376828-2cff0d8d-3b43-4332-abe5-362d9ea488b3.png)
![kv-17](https://user-images.githubusercontent.com/83211588/149376839-318ec072-0977-431e-9aa3-7764f0492602.png)
![kv-18](https://user-images.githubusercontent.com/83211588/149376842-dfee6000-a056-497c-a324-06703984a52b.png)
![kv-19](https://user-images.githubusercontent.com/83211588/149376846-df613dec-703e-47d8-9a64-a33be251db34.png)



![kv-20](https://user-images.githubusercontent.com/83211588/149376849-e9a4d59c-6f7f-41a7-bfdc-aa1635a3d321.png)
![kv-21](https://user-images.githubusercontent.com/83211588/149376857-25733dd7-d574-43ce-90cf-51ac0c3b49cc.png)
![kv-22](https://user-images.githubusercontent.com/83211588/149376862-a021c1cf-e000-40d1-9d1e-ff5047b52f02.png)
![kv-23](https://user-images.githubusercontent.com/83211588/149376869-966ea85e-4baa-4a47-96b6-ecee00410d09.png)
![kv-24](https://user-images.githubusercontent.com/83211588/149376873-23b818c9-3955-49df-9f35-ebc49e985c44.png)
![kv-25](https://user-images.githubusercontent.com/83211588/149376879-c330c50e-4a41-4587-85ea-bbf9a1be5778.png)
![kv-26](https://user-images.githubusercontent.com/83211588/149376891-5cc31472-4195-4636-aefc-3f709e5fcc6a.png)
![kv-27](https://user-images.githubusercontent.com/83211588/149376896-ebe30490-3341-45f4-8372-f38876736430.png)

##	Access the secret from your source code with a KeyVaultClient
Assuming you have already deployed your web api to app services created above.

```
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.KeyVault;
using Microsoft.Azure.KeyVault.Models;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Extensions.Logging;
using System;
using System.Threading.Tasks;

namespace KeyVaultAccessDemo.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        private readonly ILogger<WeatherForecastController> _logger;

        public WeatherForecastController(ILogger<WeatherForecastController> logger)
        {
            _logger = logger;
        }

        [HttpGet]
        public async Task<string> Get()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            var keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
            SecretBundle secret = await keyVaultClient.GetSecretAsync("https://development-kv-0976.vault.azure.net/", "MySecret");
            return secret.Value;
        }
    }
}

```

![kv-28](https://user-images.githubusercontent.com/83211588/149376901-6b29f761-b27d-455a-b690-fcbebbbdcdfd.png)

