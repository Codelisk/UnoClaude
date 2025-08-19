Eine NotifyPropertyChanged property soll mit ObservableProperty angelegt werden
[ObservableProperty]
string imageUri;

Wenn es ein anderes Property updaten soll dann soll es NotifyPropertyChangeForAttribute verwenden
[NotifyPropertyChangedFor(nameof(HasParkedImage))]
[ObservableProperty]
string imageUri;