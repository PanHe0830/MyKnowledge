# World相关
## 1.FWorldDelegates
1.一个关于UE中UWorld(关卡)相关的钩子 本质是全局静态Delegate集合，用来监听所有World的生命周期事件
2.可以在GameInstance/SubSystem中监听World的创建，初始化，销毁，切换，Tick 同时避免侵入GameMode/Actor和UWorld强相关的类/模块
3.在Editor World中也会触发 需要进行IsGameWorld()判断
4.PIE问题，PIE是play in editor的缩写，意思是编辑器内运行游戏，当点击Play按钮后引擎不会直接运行当前关卡而是复制一份 World，在编辑器里启动一个“游戏实例” 这个过程就是PIE
5.通常在全局系统，插件开发中使用
### 1.1 常用的Delegate选择
UI初始化（常驻UI）：
→ PostLoadMapWithWorld（推荐，语义清晰，关卡加载完成）
更底层控制（更早阶段）：
→ OnPostWorldInitialization（World初始化完成但未完全进入游戏逻辑）
World切换监听：
→ OnGameInstanceWorldChanged（GameInstance层World替换）

# PIE 的模式
1.单窗口PIE 一个Game World 一个Player
2.多人PIE 多个World 同时由于FWorldDelegates是UWorld相关的钩子，所以其中的代理会触发多次使用 World->IsGameWorld() 过滤
3.模拟模式 不是完整的GameInstance