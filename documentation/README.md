# Bynder Sitecore Connector documentation

The Bynder Sitecore Connector provides you with two methods of connecting your Sitecore platform to Bynder.  
By leveraging the [Bynder Field Types](#using-the-bynder-field-types) you are able to store references to Bynder assets that allow you to embed the assets on your website, always reflecting the latest version of the asset in Bynder.  
With the [Bynder Asset Import](#using-the-bynder-asset-import) you get to import assets from Bynder into the Sitecore Media Library. This allows you to apply the assets to the full range of Sitecore's features.

This documentation guides you through all aspects of the installation and usage of the Bynder Sitecore Connector.

- [Installation](#installation)
- [Upgrade instructions](#upgrade-instructions)
- [Configuration](#configuration)
- [Using the Bynder Field Types](#using-the-bynder-field-types)
- [Using the Bynder Asset Import](#using-the-bynder-asset-import)

## Installation

- [Supported Sitecore versions](#supported-sitecore-versions)
- [Installation package](#installation-package)
- [Required web.config changes](#required-webconfig-changes)

**If you are upgrading from _Bynder Basic Sitecore Connector_ or _Bynder Advanced Sitecore Connector_,**   
**skip ahead to the [Upgrade instructions](#upgrade-instructions).**

### Supported Sitecore versions

This module supports **Sitecore version 9.0 and higher**. Make sure to use the installation package that is suited for your version of Sitecore.

If you are using _Sitecore Experience Accelerator (SXA)_ or _Sitecore JavaScript Services (JSS)_ you will not be able to leverage the Bynder Field Types because they are targeted at Sitecore MVC renderings. You will however be able to use media items that are imported with the Bynder Asset Import.

### Installation package

The connector is provided as a Sitecore Installation Package.  
Simply download the `Bynder-Sitecore-Connector_X.X.X_Installation-Package_X.X-X.X.zip` package and install it using the [Sitecore Package Installation Wizard](https://doc.sitecore.com/en/SdnArchive/Articles/Administration/Installing%20Modules%20and%20Packages.html) in the Sitecore Client.
**DO NOT install a Sitecore package directly on a production environment without consulting your Sitecore system administrator!**

Make sure to choose the package that is targeted at your Sitecore version.  
We currently offer one package for Sitecore _9.3 to 10.2_ and another package for _Sitecore 9.0 to 9.2_.

We highly recommend you to install the package on a (local) environment for testing purposes first before rolling it out across other environments.  
Consult Bynder support if you need additional assistance or consultancy during the integration process.

### Required `web.config` changes

The connector requires several changes to the `web.config` of your Sitecore application in order to function correctly. You must manually apply these changes as the installation package is not capable of performing this update.

- [Override MediaRequestHandler](#override-mediarequesthandler)
- [Content Security Policy header configuration](#content-security-policy-header-configuration)

#### Override MediaRequestHandler

The connector needs to override Sitecore's default media requesthandler to facilitate redirecting to Bynder CDN uri if you are not storing Blob data (i.e. if you are not storing a copy of Bynder's binary data).

Locate the handler configuration in the `web.config` and update the handler for path `sitecore_media.ashx` to the type `Bynder.SitecoreConnector.Providers.BynderMediaRequestHandler, Bynder.SitecoreConnector` as demonstrated in this example:

```
<handlers>
  <add verb="*" path="sitecore_media.ashx" type="Bynder.SitecoreConnector.Providers.BynderMediaRequestHandler, Bynder.SitecoreConnector" name="Sitecore.MediaRequestHandler"/>

  <!-- ... snipped other headers for simplicity ... -->
</handlers>
```

#### Content Security Policy header configuration

As of version 9.3 of Sitecore, the `web.config` configures a [Content Security Policy header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy) that prevents malicious client-side requests from your web application. If you are using an older version of Sitecore, this may still apply if your system administrator applied this configuration manually.  

In order for the Bynder Sitecore Connector to be able to connect to Bynder and properly display its content, you must update your Content Security Policy (CSP) configuration to allow this.

The following Content Security Policies must be configured:

```
img-src https://YOUR-BYNDER-HOST.bynder.com;
frame-src https://YOUR-BYNDER-HOST.bynder.com; 
script-src https://d8ejoa1fys2rk.cloudfront.net; 
style-src https://d8ejoa1fys2rk.cloudfront.net; 
font-src https://d8ejoa1fys2rk.cloudfront.net; 
connect-src https://d8ejoa1fys2rk.cloudfront.net https://YOUR-BYNDER-HOST.bynder.com;
```

When applied to the **default** Sitecore `web.config` (again, this applies to Sitecore 9.3 and higher), the configuration must look like this:

```
<location path="sitecore">
  <system.webServer>
    <httpProtocol>
      <customHeaders>

        <!-- ... snipped other headers for simplicity ... -->
        <!-- ... used line breaks for clearity, DO NOT USE LINE BREAKS in the actual config! ... -->

        <add name="Content-Security-Policy" 
             value="default-src 'self' 'unsafe-inline' 'unsafe-eval'; 
                    img-src 'self' data: https://YOUR-BYNDER-HOST.bynder.com; 
                    frame-src 'self' https://YOUR-BYNDER-HOST.bynder.com; 
                    script-src 'self' 'unsafe-inline' 'unsafe-eval' https://d8ejoa1fys2rk.cloudfront.net; 
                    style-src 'self' 'unsafe-inline' https://fonts.googleapis.com https://d8ejoa1fys2rk.cloudfront.net; 
                    font-src 'self' 'unsafe-inline' https://fonts.gstatic.com https://d8ejoa1fys2rk.cloudfront.net; 
                    connect-src 'self' 'unsafe-inline' 'unsafe-eval' https://d8ejoa1fys2rk.cloudfront.net https://YOUR-BYNDER-HOST.bynder.com; 
                    upgrade-insecure-requests; block-all-mixed-content;" />

        <!-- ... make sure to replace YOUR-BYNDER-HOST with your own Bynder portal hostname ... -->

    </customHeaders>
    </httpProtocol>
  </system.webServer>
</location>
```

Make sure that your `web.config` configuration is properly updated according this description! Failure to do so will result in the connector **not being able to connect to Bynder**, the **Compact View not showing properly** or the Compact View **missing thumbnails**.

## Upgrade instructions

If you are upgrading from an older version of the connector (either the Bynder Basic Sitecore Connector or the Bynder Advanced Sitecore Connector), follow these upgrade instructions.

### 1. Backup your environment

It's **really important** that you **create a backup** of your `master` and `core` database and the `webroot` of your Sitecore application, before applying the installation package! The effect of an upgrade cannot always be predicted so you really want a rollback scenario.

### 2. Install the connector installation package

Install the Sitecore Installation Package as described in the [Installation package](#installation-package) chapter.  
If you are prompted to choose wether to overwrite or skip items, choose to overwrite.

### 3. Update your environment settings

If you were using the _Bynder Advanced Sitecore Connector_ previously, then you must recreate and relink your Bynder environment settings items.  

1. Using the Sitecore Content Editor, find your legacy settings items under `/sitecore/system/Modules/Experience Explorer/Bynder Settings`.
2. Now recreate each of those items under the new location at `/sitecore/system/Modules/Bynder/Environments` and fill in the field values that you find in the legacy items. **Important:** You must recreate them. It does not work if you move the existing items!
3. In order to relink the Bynder media folders to the new settings items, we can leverage Sitecore links for that:
    1. Delete the old settings item, e.g. `/sitecore/system/Modules/Experience Explorer/Bynder Settings/Default`.
    2. Sitecore will now prompt you that links to this item exist.
    3. Choose **Link to another item** and click the **Edit links** button.
    4. For each item listed, click **Link to Other Item** and select the new settings item from the content tree, e.g. `/sitecore/system/Modules/Bynder/Environments/Default`.
4. Your environment settings are now recreated and relinked.

### 4. Update your `web.config`

The connector requires an additional Content Security Policy (`frame-src`) and the MediaRequestHandler has been overridden. Follow the instructions from [Required web.config changes](#required-webconfig-changes) to update your `web.config` accordingly.

### 5. Disable obsolete config files

There is a new Sitecore config file for the connector which will conflict with the legacy config files.  
Rename the following legacy config files to `*.disabled` (or remove them altogether) so they no longer affect the application:

- `<webroot>/App_Config/Include/Modules/Bynder/Bynder.Sitecore.Connector.config`
- `<webroot>/App_Config/Include/Bynder/BynderSitecorePlugin.Commands.config`

### 6. Workflow changes

The Bynder Workflow is no longer active on imported media items by default.  
If you were using the Bynder Workflow and you would like to continue using that, you must manually reinstate it.

You can reinstate it by following these steps:

1. Locate the _Standard Values item_ for the the _MediaItem_ template: `/sitecore/templates/Bynder/MediaItem/__Standard Values`.
2. Select the item in the tree and open the _Review_ ribbon.
3. In the _Workflow_ section, click _Initial_ and choose the _Bynder Workflow_.

### 7. Force synchronization of previously imported assets

Because of changes to the MediaItem template, previously imported assets must be synchronized. 
Perform this by selecting the Bynder root folder in the media library (e.g. `/sitecore/media library/Bynder`) and clicking **Synchronize (forced)** in the Ribbon.

If you have multiple Bynder root folders in your media library, perform this task on each of them.

### Note on legacy media items with '-DAT' suffix

The previous version of the connector would import so called 'DAT' derivatives for assets. These derivatives did not serve any real purpose and will no longer be imported or updated through synchronization. They are **not to be confused with** assets that support _[Dynamic Asset Transformation (DAT)](https://support.bynder.com/hc/en-us/articles/360018559260-Dynamic-Asset-Transformations-DAT-)_.

If your asset has _Dynamic Asset Transformation_ enabled and you have previously imported it with the old connector, then synchronization will **not** add the DAT enabled derivative for you asset. You must import the asset again to enable DAT.  
Once reimported, the DAT derivative will be created using the '-transform' suffix (assuming your environment settings allow it). Also see: [Dynamic Asset Transformation (DAT) support](#dynamic-asset-transformation-dat-importer-support)

## Configuration

- [Configuring the Bynder Field Types](#configuring-the-bynder-field-types)
- [Configuring the Bynder Asset Import](#configuring-the-bynder-asset-import)

### Configuring the Bynder Field Types

The connector provides you with two new Sitecore field types: _Bynder Single Asset Selector_ and _Bynder Multi Asset Selector_.  
You can use these fields in your templates to allow editors to select Bynder assets while editing items based on those templates.

<img src="./images/field-types.png">

No additional configuration is required in order for editors to use these field types.

In order to use the content from these fields in Sitecore renderings, developers need to leverage a couple of C# methods that are supplied by the connector. This is described in [Using the Bynder Field values in your components](#using-the-bynder-field-values-in-your-components).

### Configuring the Bynder Asset Import

- [Create Bynder media folders](#create-bynder-media-folders)
- [Environment Settings](#environment-settings)
- [Scheduled synchronization](#scheduled-synchronization)

#### Create Bynder media folders

The connector can import assets from Bynder into Bynder media folders in Sitecore.  
By default, the installation creates one main Bynder media folder located at `/sitecore/media library/Bynder`.  

You are free to create your own subfolder structure within the Bynder root folder. 

#### Environment settings

In order to import assets from Bynder, the connector must be able to reach your Bynder portal.  
This is configured in an environment settings item.  
Aside from that, you can also configure what derivatives must be imported and wether or not the binary asset data (blob) must be stored in Sitecore.

Environment settings are stored in `/sitecore/system/Modules/Bynder/Environments` and there is one created by default.

<img src="./images/environments.png">

Configure the following information for your environment:

- **EnvironmentUrl** is the URL to your Bynder portal, for example: `https://acme.bynder.com`
- **ConsumerKey**, **ConsumerSecret**, **TokenKey** and **TokenSecret** are authentication details that are generated by your Bynder portal. See [API token](#api-token).
- **Files to import** can be used to control what derivatives are imported. It supports the values:
    - **all** is the default value and will import all available derivatives.
    - **all-derivatives** will import all available derivatives, but not the original file.
    - **original** will only import the original file.
    - _**derivative-name**_ will only import specific derivatives. You may supply multiple values by separating them with a comma.
- **Do not import BLOBs**, when this is enabled, the importer will not store the asset data in Sitecore, but only store the reference (CDN) URL for the asset. 

#### API token

Please note that the connector requires your Bynder application to **support OAuth 1 API tokens** which first **must be enabled by Bynder Support** in most cases.  
Try to follow the instructions from [Create API tokens for your app](https://support.bynder.com/hc/articles/360017186940#UUID-e905eb1e-78b7-2783-2c5f-a73fe23021a7) and if you don't see the correct options in your portal, contact Bynder Support and ask them to enable OAuth 1 support.

You may create multiple environment settings items and they can be linked to a specific Bynder media folder.  
To link environment settings to a Bynder media folder, first select the folder in the content tree so that the Bynder ribbon appears. Then click _Set Environment_ in the ribbon and select the desired enviroment.

<img src="./images/set-environment.png">

#### Scheduled synchronization

The connector provides a command and scheduled task to trigger a synchronization. 
You can configure this task in `/sitecore/system/Tasks/Schedules/Bynder/Synchronize`.

If you want to schedule multiple tasks or a task for a specific Bynder media folder, you can do so by duplicating the task item and populating the `Items` field with the ID or Path of the Bynder media folder item you which to synchronize.

General information on how to enable the Sitecore task runner and schedule a Sitecore task can be found in [this blog article](https://briancaos.wordpress.com/2017/08/18/sitecore-scheduled-task-schedule-time-format-and-other-quirks/).

## Using the Bynder Field Types

When your Sitecore system administrator or development team has configured the Bynder Field Types on your templates, they will appear in the Content Editor as such:

<img src="./images/use-field-types.png">

In order to select the asset(s) for a Bynder field, simply click _Select Single Bynder Asset_ or _Select Multiple Bynder Assets_.  
If this is the first time you use these fields, you are required to login to your Bynder portal.  
Once logged in, you are presented with the Compact View and can select the desired asset.

After you selected your asset(s), the selection will appear in the Bynder field:

<img src="./images/use-field-types2.png">

Use the _Clear Bynder Asset(s)_ link to clear the selection. This will not affect assets in Bynder, it will merely clear your selection in Sitecore.

### Using the Bynder Field values in your components

Developers that want to render Bynder Field Types in Sitecore renderings (components) can use the following Extension methods in their MVC Views:

```
@using Bynder.SitecoreConnector.Extensions

var singleAsset = Model.Item["Single Asset demo field"].ConvertToBynderAsset();
var multiAssets = Model.Item["Multi Assets demo field"].ConvertToBynderAssets();
```

The Multi Assets field will simply return a collection of single assets.

The resulting object provides access to most Bynder asset data:

```
<div>
    <h1>Bynder Single Asset example</h1>
    @if (singleAsset != null)
    {
        <p>Id = @singleAsset.Id</p>
        <p>Name = @singleAsset.Name</p>
        <p>Description = @singleAsset.Description</p>
        <p>BrandId = @singleAsset.BrandId</p>
        <p>Archive = @singleAsset.Archive</p>
        <p>Copyright = @singleAsset.Copyright</p>
        <p>Date created = @singleAsset.DateCreated.ToString("yyyy MMMM dd")</p>
        
        <!-- More properties available, but left out for simplicity's sake -->
    }
</div>
```

The resulting object also provides access to certain asset derivatives, for example:

```
<img src="@singleAsset.Thumbnails["webimage"]" />
```

### Dynamic Asset Transformation (DAT) support

The Bynder Field Type also provides access to the [Dynamic Asset Transformation](https://support.bynder.com/hc/en-us/articles/360018559260-Dynamic-Asset-Transformations-DAT-) URL of an asset if that is available in Bynder.  
This URL is stored in the `transformBaseUrl` thumbnail:

```
// Example of how to apply Dynamic Asset Transformation
string transformUrl = singleAsset.Thumbnails["transformBaseUrl"];
string transformations = "?io=filter:sepia";

<img src="@(transformUrl + transformations)">
```

Looking for information on DAT support for the Bynder Asset Import? See [Dynamic Asset Transformation (DAT) importer support](dynamic-asset-transformation-dat-importer-support).

## Using the Bynder Asset Import

- [Importing assets](#importing-assets)
- [Synchronizing assets](#synchronizing-assets)

### Importing assets

In order to import assets, first select a Bynder media folder in the media library and then click one of the _Import from Bynder_ buttons.

<img src="./images/import-button.png"> <img src="./images/import-button2.png">

If this is the first time you are using the connector, you will be required to login to your Bynder portal first.  
You are now presented with the [Compact View](https://support.bynder.com/hc/en-us/articles/360014369640#UUID-eb5b394d-4148-0471-d3bb-cabb368ca003) and are able to search and select the assets your wish to import.

### Synchronizing assets

If you want to update previously imported assets with changes made in Bynder, you can synchronize instead of importing them all over.
Simply select the Bynder folder (or specific asset) you would like to update and click the _Synchronize_ button in the Bynder ribbon.

<img src="./images/synchronize.png">

Synchronization may also be scheduled using a Sitecore scheduled task. See: [Scheduled synchronization](#scheduled-synchronization)

### Dynamic Asset Transformation (DAT) importer support

If an asset with DAT support is imported, the connector will create an item with the `-transform` suffix. Note that these transform items are not updated through synchronzation, but only appear once after an import.

The way transformations can be applied to these items is to add the transform configuration to the query string of the media item URL.  
This works in exactly the same manner as it does for assets that are in Bynder, as described in the [Bynder Knowledge Base article](https://support.bynder.com/hc/en-us/articles/360018559260-Dynamic-Asset-Transformations-DAT-).

Let's take a look at the following example:  
We imported the _Delicious pizza_ asset which resulted in two derivative items in Sitecore: _original_ and _transform_.

<img src="./images/transform-item.png">


The Sitecore media URL for the transform item is `https://mysitecorehostname/-/media/Bynder/Delicious-pizza-transform.ashx`.  
When we request that URL, it will return the original asset because no transformations have been applied.

<img src="./images/transform-1.png">


Now let's add a simple sepia filter transformation: `https://mysitecorehostname/-/media/Bynder/Delicious-pizza-transform.ashx?io=filter:sepia`

<img src="./images/transform-2.png">


And just like with assets in Bynder, we can add as many transformations as we'd like: `https://mysitecorehostname/-/media/Bynder/Delicious-pizza-transform.ashx?io=filter:sepia&io=overlay:box,color:red,opacity:20&io=overlay:text,content:DAT%20is%20awesome,size:250,color:white,gravity:center`

<img src="./images/transform-3.png">

### Importer limitations

Currently we don't support importing audio assets from Bynder to Sitecore.

Only basic asset information such as width, height and the MIME type are imported with the asset. Bynder metaproperties or tags are not imported.