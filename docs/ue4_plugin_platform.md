# UE4/Plugin/Platforms 언리얼 플러그인 플랫폼

## .uplugin 설정

* WhitelistPlatforms 중에서 BlacklistPlatforms 빌드 제외됨
* WhitelistPlatofrms 가 없을 경우 전체 플랫폼 빌드됨

```json
{
	"Modules" :
	[
		{
			"Name" : "이름",
			"WhitelistPlatforms" : [ "허용 플랫폼" ],
			"BlacklistPlatforms" : [ "제외 플랫폼" ]
		}
	]
}
```

## 관련 코드

<https://github.com/EpicGames/UnrealEngine/blob/4.22.2-release/Engine/Source/Programs/UnrealBuildTool/System/PluginReferenceDescriptor.cs#L189-L204>

```cs
public class PluginReferenceDescriptor
{
		public bool IsEnabledForPlatform(UnrealTargetPlatform Platform)
		{
			if (!bEnabled)
			{
				return false;
			}
			if (WhitelistPlatforms != null && WhitelistPlatforms.Length > 0 && !WhitelistPlatforms.Contains(Platform))
			{
				return false;
			}
			if (BlacklistPlatforms != null && BlacklistPlatforms.Contains(Platform))
			{
				return false;
			}
			return true;
		}
}
```