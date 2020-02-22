# WeiboAutoCommentTool

微博自动评论工具

*给自家哥哥刷数据用*

## 说明
自动评论某一条指定微博，评论内容从列表中随机选取。[原版本代码](https://github.com/ludiw823/WeiboAutoCommentTool)

## 使用方法

* 登录微博，进入【王晰微博主页】，选择【全部微博】
* 页面最下方有id对照，每次晰晰微博更新时，将需要评论的微博对应的id复制到对应的区域修改即可
* 打开浏览器打开开发者工具（Windows系统：浏览器按Shift+Ctrl+I或(Fn+)F12；苹果系统：Option+Command+I）
* 点击“console”或“控制台”把脚本内容全部复制粘贴到控制台，按回车

**模式：每轮27个评论，间隔20s，一共8轮，每轮之间间隔20分钟**
```
var weiboAutoCommentTool = (function(){

//评论内容list【可根据具体微博情况自行修改，注意文本与文本之间用英文字符'和,间隔开】
const contentList = ['#王晰有多迷人# @王晰 爱你[心]','#王晰天赐的声音# wslwsl@王晰 ','#王晰有多迷人# @王晰 好帅啊！！！','#王晰清唱# 哥哥太帅了！@王晰 ','今天又更加喜欢王晰了@王晰 ','#王晰天赐的声音# 🦊#王晰电子刊# 🦊王晰现场🦊王晰新歌🦊王晰拉麦🦊王晰军港之夜🦊王晰可爱🦊王晰清唱🦊王晰东方之珠🦊王晰摇滚🦊王晰救场🦊王晰重游往昔🦊王晰低音🦊王晰双鱼🦊王晰音域🦊王晰青藏高原🦊王晰采访🦊王晰考古🦊王晰声入人心🦊王晰金钟奖🦊王晰青歌赛 @王晰 ','#王晰天赐的声音# 🦊#王晰电子刊# @王晰 想你[亲亲]','#王晰天赐的声音# 🦊#王晰电子刊# 🦊王晰现场🦊王晰新歌🦊王晰拉麦🦊王晰军港之夜🦊王晰可爱🦊王晰清唱🦊王晰东方之珠🦊王晰救场🦊王晰低音🦊王晰双鱼🦊王晰音域🦊王晰青藏高原🦊王晰采访🦊王晰考古🦊王晰金钟奖🦊王晰青歌赛🦊王晰牛逼 @王晰  ','王晰为什么这么可爱[抓狂]','晰晰多发自拍呗[可怜]@王晰 ','#王晰天赐的声音# @王晰 哥哥超棒！[憧憬]','#王晰天赐的声音# @王晰 哥哥辛苦啦[心]','#王晰天赐的声音# @王晰 哥哥，自拍 vlog 小剧场 吃播可以安排吗[可怜]','#王晰电子刊#','#王晰天赐的声音#','💟💟💟💟💟💟💟💟💟💟💟💟💟💟  💟✨✨🎻王晰✨ 绝美男低🎻✨✨💟  💟✨✨✨♈️人间低音炮♈️✨✨✨💟  💟✨✨金钟奖 🥇🏆🥇青歌赛✨✨💟  💟✨✨✨ #王晰天赐的声音#✨✨ 💟  💟💟💟💟💟💟💟💟💟💟💟💟💟💟',' 🔶⚪️🔶⚪️🔶🎻王晰🎻🔶⚪⚪⚪🔶 🔶⚪🔶⚪️🔶🎻王晰🎻⚪️🔶⚪️🔶⚪️ 🔶⚪️🔶⚪️🔶🎻王晰🎻⚪️⚪️🔶⚪️⚪️ 🔶⚪️🔶⚪🔶🎻王晰🎻⚪️🔶⚪️🔶⚪️ 🔶🔶🔶🔶🔶🎻王晰🎻🔶⚪️⚪️⚪️🔶',' 🔴⚪️🔴⚪️🔴🎻王晰🎻🔴⚪⚪⚪🔴 🔴⚪🔴⚪️🔴🎻王晰🎻⚪️🔴⚪️🔴⚪️ 🔴⚪️🔴⚪️🔴🎻王晰🎻⚪️⚪️🔴⚪️⚪️ 🔴⚪️🔴⚪🔴🎻王晰🎻⚪️🔴⚪️🔴⚪️ ⚪🔴⚪🔴⚪🎻王晰🎻🔴⚪️⚪️⚪️🔴'];
var running = false;
var defaultOption = {
//评论间隔时间
delay: 20*1000,
//评论内容
content: '从列表中随机抽取',
//每轮评论个数
count: 27,
//每轮评论间隔,默认为2分钟
interval: 2*60*1000,
//评论总轮数
outCount: 8
};
var needCommentIdList = [];
var commentedIdList = [];
var dateFormat = function (fmt,date){
var o = {
"M+" : date.getMonth()+1, //月份
"d+" : date.getDate(), //日
"h+" : date.getHours(), //小时
"m+" : date.getMinutes(), //分
"s+" : date.getSeconds(), //秒
"q+" : Math.floor((date.getMonth()+3)/3), //季度
"S" : date.getMilliseconds() //毫秒
};
if(/(y+)/.test(fmt))
fmt=fmt.replace(RegExp.$1, (date.getFullYear()+"").substr(4 - RegExp.$1.length));
for(var k in o)
if(new RegExp("("+ k +")").test(fmt))
fmt = fmt.replace(RegExp.$1, (RegExp.$1.length==1) ? (o[k]) : (("00"+ o[k]).substr((""+ o[k]).length)));
return fmt;
}
var logger = function(msg){
console.log(dateFormat('yyyy-MM-dd hh:mm:ss', new Date()) + ": " + msg);
};
var comment = function(id){
console.log("----------------------------------------->");
logger("Commenting " + id);
var feedItem = document.querySelector('div[mid="' + id + '"]');
var commentButton = feedItem.querySelector('a[action-type="fl_comment"]');
//没有打开的话，模拟点击打开
if(commentButton.parentNode.className != "curr") {
commentButton.click();
logger("Comment list is not loaded, start loading...");
}
logger("Waiting comment list loading...");
//等待评论框出现
setTimeout(function(){
var textArea = feedItem.querySelector('.W_input');
let commentContent = contentList[Math.floor(contentList.length*Math.random())];
textArea.value = commentContent;
//console.log(commentContent);
var sendButton = feedItem.querySelector('.W_btn_a');
sendButton.click();
logger("Sending comment content completed.");
//折叠起来
commentButton.click();
commentedIdList.push(dateFormat('yyyy-MM-dd hh:mm:ss', new Date()) + ' : '+ commentContent);
console.log("<-----------------------------------------");
}, 3000);
};
var commentThread = function(id){
logger("Comment thread started.");
let count = 0;
var innerAction = function(){
//var id = needCommentIdList.shift();
if(id) {
comment(id);
} else {
logger("WARNING: No feed to process...");
}
setTimeout(function(){
//评论27次后自动停止
if(!running || count >= defaultOption.count) {
logger("Comment thread action stoped, exit.");
return;
}
innerAction();
count ++;
}, defaultOption.delay);
};
innerAction();
};
this.start = async function(op){
needCommentIdList = [];
commentedIdList = [];
logger("WeiboAutoCommentTool start running...");
if(typeof(op) !== "undefined") {
defaultOption.delay = op.delay || defaultOption.delay;
defaultOption.content = op.content || defaultOption.content;
defaultOption.id = op.id || defaultOption.id;
defaultOption.count = op.count || defaultOption.count;
defaultOption.interval = op.interval || defaultOption.interval;
defaultOption.outCount = op.outCount || defaultOption.outCount;
logger("Use option: " + JSON.stringify(defaultOption));
} else {
logger("Use default option: " + JSON.stringify(defaultOption));
}
logger("WeiboAutoCommentTool running now.");
//每10min循环一次，循环20次
running = true;
let outCount = defaultOption.outCount;
var repeatAction = function(){
if(running) {
commentThread(defaultOption.id);
} else {
logger("WARNING: exsiting!!");
}
setTimeout(function(){
//评论27次后自动停止
if(!running || outCount<=0) {
running = false;
logger("Finish comment, exit.");
return;
}
repeatAction();
outCount --;
}, defaultOption.interval);
};
repeatAction();
//running = true;
//commentThread(defaultOption.id);
};
this.stop = function(){
running = false;
needCommentIdList = [];
logger("User stoped");
};
//统计method待更新
// this.stat = function(){
// logger("Commented list: ");
// console.log(commentedIdList);
// logger("Total commented " + commentedIdList.length);
// };
return this;
})();
weiboAutoCommentTool.start({
delay: 20*1000,
count: 27,
//这里是id【这是最新的微博id】【之后发布新微博更换下面的数字即可】
id:4473307824552546,
interval: 20*60*1000,
//评论总轮数
outCount: 8
});

```
## 由于有姐妹登不进github，补评代码已经由“沉醉晚风暖暖”微博账号发布，复制微博即可，主页→https://weibo.com/7342084291/profile?topnav=1&wvr=6

## id对照（图片消失实属正常现象）

### 置顶微博 19-11-22 《双鱼》 新歌宣传
`4441389506812820`

![image](/PostScreenShot/191122.png)

### 20-2-18  不出门不刮胡子，是我最后的倔强
`4473307824552546`

![image](/PostScreenShot/200218.png)

### 20-2-14  希望未来每一天你们都像今天一样开心甜蜜.也祝福坚守在防疫一线的朋友们能早日和爱人团聚
`4471928951820754`

![image](/PostScreenShot/200214-3.png)

### 20-2-14  #爱人的眼睛# 是温暖的港湾，亦是前行的方向.大爱无声，感谢有你❤️
`4471853185862166`

![image](/PostScreenShot/200214-2.png)

### 20-2-14  谁是#天赐的声音# ？我是王晰.每周六晚20:30，不见不散
`4471808768489727`

![image](/PostScreenShot/200214-1.png)

### 20-2-8  #元宵快乐宅# 想你们[心]
`4469764149978177`

![image](/PostScreenShot/200208-2.png)

### 20-2-8  纪念每一份渺小而伟大！英雄难忘！
`4469723960364307`

![image](/PostScreenShot/200208.png)

### 20-2-6  午安.分享这首《爱乐之城》的插曲.相遇感动[心]加油！
`4468982537273488`

![image](/PostScreenShot/200206.png)

### 20-2-5  #抗疫行动#  疫情当前，我们普通人该怎么办？答案很简单，就是：每个人，都扛起自己那份责任！做好防护，保护自己与家人！团结一心，战胜疫情！
`4468557725432553`

![image](/PostScreenShot/200205.png)

### 20-1-31  万哥，你安心的走吧.愿我只要想念，就能重游往昔.🕯️
`4466613765688076`

![image](/PostScreenShot/200131.png)

### 20-1-29  旅行取消..吃个榴莲吧...
`4466029512023115`

![image](/PostScreenShot/200129.png)

### 20-1-28  #抗击新型肺炎我们在行动# #全民口罩行动#  最近流行：宅在家[ok]勤洗手、戴口罩、拒绝谣言、拒绝野味，平安过节
`4465737978934578`

![image](/PostScreenShot/200128.png)

### 20-1-27  Mamba Never Out🙏
`4465360403471578`

![image](/PostScreenShot/200127.png)

### 20-1-26  在祖国的最南端.一别数年.把我的爱再唱给你听
`4464949088228937`

![image](/PostScreenShot/200126.png)

### 20-1-24  又是一年除夕夜.今天在家做大厨.饺子已经安排上啦
`4464362863620001`

![image](/PostScreenShot/200124.png)

### 20-1-23  我们一起，打赢这场防疫战！愿平安！
`4464025196702149`

![image](/PostScreenShot/200123.png)

### 20-1-22 在北京的冬日里尝试了很多第一次.房顶的景色很不错
`4463609751160386`

![image](/PostScreenShot/200122-2.png)

### 20-1-22 稳住我们能赢
`4463574824591773`

![image](/PostScreenShot/200122.png)

### 20-1-19 东北的雪，和“牛气”的我们
`4462511103776315`

![image](/PostScreenShot/200119.png)

### 20-1-18 拒绝盗版从我做起
`4462093497648776`

![image](/PostScreenShot/200118.png)
