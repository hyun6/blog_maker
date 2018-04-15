# Scene
하나의 root 노드를 가짐
트리 형태로 노드 확장

## Scene을 하위 노드로
*다른 scene 을 하위 노드로 가져올 수 있음*
즉, 작은 단위의 scene 을 작성 후 재사용&합성해서 하나의 scene 완성 가능
원본 scene 을 수정하면 확장한 scene 에서도 수정 사항이 적용 (링크 개념)
하지만 확장한 scene 에서 원본 scene 의 instance를 수정하면 수정된 값 계속 사용

# Script
### _process(delta)
매 프레임 호출
정확한 간격으로 호출되지 않음

### _physics_process()
physics 처리 전 호출
정확한 간격으로 호출 (기본 설정 60FPS)

## Group
### 에디터-노드-그룹 에서 추가
### 코드로 추가
~~~
func _ready() :
    add_to_group("enemies")
~~~
### 그룹 활용 예
적에게 발각 되었을 때 모든 적들에게 notify 하는 법
~~~
func _on_discovered(): # this is a purely illustrative function
    get_tree().call_group("enemies", "player_was_discovered")
~~~
혹은 아래 코드를 통해 모든 적들의 노드 리스트를 얻을 수 있음
~~~
var enemies = get_tree().get_nodes_in_group("enemies")
~~~

## Notifications
윈도우 메시지 프로시저 같은 용도로 사용 가능한듯..
보통 GDScript 에서 각 notify 에 대한 overrideable 함수를 제공하기 때문에 사용할 일이 별로 없다고 함

~~~
func _notification(what):
    match what:
        NOTIFICATION_READY:
            print("This is the same as overriding _ready()...")
        NOTIFICATION_PROCESS:
            print("This is the same as overriding _process()...")
~~~

## overrideable 함수
notification 대신 사용할 수 있도록 제공되는 아래와 같은 함수들
~~~
func _enter_tree():
    # When the node enters the _Scene Tree_, it becomes active
    # and  this function is called. Children nodes have not entered
    # the active scene yet. In general, it's better to use _ready()
    # for most cases.
    pass

func _ready():
    # This function is called after _enter_tree, but it ensures
    # that all children nodes have also entered the _Scene Tree_,
    # and became active.
    pass

func _exit_tree():
    # When the node exits the _Scene Tree_, this function is called.
    # Children nodes have all exited the _Scene Tree_ at this point
    # and all became inactive.
    pass

func _process(delta):
    # This function is called every frame.
    pass

func _physics_process(delta):
    # This is called every physics frame.
    pass
~~~
## 노드 생성/제거
부모 노드가 제거되면 자식 노드들도 모두 제거됨  
`Node.new()/free()` 와 같이 사용 가능
`free()`의 경우 시그널 처리 등에 safe 하지 않기 때문에 `queue_free()`를 사용하도록 하자

## scene 생성
~~~
var scene = load("res://myscene.tscn") # will load when the script is instanced
~~~
~~~
var scene = preload("res://myscene.tscn") # will load when parsing the script
~~~
여기서 scene 은 아직 node가 아님
PackedScene.instance() 를 통해 노드화 시킬 수 있음
~~~
var node = scene.instance()
add_child(node)
~~~

## get_node() == `$`
`$` 는 현재 노드의 상대 경로로 다른 노드에 접근할 때 쓰이는 키워드로 아래 두 구문은 동일하다
~~~
$AnimatedSprite.play()
get_node("AnimatedSprite").play()
~~~

## Scene 전환
일반적으로 `get_tree().change_scene(${SCENE_PATH_STRING})` 형태로 scene 의 경로를 넣어주면 전환된다  
하지만 이 때 scene을 로드하기 위해 thread 가 block 되기 때문에 백그라운드에서 scene을 로드 할 필요가 있다

### ResourceInteractiveLoader
리소스의 로드를 stage로 나눈다. 예를 들어 이미지 10개가 있는 scene 에서는 각 이미지가 1개의 stage로 총 10개의 stage로 나뉘게 된다.

**Obtaining a ResourceInteractiveLoader**
~~~
Ref<ResourceInteractiveLoader> ResourceLoader::load_interactive(String p_path);
~~~

**비동기 로드**
~~~
Error ResourceInteractiveLoader::poll();
~~~
> Error::OK : 에러 없음  
> Error::ERR_FILE_EOF : 로딩 완료

**로드 상태 확인 (optional)**
~~~
int ResourceInteractiveLoader::get_stage_count() const;
int ResourceInteractiveLoader::get_stage() const;
~~~

**강제 동기 로딩 (optional)**
~~~
Error ResourceInteractiveLoader::wait();
~~~

**로딩된 리소스 얻기**
~~~
Ref<Resource> ResourceInteractiveLoader::get_resource();
~~~

### autoload

### call_deferred()
`call_deferred(${FUNC_NAME_STRING}, arg, arg2 ...)` : idle 타임에 해당 함수를 수행한다. 다른 스크립트가 실행 중일 때 scene 을 제거하는 등의 작업을 할 경우 위험하기 때문에 안전한 타이밍에 호출하기 위해 주로 사용함


# 참고
공식 문서 사이트 : http://docs.godotengine.org
 - 의외로 구글 번역하면 알아보기 쉬움