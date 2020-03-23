# End points

The service is available under SSL for soap web calls and also as a REST service for XML.

The service is found at <https://statfeed-api-v5.citybreak.com/BookingInformationService.svc>

[Soap WSDL](https://statfeed-api-v5.citybreak.com/BookingInformationService.svc?singleWsdl)

[XML Rest service](https://statfeed-api-v5.citybreak.com/BookingInformationService.svc/xml/help)

## Methods

The methods provided enables you to download information about bookings. 

### FindFirstAfter

public BookingSearchHit FindFirstAfter(string authToken, DateTime start);
´´
BookingInformationService.svc/xml/findfirstafter/{AUTHTOKEN}/{START}
´´

Parameter |	Description
--------- | ---------
authToken	| The api key handed to you by the Citybreak team.
start	| date (yyyy-MM-dd HH:mm) to search from.

This method enables you to find a starting point to retrieve booking data, it takes a start date and will return a SequenceId which you can use in the GetMany operation.

public class SearchHit {
   /// <summary>
   /// The date the booking was made.
   /// </summary>
   public DateTime BookingDate;

   /// <summary>
   /// The sequence id of the booking.
   /// </summary>
   public int SequenceId;

   /// <summary>
   /// The globally unique reservation version id the booking got.
   /// </summary>
   public int ReservationVersionId;

   /// <summary>
   /// The version of this booking. First version of the booking code has version 1.
   /// </summary>
   public int Version;

   /// <summary>
   /// The booking code the version has. The format is "CCCCnn" where C { A-Z } and n { 0-9 }
   /// </summary>
   public string BookingCode;
}
### GetMany

public GetManyResult GetMany(
   string authToken, int sequenceIdToStartWith, int numberOfItems, int numberOfItemsToSkip);
´´
/BookingInformationService.svc/xml/getmany/{AUTHTOKEN}/{SEQUENCEIDTOSTARTWITH}/{NUMBEROFITEMS}/{NUMBEROFITEMSTOSKIP}
´´

Returns a list of bookings from a SequenceId, pass 0 to receive the first batch of bookings. To continue batch retrieving data, pass the last booking's SequenceId you received as sequenceIdToStartWith in subsequent calls. numberOfItemsToSkip should be set to 0 for single threaded batching. To enable multi threaded batching you can for example do four requests in parallel with the same sequenceIdToStartWith and 10 numberOfItems but with 0, 10, 20 and 30 numberOfItemsToSkip respectively.
NB! This method could potentially return a great chunk of data. Please leave numberOfItems to 20 or below.

### GetData

public GetDataResult GetData(string authToken, int reservationVersion);
´´
BookingInformationService.svc/xml/get/{authToken}/{reservationVersion}
´´

This method gets the interior of a single booking.
