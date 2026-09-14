# user-snippets

This Repo is the 3x main VSCode [User Snippets](https://code.visualstudio.com/docs/editor/userdefinedsnippets) & Neovim keymaps I use for 90% of the JavaScript client side use cases I get asked to do for concepts. These are designed as examples only, the methods I use in these snippets allow you to not have to include any items specific to a Genesys Cloud ORG. This way you can have the one set of code deployed on GCP, AWS, Azure etc and each environment can use this code based with different regions and OAuths. Making it a fast secure and easy way to provide use cases. The OAuth used for these is [Web PKCE](https://developer.genesys.cloud/authorization/platform-auth/use-pkce) which is configured under the `Code Authorization / PKCE` Type in Genesys Cloud OAuth setup.

<p style="color: yellow">NOTE: Previously I was using Implicit Grant but PKCE is a more secure way of doing client side OAuth now days and is also now enforced for newer OAuth token creation</p>

## UPDATE:
In the coming releases the JavaScript SDK will not allow the token to be generated from within an iFrame you can see the release note [here](https://help.genesys.cloud/announcements/genesys-cloud/deprecation-ability-to-embed-the-genesys-cloud-login-web-application-within-an-iframe/) the SDK got a popup helper to handle this but as the customer you will need to host the popup HTML page yourself. I have more details on this in the updates i have made to this repo.

Details on the JavaScript SDK for Genesys Cloud can be found [here](https://mypurecloud.github.io/platform-client-sdk-javascript/) in short the SDK handles the codeVerifier and storage of that on the client. If you wish to set your own you can, but to keep things simple I am using the SDK's inbuilt capabilities to handle this codeVerifier in teh PKCE auth flow.

## Neovim Users: 
I have created a `genesys-snippets.lua` file that you can either copy the lua code directly and add to your `init.lua` file or add as a seperate file. This has key bindings for the 3x snippets in this repo as:

```
    <leader>gc1 = Basic Snippet
    <leader>gc2 = Advanced Snippet
    <leader>gc3 = Reporting Snippet
```

## VSCode Users:
To Access your personal user snippets in VSCode go to

    File -> Preferences -> User snippets

If you have not created any there will still most likely be the default ones that VSCode gives, then select

    New Global Snippets File...

give the snippet a name in my example iv called it

    genesys

Copy All the contents from the [genesys.code-snippets](./genesys.code-snippets) file in this repo. Remove ALL the text from the new template you have created and paste in this text. I have made these snippets to include the standalone HTML formatting including removing the robot crawler to spot these pages from being indexed by search engines such as Google. In a larger project its recommended to move the JS into separate config files etc but this is designed for when you get asked for a super quick turnaround to build out a POC concept.

Now if you save and close this file if you create a new HTML file and simply start to type "genesys" you will be shown the 3x user snippets to select from.

![](/docs/images/snippet.png?raw=true)

## Authentication:

In this latest version it uses PKCE OAuth with the new popup auth for iFrame support. I <strong>STRONGLY</strong> recommend you read the official doco on this [here](https://github.com/MyPureCloud/platform-client-sdk-javascript#popup-authentication)

For the popup Auth you need to build and host a HTML page for the redirect. There are 3x examples in the official documentation [here](https://github.com/MyPureCloud/platform-client-sdk-javascript/tree/master/build/samples) in my demos i have one built and hosted based on the `auth_popup_all.html` so then i only need to do it once for my demos. In the real world for security I would recommend using the `auth_popup_specifc.html` to allow additional security controls.

![](/docs/images/pkce.png?raw=true)

Ensure you have the correct Redirect URL in your allowed redirects. Eg: http://localhost:8080/index.html

Each use case will require different permissions based on the API calls you are making. Ensure you use least privilege and only request the permissions you need for best security practices.

## Basic:

This is giving you a HTML formatted file with the JS SDK for genesys cloud as well and the OAuth parameters being collected from the URL using a native JavaScript function. There is no additional JQuery etc used to keep the dependencies as minimal as possible. To run this you need to create an OAuth profile in the ORG then inside the URL you would pass the values eg:

    http://localhost:8080/index.html?gc_region=mypurecloud.com.au&gc_clientId=YOUR_CLIENT_ID&gc_redirectUrl=http://localhost:8080/auth_popup_all.html

## Advanced:

This is the same concept as the Basic one above but also includes getting the conversationId from a URL Param as well as setting up the [notification service](https://developer.genesys.cloud/api/rest/v2/notifications/notification_service) which is a websocket where you can subscribe to different events. A list of events/topics can be found [here.](https://developer.genesys.cloud/api/rest/v2/notifications/available_topics)

## Reporting:

This is the same concept as the Basic but with a framework for analytics queries and rendering them using the [Spark](https://spark.genesys.com/) web components so it looks and feels like a native UI.

## Embedding:

Most of the times the examples I write end up being embedded into the native Genesys Cloud UI through either a [Client App](https://developer.genesys.cloud/platform/integrations/client-apps/) or through a [Interaction Widget](https://developer.genesys.cloud/platform/integrations/client-apps/#interaction-widget) so ensure you have the required `iframe Sandbox Options`:

```
allow-scripts,allow-same-origin,allow-forms,allow-modals,allow-downloads,allow-popups
```
To install either the `Client App` or `Interaction Widget` you will need to add the integration:

Client App:
![](/docs/images/clientApps.png?raw=true)

Integration Widget:
![](/docs/images/interactionWidget.png?raw=true)

## NOTE:

When integration screens into things like the agent script and interactions widget or client Apps there are specific parameters you can pass to do with interactions into the URL. This makes it much easier to gather data about specific interactions and another reason why I use this method to pass in KVPs.

## Formatting

I have cleaned this code up and after much internal debate I have chosen to go with a standard of single quotes for Strings: 'string' and not having a semicolon at the end of a line: ; . While this is a hot topic online this is my personal choice. I have also moved to use a more standardized formatter over the internal VSCode formatter [Prettier](https://prettier.io/)

![](/docs/images/Extension.png?raw=true)

I have the default settings other then these 3x options

    Print Width: 200
    Semi: false
    Single Quote: true

Some of the functions have also been cleaned up with newer Operators such as The Conditional Operator (?:) This has enabled more short hand code in parts. I have also cleaned up the console.log() parts to use CSS options to make it more visual.
