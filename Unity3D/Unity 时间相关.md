#Unity中有关时间的总结


##时间相关的概念

###时间戳
 - 一般也称为Unix时间戳(**Unix TimeStamp**)，是从格林威治时间1970年1月1日0点0分0秒起至现在的**总秒数**
 - 在网络游戏中一般由服务器端同步给客户端为准
 - [在线时间戳转换](http://tool.chinaz.com/Tools/unixtime.aspx) 
 - 时间戳的单位一般是秒

##C#中时间相关的结构

###TimeSpan

- 表示时间间隔的结构
- 主要用在计算**DateTime**间隔中 For Example:

~~~csharp
DateTime origin = new DateTime(1970,1,1,0,0,0,0);
DateTime now = DateTime.Now;
TimeSpan diff = now - origin;
~~~

这样即可得到有关从1970年到现在的时间间隔的各种属性：

~~~csharp
diff.TotalSeconds();
diff.Days;
~~~

###DateTime

- 表示具体日期的结构
- 通过实例化具体日期得到相关的属性

~~~csharp
DateTime dateTime = new DateTime(1970,1,1,0,0,0);
dateTime.ToUniversalTime();
dateTime.ToLocalTime();
~~~

其中 ToUniversalTime 表示世界协调时，ToLocalTime表示本地时间


###string.Format

- 格式化时间的输出跟显示
 
例如：

~~~csharp
string timeStr = "00:00:00";
timeStr = string.Format("{0:00}:{1:00}:{2:00}",diff.Hours,diff.Minutes,diff.Seconds);
print("now is," + timeStr);

//now is,15:03:50
~~~

##游戏中有关的用法

###与服务器同步时间

 - 每次与服务器同步 记录Unity自己的从游戏开始到现在的时间

 ~~~csharp
 	private ulong lastServerTimestamp = 0;
	private float lastRealUnityTime = 0.0f;
	void OnTimeStampUpdate(ulong timestamp) {
		lastRealUnityTime = Time.realtimeSinceStartup;
		lastServerTimestamp = timestamp;
	}
 ~~~
 
- 需要取的时候，用服务器给的时间戳加上从游戏开始到现在的时间差

~~~csharp
	//Seconds of server time
	public ulong Timestamp {
		get {
			return lastServerTimestamp + (ulong)(Time.realtimeSinceStartup-lastRealUnityTime);
		}
	}
~~~

###正计时

 - 利用游戏开始时间的时间差

 ~~~csharp
 float _startTime = Time.time;
 float nowTimer = Time.time -_startTime;
 ~~~
 
 
###倒计时
 
 - 与正计时的原理类似 只需在开始的时候用当前时间加上需要计时的时间

 ~~~csharp
 float _countTime = 60f;
 float _startTime = Time.time + _countTime;
 float nowTimer = _startTime - Time.time;
 ~~~
 
- 注意边界的判断