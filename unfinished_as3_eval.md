博客地址：<http://zengrong.net/post/1395.htm>

一个未完成的简单eval实现

AS3没有eval确实不方便，目前我所知有两种方法：

1. 使用[ActionScript 3 Eval Library](http://eval.hurlant.com/)来实现。但这个库比较大，debug编译越140KB。如果项目非常注重文件大小，则需要考虑；
2. 如果swf部署在浏览器环境中，可以将eval交给Javascript来处理，然后获取Javascript的返回值即可。

我在工作中需要使用eval的地方，往往比较简单，且比较规律。这种情况下，完全可以自己写一些简单的逻辑来实现。

这个例子中的需求就很简单，是实现一个较为灵活的站位算法。我在外部配置文件中定位了12个站位坐标（包含xy值），人物进入界面后，基于这些站位点来站位。但由于舞台的大小是变化的，如果定义一个确定的值，无法根据舞台的大小进行变化。

如果让外部的配置文件中支持类似于 `h/2` 或者 `w-100` 这种语法，就非常灵活了。

这个实现仅支持一次运算，而且不支持单个的 `w` 这种类型的值，要获取 `w`，必须用 `w+0` 这种语法。但对当前的需求来说，足够了。

下面就是代码：<!--more-->

<pre lang="actionscript">
/**
 * 站位容器宽高
 */
private var box_w:int = 600;
private var box_h:int = 400;

/**
 * 对传来的值进行运算，支持加减乘除的一次运算，变量支持w和h
 */
public function calculate($coord:*):int
{
	 /*支持的运算符和变量
	 例如：w-8 h/2 等等。格式必须是 1(w或h) 2(运算符) 3(数字)*/
	const reg:RegExp = /([w|h])([+|\-|*|\/]+)(\d+)/;
	var __num:Number = parseInt($coord);
	//如果可以解析，就返回解析后的值
	if(!isNaN(__num)) return __num;
	//如果该值不是字符串，就返回0

	if(!($coord is String)) return 0;
	var __coord:String = String($coord).toLowerCase();
	//不符合规则，返回0
	if(!reg.test(__coord)) return 0;
	//如果没有设置容器宽高，返回0
	if(box_w == 0 || box_h == 0) return 0;
	var __arr:Array = __coord.match(reg);
	//trace('arr:', __arr);
	var __box:int = __arr[1] == 'w' ? box_w : box_h;
	__num = int(__arr[3]);
	//加减乘除运算
	if(__arr[2] == '+')
		return __box + __num;
	else if(__arr[2] == '-')
		return __box - __num;
	else if(__arr[2] == '*')
		return __box * __num;
	return int(__box/__num);
}
</pre>
