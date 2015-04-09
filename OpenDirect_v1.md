#OPENDIRECT
##API SPECIFICATION VERSION 1.0

##Introduction
AOL, Yahoo!, Microsoft, and Yieldex created the OpenDirect working group in June 2013 to develop and support a standard for the programmatic selling and buying of guaranteed digital ad inventory. The OpenDirect API specification will allow buyers who want to access guaranteed inventory via automated processes avoid multiple, costly, custom integrations. This document is the culmination of those efforts.

##About the Working Group
The working group consisted of Technical Analysts, Product Managers, and System Architects from AOL, Yahoo!, MicroTsoft, and Yieldex. The group was later joined by MediaMath, Bionic, and the Interactive Advertising Bureau (IAB). The working group met via weekly conference calls and quarterly in-person working sessions to finalize version 1.0 for public comment.

##Contact Information

###IAB Contact
Ramona Gonzales
ramona@iab.net

###OpenDirect Contact
Lucas Black, AOL
lucas.black@teamaol.com

##License/Intellectual Property Notice
Upon any person or entity’s request, AOL, Yahoo!, Microsoft, Yieldex, Bionic, MediaMath, and IAB (“Contributors”) agree to offer such person or entity, under such Contributor’s necessary patent claims, a no-charge, royalty free, fully paid-up, non-exclusive license under and to such Contributor’s necessary patent claims on reasonable and non-discriminatory terms for purposes of implementing any this specification. Such license may be subject to the condition of reciprocity by the licensee with respect to, among other things, a license to be granted by such licensee to such party with respect to such licensee’s necessary patent claims and other reasonable and nondiscriminatory terms.

THE CONTRIBUTIONS AND SPECIFICATION ARE PROVIDED "AS IS." THE ENTIRE RISK AND LIABILITY WITH RESPECT TO THE IMPLEMENTATION OR ANY OTHER USE OR EXPLOITATION OF ANY CONTRIBUTION, DRAFT SPECIFICATION OR FINAL SPECIFICATION ARE ASSUMED BY THE IMPLEMENTER, USER AND EXPLOITER. EACH CONTRIBUTOR EXPRESSLY DISCLAIMS ANY WARRANTIES (EXPRESS, IMPLIED, OR OTHERWISE), INCLUDING, WITHOUT LIMITATION, IMPLIED WARRANTIES OF MERCHANTABILITY, NON-INFRINGEMENT, FITNESS FOR A PARTICULAR PURPOSE, OR TITLE, RELATED TO ANY CONTRIBUTION, DRAFT SPECIFICATION OR FINAL SPECIFICATION. IN NO EVENT WILL ANY CONTRIBUTOR BE LIABLE TO ANY OTHER CONTRIBUTOR, PERSON OR ENTITY FOR ANY LOST PROFITS OR ANY FORM OF INDIRECT, SPECIAL, INCIDENTAL, OR CONSEQUENTIAL DAMAGES OF ANY CHARACTER FROM ANY CAUSES OF ACTION OF ANY KIND WITH RESPECT TO THIS AGREEMENT OR ANY SUBJECT MATTER OF THIS AGREEMENT, WHETHER BASED ON BREACH OF CONTRACT, TORT (INCLUDING NEGLIGENCE), OR OTHERWISE, WHETHER OR NOT ANY CONTRIBUTOR, PERSON OR ENTITY HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGE, AND EVEN IF THE REMEDIES PROVIDED FOR IN THIS AGREEMENT FAIL OF THEIR ESSENTIAL PURPOSE.

##Getting Started
The OpenDirect specification defines the API that publishers should implement to support programmatic direct buying of their premium guaranteed ad inventory. Implementing the API lets buyers write a single client that can access inventory from multiple publishers without the need to write custom code for each integration. Implementing the API would make integrating with a publisher more attractive.

Version 1 of the specification provides the basic features required to support programmatic buying of guaranteed inventory. For example, the API supports getting and searching product inventory, determining pricing and availability, applying targeting and frequency constraints, creating orders, adding lines to orders, uploading creative, assigning creatives to lines, and reserving and booking inventory.

The API is a RESTful API that supports JSON and uses OAuth to authenticate users.

The following should be noted:

   - The API takes an advertiser first approach. Both advertisers and agencies must sign up with the publisher and receive credentials. Advertisers may perform their own buys or provide consent to have an agency perform buys on their behalf; for an agency to perform buys on behalf of an advertiser, the advertiser must provide consent. The process of signing up and providing consent is publisher defined.
   - To book a line, the line must have a creative assigned to it. The creative may be the actual creative that the advertiser plans to run or a placeholder creative that is later replaced with the actual creative when it becomes available. However, the line will run with whichever creative is assigned to it (the actual creative or placeholder creative).

For details about the programming elements that this specification defines, see the following sections.

|    |       |
|----|-------|
| Resources | Defines the resource objects. For a diagram that shows the resource relationships, see Resource Model. |
| Common Objects | Defines the objects used by one or more resource. |
| Reference Data  | Defines the reference data objects and possible values. Reference data provides enumerated values for a resource property. |
| Collection Objects | Defines the collection objects that GET calls return. |
| URIs | Defines the URI and supported HTTP verbs for each resource.  |
| Authentication | Defines the authentication scheme that publisher must use. |
| Versioning | Defines the versioning scheme that publishers must use. |
| Error Handling | Defines the error objects that publishers must return for 400 Bad Request. |
| Reporting | Defines the reporting URIs and objects. |
| Workflow | Outlines the calls required to create an order. |

##Resources
The OpenDirect API is a RESTful API that supports JSON. This section defines the JSON resource objects used by the API. For a diagram that shows the relationships between these resources, see Resource Model.

For a list of URIs that use these resources, see URIs.

###Account
Defines an Account resource. An Account associates an advertiser with a buyer. A buyer is typically an agency but may also be the advertiser. A buyer may be associated with one or more advertisers and an advertiser may be associated with one or more buyers. 

Before an agency may create accounts and perform buys on behalf of the advertiser, the advertiser must give permissions to the agency. The process of giving or removing permissions is publisher defined. Creating an account must fail if the advertiser has not given the agency permissions. 

The Account owns the orders and creatives.

| Property     | Type   | Constraints                  | Add       | Update    | Publisher Requirement | Description                                                                                                                                                                                                                                                                                                                                          |
|--------------|--------|------------------------------|-----------|-----------|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AdvertiserId | String | Maximum of 36 characters.    | Required  | Read-only | Must support          | An ID that identifies the organization that is acting as the advertiser.                                                                                                                                                                                                                                                                             |
| BuyerId      | String | Maximum of 36 characters.    | Required  | Read-only | Must support          | An ID that identifies the organization that is acting as the buyer. If the advertiser is performing their own buys, AdvertiserId and BuyerId must be the same.                                                                                                                                                                                      
| Id           | String | Maximum of 36 characters.    | Read-only | Read-only | Must support          | A system-generated opaque ID that uniquely identifies this resource.                                                                                                                                                                                                                                                                                 |
| Name         | String | Maximum of 255 characters    | Required  | Optional  | Must support          | The name of the account. Used for display purposes.                                                                                                                                                                                                                                                                                                  |
| ProviderData | String | Maximum of 1,000 characters. | Optional  | Optional  | May support           | An opaque blob of provider-defined data. Providers may use this field as needed (for example, to store an ID that correlates this object with resources within their system). Note that any provider that edits this object may override the data in this field. The data should include a marker that you can identify to ensure the data is yours. |

Notes: A organization may organize accounts by brand, subsidiaries, divisions, or anything else that supports their model. 

###Assignment
Defines an Assignment resource. An Assignment associates a creative with a line of the order.

A creative may be assigned to one or more lines and a line may be assigned one or more creatives. 

| Property     | Type   | Constraints                       | Add       | Update    | Publisher Requirement | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|--------------|--------|-----------------------------------|-----------|-----------|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CreativeId   | String | Maximum of 36 characters.         | Required  | Read-only | Must support          | The ID of the creative to display when the line runs.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| Id           | String | Maximum of 36 characters.         | Read-only | Read-only | Must support          | A system-generated opaque ID that uniquely identifies this resource.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| LineId       | String | Maximum of 36 characters.         | Required  | Read-only | Must support          | The ID of the line that will display the creative.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ProviderData | String | Maximum of 1,000 characters.      | Optional  | Optional  | May support           | An opaque blob of provider-defined data. Providers may use this field as needed (for example, to store an ID that correlates this object with resources within their system). Note that any provider that edits this object may override the data in this field. The data should include a marker that you can identify to ensure the data is yours.                                                                                                                                                                                                                                                                                                                        |
| Status       | String |                                   | Read-only | Read-only | Must support          | A value that determines whether the creative serves. The following are the possible values. • Active—The creative may serve. Set at create time. • Inactive—The creative may not serve. Set by the disable verb. The status may not transition from Inactive to Active.                                                                                                                                                                                                                                                                                                                                                                                                     |
| Weight       | Byte   | Numeric value from 1 through 100. | Optional  | Optional  | Should support        | Determines how much the creative is displayed relative to the other creatives assigned to the same line. To provide even rotation, do not specify a weight. If weight is specified, all assignments that specify the same line must specify a weight and the weight of all the assignments must add up to 100.  If the weight of all assignments does not add up to 100, even rotation is applied. Assignments with heavier weight get proportionally more rotation compared to those with lesser weight. For example, if the line has 2 creatives, A and B, assigned with the same dates, and A has weight 25 and B has weight 75, B will serve three times as often as A. |

Notes: The assignment must fail if the following are true.

  - The language of the creative does not match the language of the product (the line identifies the product). 
  - The specified maturity level of the creative does not match the maturity level of the product specified by the line.

A creative must be assigned to a line before the line may be booked.

To change the creative assigned to a line, first assign a new creative to the line to ensure that the line continues to deliver and then use the disable verb to set the current assignment to Inactive.

To display different creatives at different times, add a line for each creative. If weighting is used, providers should indicate in the user interface whether all assignments for a line specify a weight and that the sum of all weights is 100.

###Creative
Defines a Creative resource. The Creative provides information about an ad. 

To assign a creative to a line, see Assignment.

| Property     | Type   | Constraints               | Add      | Update    | Publisher Requirement | Description                                                                                                                                 |
|--------------|--------|---------------------------|----------|-----------|-----------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| AccountId    | String | Maximum of 36 characters. | Required | Read-only | Must support          | The ID of the account that owns the creative.                                                                                               |
| AdFormatType | String |                           | Required | Read-only | Must support          | The ad’s format. For a list of possible values, see AdFormatType. The product that the line specifies must support the specified ad format. |
| AdQualityRejectionReason | String |   | Read-only | Read-only | Must support | The reason why the creative audit did not approve the creative.                                                                                                                                                                                                                                                                                                                                       |
| AdQualityStatus          | String |   | Read-only | Read-only | Must support | A status value that indicates where in the audit process the creative is. The following are the possible values. Pending – The creative was submitted and is either waiting for review or is in the process of being reviewed. Approved – The Creative passed the review. Rejected – The creative failed the review. The AdQualityRejectionReason field contains the reason why it failed the review. |
| BackupFlashAsset | String |   | Optional | Read-only | Should support | A base64 string that contains the backup Image in case the user’s browser does not support Flash. The image must be of one of the following mime types. • GIF • JPEG • PNG The CreativeAsset property contains the Flash creative. The publisher’s documentation should indicate any size constraints. If the asset exceeds the constraint, the publisher must return error code, BackupCreativeTooLarge. |
| ClickUrl | String |   | Required | Optional | Must support | The URL of a webpage that the user is taken to if they click the ad. |
| CreativeAsset | String |   | Required | Read-only | Must support | A string that contains the creative. The AdFormatType determines whether the string is a character string or a base64 string. Image and Flash creatives, must use base64 strings and all others (tags, text, and video) use character strings.If the creative is an image, it must be of one of the following mime types. |
| Geometry           | Size    |                             | Required  | Read-only | Must support   | The size of the creative.                                                                                                                                                                                                                                                                                                                            |
| HttpsCompatible    | Boolean |                             | Optional  | Optional  | Should support | A Boolean value that determines whether the creative can,properly render on an HTML web page served over HTTPS. Defaults to False.                                                                                                                                                                                                                   |
| Id                 | String  |                             | Read-only | Read-only | Must support   | A system-generated opaque ID that uniquely identifies this resource.                                                                                                                                                                                                                                                                                 |
| Language           | String  |                             | Required  | Optional  | Must support   | The ISO 639-1 language code that identifies the language,used in the ad. For example, if the ad uses English, the ISO code would be EN.                                                                                                                                                                                                              |
| MaturityLevel,,,,, | String  |                             | Optional  | Optional  | May support    | The maturity level of the creative content. The following are the possible values. · Children · General · Mature At assignment time, the assignment must be rejected if the specified maturity level does not match the maturity level of the product specified by the line. The default is General.                                                 |
| Name               | String  |                             | Required  | Optional  | Must support   | The display name of the creative.                                                                                                                                                                                                                                                                                                                    |
| ProviderData       | String  | Maximum of 1,000 characters | Optional  | Optional  | May support    | An opaque blob of provider-defined data. Providers may use this field as needed (for example, to store an ID that correlates this object with resources within their system). Note that any provider that edits this object may override the data in this field. The data should include a marker that you can identify to ensure the data is yours. |

Notes: Updates to the creative are not allowed for V1.

###Line
Defines a Line resource. A Line specifies the ad product to book, the quantity, and when the line runs.

For information about assigning creatives to a line, see Assignment.

{{ missing table }}

###Order
Defines an Order resource. The Order specifies the plan’s start and end dates, estimated budget, currency, and preferred billing method. 

To specify the details of the order, use the Line resource.

{{ missing table }}

###Organization
Defines an organization resource. The organization resource may represent an advertiser or agency (buyer). The Account determines the role that the organization plays. The organization’s role may vary by account. For example, the organization may be an advertiser in one account and a buyer in another. 

{{ missing table }}

Notes: An advertiser may create one or more organizations to meet their business needs. For example, they may create a single organization and then create accounts for each brand, subsidiary, or division. Or, they may create an organization for each brand. It is up to the advertiser to determine how they use Organization and Account to meet their organizational needs.

###Product
Defines a Product resource. A Product identifies anything from an ad placement to a Run of Network product in the publisher’s product catalog. 

| Property                 | Type     | Constraints                                                                                   | Publisher Requirement | Description                                                                                                                                                  |
|--------------------------|----------|-----------------------------------------------------------------------------------------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ActiveDate               | String   | Maximum of 26 characters.                                                                     | Should support        | The date and time, in UTC, that the product may become part of the bookable inventory.                                                                       |
| AdFormatTypes            | String[] |                                                                                               | Must support          | A list of ad types that the product supports. For a list of possible values, see AdFormatType.                                                               |
| BasePrice                | Decimal  |                                                                                               | Must support          | The product’s base retail price; this is not the rate card price. The actual price may be more if targeting is specified.                                    |
| Currency                 | String   |                                                                                               | Must support          | The currency that the BasePrice and MinSpend properties are specified in. For a list of possible currencies, see Currency.                                   |
| DeliveryType             | String   | Maximum of 10 characters.                                                                     | Should support        | The type of delivery. For example, exclusive or guaranteed. For a list of possible values, see DeliveryType.                                                 |
| Description              | String   | Maximum of 256 characters.                                                                    | May support           | The product’s description.                                                                                                                                   |
| Domain                   | String   | Maximum of ?? characters.                                                                     | Should support        | The product’s domain. For example, yahoo.com.                                                                                                                |
| EstimatedDailyAvails     | String   |                                                                                               | Should support        | An estimated range of available daily impressions. The ranges should be of the form: Thousands, Tens of Thousands, Hundreds of Thousands, and so on.         |
| Geometry                 | Size[]   |                                                                                               | Must support          | A list of ad format sizes that the product supports.                                                                                                         |
| HttpsCompatible          | Boolean  |                                                                                               | Should support        | A Boolean value that determines whether the product supports creatives that can properly render on an HTML web page served over HTTPS.                       |
| Icon                     | String   | Publishers should support icons that are 150x150 or less. The maximum size is 10 KB.          | May support           | URL to a thumbnail icon of the product. May be used to display next to the product in the product catalog.                                                   |
| Id                       | String   | Maximum of 36 characters.                                                                     | Must support          | A system-generated opaque ID that uniquely identifies this resource.                                                                                         |
| InventoryType            | String[] |                                                                                               | Should support        | A list of devices that the product may serve on. For a list of possible values, see Inventorytype. The default is Desktop.                                   |
| Languages                | String[] |                                                                                               | May support           | A list of creative languages that the product supports.                                                                                                      |
| LeadTime                 | Short    |                                                                                               | May support           | The number of days (n) from today that a line that reference this product can begin running; the line’s start date must be equal to or later than today + n. |
| Name                     | String   | Maximum of 38 characters.                                                                     | Must support          | The product’s display name.                                                                                                                                  |
| The name must be unique. |          |                                                                                               |                       |                                                                                                                                                              |
| MaturityLevel            | String   |                                                                                               | May support           | The maturity level of the publisher’s content. For a list of possible values, see MaturityLevel.                                                             |
| MaxDuration              | Short    |                                                                                               | Should support        | The maximum number of days that the product may be booked for. The line must enforce the duration.                                                           |
| MinDuration              | Short    |                                                                                               | Should support        | The minimum number of days that the product must be booked for. The line must enforce the duration.                                                          |
| MinSpend                 | Decimal  |                                                                                               | Should support        | The minimum amount of money that must be spent on this product in order to book it.                                                                          |
| Position                 | Byte     |                                                                                               | Should support        | The position of the ad as a relative measure of visibility or prominence. For a list of possible values, see AdPosition.                                     |
| ProductTags              | String[] | The list may contain a maximum of 500 tags. Each tag may contain a maximum of 100 characters. | May                   | List of tags used for searching the product catalog.                                                                                                         |
| RateType                 | String   |                                                                                               | Must support          | The unit of measure that BasePrice is expressed in. For a list of possible values, see RateType.                                                             |
| RetirementDate           | String   | Maximum of 26 characters.                                                                     | Should support        | The date and time, in UTC, that the product may be removed from the bookable inventory.                                                                      |
| TargetTypes              | String[] |                                                                                               | Should support        | A list of IDs that identify the types of targeting that the product supports. For example, DMA or Gender.                                                    |
| TimeZone                 | String   |                                                                                               | Should support        | The time zone that the product runs in.                                                                                                                      |
| Url                      | String   |                                                                                               | Should support        | A URL to the specification that describes the creative requirements.                                                                                        |

##Common Objects

The following objects are common to one or more resources.

###Address

Defines a postal address.
| Property     | Type   | Constraint                                                                                      | Add      | Update   | Publisher Requirement | Description                     |
|--------------|--------|-------------------------------------------------------------------------------------------------|----------|----------|-----------------------|---------------------------------|
| City         | String | Maximum of 35 alpha characters. Cannot be an empty string.                                      | Required | Optional | Must support          | The city.                       |
| Country      | String | Maximum of 2 alpha characters. Must be a valid ISO 3166-1 country code.                         | Required | Optional | Must support          | The country/region.             |
| AddressLine1 | String | Maximum of 255 alphanumeric characters. Cannot be an empty string.                              | Required | Optional | Must support          | The first line of the address.  |
| AddressLine2 | String | Maximum of 255 alphanumeric characters.                                                         | Optional | Optional | Must support          | The second line of the address. |
| PostalCode   | String | Maximum of 15 alphanumeric characters. Can include a dash and space. Cannot be an empty string. | Optional | Optional | Must support          | The postal or ZIP code.         |
| State        | String | Maximum of 35 alpha characters.  Cannot be an empty string.                                     | Optional | Optional | Must support          | The state or province.          |

###Contact
Defines an agency or advertiser contact. 

{{ missing table }}

###ProductAvails
Defines the availability and pricing information that a product availability search request returns. 

{{ missing table }}

###ProductAvailsSearch
Defines the search criteria used to search for product availability and pricing information. 

{{ missing table }}

###ProductSearch
Defines the search criteria used to search the product catalog. 

{{ missing table }}

Note that product selection uses a logical AND between fields and a logical OR between field values. For example, the product is select if it supports the Flash OR Image OR Text ad format, AND supports USD currency, AND specifies the foo OR bar product tag.

At least one field must be specified.

###Size
Defines the geometry of a creative. 

{{ missing table }}

###Segment
Defines the target and target values used to search for product availability and to specify targeting for a line.

{{ missing table }}

The values for these fields come from the Target and TargetValue reference data.

##Reference Data 
This section defines the reference data that an OpenDirect API must support. Reference data provides enumerated values for a resource property. The publisher must return only those values that they support.

##AdFormatType
Defines the possible ad formats.

| Property | Type   | Description                                                                                                         |
|----------|--------|---------------------------------------------------------------------------------------------------------------------|
| Id       | String | A system-generated opaque ID that uniquely identifies this resource. The ID may contain a maximum of 36 characters. |
| Name     | String | The ad format’s display name.                                                                                       |
