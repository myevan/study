# Unreal Project Program 언리얼 프로젝트 프로그램 

## 구조

<https://github.com/EpicGames/UnrealEngine/tree/4.22.2-release/Engine/Source/Programs/BlankProgram>

* $(ProjectName)/
    * Source/
        * Programs/
            * $(ProgramName)/
                * $(ProgramName).Build.cs
                * $(ProgramName).Target.cs
                * Resources/Windows/$(ProgramName).ico
                * Private/
                    * $(ProgramName).h
                    * $(ProgramName).cpp

## Build.cs

< https://github.com/EpicGames/UnrealEngine/blob/4.22.2-release/Engine/Source/Programs/BlankProgram/BlankProgram.Build.cs#L9-L11>

상대 경로를 사용할 경우 프로젝트내 경로로 인지하므로, 절대 경로로 엔진 경로를 설정합니다.

### 예제

```cs
using UnrealBuildTool;

public class HelloWorld : ModuleRules
{
	public HelloWorld(ReadOnlyTargetRules Target) : base(Target)
	{
		PublicIncludePaths.Add(EngineDirectory + "\\Source\\Runtime\\Launch\\Public");

		PrivateIncludePaths.Add(EngineDirectory + "\\Source\\Runtime\\Launch\\Private");		// For LaunchEngineLoop.cpp include

		PrivateDependencyModuleNames.Add("Core");
		PrivateDependencyModuleNames.Add("Projects");
	}
}
```

### 분석

프로젝트 헤더와 라입 경로는 프로젝트 폴더를 기준으로 합니다. 

```cs
class UEBuildTarget
{		
    public UEBuildModule FindOrCreateModuleByName(string ModuleName, string ReferenceChain)
    {
        // ... 전략 ...

        // Get the base directory for paths referenced by the module. If the module's under the UProject source directory use that, otherwise leave it relative to the Engine source directory.
        if (ProjectFile != null)
        {
            DirectoryReference ProjectSourceDirectoryName = DirectoryReference.Combine(ProjectFile.Directory, "Source");
            if (RulesObject.File.IsUnderDirectory(ProjectSourceDirectoryName))
            {
                RulesObject.PublicIncludePaths = CombinePathList(ProjectSourceDirectoryName, RulesObject.PublicIncludePaths);
                RulesObject.PrivateIncludePaths = CombinePathList(ProjectSourceDirectoryName, RulesObject.PrivateIncludePaths);
                RulesObject.PublicLibraryPaths = CombinePathList(ProjectSourceDirectoryName, RulesObject.PublicLibraryPaths);
            }
        }

        /// ... 후략 ...
    }

    private static List<string> CombinePathList(DirectoryReference BasePath, List<string> PathList)
    {
        List<string> NewPathList = new List<string>();
        foreach (string Path in PathList)
        {
            NewPathList.Add(System.IO.Path.Combine(BasePath.FullName, Path));
        }
        return NewPathList;
    }
}
```

PrivateIncludePaths 는 절대 경로가 아닐 경우 강제로 프로젝트 소스 폴더를 기준으로 변경합니다.

```cs
class UEBuildTarget
{		
    public UEBuildModule FindOrCreateModuleByName(string ModuleName, string ReferenceChain)
    {
        // ... 전략 ...

                // Resolve private include paths against the project source root
                for (int Idx = 0; Idx < RulesObject.PrivateIncludePaths.Count; Idx++)
                {
                    string PrivateIncludePath = RulesObject.PrivateIncludePaths[Idx];
                    if (!Path.IsPathRooted(PrivateIncludePath))
                    {
                        PrivateIncludePath = DirectoryReference.Combine(BaseSourceDirectory, PrivateIncludePath).FullName;
                    }
                    RulesObject.PrivateIncludePaths[Idx] = PrivateIncludePath;
                }

        /// ... 후략 ...
    }
}
```