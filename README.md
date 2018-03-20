# Entitas-Guideline
自己操作Entitas的經驗與心得

# Class Design
* Component的Class不要使用NameSpace，避免在System使用時的麻煩步驟。

# 觀念

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
