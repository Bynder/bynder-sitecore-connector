# 2.2

### Import notice:

THIS RELEASE CONTAINS BREAKING CHANGES  
**MAKE SURE TO READ THE DOCUMENTATION BEFORE UPGRADING!!**

### Breaking changes:

- The data stored by Bynder asset fields uses a different JSON format than previous versions of the connector. Legacy data is still supported by the Bynder extension methods.
- The extension methods `GetBynderSingleAssetField` and `GetBynderMultiAssetsField` now return a `CompactViewResponse` object instead of a `Media` object. 
- Only public derivatives can be imported. Previous versions imported all derivatives, which was unintended and also produced unexpected results when blobs were not stored.

### Enhancements:

- Implemented the new Universal Compact View.
- Support for Webdam in Bynder asset fields.
- Allow a single asset derivative to be selected in a Bynder asset field. 
- Allow a single asset derivative to be selected for import.
- Added NuGet as distribution channel for the connector.
- Added a custom MediaRequestHandler for instances that run Sitecore Experience Accelerator (SXA).
- Certain Bynder environment settings can be overridden on the media folder item.

### Bug fixes:

- DAT URL could not be resolved for Bynder asset fields.
- `ClientId` and `ClientSecret` fields on `Environment` template were not set to _Shared_.
- Importer would cause an error if the Compact View was closed without making a selection.
- Thumbnail visual proportions in Bynder asset fields improved.
- Media items without stored blob did not function properly. 
- Incorrect media URL was resolved for items that had no blob stored.