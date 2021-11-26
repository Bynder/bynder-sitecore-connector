# 2.0.0

### Import notice:

THIS RELEASE CONTAINS BREAKING CHANGES.
**MAKE SURE TO READ THE DOCUMENTATION BEFORE UPGRADING!!**

### Breaking changes:

- We are no longer supporting Sitecore 8.x. This release will not run on those versions of Sitecore.
- Several of the enhancements in this release lead to breaking changes. Make sure to thorougly read the documentation when upgrading.

### Enhancements:

- Merged separate connector modules to one module.
- Implemented initial support for DAT on imported assets.
- Bynder Workflow is no longer enabled by default on Bynder media items.
- HTTP requests to media items without stored Blob now redirect to their Bynder CDN url.
- Bynder environment settings have been moved to a more appropiate location in the content tree.
- Added a progress UI that provides feedback during the synchronization process.
- Synchronization of previously imported items can now be forced.
- Bynder media and settings items are now unversioned and language indepenent.
- Additional logging has been added.

### Bug fixes:

- Prevent `Length cannot be zero` exception.