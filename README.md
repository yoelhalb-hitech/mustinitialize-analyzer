# MustInitialize Analyzer

## What is the purpose of MustInitialize Analyzer?
- Allows you enforce that the given property or field has to be initialized when instantiated.
- Removes the need to set a value when in a nullable context (in C# 8 and upwards) for such a property or field

##### Example Code

    1.   public class TestClass
    2.   {
    3.       [MustInitialize] public string TestProperty { get; set; } 
    4.   }
    5.   var testObj = new TestClass();

Without MustInitialize the following error will be reported on line 3

    warning CS8618: Non-nullable field 'TestProperty' must contain a non-null value when exiting constructor. Consider declaring the field as nullable.

However with MustInitialize it will report the following on line 5

    warning MustInitialize: Property 'TestProperty' must be initialized

## Componenets of MustInitialize Analyzer
- A Roslyn analyzer which can be installed as a Nuget package
- A Visual Studio extension

## Installing as Nuget from local
Add in the same folder as the `.sln` file for your project a file named `nuget.config` with the following content (and replace Path/to/dll/folder with the actual path for the nupkg is stored) 

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
        <packageSources>
            <add key="MyLocalSharedSource" value="Path/to/nupkg/folder" />
        </packageSources>
    </configuration>

Afterwards it will show up as a package source in the Nuget Package sources, so you can install it from there

See sample image from the Visaul Studio `Tools -> Nuget Package Manager -> Manage Nuget Packages for Solution...` page

![image](https://user-images.githubusercontent.com/92554300/150062703-d9dcb61d-236c-4355-ac7f-e8c628372a4d.png)

## Debugging

##### To run in Visual Studio
Just load up the project nad hit start.

When running the project sometimes the breakpoints are not being hit, you can use the suggestions described in [this issue](https://github.com/dotnet/roslyn-sdk/issues/515):
    - Turn off `Use 64 bit process for code analysis`
    - Add a `Debugger.Launch()` statement
    
##### To run in [dnSpy](https://github.com/dnSpy/dnSpy)
Here are the steps involved:
1. Open VS Develoepr Command Prompt and run MS build on your project
2. From the output get the entire `csc` command line
    - Since it won't fit on the command line save the entire string (besides the csc path) in a file named `options.rsp`

3. Open [dnSpy](https://github.com/dnSpy/dnSpy)
    - Use the 64 Bit version if you are on a x64 machine and csc is in `Program Files` not in `Program Files (x86)`, otherwise use the 32 Bit version
4. From `File -> Open` load all `MustInitializeAnalyzer.dll` files referenced in the string of step 2
    - Set breakpoints in all loaded files (to ensure that it is breaking)
5. Run `Start` in dnSpy and set the following parameters:
      - Executable: Should be set to the `csc` path of step 2
      - Arguments: Should be `@"options.rsp"` (the full path of the file of step 2)
      - Working Directory: Should be set the directory of your .csproj file to be debugged
