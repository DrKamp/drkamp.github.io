### Starting status

- Fresh install from of GLPI in the cloud
- admin is the default admin of the GLPI tenant
- Having and admin account on AzureAD

### Steps

<p class="callout info"><span style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Oxygen, Ubuntu, Roboto, Cantarell, 'Fira Sans', 'Droid Sans', 'Helvetica Neue', sans-serif; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400;">Use the help file </span>[https://glpi-plugins.readthedocs.io/en/latest/oauthsso/entra.html](https://glpi-plugins.readthedocs.io/en/latest/oauthsso/entra.html)<span style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Oxygen, Ubuntu, Roboto, Cantarell, 'Fira Sans', 'Droid Sans', 'Helvetica Neue', sans-serif; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400;"> </span></p>

#### Setup Basic SSO information in GLPI

##### Install Oauth SSO Plugin

- `Setup > Plugins > Discover > SSO > Oauth SSO`
- ⚠️ *Do not install imap SSO*

##### Configure External Authentications

- `Setup > Authentication > Setup`
    - Set `Automatically add users from an external authentication source` to **YES**

- Setup `> Authentication > Others authentication methods`
    - Match the fields in GLPI by those sent in the URL by the ID Provider  
        Use the PowerShell commands `Connect-AzureAD` and `Get-AzureADUser -ObjectId user@dom.fr | Get-Member` to get a list of all the fields used in Azure to be matched again GLPI fileds
    - Configure the fields in Other euthentication sent in the HTTP request

<details id="bkmrk-other-authentication"><summary>Other authentication sent in the HTTP request</summary>

<p class="callout info">If not mentionned : Leave the field empty</p>

<table border="1" style="border-collapse: collapse; width: 100%; height: 188.375px;"><colgroup><col style="width: 50.0642%;"></col><col style="width: 50.0642%;"></col></colgroup><thead><tr style="height: 29.7969px;"><td style="height: 29.7969px;">Field</td><td style="height: 29.7969px;">Value </td></tr></thead><tbody><tr style="height: 29.7969px;"><td style="height: 29.7969px;">Field Storage of the login in HTTP request</td><td style="height: 29.7969px;">HTTP\_AUTH\_USER</td></tr><tr style="height: 29.7969px;"><td style="height: 29.7969px;">Remove the domain of logins</td><td style="height: 29.7969px;">**NO**</td></tr><tr style="height: 35.3906px;"><td style="height: 35.3906px;">Surname</td><td style="height: 35.3906px;">Surname

</td></tr><tr style="height: 29.7969px;"><td style="height: 29.7969px;">FirstName</td><td style="height: 29.7969px;">GiveName</td></tr><tr><td>Email</td><td>mail</td></tr><tr><td>Phone</td><td>TelephoneNumber</td></tr><tr><td style="height: 33.7969px;">Mobile Phone</td><td style="height: 33.7969px;">Mobile</td></tr><tr><td>Title</td><td>JobTitle</td></tr><tr style="height: 33.7969px;"><td style="height: 33.7969px;">LanguageLanguage  
</td><td style="height: 33.7969px;"> </td></tr></tbody></table>

</details>##### Configure External Authentications

`Setup > Plugins > Oauth SSO applications`

- Add an app `+ add` (top of the screen) 
    - Give a name : **AzureAD - GLPI**
    - Active : **YES**
    - Select an Icon
    - Client Secret : admin password by default &gt; **Will be changed later**
    - Fetch info from user profile : **YES**
    - **⚠️** Callback URL **: Copy** and make a note of it
    - Oauth provider : **Azure**
    - Field used as login **: User Principal Name**
    - Endpoint version : **V2.0**
    - **✅** Validate with `+ Add`

### Manage link in AzureAD

`Azure > Admin > Entra >Identity > Applications > App registration`

- New Registration 
    - Give a name : **GLPI cloud SSO**
    - Supported Account types : **Organizational directory only**
    - Redirect URI : **WEB / Return url** (use Callback URL in previous step)
    - ✅ Validate by clicking on **Register**

- **Certificate and secrets**
    - <p class="callout warning">When creating the secret, the Secret\_ID and Value are only visible ONCE... Note them</p>
    - In the `GLPI cloud SSO` app, select `Certificates & secrets`
    - Create a `New Client Secret`
        - Give it a description and expiry date (Oauth\_SSO\_Azure\_GLPI\_2024 / 18 months)
        - ✅Validate with `Add`
    - Value : xxx
    - Secret\_ID : xxx

- Token configuration 
    - In the `GLPI cloud SSO` app, select `Token configuration`
        - Add optional claims of ID type &gt; select the 4 fileds `email, family_name, given_name, upn`
- API premissions 
    - Click on `Microsoft Graph` (parent of properties)
    - Select `email, Offline_access, profile, user.read` (at the bottom)
    - ✅ Validate with `Update permissions`

- Take not of the following informations in the Overview panel of the app

[![image.png](https://doc.snapi.fr/uploads/images/gallery/2024-04/scaled-1680-/RHdCQeuIGOUm88Kc-image.png)](https://doc.snapi.fr/uploads/images/gallery/2024-04/RHdCQeuIGOUm88Kc-image.png)

### Setup GLPI

- Return to `Setup > Plugins > Oauth SSO applications`
- Modify the 3 fields with the info provided by Azure 
    - Tenant ID = Directory (Tenant) ID `<strong>A</strong>`
    - Client ID = Application (client) ID `<strong>B</strong>`
    - Licnet secret = **VALUE** of the secret created before ( ⚠️ not the Secret\_ID, but its value)
