---
title: 获取全年法定节假日API
date: 2019-03-21 10:52:10
tags: 节假日
categories: 学习交流
comments: true
---
# 引言
大家都知道，每年{% link 中国政府网 http://www.gov.cn/xinwen/2018-12/06/content_5346287.htm 2019年公休安排 %}都会公布法定节日调休安排，我们如何获取这些数据呢？

# 开始

##  获取某个月的节假日
网上有一些现成的访问地址，提供了这些功能。
如：http://www.easybots.cn/api/holiday.php?m=201901 直接访问后，返回JSON如下。
{% codeblock lang:json %}
{
	"201901": {
		"01": "2",
		"05": "2",
		"06": "2",
		"12": "2",
		"13": "1",
		"19": "2",
		"20": "1",
		"26": "2",
		"27": "1"
	}
}
{% endcodeblock %}

##  获取多个月的节假日
后面可以接多个月份，如：http://www.easybots.cn/api/holiday.php?m=201901,201902,201903
{% codeblock lang:json %}
{
	"201901": {
		"01": "2",
		"05": "1",
		"06": "1",
		"12": "2",
		"13": "1",
		"19": "2",
		"20": "1",
		"26": "2",
		"27": "2"
	},
	"201902": {
		"04": "1",
		"05": "2",
		"06": "2",
		"07": "2",
		"08": "2",
		"09": "2",
		"10": "1",
		"16": "2",
		"17": "2",
		"23": "1",
		"24": "1"
	},
	"201903": {
		"02": "1",
		"03": "2",
		"09": "2",
		"10": "2",
		"16": "1",
		"17": "2",
		"23": "1",
		"24": "1",
		"30": "1",
		"31": "2"
	}
}
{% endcodeblock%}
返回的JSON，1代表假日，2代表节日；由于是免费版本，代表节日或假日的标识符会随机变化，如果想要正确数据，请访问{% link EasyBots.cn http://www.easybots.cn/holiday_api.net %}去购买正版。
不过只需要区分节假日和工作日，不看标识符，那么这个数据还是正确的，包括调休。

## 获取某一天是否节假日
如果只想获取某一天是否假日，可以把参数改一下，如：http://www.easybots.cn/api/holiday.php?d=20190103,20190104
参数同样可以多个，不过要注意的是，对于免费版本来说，此数据不准确。

## 获取工作日完整Java代码
下面就贴一份获取全年工作日的java代码片段：
{% codeblock lang:java %}
/**
 * 同步一年中的假期;
 */
public void synWorkday() {
    StringBuffer sbf = new StringBuffer();
    try {
        // 访问的地址替换当前年份
        int currentYear = Calendar.getInstance().get(Calendar.YEAR);
        URL url = new URL(holiday_url.replaceAll("\\{year\\}", String.valueOf(currentYear)));
        HttpURLConnection connection = (HttpURLConnection) url.openConnection();
        // 插入启动钥匙
        connection.setRequestMethod("GET");
        // 热能装置链接
        connection.connect();
        // 打开电源
        InputStream is = connection.getInputStream();
        // 增加推力
        BufferedReader reader = new BufferedReader(new InputStreamReader(is, "UTF-8"));
        String strRead = null;
        while ((strRead = reader.readLine()) != null) {
            sbf.append(strRead);
            sbf.append("\r\n");
        }
        reader.close();
        // 转换，假日JSON
        JSONObject map = JSON.parseObject(sbf.toString());
        List<String> list = new ArrayList<>();
        for (Map.Entry<String, Object> entry : map.entrySet()) {
            String year = entry.getKey();
            // 拼接年份到结果集，然后生成新的list
            List<String> days = (List<String>) ((Map) entry.getValue()).keySet().stream().map(e -> year + e).collect(Collectors.toList());
            list.addAll(days);
        }

        // 获取当年所有日期
        List<String> currentYearFullDay = getYearFullDay(currentYear);
        // 移除假期，剩下工作日
        currentYearFullDay.removeAll(list);
        // 排序从远端同步过来的假期数据
        currentYearFullDay.sort(String::compareTo);

        // 查找现有数据库已经存储的数据
        List<Workday> workdays = workdayRepository.findAllById(currentYearFullDay);
        // 转换List<Workday>为List<String>，方便删除重复数据
        List<String> existList = workdays.stream().map(Workday::getWorkday).collect(Collectors.toList());
        // 删除重复数据，只保留没有同步过的数据
        currentYearFullDay.removeAll(existList);

        /**********这部分是入库操作，不需要入库的请忽略 start**********/
        // 存储没有保存过的数据
        for (String d : currentYearFullDay) {
            Workday day = new Workday();
            day.setWorkday(d);
            workdayRepository.save(day);
        }
        /********** end **********/
    } catch (Exception e) {
        e.printStackTrace();
    }
}

/**
 * 获取全年所有天;
 */
private List<String> getYearFullDay(int year) {
    List<String> fullDayList = new ArrayList<>(366);
    for (int i = 1; i <= 12; i++) {
        fullDayList.addAll(this.getMonthFullDay(year, i));
    }
    return fullDayList;
}

/**
 * 获取某年某个月的所有天;
 */
private List<String> getMonthFullDay(int year, int month) {
    List<String> fullDayList = new ArrayList<>(31);
    SimpleDateFormat dateFormatYYYYMMDD = new SimpleDateFormat("yyyyMMdd");
    // 获得当前日期对象
    Calendar cal = Calendar.getInstance();
    cal.clear();// 清除信息
    cal.set(Calendar.YEAR, year);
    // 1月从0开始
    cal.set(Calendar.MONTH, month - 1);
    // 当月1号
    cal.set(Calendar.DAY_OF_MONTH, 1);
    int count = cal.getActualMaximum(Calendar.DAY_OF_MONTH);
    for (int j = 1; j <= count; j++) {
        fullDayList.add(dateFormatYYYYMMDD.format(cal.getTime()));
        cal.add(Calendar.DAY_OF_MONTH, 1);
    }
    return fullDayList;
}
{% endcodeblock %}

# 其他节假日API：
## {% link goseek http://api.goseek.cn/ %}
http://api.goseek.cn/Tools/holiday?date=20190202
只有判断天是否节假日，0代表工作日，1代表节日，2代表假日，数据准确，但无节日的调休信息。


## {% link 搜啊 http://tool.bitefu.net/ %}
http://tool.bitefu.net/jiari/?d=2019
http://tool.bitefu.net/jiari/?d=201902
http://tool.bitefu.net/jiari/?d=20190203
只返回法定节日，0代表工作日，1代表调休日，2代表节日，数据准确，但是无假日，也没有节日调休，周末的补班日。

