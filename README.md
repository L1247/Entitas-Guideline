# Entitas-Guideline
自己操作Entitas的經驗與心得

# Class Design
* Component的Class不要使用NameSpace，避免在System使用時的麻煩步驟。

# 觀念
* Group ： 開發者宣告的收集器的種類
如 _context.GetGroup(GameMatcher.DebugMessage); 、 _context.GetGroup(GameMatcher.AllOf(GameMatcher.DebugMessage , GameMatcher.Character));
就會產生兩個Group
