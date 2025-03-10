# ♾️常用
## 💫debug

```csharp
Debug.Log()
```

## 💫transform.positon
> 注意, 这里是值传递!
> - 2D用 Vector2, 3D用Vector3

```csharp
		Vector2 position = transform.position;
		position.x += 0.001f;
		transform.position = position;
```

## 💫Input
> 水平: Horizontal
> 垂直: Vertical
> Time.deltaTime: 每帧之间的时间间隔

使用wasd控制人物
```csharp
    void Update() {
        var horizontal = Input.GetAxis("Horizontal");
        var vertical = Input.GetAxis("Vertical");
        var position = (Vector2)transform.position;
        position.x += 1f * horizontal * Time.deltaTime;
        position.y += 1f * vertical * Time.deltaTime;
        transform.position = position;
    }
```

1f * vertical * Time.deltaTime;
这句话的意思是, 保证1s可以移动1个单位

## 💫触发检测
```csharp
public class Potion : MonoBehaviour {
    private void OnTriggerEnter2D(Collider2D collision) {
        Debug.Log("Potion collided with " + collision.gameObject.name);
    }

    private void OnTriggerStay2D(Collider2D collision) {

    }

    private void OnTriggerExit2D(Collider2D collision) {

    }
}
```

## 💫销毁物体
```csharp
Destroy(gameObject);
Destroy(collision.gameObject);
```


# ♾️其他
## 💫调整帧率
```csharp
void Start() {
    Application.targetFrameRate = 10;
}
```

## 💫数值计算(限定最大最小值)
```csharp
Mathf.Clamp(currentHealth + amount, 0, maxHealth);
```

Mathf.Clamp(value, min, max)：将计算结果限制在 0 到 maxHealth 之间。
•	如果计算结果小于 0，则返回 0。
•	如果计算结果大于 maxHealth，则返回 maxHealth。
•	否则，返回计算结果。






