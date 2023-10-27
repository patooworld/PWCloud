This article outlines the differences between the [github.com/microsoft/azuredatastudio](https://github.com/microsoft/azuredatastudio) GitHub project (referred to as Code - OSS here) and Microsoft's [Azure Data Studio](https://learn.microsoft.com/azure-data-studio/what-is-azure-data-studio) distribution.

[The Code - OSS Repository](https://github.com/microsoft/azuredatastudio/wiki/Differences-between-the-repository-and-Azure-Data-Studio#the-code---oss-repository)
The [Code - OSS](https://github.com/microsoft/azuredatastudio) repository is where we (Microsoft) develop the open-source editor upon which we build the [Azure Data Studio](https://learn.microsoft.com/azure-data-studio/what-is-azure-data-studio) product. We contribute source code and manage issues in this repository. We also maintain the wiki, respond to discussions, and provide information about contributing to the product. The source code in this repository is available to everyone under a standard [MIT license](https://github.com/microsoft/azuredatastudio/blob/main/LICENSE.txt).

[Azure Data Studio](https://github.com/microsoft/azuredatastudio/wiki/Differences-between-the-repository-and-Azure-Data-Studio#azure-data-studio)
[Azure Data Studio](https://learn.microsoft.com/azure-data-studio/what-is-azure-data-studio) is a distribution of the Code - OSS repository with Microsoft specific customizations, including additional source code and extensions, released under a traditional [Microsoft product license](https://learn.microsoft.com/en-us/azure-data-studio/license).

[What Goes Where?](https://github.com/microsoft/azuredatastudio/wiki/Differences-between-the-repository-and-Azure-Data-Studiowhat-goes-where)
We take great care to ensure that no proprietary code or assets are accidently contributed to the Code - OSS repo, so that it can be cloned and freely used by anyone in the community.

Our guiding principle is that everything should be open source. If it isn't open source, it must be cleanly separated from the Code - OSS repository so that it is always possible to fork the repo and build a functional editor.

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

Visual Studio Marketplace Integration | The Visual Studio Marketplace is a service that we provide to users of the Visual Studio family of products (Visual Studio for Windows and Mac, Visual Studio Code, and Azure DevOps, formerly Visual Studio Team Services). The Marketplace not only provides discovery and hosting services, it provides ratings, comments, Q&A, publisher verification, virus scanning, conflict resolution services, payment services for Azure DevOps extensions, as well as support for publishers. The Marketplace is not a general purpose store designed to support any distribution or a subset of distributions. Access to the Marketplace is governed by the Marketplace Terms of Use. | product.json includes URLs that point to the Visual Studio Marketplace.

Extension Recommendations | We maintain a curated list of "important" and "general" extension recommendations that we recommend installing with Azure Data Studio. | product.json includes an array of extension IDs.
Remote Development | Portions of the Remote Development extensions are used in developer services that are run under a proprietary license. While these extensions do not require these services to work, there is enough code reuse that the extensions are also under a proprietary license. While the bulk of the code is in the extensions and in the Code - OSS repository, a handful of small changes are in the Azure Data Studio distribution. | Parts of the code to negotiate a connection to the Azure Data Studio server are proprietary.

Enable Proprietary Debug Adapters, Visual Studio Code Server | Certain Microsoft extensions (e.g. C#/.NET debugger) and the Visual Studio Code Server are distributed under a license that restricts their use to the Visual Studio family of products (Visual Studio, Visual Studio Code, or Visual Studio for Mac). | The distribution includes a native node module that allows the extension to use a "handshake" to adhere to the license by ensuring it is being used from Visual Studio Code.

Extensions Using Proposed APIs | We follow a strict API process based on proposed APIs (Extension API Process). Because proposed APIs often break, we generally do not allow that any extension using proposed APIs can be published to the Marketplace and loaded into Stable builds. We do however maintain a small list of extensions that are exempted from this rule because the publisher (typically Microsoft) has agreed to track the API changes. | product.json includes a list of extension IDs that can be published to the Marketplace and use proposed APIs.

Telemetry, Surveys, Crash Reporting | Microsoft collects anonymous usage statistics, survey data, and crash dumps to help improve the quality of the product. No personally identifiable information is gathered. Any data we do collect, such as crash dumps, is persisted in compliance with GDPR guidelines (FAQ). You can disable telemetry collection, please see our FAQ for more details. | The App Insights telemetry keys, Hockey App URLs, Experiment URLs, and Survey probabilities and URLs are stored in product.json and are private so that other distributions cannot send us telemetry and we can maintain GDPR compliance.

Update Services	 | Azure Data Studio periodically checks with a service that we host to see if there are updates to be installed | 
 product.json contains the URL of the update service.
