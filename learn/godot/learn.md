# ♾️生命周期

常用生命周期函数

```csharp
public partial class Sprite2d : Sprite2D {
    public override void _EnterTree() {
        base._EnterTree();
    }

    public override void _Ready() {
    }

    public override void _Process(double delta) {
        base._Process(delta);
    }

    public override void _PhysicsProcess(double delta) {
        base._PhysicsProcess(delta);
    }

    public override void _ExitTree() {
        base._ExitTree();
    }
}
```

## 💫EnterTree && Ready
背景:
- 父子关系 Node -> C1 -> C2 -> C3
- Node是组祖宗

EnterTree: 将子节点按照 C1, C2, C3 的顺序挂载到 Node 上. 此时如果 C1 调用了 C2 的方法, 会有空指针异常!
Ready: 按照 C3, C2, C1 的顺序执行Ready里面的业务, 由于此时所有子节点已经全部挂载并初始化, 因此可以互相调用

> 推荐通常在 Ready 中进行初始化


## 💫ExitTree
销毁

## 💫Process && PhysicsProcess
Process: 每一帧调用一次
PhysicsProcess: 物理帧调用





