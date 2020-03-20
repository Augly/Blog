### 引
在我们生活的世界中，每个人每个物体之间都会产生一些错综复杂的联系。在应用程序里也是一样，程序由大大小小的单一对象组成，所有这些对象都按照某种关系和规则来通信。

平时我们大概能记住10个朋友的电话、30家餐馆的位置。在程序里，也许一个对象会和其他10个对象打交道，所以它会保持10个对象的引用。当程序的规模增大，对象会越来越多，它们之间的关系也越来越复杂，难免会形成网状的交叉引用。当我们改变或删除其中一个对象的时候，很可能需要通知所有引用到它的对象。这样一来，就像在心脏旁边拆掉一根毛细血管一般，即使一点很小的修改也必须小心翼翼，如图所示。
![网状图](https://user-gold-cdn.xitu.io/2019/12/7/16edf892dc15c9bf?w=633&h=327&f=png&s=14738)

面向对象设计鼓励将行为分布到各个对象中，把对象划分成更小的粒度，有助于增强对象的可复用性，但由于这些细粒度对象之间的联系激增，又有可能会反过来降低它们的可复用性。

中介者模式的作用就是**解除对象与对象之间的紧耦合关系**。增加一个中介者对象后，所有的相关对象都通过中介者对象来通信，而不是互相引用，所以当一个对象发生改变时，只需要通知中介者对象即可。中介者使各对象之间耦合松散，而且可以独立地改变它们之间的交互。中介者模式使网状的多对多关系变成了相对简单的一对多关系，如图所示
![中介者](https://user-gold-cdn.xitu.io/2019/12/7/16edf892dcac8ddd?w=632&h=341&f=png&s=12835)

在图中，如果对象A发生了改变，则需要同时通知跟A发生引用关系的B、D、E、F这4个对象；而在图14-2中，使用中介者模式改进之后，A发生改变时则只需要通知这个中介者对象即可。

### 现实中的中介者
在现实生活中也有很多中介者的例子，下面列举几个。

#### 1.机场指挥塔
中介者也被称为调停者，我们想象一下机场的指挥塔，如果没有指挥塔的存在，每一架飞机要和方圆100公里内的所有飞机通信，才能确定航线以及飞行状况，后果是不可想象的。现实中的情况是，每架飞机都只
需要和指挥塔通信。指挥塔作为调停者，知道每一架飞机的飞行状况，所以它可以安排所有飞机的起降时间，及时做出航线调整。
![机场指挥塔](https://user-gold-cdn.xitu.io/2019/12/7/16edf892dd0dd16e?w=629&h=365&f=png&s=21044)

#### 2.博彩公司
打麻将的人经常遇到这样的问题，打了几局之后开始计算钱，A自摸了两把，B杠了三次，C点炮一次给D，谁应该给谁多少钱已经很难计算清楚，而这还是在只有4个人参与的情况下。

在世界杯期间购买足球彩票，如果没有博彩公司作为中介，上千万的人一起计算赔率和输赢绝对是不可能实现的事情。有了博彩公司作为中介，每个人只需和博彩公司发生关联，博彩公司会根据所有人的投注情况计算好赔率，彩民们赢了钱就从博彩公司拿，输了钱就交给博彩公司。

### 中介者模式的例子——泡泡堂游戏

首先先定义一个玩家构造函数，它有3个简单的原型方法：Play.prototype.win、Play.prototype.lose以及表示玩家死亡的Play.prototype.die。

因为玩家的数目是2，所以当其中一个玩家死亡的时候游戏便结束, 同时通知它的对手胜利。这段代码看起来很简单：
```
function Player( name ){
    this.name = name
    this.enemy = null; // 敌人
};
Player.prototype.win = function(){
    console.log( this.name + ' won ' );
};
Player.prototype.lose = function(){
    console.log( this.name +' lost' );
};
Player.prototype.die = function(){
    this.lose();
    this.enemy.win();
};

```
接下来创建2个玩家对象：
```
var player1 = new Player( '皮蛋' );
var player2 = new Player( '小乖' )
```
给玩家相互设置敌人：
```
player1.enemy = player2;
player2.enemy = player1;
```
当玩家player1被泡泡炸死的时候，只需要调用这一句代码便完成了一局游戏：
```
player1.die();// 输出：皮蛋 lost、小乖 won
```

#### 为游戏增加队伍
现在我们改进一下游戏。因为玩家数量变多，用下面的方式来设置队友和敌人无疑很低效：
```
player1.partners= [player1,player2,player3,player4];
player1.enemies = [player5,player6,player7,player8];
Player5.partners= [player5,player6,player7,player8];
Player5.enemies = [player1,player2,player3,player4];

```
所以我们定义一个数组players来保存所有的玩家，在创建玩家之后，循环players来给每个玩家设置队友和敌人:
```
var players = [];
```
再改写构造函数Player，使每个玩家对象都增加一些属性，分别是队友列表、敌人列表、玩家当前状态、角色名字以及玩家所在的队伍颜色：
```
function Player( name, teamColor ){
    this.partners = []; // 队友列表
    this.enemies = []; // 敌人列表
    this.state = 'live'; // 玩家状态
    this.name = name; // 角色名字
    this.teamColor = teamColor; // 队伍颜色
};
```
玩家胜利和失败之后的展现依然很简单，只是在每个玩家的屏幕上简单地弹出提示：
```
Player.prototype.win = function(){ // 玩家团队胜利
    console.log( 'winner: ' + this.name );
};
Player.prototype.lose = function(){ // 玩家团队失败
    console.log( 'loser: ' + this.name );
};
```
玩家死亡的方法要变得稍微复杂一点，我们需要在每个玩家死亡的时候，都遍历其他队友的生存状况，如果队友全部死亡，则这局游戏失败，同时敌人队伍的所有玩家都取得胜利，代码如下：
```
Player.prototype.die = function(){ // 玩家死亡
    var all_dead = true;
    this.state = 'dead'; // 设置玩家状态为死亡
    for ( var i = 0, partner; partner = this.partners[ i++ ]; ){ // 遍历队友列表
        if ( partner.state !== 'dead' ){ // 如果还有一个队友没有死亡，则游戏还未失败
            all_dead = false;
            break;
        }
    }
    if ( all_dead === true ){ // 如果队友全部死亡
        this.lose(); // 通知自己游戏失败
        for ( var i = 0, partner; partner = this.partners[ i++ ]; ){ // 通知所有队友玩家游戏失败
            partner.lose();
        }
        for ( var i = 0, enemy; enemy = this.enemies[ i++ ]; ){ // 通知所有敌人游戏胜利
            enemy.win();
        }
    }
};
```
最后定义一个工厂来创建玩家：
```
var playerFactory = function( name, teamColor ){
    var newPlayer = new Player( name, teamColor ); // 创建新玩家
    for ( var i = 0, player; player = players[ i++ ]; ){ // 通知所有的玩家，有新角色加入
        if ( player.teamColor === newPlayer.teamColor ){ // 如果是同一队的玩家
            player.partners.push( newPlayer ); // 相互添加到队友列表
            newPlayer.partners.push( player );
        }else{
            player.enemies.push( newPlayer ); // 相互添加到敌人列表
            newPlayer.enemies.push( player );
        }
    }
    players.push( newPlayer );
    return newPlayer;
};
```
现在来感受一下, 用这段代码创建8个玩家：
```
//红队：
    var player1 = playerFactory( '皮蛋', 'red' ),
    player2 = playerFactory( '小乖', 'red' ),
    player3 = playerFactory( '宝宝', 'red' ),
    player4 = playerFactory( '小强', 'red' );
//蓝队：
    var player5 = playerFactory( '黑妞', 'blue' ),
    player6 = playerFactory( '葱头', 'blue' ),
    player7 = playerFactory( '胖墩', 'blue' ),
    player8 = playerFactory( '海盗', 'blue' );
```
让红队玩家全部死亡：
```
player1.die();
player2.die();
player4.die();
player3.die();
```

#### 玩家增多带来的困扰
现在我们已经可以随意地为游戏增加玩家或者队伍，但问题是，每个玩家和其他玩家都是紧紧耦合在一起的。在此段代码中，每个玩家对象都有两个属性，this.partners和this.enemies，用来保存其他玩家对象的引用。当每个对象的状态发生改变，比如角色移动、吃到道具或者死亡时，都必须要显式地遍历通知其他对象。

在这个例子中只创建了8个玩家，或许还没有对你产生足够多的困扰，而如果在一个大型网络游戏中，画面里有成百上千个玩家，几十支队伍在互相厮杀。如果有一个玩家掉线，必须从所有其他玩家的队友列表和敌人列表中都移除这个玩家。游戏也许还有解除队伍和添加到别的队伍的功能，红色玩家可以突然变成蓝色玩家，这就不再仅仅是循环能够解决的问题了。面对这样的需求，我们上面的代码可以迅速进入投降模式。

#### 用中介者模式改造泡泡堂游戏

现在我们开始用中介者模式来改造上面的泡泡堂游戏， 改造后的玩家对象和中介者的关系如图所示。


![](https://user-gold-cdn.xitu.io/2019/12/7/16edf9e4ec2ca03e)

首先仍然是定义Player构造函数和player对象的原型方法，在player对象的这些原型方法中，不再负责具体的执行逻辑，而是把操作转交给中介者对象，我们把中介者对象命名为playerDirector：
```
function Player( name, teamColor ){
    this.name = name; // 角色名字
    this.teamColor = teamColor; // 队伍颜色
    this.state = 'alive'; // 玩家生存状态
};
Player.prototype.win = function(){
    console.log( this.name + ' won ' );
};
Player.prototype.lose = function(){
    console.log( this.name +' lost' );
};
/*******************玩家死亡*****************/
Player.prototype.die = function(){
    this.state = 'dead';
    playerDirector.ReceiveMessage( 'playerDead', this ); // 给中介者发送消息，玩家死亡
};
/*******************移除玩家*****************/
Player.prototype.remove = function(){
    playerDirector.ReceiveMessage( 'removePlayer', this ); // 给中介者发送消息，移除一个玩家
};
/*******************玩家换队*****************/
Player.prototype.changeTeam = function( color ){
    playerDirector.ReceiveMessage( 'changeTeam', this, color ); // 给中介者发送消息，玩家换队
};

```
再继续改写之前创建玩家对象的工厂函数，可以看到，因为工厂函数里不再需要给创建的玩家对象设置队友和敌人，这个工厂函数几乎失去了工厂的意义：
```
var playerFactory = function( name, teamColor ){
    var newPlayer = new Player( name, teamColor ); // 创造一个新的玩家对象
    playerDirector.ReceiveMessage( 'addPlayer', newPlayer ); // 给中介者发送消息，新增玩家
    return newPlayer;
};
```
最后，我们需要实现这个中介者playerDirector对象，一般有以下两种方式。

- 利用发布—订阅模式。将playerDirector实现为订阅者各player作为发布者，一旦player的状态发生改变，便推送消息给playerDirector，playerDirector处理消息后将反馈发送给其他player。
- 在playerDirector中开放一些接收消息的接口，各player可以直接调用该接口来给playerDirector发送消息，player只需传递一
个参数给playerDirector，这个参数的目的是使playerDirector可以识别发送者。同样，playerDirector接收到消息之后会将处理结果反馈给其他player。

这两种方式的实现没什么本质上的区别。在这里我们使用第二种方式，playerDirector开放一个对外暴露的接口ReceiveMessage，负
责接收player对象发送的消息，而player对象发送消息的时候，总是把自身this作为参数发送给playerDirector，以便playerDirector识别消息来自于哪个玩家对象，代码如下：
```
var playerDirector= ( function(){
    var players = {}, // 保存所有玩家
    operations = {}; // 中介者可以执行的操作
    /****************新增一个玩家***************************/
    operations.addPlayer = function( player ){
        var teamColor = player.teamColor; // 玩家的队伍颜色
        players[ teamColor ] = players[ teamColor ] || []; // 如果该颜色的玩家还没有成立队伍，则
        新成立一个队伍
        players[ teamColor ].push( player ); // 添加玩家进队伍
    };
    /****************移除一个玩家***************************/
    operations.removePlayer = function( player ){
        var teamColor = player.teamColor, // 玩家的队伍颜色
        teamPlayers = players[ teamColor ] || []; // 该队伍所有成员
        for ( var i = teamPlayers.length - 1; i >= 0; i-- ){ // 遍历删除
            if ( teamPlayers[ i ] === player ){
                teamPlayers.splice( i, 1 );
            }
        }
    };
    /****************玩家换队***************************/
    operations.changeTeam = function( player, newTeamColor ){ // 玩家换队
        operations.removePlayer( player ); // 从原队伍中删除
        player.teamColor = newTeamColor; // 改变队伍颜色
        operations.addPlayer( player ); // 增加到新队伍中
    };
    operations.playerDead = function( player ){ // 玩家死亡
        var teamColor = player.teamColor,
        teamPlayers = players[ teamColor ]; // 玩家所在队伍
        var all_dead = true;
        for ( var i = 0, player; player = teamPlayers[ i++ ]; ){
            if ( player.state !== 'dead' ){
                all_dead = false;
                break;
            }
        }
        if ( all_dead === true ){ // 全部死亡
            for ( var i = 0, player; player = teamPlayers[ i++ ]; ){
                player.lose(); // 本队所有玩家lose
            }
            for ( var color in players ){
                if ( color !== teamColor ){
                    var teamPlayers = players[ color ]; // 其他队伍的玩家
                    for ( var i = 0, player; player = teamPlayers[ i++ ]; ){
                        player.win(); // 其他队伍所有玩家win
                    }
                }
            }
        }
    };
    var ReceiveMessage = function(){
        var message = Array.prototype.shift.call( arguments ); // arguments的第一个参数为消息名称
        operations[ message ].apply( this, arguments );
    };
    return {
        ReceiveMessage: ReceiveMessage
    }
})();
```
可以看到，除了中介者本身，没有一个玩家知道其他任何玩家的存在，玩家与玩家之间的耦合关系已经完全解除，某个玩家的任何操作都不需要通知其他玩家，而只需要给中介者发送一个消息，中介者处理完消息之后会把处理结果反馈给其他的玩家对象。我们还可以继续给中介者扩展更多功能，以适应游戏需求的不断变化。

我们来看下测试结果：
```
// 红队：
    var player1 = playerFactory( '皮蛋', 'red' ),
    player2 = playerFactory( '小乖', 'red' ),
    player3 = playerFactory( '宝宝', 'red' ),
    player4 = playerFactory( '小强', 'red' );
// 蓝队：
    var player5 = playerFactory( '黑妞', 'blue' ),
    player6 = playerFactory( '葱头', 'blue' ),
    player7 = playerFactory( '胖墩', 'blue' ),
    player8 = playerFactory( '海盗', 'blue' );
    player1.die();
    player2.die();
    player3.die();
    player4.die();

```
假设皮蛋和小乖掉线
```
player1.remove();
player2.remove();
player3.die();
player4.die();
```
假设皮蛋从红队叛变到蓝队
```
player1.changeTeam( 'blue' );
player2.die();
player3.die();
player4.die();
```

### 中介者模式的例子——购买商品
假设我们正在编写一个手机购买的页面，在购买流程中，可以选择手机的颜色以及输入购买数量，同时页面中有两个展示区域，分别向用户展示刚刚选择好的颜色和数量。还有一个按钮动态显示下一步的操作，我们需要查询该颜色手机对应的库存，如果库存数量少于这次的购买数量，按钮将被禁用并且显示库存不足，反之按钮可以点击并且显示放入购物车。

这个需求是非常容易实现的，假设我们已经提前从后台获取到了所有颜色手机的库存量：

```
var goods = { // 手机库存
"red": 3,
"blue": 6
};
```
那么页面有可能显示为如下几种场景：
选择红色手机，购买4个，库存不足。

![](https://user-gold-cdn.xitu.io/2019/12/7/16edfa88062fc680?w=633&h=275&f=png&s=25815)
选择蓝色手机，购买5个，库存充足，可以加入购物车。

![](https://user-gold-cdn.xitu.io/2019/12/7/16edfa8d46794b6e?w=625&h=338&f=png&s=27423)
或者是没有输入购买数量的时候，按钮将被禁用并显示相应提示。

![](https://user-gold-cdn.xitu.io/2019/12/7/16edfa97cb9e32ed?w=626&h=275&f=png&s=26976)
我们大概已经能够猜到，接下来将遇到至少5个节点，分别是：
- 下拉选择框 colorSelect
- 文本输入框 numberInput
- 展示颜色信息 colorInfo
- 展示购买数量信息 numberInfo
- 决定下一步操作的按钮 nextBtn

#### 开始编写代码
我们从编写HTML代码开始。
```
<body>
    选择颜色: <select id="colorSelect">
                <option value="">请选择</option>
                <option value="red">红色</option>
                <option value="blue">蓝色</option>
              </select>
    输入购买数量: <input type="text" id="numberInput"/>
    您选择了颜色: <div id="colorInfo"></div><br/>
    您输入了数量: <div id="numberInfo"></div><br/>
    <button id="nextBtn" disabled="true">请选择手机颜色和购买数量</button>
</body>
```
接下来将分别监听colorSelect的onchange事件函数和numberInput的oninput事件函数，然后在这两个事件中作出相应处理。
```
<script>
var colorSelect = document.getElementById( 'colorSelect' ),
numberInput = document.getElementById( 'numberInput' ),
colorInfo = document.getElementById( 'colorInfo' ),
numberInfo = document.getElementById( 'numberInfo' ),
nextBtn = document.getElementById( 'nextBtn' );
var goods = { // 手机库存
    "red": 3,
    "blue": 6
};
colorSelect.onchange = function(){
    var color = this.value, // 颜色
    number = numberInput.value, // 数量
    stock = goods[ color ]; // 该颜色手机对应的当前库存
    colorInfo.innerHTML = color;
    if ( !color ){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请选择手机颜色';
        return;
    }
    if ( ( ( number - 0 ) | 0 ) !== number - 0 ){ // 用户输入的购买数量是否为正整数
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请输入正确的购买数量';
        return;
    }
    if ( number > stock ){ // 当前选择数量超过库存量
        nextBtn.disabled = true;
        nextBtn.innerHTML = '库存不足';
        return ;
    }
    nextBtn.disabled = false;
    nextBtn.innerHTML = '放入购物车';
};
</script>

```
#### 对象之间的联系
来考虑一下，当触发了colorSelect的onchange之后，会发生什么事情。

首先我们要让colorInfo中显示当前选中的颜色，然后获取用户当前输入的购买数量，对用户的输入值进行一些合法性判断。再根据库存数量来判断nextBtn的显示状态。

别忘了，我们还要编写numberInput的事件相关代码：

```
numberInput.oninput = function(){
    var color = colorSelect.value, // 颜色
    number = this.value, // 数量
    stock = goods[ color ]; // 该颜色手机对应的当前库存
    numberInfo.innerHTML = number;
    if ( !color ){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请选择手机颜色';
        return;
    }
    if ( ( ( number - 0 ) | 0 ) !== number - 0 ){ // 输入购买数量是否为正整数
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请输入正确的购买数量';
        return;
    }
    if ( number > stock ){ // 当前选择数量没有超过库存量
        nextBtn.disabled = true;
        nextBtn.innerHTML = '库存不足';
        return ;
    }
    nextBtn.disabled = false;
    nextBtn.innerHTML = '放入购物车';
};
```
#### 可能遇到的困难
虽然目前顺利完成了代码编写，但随之而来的需求改变有可能给我们带来麻烦。假设现在要求去掉colorInfo和numberInfo这两个展示区域，我们就要分别改动colorSelect.onchange和numberInput.oninput里面的代码，因为在先前的代码中，这些对象确实是耦合在一起的。

目前我们面临的对象还不算太多，当这个页面里的节点激增到10个或者15个时，它们之间的联系可能变得更加错综复杂，任何一次改动都将变得很棘手。为了证实这一点，我们假设页面中将新增另外一个下拉选择框，代表选择手机内存。现在我们需要计算颜色、内存和购买数量，来判断nextBtn是显示库存不足还是放入购物车。

首先我们要增加两个HTML节点：
```
<body>
选择颜色: <select id="colorSelect">
<option value="">请选择</option>
<option value="red">红色</option>
<option value="blue">蓝色</option>
</select>
选择内存: <select id="memorySelect">
<option value="">请选择</option>
<option value="32G">32G</option>
<option value="16G">16G</option>
</select>
输入购买数量: <input type="text" id="numberInput"/><br/>
您选择了颜色: <div id="colorInfo"></div><br/>
您选择了内存: <div id="memoryInfo"></div><br/>
您输入了数量: <div id="numberInfo"></div><br/>
<button id="nextBtn" disabled="true">请选择手机颜色和购买数量</button>
</body>
<script>
var colorSelect = document.getElementById( 'colorSelect' ),
numberInput = document.getElementById( 'numberInput' ),
memorySelect = document.getElementById( 'memorySelect' ),
colorInfo = document.getElementById( 'colorInfo' ),
numberInfo = document.getElementById( 'numberInfo' ),
memoryInfo = document.getElementById( 'memoryInfo' ),
nextBtn = document.getElementById( 'nextBtn' );
</script>

```
接下来修改表示存库的JSON对象以及修改colorSelect的onchange事件函数：
```
<script>
var goods = { // 手机库存
    "red|32G": 3, // 红色32G，库存数量为3
    "red|16G": 0,
    "blue|32G": 1,
    "blue|16G": 6
};
colorSelect.onchange = function(){
    var color = this.value,
    memory = memorySelect.value,
    stock = goods[ color + '|' + memory ];
    number = numberInput.value, // 数量
    colorInfo.innerHTML = color;
    if ( !color ){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请选择手机颜色';
        return;
    }
    if ( !memory ){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请选择内存大小';
        return;
    }
    if ( ( ( number - 0 ) | 0 ) !== number - 0 ){ // 输入购买数量是否为正整数
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请输入正确的购买数量';
        return;
    }
    if ( number > stock ){ // 当前选择数量没有超过库存量
        nextBtn.disabled = true;
        nextBtn.innerHTML = '库存不足';
        return ;
    }
    nextBtn.disabled = false;
    nextBtn.innerHTML = '放入购物车';
};
</script>
```

当然我们同样要改写numberInput的事件相关代码，具体代码的改变跟colorSelect大同小异，读者可以自行实现。
最后还要新增memorySelect的onchange事件函数：
```
<script>
memorySelect.onchange = function(){
    var color = colorSelect.value, // 颜色
    number = numberInput.value, // 数量
    memory = this.value,
    stock = goods[ color + '|' + memory ]; // 该颜色手机对应的当前库存
    memoryInfo.innerHTML = memory;
    if ( !color ){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请选择手机颜色';
        return;
    }
    if ( !memory ){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请选择内存大小';
        return;
    }
    if ( ( ( number - 0 ) | 0 ) !== number - 0 ){ // 输入购买数量是否为正整数
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请输入正确的购买数量';
        return;
    }
    if ( number > stock ){ // 当前选择数量没有超过库存量
        nextBtn.disabled = true;
        nextBtn.innerHTML = '库存不足';
        return ;
    }
    nextBtn.disabled = false;
    nextBtn.innerHTML = '放入购物车';
};
</script>
```
很遗憾，我们仅仅是增加一个内存的选择条件，就要改变如此多的代码，这是因为在目前的实现中，每个节点对象都是耦合在一起的，改变或者增加任何一个节点对象，都要通知到与其相关的对象。

#### 引入中介者

现在我们来引入中介者对象，所有的节点对象只跟中介者通信。当下拉选择框colorSelect、memorySelect和文本输入框numberInput发生了事件行为时，它们仅仅通知中介者它们被改变了，同时把自身当作参数传入中介者，以便中介者辨别是谁发生了改变。剩下的所有事情都交给中介者对象来完成，这样一来，无论是修改还是新增节点，都只需要改动中介者对象里的代码。
```
var goods = { // 手机库存
    "red|32G": 3,
    "red|16G": 0,
    "blue|32G": 1,
    "blue|16G": 6
};
var mediator = (function(){
    var colorSelect = document.getElementById( 'colorSelect' ),
    memorySelect = document.getElementById( 'memorySelect' ),
    numberInput = document.getElementById( 'numberInput' ),
    colorInfo = document.getElementById( 'colorInfo' ),
    memoryInfo = document.getElementById( 'memoryInfo' ),
    numberInfo = document.getElementById( 'numberInfo' ),
    nextBtn = document.getElementById( 'nextBtn' );
    return {
        changed: function( obj ){
            var color = colorSelect.value, // 颜色
            memory = memorySelect.value,// 内存
            number = numberInput.value, // 数量
            stock = goods[ color + '|' + memory ]; // 颜色和内存对应的手机库存数量
            if ( obj === colorSelect ){ // 如果改变的是选择颜色下拉框
                colorInfo.innerHTML = color;
            }else if ( obj === memorySelect ){
                memoryInfo.innerHTML = memory;
            }else if ( obj === numberInput ){
                numberInfo.innerHTML = number;
            }
            if ( !color ){
                nextBtn.disabled = true;
                nextBtn.innerHTML = '请选择手机颜色';
                return;
            }
            if ( !memory ){
                nextBtn.disabled = true;
                nextBtn.innerHTML = '请选择内存大小';
                return;
            }
            if ( ( ( number - 0 ) | 0 ) !== number - 0 ){ // 输入购买数量是否为正整数
                nextBtn.disabled = true;
                nextBtn.innerHTML = '请输入正确的购买数量';
                return;
            }
            nextBtn.disabled = false;
            nextBtn.innerHTML = '放入购物车';
        }
    }
})();
// 事件函数：
colorSelect.onchange = function(){
    mediator.changed( this );
};
memorySelect.onchange = function(){
    mediator.changed( this );
};
numberInput.oninput = function(){
    mediator.changed( this );
};

```
可以想象，某天我们又要新增一些跟需求相关的节点，比如CPU型号，那我们只需要稍稍改动mediator对象即可：
```
var goods = { // 手机库存
"red|32G|800": 3, // 颜色red，内存32G，cpu800，对应库存数量为3
"red|16G|801": 0,
"blue|32G|800": 1,
"blue|16G|801": 6
};
var mediator = (function(){
    // 略
    var cpuSelect = document.getElementById( 'cpuSelect' );
    return {
        change: function(obj){
            // 略
            var cpu = cpuSelect.value,
            stock = goods[ color + '|' + memory + '|' + cpu ];
            if ( obj === cpuSelect ){
                cpuInfo.innerHTML = cpu;
            }
            // 略
        }
    }
})();
```

### 小结
中介者模式是迎合迪米特法则的一种实现。迪米特法则也叫最少知识原则，是指一个对象应该尽可能少地了解另外的对象（类似不和陌生人说话）。如果对象之间的耦合性太高，一个对象发生改变之后，难免会影响到其他的对象，跟“城门失火，殃及池鱼”的道理是一样的。而在中介者模式里，对象之间几乎不知道彼此的存在，它们只能通过中介者对象来互相影响对方。

因此，中介者模式使各个对象之间得以解耦，以中介者和对象之间的一对多关系取代了对象之间的网状多对多关系。各个对象只需关注自身功能的实现，对象之间的交互关系交给了中介者对象来实现和维护。

不过，中介者模式也存在一些缺点。其中，最大的缺点是系统中会新增一个中介者对象，因为对象之间交互的复杂性，转移成了中介者对象的复杂性，使得中介者对象经常是巨大的。中介者对象自身往往就是一个难以维护的对象。

我们都知道，毒贩子虽然使吸毒者和制毒者之间的耦合度降低，但毒贩子也要抽走一部分利润。同样，在程序中，中介者对象要占去一部分内存。而且毒贩本身还要防止被警察抓住，因为它了解整个犯罪链条中的所有关系，这表明中介者对象自身往往是一个难以维护的对象。

中介者模式可以非常方便地对模块或者对象进行解耦，但对象之间并非一定需要解耦。在实际项目中，模块或对象之间有一些依赖关系是很正常的。毕竟我们写程序是为了快速完成项目交付生产，而不是堆砌模式和过度设计。关键就在于如何去衡量对象之间的耦合程度。一般来说，如果对象之间的复杂耦合确实导致调用和维护出现了困难，而且这些耦合度随项目的变化呈指数增长曲线，那我们就可以考虑用中介者模式来重构代码。


