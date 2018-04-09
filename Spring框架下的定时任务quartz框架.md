## Spring框架下的定时任务quartz框架

### 一、quartz使用
   导入所需jar包
1、配置 
    1.1 导入所需quartz-2.2.3.jar和quartz-jobs-2.2.3.jar包。
     
    1.2 spring配置文件头部配置
    <beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:aop="http://www.springframework.org/schema/aop"  
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc
    	http://www.springframework.org/schema/mvc/spring-mvc.xsd
		http://www.springframework.org/schema/aop
	 	http://www.springframework.org/schema/aop/spring-aop.xsd
		http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		http://www.springframework.org/schema/context/spring-context.xsd">

    1.3 执行业务代码逻辑
        import org.springframework.stereotype.Component;

        @Component("自定义名称1")  
        public class Jobs { 
            public void 方法名1(){
                System.out.print("This is a test");
   	 	    }
        }

    1.4 spring配置文件
	<bean id="自定义名称2"
		class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
		<property name="targetObject" ref="自定义名称1"></property>
		<property name="targetMethod" value="方法名1"></property>
		<property name="concurrent" value="false"></property>
		<!-- concurrent : false表示等上一个任务执行完后再开启新的任务 -->
	</bean>

	<!-- 配置任务调度的的时间/周期 -->
	<bean id="自定义名称3"
		class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
		<property name="jobDetail" ref="自定义名称2"></property>
		<property name="cronExpression" value="0 0 20 * * *"></property>
		<!-- property name="cronExpression" value="0 0 23 * * ?"></property -->
		<property name="startDelay" value="3000"></property>
	</bean>

	<bean id="schedulerFactoryBean"
		class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
		<property name="triggers">
			<list>
				<ref bean="自定义名称3" />  
			</list>
		</property>
	</bean>

2、Quartz定时调度时间配置格式说明
    格式: [秒] [分] [小时] [日] [月] [周] [年]

序号  |说明      |是否必填  |允许填写的值      |允许的通配符
------|---------|----------|----------------|--------
1     |秒       |是      |0-59              | , - * /
2     |分       |是      |0-59              | , - * /
3     |小时     |是      |0-23              | , - * /
4     |日       |是      |1-31              | , - * ? / L W
5     |月       |是      |1-12 or JAN-DEC   | , - * /
6     |周       |是      |1-7 or SUN-SAT    | , - * ? / L #
7     |年       |否      |empty or 1970-2099| , - * /

    2.1 通配符说明
      * ：表示所有值. 例如:在分的字段上设置 "*",表示每一分钟都会触发。
      ? ：表示不指定值。使用的场景为不需要关心当前设置这个字段的值。例如:要在每月的10号触发一个操作，但不关心是周几，所以需要周位置     的那个字段设置为"?" 具体设置为 0 0 0 10 * ?
      - ：表示区间。例如 在小时上设置 "10-12",表示 10,11,12点都会触发。
      , ：表示指定多个值，例如在周字段上设置 "MON,WED,FRI" 表示周一，周三和周五触发
      / ：用于递增触发。如在秒上面设置"5/15" 表示从5秒开始，每增15秒触发(5,20,35,50)。 在月字段上设置'1/3'所示每月1号开始，每隔三天触发一次。
      L ：表示最后的意思。在日字段设置上，表示当月的最后一天(依据当前月份，如果是二月还会依据是否是润年[leap]), 在周字段上表示星期六，相当于"7"或"SAT"。如果在"L"前加上数字，则表示该数据的最后一个。例如在周字段上设置"6L"这样的格式,则表示“本月最后一个星期五"
      
      W ：表示离指定日期的最近那个工作日(周一至周五). 例如在日字段上设置"15W"，表示离每月15号最近的那个工作日触发。如果15号正好是周六，则找最近的周五(14号)触发, 如果15号是周未，则找最近的下周一(16号)触发.如果15号正好在工作日(周一至周五)，则就在该天触发。如果指定格式为 "1W",它则表示每月1号往后最近的工作日触发。如果1号正是周六，则将在3号下周一触发。(注，"W"前只能设置具体的数字,不允许区间"-")
      （'L'和 'W'可以一组合使用。如果在日字段上设置"LW",则表示在本月的最后一个工作日触发）

      # ：序号(表示每月的第几周星期几)，例如在周字段上设置"6#3"表示在每月的第三个周星期六.注意如果指定"6#5",正好第五周没有星期六，则不会触发该配置
      （周字段的设置，若使用英文字母是不区分大小写的 MON 与mon相同）

   2.2  常用示例
      格式: [秒] [分] [小时] [日] [月] [周] [年]

      0 0 12 * * ?              每天12点触发 
      0 15 10 ? * *             每天10点15分触发 
      0 15 10 * * ?             每天10点15分触发  
      0 15 10 * * ? *           每天10点15分触发  
      0 15 10 * * ? 2005        2005年每天10点15分触发 
      0 * 14 * * ?              每天下午的 2点到2点59分每分触发 
      0 0/5 14 * * ?            每天下午的 2点到2点59分(整点开始，每隔5分触发)  
      0 0/5 14,18 * * ?         每天下午的 18点到18点59分(整点开始，每隔5分触发) 
      0 0-5 14 * * ?            每天下午的 2点到2点05分每分触发 
      0 10,44 14 ? 3 WED        3月分每周三下午的 2点10分和2点44分触发 
      0 15 10 ? * MON-FRI       从周一到周五每天上午的10点15分触发 
      0 15 10 15 * ?            每月15号上午10点15分触发 
      0 15 10 L * ?             每月最后一天的10点15分触发 
      0 15 10 ? * 6L            每月最后一周的星期五的10点15分触发 
      0 15 10 ? * 6L 2002-2005  从2002年到2005年每月最后一周的星期五的10点15分触发
      0 15 10 ? * 6#3           每月的第三周的星期五开始触发 
      0 0 12 1/5 * ?            每月的第一个中午开始每隔5天触发一次 
      0 11 11 11 11 ?           每年的11月11号 11点11分触发(光棍节)

      


