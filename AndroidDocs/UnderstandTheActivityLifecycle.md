# 액티비티 생명주기 이해하기

[Understand the Activity Lifecycle](https://developer.android.com/guide/components/activities/activity-lifecycle)



## 액티비티 생명주기 개념



## 라이프사이클 콜백

이번 섹션에서는 액티비티 생명주기 동안 사용되는 콜백 메소드에 대한 개념적, 구현적 정보에 대해서 알아본다.

secContentView()와 같은 몇몇의 액션은 액티비티 생명주기 메소드에 종속 되어있다. 하지만 독립된 구성요소의 동작을 구현한 코드는 컴포넌트 자체에 위치해야한다. 이를 달성하기 위해 lifecycle-aware 컴포넌트를 만들어야 한다. 이를 만드는 방법을 배우기 위해서 Handling Lifecycles with Lifecycle-Aware Components 문서를 확인하라.



### onCreate()

### onStart()

만약 액티비티가 시작되는 상태에 들어간다면, 시스템은 이 메소드를 부릅니다. `onStart()`호출은 어플이 액티비티를 포그라운드에 들어가게하고 상호작용하게 만들기 때문에, **액티비티가 사용자에게 보이게 만든다.** 예를 들어 이 메소드는 앱이 UI를 유지보수하는 코드를 초기화하는 곳에 있다.

액티비티가 시작상태에 들어가게 되면, 액티비티의 라이프 사이클과 연결된 lifecycle-aware 컴포넌트는 `ON_START`이벤트를 수신한다.

 `onStart()` 메소드는 매우 빠르게 진행되고, Created sated와 마찬가지로 액티비타가 Started state를 유지 하지 않는다.  콜백이 끝나면 액티비티는 Resumed state에 들어가고 시스템은 `OnResume()` 메소드를 부른다.



### onResume()

액티비티가 Resumed state에 들어가게 되면, 액티비티는 포그라운드에 들어가게 되고 시스템은 `OnResume()`콜백 메소드를 호출한다. 이는 액티비티가 유저와 상호작용하는 상태이다. 이 상태는 포커스가 앱으로부터 벗어나는 어떤 일이 일어나기 전까지 지속된다. 예를 들어 전화를 받는 다거나 다른 액티비티로 화면을 전환한다던가 기기의 화면을 끄는 활동들이 해당된다.

액티비티가 resumed sate가 되면 lifecycle-aware 컴포넌트는 `ON-RESUME`이벤트를 받게 된다. 여기서 라이프 사이클 구성 요소를 사용하면 카메라 미리보기 시작과 같은 구성 요소가 표시되고 폭라운드에서 실행해야하는 모든 기능을 사용 할 수 있다.

만약 interruptive 이벤트가 발생한다면 액티비티는 Paused state에 들어가게 되고 시스템은 `onPause()`를 콜백한다.

만약 Paused state에서 다시 Resumed sate가 된다면 시스템은 다시한번 `onResume()`메소드를 부른다. 이런 이유 때문에 `onResume()`에 `onPuase()`에서 방출한 컴포넌트를 다시 호출해야 한다. 그리고 다시 Resumed 상태가 되었을 때 발생해야하는 어떤 동작도 다시 초기화 해야한다.

다음은 카메라를 부르는 lifecycle-aware 컴포넌트가 ON_RESUME 이벤트를 수신 받았을 때 생기는 예제이다.	

```java
public class CameraComponent implements LifecycleObserver {
    
   @OnlifecycleEvent(Lifecycle.Event.ON_RESUME)
    public void initializeCamera(){
        if (camera == null){
getCamera();}
    }
}
```

위의 코드는 `LifecycleObserver`가 ON_RESUME 이벤트를 수신하면 카메라를 초기화 하는 코드이다. 하지만 다중창 모드에서 액티비티는 Pause 상태라도 화면에 보여질수 있다. 예를 들어 멀티 윈도위 모드이면서 현재 액티비티를 포함하지않은 다른 윈도우를 선택하게되면, 현재 액티비티는 Paused 상태가 된다. 만약 Resumed 상태에서 카메라 액티비티를 원한다면, 위에서 설명한 ON_RESUME 이벤트 이후에 카메라를 초기화한다. 만약 카메라 액티비티가 보여지는 상황에서 Paused 상태가 되기를 원한다면, ON_START 이벤트 이후에 카메라를 초기화 해야한다. 하지만 Paused 상태에서 카메라를 활성화하면 다중 창 모드에서 다시 재개 된 앱에 대한 카메라 액세스가 거부 될 수 있다. 활동이 일시 중지되어있는 동안 카메라를 활성 산태로 유지해야 할 때가 있지만 전체적인 사용자 환경을 실제로 저하시킬 수 있다. 라이프 사이클에서 멀티 윈도우 환경에서 공유 시스템 리소스를 제어하는 것이 더 적절한지 주의 깊게 생각해야 한다. 멀티윈도우에 대해서 더 알고 싶다면 Multi-Window Support 문서를 참조하라.

초기화를 어느 생명주기에서 시작하는 것과 관계없이, 상응되는 라이프사이클에서 리소스를 해제하는 것을 확실히 해야한다. 만약 ON_START 이벤트 이후에 초기화 했다면, ON_STOP 이벤트에서 해제해야한다. 만약 ON_RESUME 이벤트에서 초기화 했다면 ON_PAUSE 이벤트에서 초기화 해야한다.

위 코드 스니펫은  Lifecycle-aware 컴포넌트의 요소에 카메라 초기화 코드를 저장해야한다. 바로 Lifecycle 콜백메소드에 작성할수 있지만 추천되지는 않는다. 이 로직을 독립적으로 구성한다면, Lifecycle-aware 컴포넌트는 복수의 액티비티에서 코드를 복사하지 않고 사용할 수 있다. 

 

### onPause()

시스템은 사용자가 액티비티를 떠났다는 최초의 표시로 이 메소드를 호출한다( 비록 이는 액티비티가 항상 파괴되고 있다는 것을 의미하는 것은 아니다.) 이는 액티비티가 더이상 포그라운드이 있지 않음을 의미한다(비록 멀티윈도우 상태라면 보여지는 상태일지라도). 액티비티가 Paused state에 있는 동안 혹은 즉시 재사용이 예상될 때, 더이상 지속되지 않은 활동을 멈추고 저정하기 위해서 `onPuase()`메소드를 사용하라. 이 상태로 들어오게되는 몇가지 이유가 있다.

- 일부 이벤트는 `onResume()` 섹션에서 설명한대로 앱 실행을 중단한다. 이것이 가장 일반적인 경우이다.
- 안드로이드 7.0(API level 24) 이상에서, 멀티윈도우 상황에서 복수의 앱이 실행된다. 한 번에 한 앱(윈도우)만이 포커싱되기 때문에 다른 앱들은 모드 pause 상태이다.
- 다이얼 로그같은 반투명활동이 새로 오픈되었을 때. 액티비티가 보이기는 하지만 부분적으로 보이면 포커스 아웃됐을 때 pause 상태가 된다.

액티비티가 puased 상태가 된다면, lifecycle-aware 컴포넌트는 ON_PAUSE 이벤트를 수신한다. 여기서 라이프 사이클 구성 요소는 카메라 미리보기 중지와 같은 구성 요소가 포그라운드에 있지 않을 때 실행할 필요가 없는 기능을 중지 할 수 있다.

또한 `onPuase()`메소드를 기소스를 해제하기 위해서 사용할 수 있따. 액티비티가 paused 되어 더이상 리소스를 사용하고자 하지 않을 때 베터리상태에 영향을 주는 gps와 같은 센서나 리소스를 다룰 수 있다. 하지만 `onResume()` 섹션에서 다뤘던 것 처럼 Paused activity는 멀티 윈도우 상태라면 여전히 보여지는 상태이다.  따라서 `onPause()` 대신에 `onStop()`을 사용하여 다중 윈도우 모드를 보다 잘 지원 할 수 있도록 UI관련 자원 및 작업을 완전히 해제하거나 조정하는 것을 고려해야 한다.

다음의 ON_PAUSE 이벤트를 다루는 lifecyclerObserver 예제는 ON_RESUM 이벤트가 수신 된 후 초기화 된 카메라를 해제하고 위의 ON_RESUME 이벤트 예제와 상응한다.

```java
public class JavaCameraComponent implements LifecycleObserver {

    ...

    @OnLifecycleEvent(Lifecycle.Event.ON_PAUSE)
    public void releaseCamera() {
        if (camera != null) {
            camera.release();
            camera = null;
        }
    }

    ...
}
```

위 코드 스니펫은 LifecycleObserver가 ON_PAUSE 이벤트를 수신 한 후 카메라 릴리스 코드를 배치한 것이다. 앞서 언급한 것처럼 [Handling Lifecycles with Liecycle-Aware Component](https://developer.android.com/topic/libraries/architecture/lifecycle)문서에서 자세한 것을 배울 수 있다.

`onPause()`의 실행은 매우 짧다. 상태를 저장하는 것 처럼 충분한 시간이 필요한 작업에는 적당하지 않다. 이런 이유에서 `onPause()`를 사용자 정보를 저장하거나, 네트워크, 데이터베이스와 연결하기 위해서 사용해서는 안된다. 이와 같은 작업들은 작업을 완료할 수 없다. 대신에 작업량이 많은 셧다운 작업은 `onStop()`에서 수행해야한다. 

`onPause()`의 완료는 액티비티가 Paused 상태를 벗어난 것을 의미하지않는다. 대신 상태가 재개되거나 사용자에게 완전히 보여지지 않을때까지 유지된다. 만약 액티비티가 재개된다면, 시스템은 다시한번 `onResume()`을 호출한다. 다시 pasued 상태에서 resume 상태가 되면, 시스템은 메모리에 액티비티를 상주하도록 한다. 이 시나리오에서 컴포넌트를 다시 초기화 할 필요가 없다. 만약 액티비티가 완전히 보여지지 않은 상태가 된다면 시스템은 onStop() 콜백 메소드를 호출한다. 



### onStop()

액티비티가 더이상 사용자에게 보이지 않는다면, 이는 Stopped 상태가 된것이며 시스템은 `onStop()`을 호출한다. 예를 들어 새로운 액티비티가 런치되어 전체 스크린을 덮을 때 발생한다. 시스템은 또한  액티비티가 실행상태를 멈추거나 종료되면 이를 호출한다.

만약 액티비티가 stopped 상태가 된다면, lifecycle-aware 컴포넌트는 ON_STOP 이벤트를 수신받는다. 여기서 라이프 사이클 구성 요소는 화면에서 구성 요소를 볼 수 없는 동안 실행할 필요가 없는 기능을 중지 할 수 있다.

`onStop()`메소드에서 사용자에게 보이지 않는 리소스들을 해제하거나 조절해야한다. 예를 들어 앱에서 애니메이션을 일시 중지하거나 세분화 된 위치 업데이트에서 다른 위치 업데이트로 전환 할 수 있다. `onPause()`대신 `onStop()`를 사용하면 사용자가 다중 창 모드에서 활동을 보고있는 경우에도 UI관련 작업이 계속 진행된다.

또한 비교적 cpu를 많이 사용하는 셧다운 작업을 수행하려면 onStop()을 사용해야 한다. 예를 들어 데이터베이스에 저장 더 적절할 시간을 찾을 수 없을 것이다. 다음의 예제는 persistent storage에 note의 초안을 저장하는 onStop() 메소드의 구현이다.

```java
@Override
protected void onStop() {
    // call the superclass method first
    super.onStop();

    // save the note's current draft, because the activity is stopping
    // and we want to be sure the current note progress isn't lost.
    ContentValues values = new ContentValues();
    values.put(NotePad.Notes.COLUMN_NAME_NOTE, getCurrentNoteText());
    values.put(NotePad.Notes.COLUMN_NAME_TITLE, getCurrentNoteTitle());

    // do this update in background on an AsyncQueryHandler or equivalent
    mAsyncQueryHandler.startUpdate (
            mToken,  // int token to correlate calls
            null,    // cookie, not used here
            mUri,    // The URI for the note to update.
            values,  // The map of column names and new values to apply to them.
            null,    // No SELECT criteria are used.
            null     // No WHERE columns are used.
    );
}
```

위의 예제는 SQLite를 직접적으로 사용한 것이다. SQLite를 통해 추상화 계층을 제공하는 persistence libray인 Room을 사용해야 한다. Room 사용의 이점을 알아보고 사용법을 알아보기 위해서 [Room Persistence Libray](https://developer.android.com/topic/libraries/architecture/room) 가이드를 확인하라.

액티비티가 Stopped 상태에 들어가게 되면, 액티비티 객체는 메모리에 남게 된다. 이는 모든 상태와 멤버들이 남게 하지만 윈도우 매니저에 붙어지지은 않는다. 만약 액티비티가 다시 resume 된다면, 액티비티는 이정보를 다시 불러올 것이다. Resumed 상태에 이르는 콜백 메소드 중 생성 된 구성 요소를 다시 초기화 할 필요가 없다. 또한 시스템은 레이아웃의 각 View 객체에 대한 현재 상태를 추적하므로 사용자가 EditText 위젯에 텍스트를 입력하면 해당 내용이 유지되어 해당 내용을 저장하고 복원 할 필요가 없다.

Stopped 상태로부터 액티비티는 다시 사용자와 상호작용 할 수 있으며, 또는 영원히 사라질 수도 있다. 만약 액티비티가 다시 돌아온다면 시스템은 `onRestart()`메소드를 호출한다. 액티비타가 멈춘다면 시스템은 `onDestory()`를 호출하게 된다.



### onDestory()

`onDestory()`는 액티비티가 파괴되기 전에 불린다. 시스템은 다음과 같은 이유에서 콜백한다.

1. 액티비티가 종료된다(사용자가 활동을 완전히 취소했거나 finishi()를 를 호출함
2. 장치 회전 또는 다중 창모드 등의 시스템 구성 변경으로 인해 활동을 일시적으로 파괴

액티비티가 destoryed 상태가 되면 lifecycle-aware 컴포넌트는 ON_DESTORY 이벤트를 수신한다. lifecycle-aware 컴포넌트는 액티비티가 완전히 파괴되기전 필요한 모든 것을 정리한다.

활동에 논리를 두어 파괴 이유를 판별하는 대신 ViewModel 객체를 사용하여활동에 대한 관련 뷰 데이터를 포함해야 한다. 구성 변경으로 인해 액티비티가 다시 생성 될 경우 ViewModel은 보존되어 다음 액티비티 인스턴스에 주어지기 때문에 아무것도 할 필요가 없다. 만약 액티비티가 재 작성되지 않는다면 ViewModel은 onCleared() 메소드를 호출하여 파괴되기 전에 필요한 모든 데이터를 정리 할 수 있다.

isFinishing() 메서드를 사용하여 이 두 시나리오를 구별 할 수 있다.

액티비티가 완료되면 onDestory()는 액티비티가 수신하는 최종 라이프 사이클 콜백이다. 구성 변광의 결과로 onDestory()가 호출되면 시스템은 즉시 새 활동 인스턴스를 만든 다음 새 구성의 해당 새 인스턴스에서 onCreate()를 호출한다.

`onDestory()` 콜백은 `onStop()`과 같은 이전 콜백에 의해 아직 해제되지 않은 모든 리소스를 해제해야 한다.

