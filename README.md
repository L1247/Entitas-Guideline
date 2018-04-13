# Entitas-Guideline
自己操作Entitas的經驗與心得

# Online Toturials
[官方Entitas Github](https://github.com/sschmid/Entitas-CSharp)

[Entitas CookBook](https://github.com/mzaks/EntitasCookBook)

[Cookbook 簡體中文翻譯](http://skyrimwu.cn/)

[Unity3D ECS框架 Entitas入门学习系列](http://blog.csdn.net/u012632851/article/category/7034956)

[用Entitas开发游戏的体验是怎么样的？](https://www.zhihu.com/question/64701202)

[Entitas-Lite](https://github.com/rocwood/Entitas-Lite)

# Class Design
* Component的Class盡量不使用NameSpace，避免在System使用時的麻煩步驟。

# 經驗
### UniRx 
* 對象如果是Null 在後面加上Observerable，則無反應也不會報錯誤。
### Unity Editor Inspector Order.
![alt text](https://raw.githubusercontent.com/L1247/Entitas-Guideline/master/Experience%20Textures/Component%20Order.png)
使用

> 使用Event( bool bindToEntity ) 時，設定bindToEntity此數值為True與False有著很大的差別。
![alt text](https://github.com/L1247/Entitas-Guideline/blob/master/Experience%20Textures/Event(bool%20bindToEntity%20).png?raw=true)

True的情況：會在Entity上做監聽，監聽的是擁有Data Component與Listener的Entity。

False的情況則是只監聽Data Component而已。
![alt text](https://github.com/L1247/Entitas-Guideline/blob/master/Experience%20Textures/Event(bool%20bindToEntity%20)_Difference.png?raw=true)

* 找尋Entity的做法。
1. 
```Csharp
GameObject aa         = new GameObject("aa");
GameObject bb         = new GameObject("bb");
var        gameEntity = _gameContext.CreateEntity();
gameEntity.AddMonster(aa.transform);
gameEntity.AddDebugMessage("abc");
aa.Link(gameEntity , _gameContext);
bb.Link(gameEntity , _gameContext);

aa.GetEntityLink().entity.OnComponentReplaced 
    += (entity , index , component , newComponent) =>
       {
           if (component is DirectionComponent)
           {
               var directionComponent = newComponent as DirectionComponent;
               Debug.Log(directionComponent.value);
               Debug.Log(newComponent);
           }
       };
Debug.Log(((GameEntity)(aa.GetEntityLink().entity)).debugMessage.message);
Debug.Log(_gameContext.GetEntityWithMonster(aa.transform).debugMessage.message);
```

# 觀念
* 預設繼承IComponent的Component是第一個Context，通常預設不改則為GameContext所有。

* Group ： 開發者宣告的收集器的種類
如 _context.GetGroup(GameMatcher.DebugMessage); 、 _context.GetGroup(GameMatcher.AllOf(GameMatcher.DebugMessage , GameMatcher.Character));
就會產生兩個Group

# ReactiveSystem<TEntity> 
```Csharp
預設如果只用以下方式，只有在被觀察的Component加入到Entity或者數值改變時會抓到事件。
protected override ICollector<GameEntity> GetTrigger(IContext<GameEntity> context)
{
    return context.CreateCollector(matcher);
}

如果連移除Component都想抓到時可以改成以下方式。
protected override ICollector<TEntity> GetTrigger(IContext<TEntity> context)
{
    return context.CreateCollector(new TriggerOnEvent<TEntity>(matcher, GroupEvent.AddedOrRemoved));
}

或者其餘方式可參考
public enum GroupEvent : byte
{
  Added,
  Removed,
  AddedOrRemoved,
}
```
# MultiReactiveSystem 
[MultiReactiveSystem Tutorial](https://github.com/sschmid/Entitas-CSharp/wiki/MultiReactiveSystem-Tutorial#performing-context-specific-actions-in-multi-reactive-systems)
## 與官方Github宣告的操作方式不同，估計是新版的做法改變了，Ver. 1.42
![alt text](https://raw.githubusercontent.com/L1247/Entitas-Guideline/master/Error%20Textures/IviewEntity.png )
## 如果遇到以下錯誤，就是找不到對應的isAssignView，因為沒有使用IviewEntity，使用的IViewEntity沒有繼承IAssignViewEntity。
![alt text](https://raw.githubusercontent.com/L1247/Entitas-Guideline/master/Error%20Textures/MultiReactiveSystem%20Runtime%20Error.png )
