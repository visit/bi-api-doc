# Webhook

We can provide a webhook call on each booking version made in the system. It will be a call to a static POST endpoint with the following body:


```
public class WebhookBookingModel
{
    public int Version { get; set; }

    public int ReservationVersionId { get; set; }

    public DateTime BookingDate { get; set; }

    public int SalesPointId { get; set; }

    public string Currency { get; set; }

    public decimal VAT { get; set; }

    public decimal Price { get; set; }

    public DateTime AutoCancelDate { get; set; }

    public DateTime CancellationDate { get; set; }

    public string BookingCode { get; set; }

    public bool IsMobileBooking { get; set; }

    public int? SellerOrganizationId { get; set; }

    public int AgentOrganizationId { get; set; }
}
```
