# Bookings

When a booking is committed in Citybreak, a booking version is created. That version is assigned a globally unique id, known as bookingVersionId or reservationVersionId. The booking is also assigned a booking code. The format is four letters and two digits (ex AAAA01). That particular version is datestamped when it was committed into a reservation. This is known by the BookingDate. 

When a booking is changed, a brand new booking version is created with a complete different bookingVersionId with the same booking code. This booking version will mark all products that are removed as Removed, all products not changed as NotChanged and new products with status New.

ReservationVersionId is non-sequential, so to simplify batch retrieval operations we provide a sequential SequenceId to be used instead.

## Actors
There are several actors in a booking. What you probably are looking for is to analyze the organizers view. The organizer is the "owner" of the booking. There are also other actors that you may want to pay attention to. 

Actor | Description
-------- | --------
Supplier | The organization that owns a product. There may be several suppliers in a booking.
Agent | The organization that makes a reservation on behalf of a end customer. This may be omitted or the same organization as the organizer.
Seller organization | This is a different name for the organizer.
Point of sales | This is the "location" or the business unit that performs the reservation. It could be the online point of sales or a specific call center.

## GetDataResult / GetManyResult object
This result object simply wraps the BookingInformationResult object(s). We communicate the result of the authentication through this object.

```
       /// <summary>
       /// Gets or sets the booking result if the status of the authentication was Ok
       /// </summary>
       /// <value>
       /// The booking.
       /// </value>
       public BookingInformationResult BookingResult { get; set; }

       /// <summary>
       /// Gets the authentication status of the operation
       /// </summary>
       /// <value>
       /// The authentication status.
       /// </value>
       public AuthenticationStatus Status { get; set; }

       /// <summary>
       /// Gets or sets the key expire date.
       /// </summary>
       /// <value>
       /// The key expire date.
       /// </value>
       public DateTime KeyExpireDate { get; set; }
```
      
The enumeration AuthenticationStatus contains the status of the authentication. If the status is different from Ok, the BookingResult property is empty.

```
   /// <summary>
   /// Tells the status of the authentication
   /// </summary>
   public enum AuthenticationStatus
    {
       /// <summary>
       /// The operation went ok, check the result
       /// </summary>
       Ok = 0,

       /// <summary>
       /// The authentication failed
       /// </summary>
       Fail = 2,

       /// <summary>
       /// The API key expired
       /// </summary>
       ApiKeyExpired = 3,
    }
```
    
## BookingInformationResult object
The booking information result object simply wraps a BookingInformation object. We communicate the result of the retrieval through this object. The class contains three fields.

```
       /// <summary>
       /// Gets or sets the booking if the status of the retrieval was Ok
       /// </summary>
       /// <value>
       /// The booking.
       /// </value>
       public BookingInformation Booking { get; set; }

       /// <summary>
       /// Gets the retrieval status of the operation
       /// </summary>
       /// <value>
       /// The retrieval status.
       /// </value>
       public BookingRetrievalStatus RetrievalStatus { get; set; }
```

The enumeration BookingRetrievalStatus contains the status of the retrieval. If the status is different from Ok, the Booking property is empty.

```
   /// <summary>
   /// Tells the status of the operation retrieve a booking
   /// </summary>
   public enum BookingRetrievalStatus
    {
       /// <summary>
       /// The operation went ok, check the result
       /// </summary>
       Ok = 0,

       /// <summary>
       /// The operation failed, no result available
       /// </summary>
       Fail = 1,

       /// <summary>
       /// The user had no access to the booking.
       /// </summary>
       NoAccessToBooking = 4,

       /// <summary>
       /// The item is not yet ready to be downloaded. Try again in a while.
       /// </summary>
       Delayed = 5,
    }
```

The fail represents a state where we were unable to deliver the result of the booking. This simply means that you can't get the result. You should however not be dependent on it. There will be a successor if booking version if changes should be communicated. If you feel that you are missing out of something, please contact Citybreak Support that will take it further.

## BookingInformation object
This is the container for all information for a specific version of a booking.

Entity | Description
-------- | --------
Fees | Booking fees
ReservationVersionId | Uniqe reservation id
Booking code | The booking code on the format AAAA01.
Booking date | The date and time the booking was committed into a reservation. 
Status | The status indication of the booking.
New | New booking
Changed | Changed booking
Cancelled | Cancelled booking
Agent | The agent of this booking. 
Supplier | A list of suppliers that are included in this version.
SellerOrganization | Information about the organizer/seller organization/channel owner. (Different names for the same entity)
Channel | Information about that channel that was used when performing the bookings.
Products | A list of products that this booking includes.
ProductGroup | A list of producttype groups. A producttype group is the container or grouping entity of a product. It would represent a hotel for instance. A product belonging to a hotel would be a double room for instance.
NupacsInfo | A list of nupac info. Id + name, to link with NupackId field on products.

Guests | A list of known guests in a booking. This list may or may not exist. A product would have GuestLinks that may or may not be connected to a guest in this list. The GuestLink represents that one guest will certain attributes (like if it is an adult or a child for instance) is one of the consumers of that product. We may not know more information than that. A double room with two guests from Jan 1 to Jan 2 and a double room with two guests from Jan 2 to Jan 3 has two guests. Total amount of guests in the booking may be two or four. We can't know if it is the same two guests using room one also using the second room or if there are other guests using that room. The agent (online or person creating the booking) don't have to submit a configured Guest list, hence no business logic may be applied to contents of this list.  **_Read more about calculating guests in the section Calculate Guests below._**
OnlineGuide | If this reservation was made through the online booking the information about that guide will be reflected here. If this was a call center booking, this field will be undefined.
PointOfSale | Information about the point of sales that were used for this booking version.
BookingUser | Information about the user that committed the booking.
Customer | Information about the person that is responsible for paying for the booking. This may or may not be one of the guests.
CrmAnswers | The answers to the customizable customer questions, if any. This is commonly used for Newsletter subscriptions etc.
ExchangeRates | If currency conversion was used to calculate the price for the end customer, these rates could be applicable. No critical financial decisions should be made about the information in this field. There is no actual relation between the values in this field and the actual currency conversion rate that the organization actually used. 
BookingDiscountInfo | Information about discounts handed out by the Seller organization id.
AgentReference | Text information, usually a name, in the case of an agent organization calls in to the call center who commits the booking by proxy for the agent.
Package | Information about the package that was referenced in this booking. If no package were used, this field is undefined.
NetWorth | Shortcut to get the net worth of the booking for the seller organization.
GroupReferenceName | Groupe referenace name
TrackingId | User-defined tracking value from the booking client
ClientConfirmationPdfUrl | A URL to the downloadable client confirmation PDF

## MainProduct

Extending Product with a list of sub products that are associated with the main product. They are bought under the same agreement and may include business restrictions when buying them, like they may be mandatory included when buying the main product.

## SubProduct

A product that is associated with a MainProduct.

## Product

The base class for MainProduct and SubProduct. 

Entity | Description
-------- | --------
Id | A unique id of this item in the context of this booking. The product will keep the id in all versions of a booking, but is not globally unique outside the context of this booking (booking code).
Organizer | Summary of the organizers economic information for the organizer.
Agent | Summary of the organizers economic information for the agent.
Supplier | Summary of the organizers economic information for the supplier.
SubSupplier | In some sales models a sub supplier is incorporated. This is currently representing like a private owner of a cottage for instance.
DateSpan | The date span in which this product is used.
GuestLinks | Information about the guests that are using this product.
Status | The status of this item in this version. Sample: New, NotChanged or Cancelled
DateStamp | When the product was added to the basket.
RateCode | Information about the rate code that was used to defined the rate used to booking this item. 
RemoteId | A reference to an external system.
Position | The long/lat position if available.
ProducttypeInfo | This describes type and category information that a product has. This is different from the content categories that you may encounter on Online 3 for instance. These are the Citybreak system categories and type information, which is invariant to specific content information.

### ProductOrganizationInfo 
Summary of the economy for a specific organization in the context of a product.

Entity | Description
-------- | --------
Owner | Basic information about the organization that this information concerns.
PriceBuy | The price the organization bought the product for.
SellBuy | The price the organization sold the product for.
CommissionIn | The money earned in commission when selling the product.
CommissionOut | The money spend to another organization to compensate them for selling the product for you.
OrganizationCurrency | The currency used for this organization.
NetWorth | The price that this product was sold for.
Discount | Discount handed out by the organizer.
ProducttypeName | The name of the product in the language that this organization normally uses.
ProductSellerDiscount | Discount handed out by the supplier, like stay three days pay for two.

### ProducttypeInfo

Entity | Description
-------- | --------
ProducttypeId | This is the Citybreak globally unique id for this producttype. 
Name | The system name of the product.
ProducttypeType | An int which could be decoded to find the system type of the product. (see further down)
Category | The system category the product belongs to. See the enum for values.
ProducttypeGroupId | The id of the productgroup that the producttype belongs to. For instance a double room belongs to a producttype group, for instance "Hotel 11". This field will hold the globally unique identifier for "Hotel 11". That group could be found in BookingInformation.ProductGroup
DescriptionInfo | Additional information that a sub system may know of this product. It is typed and dependent on the underlying sub system.
ExternalBookingNumber | An optional reference to an external system's booking.
