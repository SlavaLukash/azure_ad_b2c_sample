---
page_type: sample
description: "The sample covers calling an OpenID Connect identity provider (Azure AD B2C) and acquiring a token from Azure AD B2C using MSAL."
languages:
  - csharp
products:
  - dotnet
  - azure
  - azure-active-directory
---

# Azure AD B2C: 
The sample covers the following:

- Calling an OpenID Connect identity provider (Azure AD B2C)
- Acquiring a token from Azure AD B2C using MSAL

## How To Run This Sample

There are two ways to run this sample:

1. **Using your own Azure AD B2C tenant** - Follow the steps listed below in the section [Using your own Azure AD B2C tenant](#Using-your-own-Azure-AD-B2C-Tenant)

### Step 1: Clone or download this repository

From your shell or command line:

`git clone https://github.com/SlavaLukash/azure_ad_b2c_sample.git`

### Step 2: Run the project

Open the `B2C-WebAPI-DotNet.sln` in Visual Studio. 

The sample demonstrates the following functionality once signed-in: 

1. Click your **``<Display Name>``** in upper right corner to edit your profile or reset your password. 
2. Click **Claims** to view the claims associated with the signed-in user's id token. 
3. Sign out and sign in as a different user. Create tasks for this second user. Notice how the tasks are stored per-user on the API, because the API extracts the user's identity from the access token it receives.

## Using your own Azure AD B2C Tenant

In the previous section, you learned how to run the sample application using the demo environment. In this section, you'll learn how to configure the ASP.NET Web Application and the ASP.NET Web API to work with your own Azure AD B2C Tenant.

### Step 1: Get your own Azure AD B2C tenant

First, you'll need an Azure AD B2C tenant. If you don't have an existing Azure AD B2C tenant that you can use for testing purposes, you can create your own by following these [instructions](https://azure.microsoft.com/documentation/articles/active-directory-b2c-get-started/).

### Step 2: Create your own policies

This sample uses three types of policies: a unified sign-up/sign-in policy, a profile editing policy, and a password reset policy. Create one policy of each type by following [the built-in policy instructions](https://azure.microsoft.com/documentation/articles/active-directory-b2c-reference-policies). You may choose to include as many or as few identity providers as you wish.

If you already have existing policies in your Azure AD B2C tenant, feel free to re-use those policies in this sample.

Make sure that all the three policies return **User's Object ID** and **Display Name** on **Application Claims**. To do that, on Azure Portal, go to your B2C Directory then click **User flows (policies)** on the left menu and select your policy. Then click on **Application claims** and make sure that **User's Object ID** and **Display Name** is checked.

### Step 3: Register your ASP.NET Web Application with Azure AD B2C

Follow the instructions at [register a Web Application with Azure AD B2C](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-dotnet-susi)

Your web application registration should include the following information:

- Provide a descriptive Name for your web appliation, for example, `My Test ASP.NET Web Application`. You can identify this application by its Name within the Azure portal.
- Mark **Yes** for the **Include web app / web API** option.
- Set the Reply URL to `https://localhost:44316/` This is the port number that this ASP.NET Web Application sample is configured to run on. 
- Create your application.
- Once the application is created, you need to create a Web App client secret. Go to the **Keys** page for your Web App registration and click **Generate Key**. Note: You will only see the secret once. Make sure you copy it.
- Open your `My Test ASP.NET Web Application` and open the **API Access** window (in the left nav menu). Click Add and select the name of the Web API you registered previously, for example `My Test ASP.NET Web API`. Select the scope(s) you defined previously, for example, `read` and `write` and hit **Ok**.

### Step 5: Configure your Visual Studio project with your Azure AD B2C app registrations

In this section, you will change the code in both projects to use your tenant. 

:warning: Since both projects have a `Web.config` file, pay close attention which `Web.config` file you are modifying.

#### Step 5a: Modify the `TaskWebApp` project

1. Open the `Web.config` file for the `TaskWebApp` project.
1. Find the key `ida:Tenant` and replace the value with your `<your-tenant-name>.onmicrosoft.com`.
1. Find the key `ida:AadInstance` and replace the value with your `<your-tenant-name>.b2clogin.com`.
1. Find the key `ida:TenantId` and replace the value with your Directory ID.
1. Find the key `ida:ClientId` and replace the value with the Application ID from your web application `My Test ASP.NET Web Application` registration in the Azure portal.
1. Find the key `ida:ClientSecret` and replace the value with the Client secret from your web application in in the Azure portal.
1. Find the keys representing the policies, e.g. `ida:SignUpSignInPolicyId` and replace the values with the corresponding policy names you created, e.g. `b2c_1_SiUpIn`
1. Comment out the aadb2cplayground site and uncomment the `locahost:44332` for the TaskServiceUrl – this is the localhost port that the Web API will run on. Your code should look like the following below.

    ```csharp
    <!--<add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net/" />-->
    
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. Change the `api:ApiIdentifier` key value to the App ID URI of the API you specified in the Web API registration. This App ID URI tells B2C which API your Web Application wants permissions to.

    ```csharp
    <!--<add key="api:ApiIdentifier" value="https://fabrikamb2c.onmicrosoft.com/api/" />—>

    <add key="api:ApiIdentifier" value="https://<your-tenant-name>.onmicrosoft.com/demoapi/" />
    ```

    :memo: Make sure to include the trailing '/' at the end of your `ApiIdentifier` value. 

1. Find the keys representing the scopes, e.g. `api:ReadScope` and replace the values with the corresponding scope names you created, e.g. `read`



## Known Issues

- MSAL cache needs a TenantId along with the user's ObjectId to function. It retrieves these two from the claims returned in the id_token. As TenantId is not guranteed to be present in id_tokens issued by B2C unless the steps [listed in this document](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#caching-with-b2c-in-msalnet), 
if you are following the workarounds listed in the doc and tenantId claim (tid) is available in the user's token, then please change the code in [ClaimsPrincipalsExtension.cs](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/blob/nvalluri-b2c/TaskWebApp/Utils/ClaimsPrincipalExtension.cs) GetB2CMsalAccountId() to let MSAL pick this from the claims instead.

## Next Steps

Customize your user experience further by supporting more identity providers.  Checkout the docs belows to learn how to add additional providers:

[Microsoft](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)

[Facebook](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app)

[Google](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app)

[Amazon](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app)

[LinkedIn](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app)


## Additional information

Additional information regarding this sample can be found in our documentation:

* [How to build a .NET web app using Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-dotnet-susi)
* [How to build a .NET web API secured using Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-api-dotnet)
* [How to call a .NET web api using a .NET web app](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet)

## Questions & Issues

Please file any questions or problems with the sample as a github issue. You can also post on [StackOverflow](https://stackoverflow.com/questions/tagged/azure-ad-b2c) with the tag `azure-ad-b2c`.
"# azure_ad_b2c_sample" 
