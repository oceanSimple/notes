# ♾️csharp
## 💫控制人物移动
```csharp
    void Update() {
        // 获取水平输入
        var horizontal = Input.GetAxis("Horizontal");
        // 获取垂直输入
        var vertical = Input.GetAxis("Vertical");
        // 创建一个新的方向向量
        var direction = new Vector3(horizontal, 0, vertical);

        // 根据输入方向移动玩家，速度为每秒5个单位
        transform.position += direction * Time.deltaTime * 5;
    }
```

>优化版

```csharp
    void Update() {
        // 获取水平输入
        var horizontal = Input.GetAxis("Horizontal");
        // 获取垂直输入
        var vertical = Input.GetAxis("Vertical");

        // 创建一个新的方向向量
        var direction = new Vector3(horizontal, 0, vertical);
        // 归一化方向向量, 确保向量的模为1
        // 作用: 确保玩家的移动速度是恒定的
        direction = direction.normalized;

        // 根据输入方向移动玩家，速度为每秒5个单位
        transform.position += direction * Time.deltaTime * 5;
    }
```

> 人物朝向模块

```csharp
if (transform.forward != Vector3.zero) {
	transform.forward = Vector3.Lerp(transform.forward, direction, Time.deltaTime * rotateSpeed);
	transform.forward = Vector3.Slerp(transform.forward, direction, Time.deltaTime * rotateSpeed);
}
```
•	Lerp 和 Slerp 都用于平滑旋转玩家的朝向。
•	Lerp 是线性插值，Slerp 是球面插值，Slerp 的效果更自然。

## 💫状态机转换
> 示例: 静止和移动状态的转换

当前两个状态机
![[Pasted image 20250318150247.png|280]]

设置一个`IsWalking`字段

通过代码控制
```csharp
public class PlayerAnimator : MonoBehaviour {
    private const string IS_WALKING = "IsWalking";

    private Animator anim;

    [SerializeField]
    private Player player;

    // Start is called once before the first execution of Update after the MonoBehaviour is created
    void Start() {
        anim = GetComponent<Animator>();
    }

    // Update is called once per frame
    void Update() {
        anim.SetBool(IS_WALKING, player.IsWalking);
    }
}
```

在player中加入判定字段字段
```csharp
public class Player : MonoBehaviour {
    [SerializeField] private float moveSpeed = 5f;
    [SerializeField] private float rotateSpeed = 10f;
    private bool isWalking = false;

    // Start is called once before the first execution of Update after the MonoBehaviour is created
    void Start() {

    }

    // Update is called once per frame
    void Update() {
        // 获取水平输入
        var horizontal = Input.GetAxis("Horizontal");
        // 获取垂直输入
        var vertical = Input.GetAxis("Vertical");

        // 创建一个新的方向向量
        var direction = new Vector3(horizontal, 0, vertical);

        isWalking = direction != Vector3.zero;

        // 归一化方向向量, 确保向量的模为1
        // 作用: 确保玩家的移动速度是恒定的
        direction = direction.normalized;

        // 根据输入方向移动玩家，速度为每秒5个单位
        transform.position += direction * Time.deltaTime * moveSpeed;

        if (transform.forward != Vector3.zero) {
            transform.forward = Vector3.Lerp(transform.forward, direction, Time.deltaTime * rotateSpeed);
            transform.forward = Vector3.Slerp(transform.forward, direction, Time.deltaTime * rotateSpeed);
        }
    }

    public bool IsWalking {
        get { return isWalking; }
    }
}
```

## 💫按键映射
```csharp
using UnityEngine;

public class GameInput : MonoBehaviour {
    private GameControl gameControl;

    private void Awake() {
        gameControl = new GameControl();
        // 启用输入
        gameControl.Player.Enable();
    }

    public Vector3 GetMovementDirectionNormalized() {

        var inputVector2 = gameControl.Player.Move.ReadValue<Vector2>();


        // 创建一个新的方向向量
        var direction = new Vector3(inputVector2.x, 0, inputVector2.y);
        direction = direction.normalized;
        return direction;
    }
}
```

## 💫解决刚体晃动问题
> 将控制移动的函数放入FixedUpdate中

```csharp
    private void FixedUpdate() {
        var direction = gameInput.GetMovementDirectionNormalized();

        isWalking = direction != Vector3.zero;

        // 归一化方向向量, 确保向量的模为1
        // 作用: 确保玩家的移动速度是恒定的
        direction = direction.normalized;

        // 根据输入方向移动玩家，速度为每秒5个单位
        transform.position += direction * Time.deltaTime * moveSpeed;

        if (transform.forward != Vector3.zero) {
            transform.forward = Vector3.Lerp(transform.forward, direction, Time.deltaTime * rotateSpeed);
            transform.forward = Vector3.Slerp(transform.forward, direction, Time.deltaTime * rotateSpeed);
        }
    }
```

## 💫交互操作
- 给目标物体绑定脚本
```csharp
public class ClearCounter : MonoBehaviour {
    // Start is called once before the first execution of Update after the MonoBehaviour is created
    public void Interact() {
        print(this.gameObject + "is interacting ...");
    }
}
```

- 主角进行调用
```csharp
    private void Update() {
        HandleInteraction();
    }
    
    private void HandleInteraction() {
        // 判断物体视线前方是否有物体
        if (Physics.Raycast(transform.position, transform.forward, out RaycastHit hitinfo, 2f)) {
            // 判断是否是柜台
            if (hitinfo.transform.TryGetComponent<ClearCounter>(out ClearCounter clearCounter)) {
                clearCounter.Interact();
            }
        }
    }
```


> 优化, layer

![[Pasted image 20250320113102.png|400]]

创建一个`Counter`层

```csharp
	[SerializeField]
    private LayerMask counterLayerMask;
    
    private void HandleInteraction() {
        // 判断物体视线前方是否有物体
        if (Physics.Raycast(transform.position, transform.forward, out RaycastHit hitinfo, 2f, counterLayerMask)) {
            // 判断是否是柜台
            if (hitinfo.transform.TryGetComponent<ClearCounter>(out ClearCounter clearCounter)) {
                clearCounter.Interact();
            }
        }
    }
```

![[Pasted image 20250320113214.png|450]]


