- Ein Entity schaut im einfachsten Fall so ausschauen und MUSS auch ZUMINDEST so ausschauen:
Summary, record {Name}Entity und erbt von BaseEntity oder einem anderen Entity was von BaseEntity erbt
/// <summary>
/// Represents a contact with call tracking information.
/// </summary>
public record ContactEntity : BaseEntity;

- Ein Entity enthält für jdes property eine param Documentation:
/// <param name="WixId">The unique Wix identifier for the contact.</param>
- Ein Entity enthält wenn möglich für jedes Property auch DataAnnotations.