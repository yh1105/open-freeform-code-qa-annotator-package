
# Post \#53702038 [Link](https://stackoverflow.com/questions/53702038/)

## Android Gradle 5.0 Update:Cause: org.jetbrains.plugins.gradle.tooling.util

**Vote**: 89 (72/702) **Views**: 117710 (72/702) 

**Internal ID** \#2-5-339

Created at 2018-12-10 08:39:03

Tags: `java` `android` `android-studio` `gradle`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

On Updating version my project `gradle` to 5.0 release I am getting error in android studio it was working fine with `gradle` `5.0 rc` `3` and `4` release. following is the error detail:  

> Cause: org.jetbrains.plugins.gradle.tooling.util.ModuleComponentIdentifierImpl.getModuleIdentifier()Lorg/gradle/api/artifacts/ModuleIdentifier;

Here is the detail logs:  

```
java.lang.AbstractMethodError: org.jetbrains.plugins.gradle.tooling.util.ModuleComponentIdentifierImpl.getModuleIdentifier()Lorg/gradle/api/artifacts/ModuleIdentifier;
at org.gradle.api.internal.artifacts.ivyservice.resolutionstrategy.DefaultCachePolicy.mustRefreshModule(DefaultCachePolicy.java:205)
at org.gradle.api.internal.artifacts.ivyservice.resolutionstrategy.DefaultCachePolicy.mustRefreshMissingModule(DefaultCachePolicy.java:188)
at org.gradle.api.internal.artifacts.ivyservice.ivyresolve.CachingModuleComponentRepository$LocateInCacheRepositoryAccess.resolveComponentMetaDataFromCache(CachingModuleComponentRepository.java:198)
at org.gradle.api.internal.artifacts.ivyservice.ivyresolve.CachingModuleComponentRepository$LocateInCacheRepositoryAccess.resolveComponentMetaData(CachingModuleComponentRepository.java:189)
at org.gradle.api.internal.artifacts.ivyservice.ivyresolve.ErrorHandlingModuleComponentRepository$ErrorHandlingModuleComponentRepositoryAccess.lambda$resolveComponentMetaData$3(ErrorHandlingModuleComponentRepository.java:153)
at org.gradle.api.internal.artifacts.ivyservice.ivyresolve.ErrorHandlingModuleComponentRepository$ErrorHandlingModuleComponentRepositoryAccess.lambda$tryResolveAndMaybeBlacklist$15(ErrorHandlingModuleComponentRepository.java:222)
at org.gradle.api.internal.artifacts.ivyservice.ivyresolve.ErrorHandlingModuleComponentRepository$ErrorHandlingModuleComponentRepositoryAccess.tryResolveAndMaybeBlacklist(ErrorHandlingModuleComponentRepository.java:236)
at org.gradle.api.internal.artifacts.ivyservice.ivyresolve.ErrorHandlingModuleComponentRepository$ErrorHandlingModuleComponentRepositoryAccess.tryResolveAndMaybeBlacklist(ErrorHandlingModuleComponentRepository.java:221)
at org.gradle.api.internal.artifacts.ivyservice.ivyresolve.ErrorHandlingModuleComponentRepository$ErrorHandlingModuleComponentRepositoryAccess.performOperationWithRetries(ErrorHandlingModuleComponentRepository.java:214)
at org.gradle.api.internal.artifacts.ivyservice.ivyresolve.ErrorHandlingModuleComponentRepository$ErrorHandlingModuleComponentRepositoryAccess.resolveComponentMetaData(ErrorHandlingModuleComponentRepository.java:152)
at org.gradle.api.internal.artifacts.ivyservice.ivyresolve.ComponentMetaDataResolveState.process(ComponentMetaDataResolveState.java:69)
at org.gradle.api.internal.artifacts.ivyservice.ivyresolve.ComponentMetaDataResolveState.resolve(ComponentMetaDataResolveState.java:48)
at org.gradle.api.internal.artifacts.ivyservice.ivyresolve.RepositoryChainComponentMetaDataResolver.findBestMatch(RepositoryChainComponentMetaDataResolver.java:138)
at org.gradle.api.internal.artifacts.ivyservice.ivyresolve.RepositoryChainComponentMetaDataResolver.findBestMatch(RepositoryChainComponentMetaDataResolver.java:119)
at org.gradle.api.internal.artifacts.ivyservice.ivyresolve.RepositoryChainComponentMetaDataResolver.resolveModule(RepositoryChainComponentMetaDataResolver.java:92)
at org.gradle.api.internal.artifacts.ivyservice.ivyresolve.RepositoryChainComponentMetaDataResolver.resolve(RepositoryChainComponentMetaDataResolver.java:63)
at org.gradle.api.internal.artifacts.query.DefaultArtifactResolutionQuery.buildComponentResult(DefaultArtifactResolutionQuery.java:164)
at org.gradle.api.internal.artifacts.query.DefaultArtifactResolutionQuery.createResult(DefaultArtifactResolutionQuery.java:142)
at org.gradle.api.internal.artifacts.query.DefaultArtifactResolutionQuery.execute(DefaultArtifactResolutionQuery.java:133)
at org.jetbrains.plugins.gradle.tooling.util.DependencyResolverImpl.resolveDependencies(DependencyResolverImpl.java:207)
at org.jetbrains.plugins.gradle.tooling.util.DependencyResolverImpl.resolveDependencies(DependencyResolverImpl.java:125)
at org.jetbrains.plugins.gradle.tooling.builder.ModelBuildScriptClasspathBuilderImpl.buildAll(ModelBuildScriptClasspathBuilderImpl.java:89)
at org.jetbrains.plugins.gradle.tooling.internal.ExtraModelBuilder.buildAll(ExtraModelBuilder.java:67)
at org.gradle.tooling.provider.model.internal.DefaultToolingModelBuilderRegistry$BuildOperationWrappingToolingModelBuilder$1$1.create(DefaultToolingModelBuilderRegistry.java:102)
at org.gradle.api.internal.project.DefaultProjectStateRegistry.withLenientState(DefaultProjectStateRegistry.java:132)
at org.gradle.tooling.provider.model.internal.DefaultToolingModelBuilderRegistry$BuildOperationWrappingToolingModelBuilder$1.call(DefaultToolingModelBuilderRegistry.java:98)
at org.gradle.internal.operations.DefaultBuildOperationExecutor$CallableBuildOperationWorker.execute(DefaultBuildOperationExecutor.java:315)
at org.gradle.internal.operations.DefaultBuildOperationExecutor$CallableBuildOperationWorker.execute(DefaultBuildOperationExecutor.java:305)
at org.gradle.internal.operations.DefaultBuildOperationExecutor.execute(DefaultBuildOperationExecutor.java:175)
at org.gradle.internal.operations.DefaultBuildOperationExecutor.call(DefaultBuildOperationExecutor.java:101)
at org.gradle.internal.operations.DelegatingBuildOperationExecutor.call(DelegatingBuildOperationExecutor.java:36)
at org.gradle.tooling.provider.model.internal.DefaultToolingModelBuilderRegistry$BuildOperationWrappingToolingModelBuilder.buildAll(DefaultToolingModelBuilderRegistry.java:95)
at org.gradle.tooling.internal.provider.runner.DefaultBuildController.getModel(DefaultBuildController.java:79)
at org.gradle.tooling.internal.provider.runner.DefaultBuildController.getModel(DefaultBuildController.java:61)
at org.gradle.tooling.internal.consumer.connection.BuildControllerAdapter.getModel(BuildControllerAdapter.java:58)
at org.gradle.tooling.internal.consumer.connection.AbstractBuildController.findModel(AbstractBuildController.java:39)
at org.jetbrains.plugins.gradle.model.ProjectImportAction.addExtraProject(ProjectImportAction.java:123)
at org.jetbrains.plugins.gradle.model.ProjectImportAction.execute(ProjectImportAction.java:76)
at org.jetbrains.plugins.gradle.model.ProjectImportAction.execute(ProjectImportAction.java:40)
at org.gradle.tooling.internal.consumer.connection.InternalBuildActionAdapter.execute(InternalBuildActionAdapter.java:53)
at org.gradle.tooling.internal.provider.runner.ClientProvidedBuildActionRunner.buildResult(ClientProvidedBuildActionRunner.java:83)
at org.gradle.tooling.internal.provider.runner.ClientProvidedBuildActionRunner.access$100(ClientProvidedBuildActionRunner.java:38)
at org.gradle.tooling.internal.provider.runner.ClientProvidedBuildActionRunner$1.buildFinished(ClientProvidedBuildActionRunner.java:62)
at sun.reflect.GeneratedMethodAccessor48.invoke(Unknown Source)
at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
at java.lang.reflect.Method.invoke(Method.java:498)
at org.gradle.internal.dispatch.ReflectionDispatch.dispatch(ReflectionDispatch.java:35)
at org.gradle.internal.dispatch.ReflectionDispatch.dispatch(ReflectionDispatch.java:24)
at org.gradle.internal.event.DefaultListenerManager$ListenerDetails.dispatch(DefaultListenerManager.java:376)
at org.gradle.internal.event.DefaultListenerManager$ListenerDetails.dispatch(DefaultListenerManager.java:358)
at org.gradle.internal.event.AbstractBroadcastDispatch.dispatch(AbstractBroadcastDispatch.java:58)
at org.gradle.internal.event.DefaultListenerManager$EventBroadcast$ListenerDispatch.dispatch(DefaultListenerManager.java:346)
at org.gradle.internal.event.DefaultListenerManager$EventBroadcast$ListenerDispatch.dispatch(DefaultListenerManager.java:333)
at org.gradle.internal.event.AbstractBroadcastDispatch.dispatch(AbstractBroadcastDispatch.java:42)
at org.gradle.internal.event.BroadcastDispatch$SingletonDispatch.dispatch(BroadcastDispatch.java:230)
at org.gradle.internal.event.BroadcastDispatch$SingletonDispatch.dispatch(BroadcastDispatch.java:149)
at org.gradle.internal.event.AbstractBroadcastDispatch.dispatch(AbstractBroadcastDispatch.java:58)
at org.gradle.internal.event.BroadcastDispatch$CompositeDispatch.dispatch(BroadcastDispatch.java:324)
at org.gradle.internal.event.BroadcastDispatch$CompositeDispatch.dispatch(BroadcastDispatch.java:234)
at org.gradle.internal.event.ListenerBroadcast.dispatch(ListenerBroadcast.java:140)
at org.gradle.internal.event.ListenerBroadcast.dispatch(ListenerBroadcast.java:37)
at org.gradle.internal.dispatch.ProxyDispatchAdapter$DispatchingInvocationHandler.invoke(ProxyDispatchAdapter.java:93)
at com.sun.proxy.$Proxy14.buildFinished(Unknown Source)
at org.gradle.initialization.DefaultGradleLauncher.finishBuild(DefaultGradleLauncher.java:177)
at org.gradle.initialization.DefaultGradleLauncher.finishBuild(DefaultGradleLauncher.java:140)
at org.gradle.internal.invocation.GradleBuildController$3.create(GradleBuildController.java:104)
at org.gradle.internal.invocation.GradleBuildController$3.create(GradleBuildController.java:96)
at org.gradle.internal.work.DefaultWorkerLeaseService.withLocks(DefaultWorkerLeaseService.java:183)
at org.gradle.internal.work.StopShieldingWorkerLeaseService.withLocks(StopShieldingWorkerLeaseService.java:40)
at org.gradle.internal.invocation.GradleBuildController.doBuild(GradleBuildController.java:96)
at org.gradle.internal.invocation.GradleBuildController.configure(GradleBuildController.java:85)
at org.gradle.tooling.internal.provider.runner.ClientProvidedBuildActionRunner.run(ClientProvidedBuildActionRunner.java:70)
at org.gradle.launcher.exec.ChainingBuildActionRunner.run(ChainingBuildActionRunner.java:35)
at org.gradle.launcher.exec.ChainingBuildActionRunner.run(ChainingBuildActionRunner.java:35)
at org.gradle.tooling.internal.provider.ValidatingBuildActionRunner.run(ValidatingBuildActionRunner.java:32)
at org.gradle.launcher.exec.RunAsBuildOperationBuildActionRunner$3.run(RunAsBuildOperationBuildActionRunner.java:49)
at org.gradle.internal.operations.DefaultBuildOperationExecutor$RunnableBuildOperationWorker.execute(DefaultBuildOperationExecutor.java:301)
at org.gradle.internal.operations.DefaultBuildOperationExecutor$RunnableBuildOperationWorker.execute(DefaultBuildOperationExecutor.java:293)
at org.gradle.internal.operations.DefaultBuildOperationExecutor.execute(DefaultBuildOperationExecutor.java:175)
at org.gradle.internal.operations.DefaultBuildOperationExecutor.run(DefaultBuildOperationExecutor.java:91)
at org.gradle.internal.operations.DelegatingBuildOperationExecutor.run(DelegatingBuildOperationExecutor.java:31)
at org.gradle.launcher.exec.RunAsBuildOperationBuildActionRunner.run(RunAsBuildOperationBuildActionRunner.java:44)
at org.gradle.tooling.internal.provider.SubscribableBuildActionRunner.run(SubscribableBuildActionRunner.java:51)
at org.gradle.launcher.exec.InProcessBuildActionExecuter$1.transform(InProcessBuildActionExecuter.java:47)
at org.gradle.launcher.exec.InProcessBuildActionExecuter$1.transform(InProcessBuildActionExecuter.java:44)
at org.gradle.composite.internal.DefaultRootBuildState.run(DefaultRootBuildState.java:79)
at org.gradle.launcher.exec.InProcessBuildActionExecuter.execute(InProcessBuildActionExecuter.java:44)
at org.gradle.launcher.exec.InProcessBuildActionExecuter.execute(InProcessBuildActionExecuter.java:30)
at org.gradle.launcher.exec.BuildTreeScopeBuildActionExecuter.execute(BuildTreeScopeBuildActionExecuter.java:39)
at org.gradle.launcher.exec.BuildTreeScopeBuildActionExecuter.execute(BuildTreeScopeBuildActionExecuter.java:25)
at org.gradle.tooling.internal.provider.ContinuousBuildActionExecuter.execute(ContinuousBuildActionExecuter.java:78)
at org.gradle.tooling.internal.provider.ContinuousBuildActionExecuter.execute(ContinuousBuildActionExecuter.java:52)
at org.gradle.tooling.internal.provider.ServicesSetupBuildActionExecuter.execute(ServicesSetupBuildActionExecuter.java:62)
at org.gradle.tooling.internal.provider.ServicesSetupBuildActionExecuter.execute(ServicesSetupBuildActionExecuter.java:34)
at org.gradle.tooling.internal.provider.GradleThreadBuildActionExecuter.execute(GradleThreadBuildActionExecuter.java:36)
at org.gradle.tooling.internal.provider.GradleThreadBuildActionExecuter.execute(GradleThreadBuildActionExecuter.java:25)
at org.gradle.tooling.internal.provider.ParallelismConfigurationBuildActionExecuter.execute(ParallelismConfigurationBuildActionExecuter.java:43)
at org.gradle.tooling.internal.provider.ParallelismConfigurationBuildActionExecuter.execute(ParallelismConfigurationBuildActionExecuter.java:29)
at org.gradle.tooling.internal.provider.StartParamsValidatingActionExecuter.execute(StartParamsValidatingActionExecuter.java:59)
at org.gradle.tooling.internal.provider.StartParamsValidatingActionExecuter.execute(StartParamsValidatingActionExecuter.java:31)
at org.gradle.tooling.internal.provider.SessionFailureReportingActionExecuter.execute(SessionFailureReportingActionExecuter.java:59)
at org.gradle.tooling.internal.provider.SessionFailureReportingActionExecuter.execute(SessionFailureReportingActionExecuter.java:44)
at org.gradle.tooling.internal.provider.SetupLoggingActionExecuter.execute(SetupLoggingActionExecuter.java:46)
at org.gradle.tooling.internal.provider.SetupLoggingActionExecuter.execute(SetupLoggingActionExecuter.java:30)
at org.gradle.launcher.daemon.server.exec.ExecuteBuild.doBuild(ExecuteBuild.java:67)
at org.gradle.launcher.daemon.server.exec.BuildCommandOnly.execute(BuildCommandOnly.java:36)
at org.gradle.launcher.daemon.server.api.DaemonCommandExecution.proceed(DaemonCommandExecution.java:122)
at org.gradle.launcher.daemon.server.exec.WatchForDisconnection.execute(WatchForDisconnection.java:37)
at org.gradle.launcher.daemon.server.api.DaemonCommandExecution.proceed(DaemonCommandExecution.java:122)
at org.gradle.launcher.daemon.server.exec.ResetDeprecationLogger.execute(ResetDeprecationLogger.java:26)
at org.gradle.launcher.daemon.server.api.DaemonCommandExecution.proceed(DaemonCommandExecution.java:122)
at org.gradle.launcher.daemon.server.exec.RequestStopIfSingleUsedDaemon.execute(RequestStopIfSingleUsedDaemon.java:34)
at org.gradle.launcher.daemon.server.api.DaemonCommandExecution.proceed(DaemonCommandExecution.java:122)
at org.gradle.launcher.daemon.server.exec.ForwardClientInput$2.call(ForwardClientInput.java:74)
at org.gradle.launcher.daemon.server.exec.ForwardClientInput$2.call(ForwardClientInput.java:72)
at org.gradle.util.Swapper.swap(Swapper.java:38)
at org.gradle.launcher.daemon.server.exec.ForwardClientInput.execute(ForwardClientInput.java:72)
at org.gradle.launcher.daemon.server.api.DaemonCommandExecution.proceed(DaemonCommandExecution.java:122)
at org.gradle.launcher.daemon.server.exec.LogAndCheckHealth.execute(LogAndCheckHealth.java:55)
at org.gradle.launcher.daemon.server.api.DaemonCommandExecution.proceed(DaemonCommandExecution.java:122)
at org.gradle.launcher.daemon.server.exec.LogToClient.doBuild(LogToClient.java:62)
at org.gradle.launcher.daemon.server.exec.BuildCommandOnly.execute(BuildCommandOnly.java:36)
at org.gradle.launcher.daemon.server.api.DaemonCommandExecution.proceed(DaemonCommandExecution.java:122)
at org.gradle.launcher.daemon.server.exec.EstablishBuildEnvironment.doBuild(EstablishBuildEnvironment.java:81)
at org.gradle.launcher.daemon.server.exec.BuildCommandOnly.execute(BuildCommandOnly.java:36)
at org.gradle.launcher.daemon.server.api.DaemonCommandExecution.proceed(DaemonCommandExecution.java:122)
at org.gradle.launcher.daemon.server.exec.StartBuildOrRespondWithBusy$1.run(StartBuildOrRespondWithBusy.java:50)
at org.gradle.launcher.daemon.server.DaemonStateCoordinator$1.run(DaemonStateCoordinator.java:295)
at org.gradle.internal.concurrent.ExecutorPolicy$CatchAndRecordFailures.onExecute(ExecutorPolicy.java:63)
at org.gradle.internal.concurrent.ManagedExecutorImpl$1.run(ManagedExecutorImpl.java:46)
at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
at org.gradle.internal.concurrent.ThreadFactoryImpl$ManagedThreadRunnable.run(ThreadFactoryImpl.java:55)
at java.lang.Thread.run(Thread.java:748)
```


Here is the attached error log:

[](https://i.stack.imgur.com/D8psA.png)


----------
        
## Answer \#0

**Accepted** Vote: 65

Created at 2018-12-17 17:13:42

------------

Issue has been resolved after updating Android studio version to 3.3-rc2 or latest released version.

cr: @shadowsheep

have to change version under /gradle/wrapper/gradle-wrapper.properties.
refer below url
[https://stackoverflow.com/a/56412795/7532946](https://stackoverflow.com/a/56412795/7532946)


------------
    
    
## Answer \#1

 Vote: 25

Created at 2018-12-13 15:31:40

------------

I have the same problem after upgrading to Gradle Wrapper 5.0., Now I switch back to 4.10.3 which just released 5 December 2018 based on Gradle documentation and use Android Gradle Plugin: 3.2.1 (the latest stable version).


------------
    
    
## Answer \#2

 Vote: 20

Created at 2019-06-02 06:50:31

------------

This issue is due to incompatible of your plugin Verison and required Gradle version; they need to match with each other. I am sharing how my problem was solved.
plugin version
[](https://i.stack.imgur.com/KejVb.png)
Required Gradle version is here
[](https://i.stack.imgur.com/IZl6i.png)
more compatibility you can see from here.
[Android Plugin for Gradle Release Notes](https://developer.android.com/studio/releases/gradle-plugin.html)
if you have the android studio version 4.0.1
[](https://i.stack.imgur.com/WhUKI.png)
then your top level gradle file must be like this
```
buildscript {
repositories {
    google()
    jcenter()
}
dependencies {
    classpath 'com.android.tools.build:gradle:4.0.2'
    classpath 'com.google.firebase:firebase-crashlytics-gradle:2.4.1'

    // NOTE: Do not place your application dependencies here; they belong
    // in the individual module build.gradle files
}
}
```

and the gradle version should be
[](https://i.stack.imgur.com/TtPCQ.png)
and your app gradle look like this
[](https://i.stack.imgur.com/AbcDH.png)


------------
    
    
## Answer \#3

 Vote: 19

Created at 2019-03-14 10:19:43

------------

For others who have the same problem in IntelliJ: 

upgrading to the latest IDE version should resolve the issue. 

In my case going from 2018.1 -> 2018.3.3


------------
    
    
## Answer \#4

 Vote: 5

Created at 2019-06-30 20:22:36

------------

In gradle-wrapper.properties I changed back from gradle-5.1.1 to distributionUrl=https://services.gradle.org/distributions/gradle--all.zip


------------
    
    
## Answer \#5

 Vote: 3

Created at 2019-08-27 14:50:08

------------

I solved this issue after changing the "" and "".

You just goto "" and make changes here. I have worked a combination of versions from another working project of mine and added to the Project where I was getting this problem.


------------
    
    
## Answer \#6

 Vote: 2

Created at 2018-12-30 06:50:46

------------

I have same problem after upgrading to Gradle Wrapper 5.1.rec3. I am back to Gradle 4.6


------------
    
    
## Answer \#7

 Vote: 2

Created at 2020-01-07 10:53:12

------------

I upgraded my IntelliJ Version from 2018.1 to 2018.3.6. It works !


------------
    
    
## Answer \#8

 Vote: 1

Created at 2019-08-05 12:52:40

------------

For anybody facing a similar issue at this point in time, all you need to do is update your Android Studio to the latest version


------------
    
    
## Answer \#9

 Vote: 0

Created at 2020-03-03 15:16:00

------------

The easiest way I've found is delete Android Studio from the applications folder, then download & install it again.


------------
    
    