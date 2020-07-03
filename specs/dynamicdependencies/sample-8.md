# Sample 8 - LolzKitten app ordering Packages in PackageGraph with prepend

Contoso publishes framework packages. At runtime, LolzKittens wants to use Contoso's functionality as specified by a package dependency defined when LolzKittens was installed. This framework package is added to the process' package graph with a specific rank. If package(s) are present in the package graph make sure this package is added to the Package Graph before any other packages with the same rank, via ```MddAddPackageDependency::PrependIfRankCollision```.

The package graph is updated for the remainder of the process' lifetime (i.e. the package dependencies are not explicitly removed).

## Win32

```c++
#include <MsixDynamicDependency.hpp>
#include <wil/resource.h>

HRESULT LoadPackageDependencyId(wil::unique_ptr<WCHAR[]>& packageDependencyId);

HRESULT UpdatePackageGraph()
{
    wil::unique_ptr<WCHAR[]> packageDependencyId;
    RETURN_IF_FAILED(LoadPackageDependencyId(packageDependencyId));

    const INT32 rank = 1967;
    const UINT32 flags = MddAddPackageDependency::PrependIfRankCollision;
    MDD_PACKAGEDEPENDENCY_CONTEXT packageDependencyContext = nullptr;
    RETURN_IF_FAILED(MddAddPackageDependency(
        packageDependencyId.get(), rank, flags, &packageDependencyContext, nullptr));

    return S_OK;
}

HRESULT LoadPackageDependencyId(_In_ PCWSTR what, wil::unique_ptr<WCHAR[]>& packageDependencyId)
{
    // Load the previously stored package dependency identifier
}
```

## WinRT

```c#
using Microsoft.ApplicationModel

namespace LolzKitten
{
    public class Runtime
    {
        public static void UpdatePackageGraph()
        {
            var muffinsPackageDependencyId = LoadPackageDependencyId(L"muffins");
            const int muffinsRank = -42;
            AddToPackageGraph(muffinsPackageDependencyId, muffinsRank);

            var wafflesPackageDependencyId = LoadPackageDependencyId(L"waffles");
            const int wafflesRank = 3000;
            AddToPackageGraph(wafflesPackageDependencyId, wafflesRank);
        }

        public static void AddToPackageGraph(string what, string packageDependencyId, int rank)
        {
            var packageDependency = new PackageDependency(packageDependencyId);

            var options = new AddPackageDependencyOptions(){ Rank = rank, PrependIfRankCollision = true };
            var packageDependencyContext = packageDependency.Add(options);
            Console.WriteLine($"{what} via {packageDependencyContext.PackageFullName}");
        }

        static string LoadPackageDependencyId(string what)
        {
            // Load the previously stored package dependency identifier
        }
    }
}
```