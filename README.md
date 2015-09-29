# Go Keychain

A library for accessing the Keychain for OSX and iOS.

Requires Mac OSX 9 or greater and iOS 7 or greater.

**WARNING**: This is still being tested and reviewed.

## Usage

#### Add Item

```go
item := keychain.NewItem()
item.SetSecClass(keychain.SecClassGenericPassword)
item.SetService("MyService")
item.SetAccount("gabriel")
item.SetLabel("A label")
item.SetAccessGroup("A123456789.group.com.mycorp")
item.SetData([]byte("toomanysecrets"))
item.SetSynchronizable(keychain.SynchronizableNo)
item.SetAccessible(keychain.AccessibleWhenUnlocked)
err := keychain.AddItem(item)

if err == keychain.ErrorDuplicateItem {
  // Duplicate
}
```

#### Query Item

Query for multiple results, returning attributes:

```go
query := keychain.NewItem()
query.SetSecClass(keychain.SecClassGenericPassword)
query.setService(service)
query.setAccount(account)
query.SetAccessGroup(accessGroup)
query.SetMatchLimit(keychain.MatchLimitAll)
query.SetReturnAttributes(true)
results, err := keychain.QueryItem(query)

```

Query for a single result, returning data:

```go
query := keychain.NewItem()
query.SetSecClass(keychain.SecClassGenericPassword)
query.SetService(service)
query.SetAccount(account)
query.SetAccessGroup(accessGroup)
query.SetMatchLimit(keychain.MatchLimitOne)
query.SetReturnData(true)
results, err := keychain.QueryItem(query)
if err != nil {
  // Error
} else if len(results) != 1 {
  // Not found
} else {
  password := string(results[0].Data)
}
```

#### Delete Item

Delete a generic password item with service and account:

```go
item := keychain.NewItem()
item.SetSecClass(keychain.SecClassGenericPassword)
item.SetService(service)
item.SetAccount(account)
err := keychain.DeleteItem(item)
```

### Other

There are some convenience methods for generic password:

```go
// Create generic password item with service, account, label, password, access group
item := keychain.NewGenericPassword("MyService", "gabriel", "A label", []byte("toomanysecrets"), "A123456789.group.com.mycorp")
item.SetSynchronizable(keychain.SynchronizableNo)
item.SetAccessible(keychain.AccessibleWhenUnlocked)
err := keychain.AddItem(item)
if err == keychain.ErrorDuplicateItem {
  // Duplicate
}

accounts, err := keychain.GetGenericPasswordAccounts("MyService")
// Should have 1 account == "gabriel"

err := keychain.DeleteGenericPasswordItem("MyService", "gabriel")
if err == keychain.ErrorNotFound {
  // Not found
}
```

### OSX

Settings trusted applications for item (OSX only):

```go
item := keychain.NewGenericPassword("MyService", "gabriel", "A label", []byte("toomanysecrets"), "A123456789.group.com.mycorp")
trustedApplications := []string{"/Applications/Mail.app"}
item.SetAccess(&keychain.Access{Label: "Mail", TrustedApplications: trustedApplications})
err := keychain.AddItem(item)
```

## iOS

Bindable package in `bind`. iOS project in `ios`. Run that project to test iOS.

To re-generate framework (in bind dir):

```
gomobile bind -target=ios -o ../ios/bind.framework
```
