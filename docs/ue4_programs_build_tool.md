# UnrealBuildTool 언리얼 빌드 툴

<https://github.com/EpicGames/UnrealEngine/tree/4.22.2-release/Engine/Source/Programs/UnrealBuildTool>

## Debug 디버그

### Start options/Command line arguments

```bat
$(ProjectName)$(Target) $(Platform) $(Config) $(ProjectDir)\$(ProjectName)\$(ProjectName).uproject
```

#### 프로젝트 예제

* ProjectName=Example
* Target=Editor
* Platform=Win64
* Config=DebugGame
* ProjectDir=C:\UE_Projects

```bat
ExampleEditor Win64 DebugGame C:\UE_Projects\Example\Example.uproject
```

#### 엔진 에디터 예제

* ProjectName=UE4Editor
* Platform=Win64
* Config=Debug

```bat
UE4Editor Win64 Debug 
```

#### 엔진 프로그램 예제

* ProjectName=BlankProgram
* Platform=Win64
* Config=Development

```bat
BlankProgram Win64 Development
```
