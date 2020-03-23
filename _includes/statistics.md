# Creating statistics from booking versions

## Views
What you are probably looking for is to take some sort of action depending on the data you download. If you are doing operational reports, like an arrival list to see who is arriving today, who is staying and who is leaving you are only interested in the latest version of a booking code. This is known as a Net List.
If you are statistics about performance you are looking at a gross list. 

Depending on the type of queries you would like to respond to, you are likely to want to separate products so that they could be queried separately not bundled in a booking. You probably don't want to query all products in a reservation version at the same time, but apply filter on product categories for instance. 

## Net list
This simply means that you will only focus on the latest version of a specific booking code. As soon as you receive a new version you simply throw away the old one and replaced the information with the contents just downloaded.
This means that when you create a report on arrivals in June the contents may differ at any one time when generating the report. A new booking changing the numbers may have been recorded between two different generations and the numbers may have changed.
Please note that even if the last arrival is in the past, there is no guarantee that a next version will not be available later on. 

They should be treated as snap shots in time when the report was generated.

Typical queries made into a net data sets are based on arrival and departure of specific product items. 

## Gross list
A gross list is simply an aggregated view of all versions of a booking. The query is based on booking date and the result can never change regardless when a report was created. If a product is added +1 product is added to the time span. If a product was not changed between versions +0 products were added. If a product was removed -1 product is added to the gross summary. 

This is done by adding all products from version 1. When version 2 is recorded, the version 1 is negated at the time of booking of version 2. Then all products still active in version 2 is added. 

* **Version 1 created at t1 contains one Double room from Jan 1 to Jan 10.**

* **Version 2 created at t2 the double room was changed to Jan 5 to Jan 6, and a single room was added Jan 6 to Jan 10.**

**At t1 the reservation summary went:**
``
+1 product
+9 room nights
``

**At t2 the reservation summary went:**
``
-1 product
-9 room nights 
+2 products
+4 +1 room nights
``
* A report for a period only including t1 will report +1 product and +9 room nights.
* A report for a period only including t2 will report +1 product and -4 room nights.
* A report for a period including both periods will report +2 products and 5 room nights.

If these were all versions of the booking, a net report will report the current state of the booking as +2 products and +5 room nights.

## Calculating Guests

There are different views on this operation. 

The number of guests in a booking can't be calculated in the generic case. Consider the following scenario: There are two rooms in a booking. One between May 1 to May 2 and the other between May 10 to May 12 with two adults each. You can't assume that it is the same two adults in the first room as in the second.

The one that makes the reservation may create a guest list. The list that is reflected directly under the booking object. It may be correct in terms of calculating the number of guests in the booking. There is no guarantee for it to be correct.

However, for most statistical operations this is not a problem. You are likely to analyze the number of guests per room, since that is the statistical entity most operations analyze. The number of guests in a room are the sum of the GuestLinks on the MainProduct. You also need to find possible extra bed guests. This is done by checking up on the SubProducts for the specified MainProduct. Since guest links may be found on all sorts of sub products you need to calculate only the ones with a ProductType of 14. The ProductType is found under the property ProductTypeInfo.

## Statistical entities

### Stay time

This is applicable to Category type Accommodation and is the number of nights a product is used.

### Room nights

This is applicable to Category type Accommodation and is the stay time for a product. Sum the number stay time for each product to get the total number of room nights in a booking.

### Guest nights

This is applicable to category type Accommodation and is the stay time multiplied with number of guest links on a product. This is calculated per product.
