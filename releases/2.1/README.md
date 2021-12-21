# 2.1

### Import notice:

THIS RELEASE CONTAINS BREAKING CHANGES.
**MAKE SURE TO [READ THE DOCUMENTATION](https://github.com/ParTech/Bynder-Sitecore-Connector-Releases/tree/master/documentation) BEFORE UPGRADING!!**

### Breaking changes:

- We are no longer supporting Sitecore 9.0 to 9.2. This release will not run on those versions of Sitecore.
  - If you need to use the connector on one of these versions, please contact Bynder Support for assistance.
- OAuth 1 authentication is no longer supported.
- Fields from the `Environment` template related to OAuth 1 have been removed and new fields related to OAuth2 have been added.

### Enhancements:

- Updated to latest version of Bynder C# SDK.
- Implemented OAuth2 support which is the preferred authentication method by Bynder.
- Implemented Asset Tracker support.
- Added a progress indicator for the import process.
- Assets that are not public are no longer imported if `Skip Blobs` is enabled. 
- Renamed template `Movie` to `Video`.
- Imported video assets now include their thumbnail and preview URL.
- `Derivatives to import` and `Skip Blobs` settings can now be overridden on Bynder Media Folder items.
- Improved logging.

### Bug fixes:

- Certain media information such as width, height and description was not being stored in Sitecore.

### Hotfixes:

- **2.1.1** : Incorrect SDK build caused Newtonson.Json exception in the Bynder Asset Fields.