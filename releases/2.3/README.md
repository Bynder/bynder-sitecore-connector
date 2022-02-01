# 2.3

### Import notice:

THIS RELEASE MAY CONTAINS BREAKING CHANGES DEPENDING ON YOUR CURRENT VERSION  
**MAKE SURE TO READ THE DOCUMENTATION BEFORE UPGRADING!!**

### Breaking changes:

- No breaking changes with version 2.2

### Enhancements:

- Webdam is now supported for the importer functionality.
- OAuth application scope `asset:write` is no longer required.
- Released a separate Webdam C# SDK library that third parties may use to connect to Webdam. Available on NuGet.

### Bug fixes:

- The `Description` property was missing on the `CompactViewResponse` model (used in Bynder Asset Fields).