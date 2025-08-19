Anstehend ein Codebeispiel wie ein Endpunkt ausschauen soll:
-MediatorHttpGroup allows you to group your handlers under a common route prefix.
This is useful for versioning or grouping related endpoints.
The attribute can also be used on multiple classes, but the group is only emitted once.
- Namensgebung ist immer mit Handler Postfix
- Services wenn nötig werden im Konstruktor gleich mitgegeben
- Je nachdem ob Post, Put, Get, Delete MediatorHttpXXX

[MediatorHttpGroup("/routes", RequiresAuthorization = true)]
public class MyHandler(IService service) : IRequestHandler<MyRequest, MyResult>
{
    [MediatorHttpPost("MyOperation", "/my")] // creates a route with the operationId of "MyOperation" and a route of "/routes/my"
    public async Task<MyResult> Handle(MyRequest request, IMediatorContext context, CancellationToken ct)
    {
        return new MyResult();
    }
}