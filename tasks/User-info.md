# Get user info

Helpers for getting basic info about the current system user, such as their name or domain. 

Before calling these methods, you must enable the **User Account Information** capability in your app's manifest file. 
Otherwise, they will always return an empty string. 

```C#
using System; 
using System.Threading.Tasks;
using Windows.System;

public static class UserInfoHelper
{
    public static async Task<string> GetUserAccountNameAsync() =>
        await GetUserPropertyAsync(KnownUserProperties.AccountName);

    public static async Task<string> GetUserDisplayNameAsync() =>
        await GetUserPropertyAsync(KnownUserProperties.DisplayName);

    public static async Task<string> GetUserDomainNameAsync() =>
        await GetUserPropertyAsync(KnownUserProperties.DomainName);

    public static async Task<string> GetUserFirstNameAsync() =>
        await GetUserPropertyAsync(KnownUserProperties.FirstName);

    public static async Task<string> GetUserLastNameAsync() =>
        await GetUserPropertyAsync(KnownUserProperties.LastName);

    public static async Task<string> GetUserPrincipalNameAsync() =>
        await GetUserPropertyAsync(KnownUserProperties.PrincipalName);

    public static async Task<string> GetUserProviderNameAsync() =>
        await GetUserPropertyAsync(KnownUserProperties.ProviderName);

    private static async Task<string> GetUserPropertyAsync(string property)
    {
        var users = await User.FindAllAsync(UserType.LocalUser,
            UserAuthenticationStatus.LocallyAuthenticated);
        object val = await users.FirstOrDefault()?.GetPropertyAsync(
            KnownUserProperties.ProviderName);
        return val.ToString();
    } 
}
```

**Note:** These methods assume your app is interested in the single, current, local system user. However, you can also get info 
about remotely authenticated users or more than one user at once (if your app is running on Xbox, for instance, where more 
than one user can log in at the same time). For more information, see the 
[User class](https://msdn.microsoft.com/library/windows/apps/windows.system.user.aspx)

## See also

[User class](https://msdn.microsoft.com/library/windows/apps/windows.system.user.aspx)  
[KnownUserProperties class](https://msdn.microsoft.com/library/windows/apps/windows.system.knownuserproperties)  
[Lambda expressions](https://msdn.microsoft.com/library/bb397687.aspx) (anonymous methods using the "=>" syntax)  
