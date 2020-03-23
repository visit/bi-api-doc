# BI API intro

The objective of Citybreak BI API is to enable external partners to get hold of the booking data for external analysis. Applications such as BI-analysis tools could be built upon the data.

## Description

Add description...

## Release notes

Lastest updates important information

### BI API version 5
* BREAKING CHANGE The authentication return status codes are now moved from the existing Status field to AuthenticationStatus on a new result wrapper type to better indicate failed or expired authentication keys. The existing Status is renamed RetrievalStatus to clarify the difference.
* BREAKING CHANGE The GetManyAfterReservationId operation is renamed GetMany.
* BREAKING CHANGE The SearchAfterReservation operation is removed, use FindFirstAfter to find a starting point by date and GetMany to fetch reservations instead.
* BREAKING CHANGE SequenceId replaces ReservationVersionId in GetMany to simplify usage since ReservationVersionId is not sequential in nature. To continue retrieving reservations from a previous implementation, call GetData with your latest ReservationVersionId, note the SequenceId and use that from now on.
* BREAKING CHANGE Corrected Name/Value field on ComplementBase which were accidentally swapped.
* Added TrackingId - user defined tracking id from the booking client.
* Added ClientConfirmationPdfUrl - downloadable client confirmation PDF.
* Added CrmAnswers - a list of the answers to the customizable customer questions.
