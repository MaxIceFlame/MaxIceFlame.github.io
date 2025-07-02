# 跳舞的线官方相机跟随在直角坐标系的实现方式
*****
* **官方常用坐标系沿-45°/45°方向，本文简称“斜角坐标系”；饭制常用坐标系多沿0°/90°方向，本文简称“直角坐标系”**

## 实现原理
在斜角坐标系中，x轴为相机的偏移轴，z轴是相机真正跟随线移动而移动的轴。
![follow1](img/follow1.png)

在斜角坐标系中，相机只沿z轴向前移动，相比在x轴和z轴均向前移动的直角坐标系，更加平滑。且相机在x轴存在约1.5单位长度的偏移，保证线始终在屏幕中央

欲在直角坐标系中实现此效果，只需在代码中将原本跟随的坐标空间及相关变量旋转45°即可

## 实现方式
欲在直角坐标系中实现，需先定义如下量：
```csharp
Vector3 followSpeed = new Vector3(1.2f,3f,6f); //跟随速度
Quaternion vectorRotation = Quaternion.Euler(0,-45,0); //坐标旋转角度
Transform origin; //坐标原点物体
Vector3 Translation //旋转后的跟随数据
{
    get
    {
        var targetPosition = vectorRotation * target.position;
        var followerPosition = vectorRotation * follower.position;
        return targetPosition - followerPosition;
    }
}

```
在```Start()```函数中，需设置坐标原点物体
```csharp
origin = new GameObject("TranslatingOrigin")
{
    transform =
    {
        position = Vector3.zero,
        rotation = Quaternion.Euler(0, 45, 0),
        localScale = Vector3.one
    }
}.transform;
```

csharp
在```Update()```函数中，需修改相关代码
```
var translation = new Vector3(
    Translation.x * Time.smoothDeltaTime * followSpeed.x, 
    Translation.y * Time.smoothDeltaTime * followSpeed.y, 
    Translation.z * Time.smoothDeltaTime * followSpeed.z
);
```

设置新的坐标原点
```
follower.Translate(translation, origin);
```