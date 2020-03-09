---
layout: post
title:  "[Eclipse] 이클립스 구동이 안될 때, java.lang.NoClassDefFoundError: org/eclipse/core/resources/IContainer"
subtitle:   "[Eclipse]"
categories: devsub
tags: eclipse
comments: true
---

이클립스 실행시에 구동이 안되는 이슈

<br><br>


# ISSUE LOG

> `java.lang.NoClassDefFoundError: org/eclipse/core/resources/IContainer`

```
org.eclipse.core.internal.dtree.ObjectNotFoundException: Tree element '/example-project/target/classes/META-INF/maven/com.example/example-project/pom.xml' not found.
           at org.eclipse.core.internal.dtree.AbstractDataTree.handleNotFound(AbstractDataTree.java:260)
           at org.eclipse.core.internal.dtree.DeltaDataTree.getData(DeltaDataTree.java:599)
           at org.eclipse.core.internal.dtree.DataDeltaNode.asBackwardDelta(DataDeltaNode.java:54)
           at org.eclipse.core.internal.dtree.DataDeltaNode.asBackwardDelta(DataDeltaNode.java:51)
           at org.eclipse.core.internal.dtree.DataDeltaNode.asBackwardDelta(DataDeltaNode.java:51)
           at org.eclipse.core.internal.dtree.DataDeltaNode.asBackwardDelta(DataDeltaNode.java:51)
           at org.eclipse.core.internal.dtree.DataDeltaNode.asBackwardDelta(DataDeltaNode.java:51)
           at org.eclipse.core.internal.dtree.NoDataDeltaNode.asBackwardDelta(NoDataDeltaNode.java:63)
           at org.eclipse.core.internal.dtree.NoDataDeltaNode.asBackwardDelta(NoDataDeltaNode.java:63)
           at org.eclipse.core.internal.dtree.NoDataDeltaNode.asBackwardDelta(NoDataDeltaNode.java:63)
           at org.eclipse.core.internal.dtree.DataDeltaNode.asBackwardDelta(DataDeltaNode.java:51)
           at org.eclipse.core.internal.dtree.DeltaDataTree.asBackwardDelta(DeltaDataTree.java:91)
           at org.eclipse.core.internal.dtree.DeltaDataTree.reroot(DeltaDataTree.java:835)
           at org.eclipse.core.internal.dtree.DeltaDataTree.reroot(DeltaDataTree.java:834)
           at org.eclipse.core.internal.dtree.DeltaDataTree.reroot(DeltaDataTree.java:811)
           at org.eclipse.core.internal.watson.ElementTree.immutable(ElementTree.java:522)
           at org.eclipse.core.internal.resources.SaveManager.restore(SaveManager.java:731)
           at org.eclipse.core.internal.resources.SaveManager.startup(SaveManager.java:1564)
           at org.eclipse.core.internal.resources.Workspace.startup(Workspace.java:2454)
           at org.eclipse.core.internal.resources.Workspace.open(Workspace.java:2211)
           at org.eclipse.core.resources.ResourcesPlugin.start(ResourcesPlugin.java:489)
           at org.eclipse.osgi.internal.framework.BundleContextImpl$3.run(BundleContextImpl.java:815)
           at org.eclipse.osgi.internal.framework.BundleContextImpl$3.run(BundleContextImpl.java:1)
           at java.security.AccessController.doPrivileged(Native Method)
           at org.eclipse.osgi.internal.framework.BundleContextImpl.startActivator(BundleContextImpl.java:808)
           at org.eclipse.osgi.internal.framework.BundleContextImpl.start(BundleContextImpl.java:765)
           at org.eclipse.osgi.internal.framework.EquinoxBundle.startWorker0(EquinoxBundle.java:1005)
           at org.eclipse.osgi.internal.framework.EquinoxBundle$EquinoxModule.startWorker(EquinoxBundle.java:357)
           at org.eclipse.osgi.container.Module.doStart(Module.java:589)
           at org.eclipse.osgi.container.Module.start(Module.java:457)
           at org.eclipse.osgi.framework.util.SecureAction.start(SecureAction.java:471)
           at org.eclipse.osgi.internal.hooks.EclipseLazyStarter.postFindLocalClass(EclipseLazyStarter.java:117)
           at org.eclipse.osgi.internal.loader.classpath.ClasspathManager.findLocalClass(ClasspathManager.java:570)
           at org.eclipse.osgi.internal.loader.ModuleClassLoader.findLocalClass(ModuleClassLoader.java:331)
           at org.eclipse.osgi.internal.loader.BundleLoader.findLocalClass(BundleLoader.java:395)
           at org.eclipse.osgi.internal.loader.sources.SingleSourcePackage.loadClass(SingleSourcePackage.java:39)
           at org.eclipse.osgi.internal.loader.BundleLoader.findClassInternal(BundleLoader.java:469)
           at org.eclipse.osgi.internal.loader.BundleLoader.findClass(BundleLoader.java:422)
           at org.eclipse.osgi.internal.loader.BundleLoader.findClass(BundleLoader.java:414)
           at org.eclipse.osgi.internal.loader.ModuleClassLoader.loadClass(ModuleClassLoader.java:153)
           at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
           at org.eclipse.ui.internal.ide.application.IDEApplication.start(IDEApplication.java:156)
           at org.eclipse.equinox.internal.app.EclipseAppHandle.run(EclipseAppHandle.java:203)
           at org.eclipse.core.runtime.internal.adaptor.EclipseAppLauncher.runApplication(EclipseAppLauncher.java:137)
           at org.eclipse.core.runtime.internal.adaptor.EclipseAppLauncher.start(EclipseAppLauncher.java:107)
           at org.eclipse.core.runtime.adaptor.EclipseStarter.run(EclipseStarter.java:400)
           at org.eclipse.core.runtime.adaptor.EclipseStarter.run(EclipseStarter.java:255)
           at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
           at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
           at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
           at java.lang.reflect.Method.invoke(Method.java:498)
           at org.eclipse.equinox.launcher.Main.invokeFramework(Main.java:661)
           at org.eclipse.equinox.launcher.Main.basicRun(Main.java:597)
           at org.eclipse.equinox.launcher.Main.run(Main.java:1476)

!ENTRY org.eclipse.osgi 4 0 2020-02-17 19:50:29.010
!MESSAGE Application error
!STACK 1
java.lang.NoClassDefFoundError: org/eclipse/core/resources/IContainer
           at org.eclipse.ui.internal.ide.application.IDEApplication.start(IDEApplication.java:156)
           at org.eclipse.equinox.internal.app.EclipseAppHandle.run(EclipseAppHandle.java:203)
           at org.eclipse.core.runtime.internal.adaptor.EclipseAppLauncher.runApplication(EclipseAppLauncher.java:137)
           at org.eclipse.core.runtime.internal.adaptor.EclipseAppLauncher.start(EclipseAppLauncher.java:107)
           at org.eclipse.core.runtime.adaptor.EclipseStarter.run(EclipseStarter.java:400)
           at org.eclipse.core.runtime.adaptor.EclipseStarter.run(EclipseStarter.java:255)
           at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
           at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
           at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
           at java.lang.reflect.Method.invoke(Method.java:498)
           at org.eclipse.equinox.launcher.Main.invokeFramework(Main.java:661)
           at org.eclipse.equinox.launcher.Main.basicRun(Main.java:597)
           at org.eclipse.equinox.launcher.Main.run(Main.java:1476)
Caused by: java.lang.ClassNotFoundException: An error occurred while automatically activating bundle org.eclipse.core.resources (104).
```

<br>


# ISSUE 해결

 
1. workspace-directory>\.metadata\.plugins\org.eclipse.core.resources 디렉토리 이동
2. .snap이란 파일이 있으면 삭제 하면 된다

<br>


---
[참고]  
- https://i-jen.tistory.com/147