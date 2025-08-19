Ein ViewModel von einer Page schaut mindestens so aus:

namespace UnoApp.Presentation.Pages.WixContacts;

internal partial class ContactsPageViewModel : BasePageViewModel
{
    public ContactsPageViewModel(
        BaseServices baseServices
    )
        : base(baseServices)
    {
    }
}

Bei einer View mit einem Item schaut das ViewModel mindestens so aus:
internal partial class ContactsListViewModel
    : BaseItemViewModel<ContactsListModel>
{
    public ContactsListViewModel(
        BaseServices services,
        IFeed<ContactsListModel> contact
    )
        : base(services, contacts) { }

}
- Könnte auch von BaseItemViewModel<IEnumerable<ContactsListModel>> erben
- Wenn es nur eine View ist die kein Item hat kann es auch nur von BaseViewModel erben