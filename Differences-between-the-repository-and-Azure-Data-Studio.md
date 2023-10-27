This article outlines the differences between the [github.com/microsoft/azuredatastudio](https://github.com/microsoft/azuredatastudio) GitHub project (referred to as Azure Data Studio - OSS here) and Microsoft's [Azure Data Studio](https://learn.microsoft.com/azure-data-studio/what-is-azure-data-studio) distribution.

[The Azure Data Studio - OSS Repository](https://github.com/microsoft/azuredatastudio/wiki/Differences-between-the-repository-and-Azure-Data-Studio#the-azuredatastudio---oss-repository)
The [Azure Data Studio - OSS](https://github.com/microsoft/azuredatastudio) repository is where we (Microsoft) develop the open-source editor upon which we build the [Azure Data Studio](https://learn.microsoft.com/azure-data-studio/what-is-azure-data-studio) product. We contribute source code and manage issues in this repository. We also maintain the wiki, respond to discussions, and provide information about contributing to the product. The source code in this repository is available to everyone under a standard [MIT license](https://github.com/microsoft/azuredatastudio/blob/main/LICENSE.txt).

[Azure Data Studio](https://learn.microsoft.com/azure-data-studio/what-is-azure-data-studio) is a distribution of the Azure Data Studio - OSS repository with Microsoft specific customizations, including additional source code and extensions, released under a traditional [Microsoft product license](https://learn.microsoft.com/azure-data-studio/license).

[What Goes Where?](https://github.com/microsoft/azuredatastudio/wiki/Differences-between-the-repository-and-Azure-Data-Studio#what-goes-where)
We take great care to ensure that no proprietary code or assets are accidently contributed to the Azure Data Studio - OSS repo, so that it can be cloned and freely used by anyone in the community.

Our guiding principle is that everything should be open source. If it isn't open source, it must be cleanly separated from the Azure Data Studio - OSS repository so that it is always possible to fork the repo and build a functional editor.

The small amount of assets and code that are not open source generally fall into one of the following categories:

* Trademarked assets that define the brand (e.g. logos and product names)
* Code that has a reliance on existing proprietary code or libraries
* Code that is shared with other proprietary licensed products, such as Azure Data Studio that may be used in, or provides access to, extensions hosted internally
* Pre-release code that is undergoing significant revision
* The closed source additions to the distribution make up a very small percentage of the overall product

While not open source, we believe that transparency is very important to the health of the project and community. Therefore, we want to outline "the last mile" between what you see in the repository and what is built into the Azure Data Studio distribution. Most customizations are done through the customization of product.json, but we do include a small amount of additional code and assets in the final product.

What | Why | How
-- | -- | --
Icons, Product Name (e.g. "Microsoft Azure Data Studio"), Documentation	| Microsoft trademarks, which provide brand recognition for customers and helps to avoid confusion for downstream implementations. In other words, because we protect the brands, you know it is a product from Microsoft when you see it.	| Static assets such as icons and the desktop image are included in the distro. Product names and documentation URLs are added to product.json.
Extension Recommendations | We maintain a curated list of "important" and "general" extension recommendations that we recommend installing with Azure Data Studio. | product.json includes an array of extension IDs.
Telemetry, Surveys, Crash Reporting | Microsoft collects anonymous usage statistics, survey data, and crash dumps to help improve the quality of the product. No personally identifiable information is gathered. Any data we do collect, such as crash dumps, is persisted in compliance with GDPR guidelines (FAQ). You can disable telemetry collection, please see our FAQ for more details. | The App Insights telemetry keys, Hockey App URLs, Experiment URLs, and Survey probabilities and URLs are stored in product.json and are private so that other distributions cannot send us telemetry and we can maintain GDPR compliance.
Update Services	 | Azure Data Studio periodically checks with a service that we host to see if there are updates to be installed | product.json contains the URL of the update service.
