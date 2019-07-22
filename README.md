# Blazor extra linking

Currently, client-side Blazor applications build with the Mono linker, but the configuration tells the linker only to strip code from the Mono BCL assemblies. We don't currently strip code from the Microsoft.AspNetCore.* assemblies, or any other assemblies acquired via package references.

This is a quick exploration to see what costs and benefits would be involved in enabling code stripping from *everything* (except your own app assembly, since the presumption is that you want all your own code).

## What's here

* `.csproj`
  * Uses `AdditionalMonoLinkerOptions` to enable stripping of some extra features (`etw`, `remoting`)
  * Imports `Linker.xml`
  * Target `ChangeLinkerInputs` changes the linker config so we are willing to strip code from everything apart from the primary build output assembly
* `Linker.config`
  * Fixes all the things that get broken by the above by whitelisting various types
  * Contains a summary of what linker features would be needed to know about all this automatically

## Results

This reduces the resulting app size from 2.4MB to 2.0MB. Of this, ~1MB is non-.NET code anyway (Mono binaries).

It's a decent size reduction, but this does introduce a lot of extra potential to be broken.
