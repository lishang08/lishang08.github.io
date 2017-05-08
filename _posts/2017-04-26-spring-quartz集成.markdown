---
layout:     post
title:      "spring-quartz 集成"
subtitle:   " \"spring quartz 集成\""
date:       2017-04-26 01:17:00
author:     "Lishang"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - quartz
---

## 前言

搭建了一个Spring + Quartz 的框架，是为了能更深入地理解Quartz这个任务调度器的强大，代码会持续在<a href="https://github.com/lishang08/spring-quartz.git">spring-quartz.git</a> 更新.

## 相关概念

1. Job
表示一个工作，要执行的具体内容。此接口中只有一个方法
```
void execute(JobExecutionContext context)
```
2. JobDetail
JobDetail表示一个具体的可执行的调度程序，Job是这个可执行程调度程序所要执行的内容，另外JobDetail还包含了这个任务调度的方案和策略

3. Trigger
Trigger代表一个调度参数的配置，什么时候去调。

4. Scheduler
Scheduler代表一个调度容器，一个调度容器中可以注册多个JobDetail和Trigger。当Trigger与JobDetail组合，就可以被Scheduler容器调度了。


## Spring集成

在Spring 与 Quartz集成的框架中， 使用spring来对quartz进行管理，在applicationContext.xml中配置scheduler bean, 在scheduler中配置线程池，jobstore等。
```
    <bean id="scheduler" class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <property name="quartzProperties">
            <props>
                <prop key="org.quartz.scheduler.instanceName">ClusterScheduler</prop>
                <prop key="org.quartz.scheduler.instanceId">AUTO</prop>
                <!-- 线程池配置 -->
                <prop key="org.quartz.threadPool.class">org.quartz.simpl.SimpleThreadPool</prop>
                <prop key="org.quartz.threadPool.threadCount">20</prop>
                <prop key="org.quartz.threadPool.threadPriority">5</prop>
                <!-- JobStore 配置 -->
                <prop key="org.quartz.jobStore.class">org.quartz.impl.jdbcjobstore.JobStoreTX</prop>
                <!-- 集群配置 -->
                <prop key="org.quartz.jobStore.isClustered">true</prop>
                <prop key="org.quartz.jobStore.clusterCheckinInterval">15000</prop>
                <prop key="org.quartz.jobStore.maxMisfiresToHandleAtATime">1</prop>
                <prop key="org.quartz.jobStore.misfireThreshold">120000</prop>
                <prop key="org.quartz.jobStore.tablePrefix">QRTZ_</prop>
            </props>
        </property>
        <property name="schedulerName" value="ClusterScheduler" />
        <!--必须的，QuartzScheduler 延时启动，应用启动完后 QuartzScheduler 再启动 -->
        <property name="startupDelay" value="30" />
        <property name="applicationContextSchedulerContextKey" value="applicationContextKey" />
        <!--可选，QuartzScheduler 启动时更新己存在的Job，这样就不用每次修改targetObject后删除qrtz_job_details表对应记录了 -->
        <property name="overwriteExistingJobs" value="true" />
        <!-- 设置自动启动 -->
        <property name="autoStartup" value="true" />
        <!--<property name="configLocation" value="classpath:quartz.properties" />-->
    </bean>
```

在spring controller 中添加@RequestMapping

Controller
```
	/**
	 * 保存schedulejob
	 * @param scheduleJobVo
	 * @return
	 */
	@RequestMapping(value = "save-schedule-job", method = RequestMethod.POST)
	public String saveScheduleJob(ScheduleJobVo scheduleJobVo) {
		// 测试用随便设个状态
		scheduleJobVo.setStatus("1");
		if (scheduleJobVo.getScheduleJobId() == null) {
			scheduleJobService.insert(scheduleJobVo);
		} else if (StringUtils.equalsIgnoreCase(scheduleJobVo.getKeywords(), "delUpdate")) {
			// 直接拿keywords存一下，就不另外重新弄了
			scheduleJobService.delUpdate(scheduleJobVo);
		} else {
			scheduleJobService.update(scheduleJobVo);
		}
		return "redirect:list-schedule-job.shtml";
	}
```
Service, 在service中添加实现方法
```
	/**
	 * 创建新的schedulejob
	 */
	public Long insert(ScheduleJobVo scheduleJobVo) {
		ScheduleJob scheduleJob = scheduleJobVo.getTargetObject(ScheduleJob.class);
		ScheduleUtils.createScheduleJob(scheduler, scheduleJob);
		return jdbcDao.insert(scheduleJob);
	}
```
Scheduler创建schedule job
```
	public static void createScheduleJob(Scheduler scheduler, String jobName, String jobGroup, String cronExpression,
			boolean isSync, Object param) {
		// 同步或异步
		Class<? extends Job> jobClass = isSync ? AsyncJobFactory.class : SyncJobFactory.class;

		// 构建job信息
		JobDetail jobDetail = JobBuilder.newJob(jobClass).withIdentity(jobName, jobGroup).build();

		// 表达式调度构建器
		CronScheduleBuilder scheduleBuilder = CronScheduleBuilder.cronSchedule(cronExpression);

		// 按新的cronExpression表达式构建一个新的trigger
		CronTrigger trigger = TriggerBuilder.newTrigger().withIdentity(jobName, jobGroup).withSchedule(scheduleBuilder)
				.build();

		String jobTrigger = trigger.getKey().getName();

		ScheduleJob scheduleJob = (ScheduleJob) param;
		scheduleJob.setJobTrigger(jobTrigger);

		// 放入参数，运行时的方法可以获取
		jobDetail.getJobDataMap().put(ScheduleJobVo.JOB_PARAM_KEY, scheduleJob);

		try {
			scheduler.scheduleJob(jobDetail, trigger);
		} catch (SchedulerException e) {
			LOGGER.error("创建定时任务失败", e);
			throw new ScheduleException("创建定时任务失败");
		}
	}
```
在schedule job中实现业务（下面例子只是定时去访问百度网页）
```
@PersistJobDataAfterExecution
@DisallowConcurrentExecution
public class SyncJobFactory extends QuartzJobBean {

    /* 日志对象 */
    private static final Logger LOGGER = Logger.getLogger(SyncJobFactory.class);

    protected void executeInternal(JobExecutionContext context) throws JobExecutionException {
    	LOGGER.info("SyncJobFactory execute");
        JobDataMap mergedJobDataMap = context.getMergedJobDataMap();
        ScheduleJob scheduleJob = (ScheduleJob) mergedJobDataMap.get(ScheduleJobVo.JOB_PARAM_KEY);
        LOGGER.info("jobName:" + scheduleJob.getJobName() + "  " + scheduleJob);
        String url = scheduleJob.getUrl();
        CloseableHttpClient httpclient = HttpClients.createDefault();
        HttpGet httpGet = new HttpGet(url);
        CloseableHttpResponse response;
        try {
            response = httpclient.execute(httpGet);
            LOGGER.info(response.getStatusLine());
            HttpEntity entity = response.getEntity();
            EntityUtils.consume(entity);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

spring+quartz即成搭好之后，可以更加关注于定时业务的实现，简单易用。

Mark 于 20170508 by Samfu
