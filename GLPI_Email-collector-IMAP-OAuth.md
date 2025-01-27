### Starting status

- Fresh install from of GLPI in the cloud
- admin is the default admin of the GLPI tenant
- Having and admin account on AzureAD
- ➕ SSO with AzureAD is already Setup

### STEPS

<p class="callout info">Use the help file [https://glpi-plugins.readthedocs.io/en/latest/oauthimap/index.html](https://glpi-plugins.readthedocs.io/en/latest/oauthimap/index.html)</p>

#### GLPI : Install the plugin

- As Admin : `Setup > Plugins > Discover > SSO > Oauth IMAP`
- Install the plugin
- Add an app `+ add` (top of the screen) 
    - Give a name : **AzureAD Collecteur**
    - Active : **YES**
    - Oauth provider **: Azure**
    - Client ID / Client Secret / Tenant ID : **Will be changed later**
    - **⚠️** Callback URL **: Copy** and make a note of it
    - **✅** Validate with `+ Add`

#### AzureAD Create a new App

`Azure > Admin > Entra >Identity > Applications > App registration`

- New Registration 
    - Give a name : **GLPI Collecteur**
    - Supported Account types : **Organizational directory only**
    - Redirect URI : **WEB / Return url** (use Callback URL in previous step)
    - ✅ Validate by clicking on **Register**

- **Certificate and secrets**
    - <p class="callout warning">When creating the secret, the Secret\_ID and Value are only visible ONCE... Note them</p>
    - In the `GLPI collectueur` app, select `Certificates & secrets`
    - Create a `New Client Secret`
        - Give it a description and expiry date (Oauth\_SSO\_Azure\_GLPI\_2024 / 18 months)
        - ✅Validate with `Add`
    - Value : xx thi-is-my-value
    - Secret\_ID : this-is-my-secret\ID

- - Take not of the following informations in the Overview panel of the app
    
    [![image.png](https://doc.snapi.fr/uploads/images/gallery/2024-04/scaled-1680-/RHdCQeuIGOUm88Kc-image.png)](https://doc.snapi.fr/uploads/images/gallery/2024-04/RHdCQeuIGOUm88Kc-image.png)

#### Change rights fir the App

- App &gt; API Permissions
- Click on Microsoft Graph (1) and add the following rights (visibile in the `mail` section)

[![image.png](https://doc.snapi.fr/uploads/images/gallery/2024-04/scaled-1680-/Gbj06CapTWyFWx4S-image.png)](https://doc.snapi.fr/uploads/images/gallery/2024-04/Gbj06CapTWyFWx4S-image.png)<span style="color: rgb(34, 34, 34); font-family: var(--font-heading, var(--font-body)); font-size: 2.333em; font-weight: 400;">Setup GLPI</span>

- Return to `Setup > Plugins > Oauth IMAP applications`
- Modify the 3 fields with the info provided by Azure 
    - Tenant ID = Directory (Tenant) ID `<strong>A</strong>`
    - Client ID = Application (client) ID `<strong>B</strong>`
    - Client secret = **VALUE** of the secret created before ( ⚠️ not the Secret\_ID, but its value)

- In the `Oauth authorization` tab : 
    - `+ Create an authorisation`
    - Use the invites to log in and accept the settings

- Set up the reciever as any other reciever `Setup > Reciever > +Add`
    - Server : **outlook.office365.com**
    - Connection option : **AzureAD Collecteur SSL --- NO-VALIDATE-CRT --- ---**
    - Incoming folder **: empty**
    - Port : e**mpty**
    - Login : Select `Create a new connection` &gt; Follow the invites
