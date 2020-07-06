# Enable Unity Input Field for Mobile WebGL
Unity는 크로스 플랫폼이지만 아직 공식적으로 모바일 웹에 대한 WebGL을 지원하고 있지는 않다(2020년 3월 기준).
[InputField](https://docs.unity3d.com/2018.4/Documentation/Manual/script-InputField.html)는 HTML의 input text같이 공식 UGUI의 컴포넌트이지만 모바일 WebGL에서의 입력이 지원되지 않는다. 그래서 [WebGL: Interacting with browser scripting](https://docs.unity3d.com/2018.4/Documentation/Manual/webgl-interactingwithbrowserscripting.html)을 참고하여 모바일 WebGL에서 InputField에 값을 입력할 수 있도록 만들어 보았다.

> 개인적으로 기대를 많이 하고 있는 [Instant Games - Tiny Project](https://unity.com/solutions/instant-games)이 공식적으로 릴리즈 되기 까지는 이런 식의 번거로운 수작업이 필요하다.

## Preview

<iframe style="width:100%; height:300px;" src="https://oneyedev.github.io/input-field-plugin-for-unity-mobile-webgl/" allowfullscreen></iframe>


## Source

InputField의 게임 오브젝트에 붙일 컴포넌트를 작성하고

```c#
using System.Collections;
using System.Collections.Generic;
using System.Runtime.InteropServices;
using UnityEngine;
using UnityEngine.EventSystems;
using UnityEngine.UI;

public class InputFieldMobileSupport : MonoBehaviour, IPointerClickHandler
{
    [DllImport("__Internal")]
    private static extern void Prompt(string name, string message, string defaultValue);

    public string message;

#if UNITY_WEBGL
    public void OnPointerClick(PointerEventData eventData)
    {
        Prompt(name, message, GetComponent<InputField>().text);
    }

    public void OnPromptOk(string message)
    {
        GetComponent<InputField>().text = message;
    }

    public void OnPromptCancel()
    {
        GetComponent<InputField>().text = "";
    }
#endif
}
```

브라우저에서 동작할 jslib을 작성한다.

```js
// Assets/Plugins/mobile-support.jslib
mergeInto(LibraryManager.library, {
  Prompt: function (name, message, defaultValue) {
    if(UnityLoader.SystemInfo.mobile){
        const name_str = Pointer_stringify(name);
        const message_str = Pointer_stringify(message);
        const defaultValue_str = Pointer_stringify(defaultValue);
        const result = window.prompt(message_str, defaultValue_str);
        if(result === null) {
            SendMessage(name_str, 'OnPromptCancel');
        } else {
            SendMessage(name_str, 'OnPromptOk', result);
        }
    }
  }
});
```
> `jslib`은 `Assets/Plugins` 반드시 폴더에 위치해야 인식한다.

마지막으로, 모바일 브라우저에서 WebGL을 막는 UnityLoader의 기본 동작을 막는 아래 스크립트를 추가한다. 이미 비슷한 기능이 되어 있는 경우 생략가능하다. 아래 코드는 [unity-webgl-disable-mobile-warning](https://answers.unity.com/questions/1339261/unity-webgl-disable-mobile-warning.html)에서 발췌 하였다.

```c#
// Assets/Editor/MobileSupport.cs
using System.Collections;
using System.Collections.Generic;
using System.IO;
using UnityEditor;
using UnityEditor.Callbacks;
using UnityEngine;

public class MobileSupport
{
    [PostProcessBuild]
    public static void OnPostProcessBuild(BuildTarget target, string targetPath)
    {
        var path = Path.Combine(targetPath, "Build/UnityLoader.js");
        var text = File.ReadAllText(path);
        text = text.Replace("UnityLoader.SystemInfo.mobile", "false");
        File.WriteAllText(path, text);
    }
}

```

> `Assets/Editor` 폴더에 위치해야한다. (`Editor` 서브폴더 가능)
