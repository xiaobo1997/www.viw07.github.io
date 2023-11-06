[TOC]



# 创建bean和获取bean



## bean的生命周期的概念



> 初识bean的生命周期
>
> - 1.实例化
> - 2.属性赋值
> - 3.初始化
> - 4.销毁

![image-20201220200533435](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201220200533435.png)



## bean和初始化bean



### 创建bean和初始化bean的生命周期

![image-20201220200708598](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201220200708598.png)



#### AbstractApplicationContext.class



```java
@Override
	public void refresh() throws BeansException, IllegalStateException {
		synchronized (this.startupShutdownMonitor) {
			StartupStep contextRefresh = this.applicationStartup.start("spring.context.refresh");

		。。。。

				// 实例化所有剩余的（非延迟初始化）单例。
				finishBeanFactoryInitialization(beanFactory);

				// Last step: publish corresponding event.
				finishRefresh();
			}
	}

// 返回上下文bean工厂的实例化过程，实例化所有剩余的单例类
protected void finishBeanFactoryInitialization(ConfigurableListableBeanFactory beanFactory) {
		//初始化上下文的 conversion service 
		if (beanFactory.containsBean(CONVERSION_SERVICE_BEAN_NAME) &&
				beanFactory.isTypeMatch(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class)) {
			beanFactory.setConversionService(
					beanFactory.getBean(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class));
		}

		//如果之前没有任何bean后处理器
    //（例如PropertyPlaceholderConfigurer Bean）进行过注册，
    //请注册一个默认的嵌入式值解析器：	
    	if (!beanFactory.hasEmbeddedValueResolver()) {
			beanFactory.addEmbeddedValueResolver(strVal -> getEnvironment().resolvePlaceholders(strVal));
		}

		// Initialize LoadTimeWeaverAware beans early to allow for registering their transformers early.
		String[] weaverAwareNames = beanFactory.getBeanNamesForType(LoadTimeWeaverAware.class, false, false);
		for (String weaverAwareName : weaverAwareNames) {
			getBean(weaverAwareName);
		}

		//// 停止使用类型匹配的临时类加载器 
		beanFactory.setTempClassLoader(null);

		// 允许缓存所有bean定义元数据，而不期待进一步的更改
		beanFactory.freezeConfiguration();

		//实例剩余的（非懒加载）的单例
		beanFactory.preInstantiateSingletons();
	}
```

> 懒加载的意思，指的是bean单例不是在Spring容器初始化的时候就创建的，而是在要使用该bean的时候，才会创建该bean。



#### DefaultListableBeanFactory.class





```java
@Override
	public void preInstantiateSingletons() throws BeansException {
		if (logger.isTraceEnabled()) {
			logger.trace("Pre-instantiating singletons in " + this);
		}

		//遍历一个副本以允许使用init方法，这些方法依次注册新的bean定义	List<String> beanNames = new ArrayList<>(this.beanDefinitionNames);
		//触发所有非延迟单例bean的初始化...
        for (String beanName : beanNames) {
            // 触发所有适用bean的后初始化回调
			RootBeanDefinition bd = getMergedLocalBeanDefinition(beanName);
			if (!bd.isAbstract() && bd.isSingleton() && !bd.isLazyInit()) {
				if (isFactoryBean(beanName)) {
					Object bean = getBean(FACTORY_BEAN_PREFIX + beanName);
					if (bean instanceof FactoryBean) {
						FactoryBean<?> factory = (FactoryBean<?>) bean;
						boolean isEagerInit;
						if (System.getSecurityManager() != null && factory instanceof SmartFactoryBean) {
							isEagerInit = AccessController.doPrivileged(
									(PrivilegedAction<Boolean>) ((SmartFactoryBean<?>) factory)::isEagerInit,
									getAccessControlContext());
						}
						else {
							isEagerInit = (factory instanceof SmartFactoryBean &&
									((SmartFactoryBean<?>) factory).isEagerInit());
						}
						if (isEagerInit) {
							getBean(beanName);
						}
					}
				}
				else {
					getBean(beanName);
				}
			}
		}

		//触发所有适用bean的初始化后回调...
		for (String beanName : beanNames) {
			Object singletonInstance = getSingleton(beanName);
			if (singletonInstance instanceof SmartInitializingSingleton) {
				StartupStep smartInitialize = this.getApplicationStartup().start("spring.beans.smart-initialize")
						.tag("beanName", beanName);
				SmartInitializingSingleton smartSingleton = (SmartInitializingSingleton) singletonInstance;
				if (System.getSecurityManager() != null) {
					AccessController.doPrivileged((PrivilegedAction<Object>) () -> {
						smartSingleton.afterSingletonsInstantiated();
						return null;
					}, getAccessControlContext());
				}
				else {
					smartSingleton.afterSingletonsInstantiated();
				}
				smartInitialize.end();
			}
		}
	}
```



#### AbstractBeanFactory.class

```java
// 返回一个实例，该实例可以是指定bean的共享或独立的。
protected <T> T doGetBean(
			String name, @Nullable Class<T> requiredType, @Nullable Object[] args, boolean typeCheckOnly)
			throws BeansException {
		// 去除name上存在的工厂bean的前缀
		String beanName = transformedBeanName(name);
		Object bean;

		// 快速判断单例缓存中是否存在该bean，如果存在则返回单例bean；否则返回null
    	Object sharedInstance = getSingleton(beanName);
		if (sharedInstance != null && args == null) {
			if (logger.isTraceEnabled()) {
				if (isSingletonCurrentlyInCreation(beanName)) {
					logger.trace("Returning eagerly cached instance of singleton bean '" + beanName +
							"' that is not fully initialized yet - a consequence of a circular reference");
				}
				else {
					logger.trace("Returning cached instance of singleton bean '" + beanName + "'");
				}
			}
            //获取给定bean实例的对象，如果是FactoryBean，则为bean实例本身或其创建的对象。
			bean = getObjectForBeanInstance(sharedInstance, name, beanName, null);
		}

		else {
			// 如果我们已经在创建此bean实例，则失败：大概在循环引用中。
            // We're assumably within a circular reference.
			if (isPrototypeCurrentlyInCreation(beanName)) {
				throw new BeanCurrentlyInCreationException(beanName);
			}

			// 判断该bean是否存在父工厂
            // 这里是还对ioc容器中的BeanDefinition是否存在进行检查
            //检查能否在当前的BeanFactory中获取得到我们的bean，如果得不到
            // 就向上双亲的BeanFactory去获取，还是没有去一直向上，
			BeanFactory parentBeanFactory = getParentBeanFactory();
			if (parentBeanFactory != null && !containsBeanDefinition(beanName)) {
				// 如果存在父工厂，则按照父工厂的实现来创建该实例
				String nameToLookup = originalBeanName(name);
				if (parentBeanFactory instanceof AbstractBeanFactory) {
					return ((AbstractBeanFactory) parentBeanFactory).doGetBean(
							nameToLookup, requiredType, args, typeCheckOnly);
				}
				else if (args != null) {
					//使用显式参数委派给父级。
					return (T) parentBeanFactory.getBean(nameToLookup, args);
				}
				else if (requiredType != null) {
					// No args -> delegate to standard getBean method.
					return parentBeanFactory.getBean(nameToLookup, requiredType);
				}
				else {
					return (T) parentBeanFactory.getBean(nameToLookup);
				}
			}
			// 标记该bean已经被创建
			if (!typeCheckOnly) {
				markBeanAsCreated(beanName);
			}

			StartupStep beanCreation = this.applicationStartup.start("spring.beans.instantiate")
					.tag("beanName", name);
			try {
				if (requiredType != null) {
					beanCreation.tag("beanType", requiredType::toString);
				}
                // 返回bean的定义信息，包括bean的scope、依赖、是否进行懒加载等定义
				//根据bean的名字获取BeanDefinition
                RootBeanDefinition mbd = getMergedLocalBeanDefinition(beanName);
				checkMergedBeanDefinition(mbd, beanName, args);

				// 确保当前bean依赖的bean的初始化。递归去获取一直到
                //取到一个没有如何依赖的bean
				String[] dependsOn = mbd.getDependsOn();
				if (dependsOn != null) {
					for (String dep : dependsOn) {
						if (isDependent(beanName, dep)) {
							throw new BeanCreationException(mbd.getResourceDescription(), beanName,
									"Circular depends-on relationship between '" + beanName + "' and '" + dep + "'");
						}
						registerDependentBean(dep, beanName);
						try {
							getBean(dep);
						}
						catch (NoSuchBeanDefinitionException ex) {
							throw new BeanCreationException(mbd.getResourceDescription(), beanName,
									"'" + beanName + "' depends on missing bean '" + dep + "'", ex);
						}
					}
				}

				// 开始创建这个bean，注意这里使用的lambda表达式，传入的是一个AbstracBeanFactory
				if (mbd.isSingleton()) {
					sharedInstance = getSingleton(beanName, () -> {
						try {
							return createBean(beanName, mbd, args);
						}
						catch (BeansException ex) {
							//从单例缓存中显式删除实例：创建过程可能将它急切地放置在该实例中，
                            //以实现循环引用解析。还删除所有收到对bean的临时引用的bean。							destroySingleton(beanName);
							throw ex;
						}
					});
					bean = getObjectForBeanInstance(sharedInstance, name, beanName, mbd);
				}

				else if (mbd.isPrototype()) {
					// 这是一个原型->创建一个新实例。
					Object prototypeInstance = null;
					try {
						beforePrototypeCreation(beanName);
						prototypeInstance = createBean(beanName, mbd, args);
					}
					finally {
						afterPrototypeCreation(beanName);
					}
					bean = getObjectForBeanInstance(prototypeInstance, name, beanName, mbd);
				}

				else {
					String scopeName = mbd.getScope();
					if (!StringUtils.hasLength(scopeName)) {
						throw new IllegalStateException("No scope name defined for bean ´" + beanName + "'");
					}
					Scope scope = this.scopes.get(scopeName);
					if (scope == null) {
						throw new IllegalStateException("No Scope registered for scope name '" + scopeName + "'");
					}
					try {
						Object scopedInstance = scope.get(beanName, () -> {
							beforePrototypeCreation(beanName);
							try {
								return createBean(beanName, mbd, args);
							}
							finally {
								afterPrototypeCreation(beanName);
							}
						});
						bean = getObjectForBeanInstance(scopedInstance, name, beanName, mbd);
					}
					catch (IllegalStateException ex) {
						throw new ScopeNotActiveException(beanName, scopeName, ex);
					}
				}
			}
			catch (BeansException ex) {
				beanCreation.tag("exception", ex.getClass().toString());
				beanCreation.tag("message", String.valueOf(ex.getMessage()));
				cleanupAfterBeanCreationFailure(beanName);
				throw ex;
			}
			finally {
				beanCreation.end();
			}
		}

		// Check if required type matches the type of the actual bean instance.
		if (requiredType != null && !requiredType.isInstance(bean)) {
			try {
				T convertedBean = getTypeConverter().convertIfNecessary(bean, requiredType);
				if (convertedBean == null) {
					throw new BeanNotOfRequiredTypeException(name, requiredType, bean.getClass());
				}
				return convertedBean;
			}
			catch (TypeMismatchException ex) {
				if (logger.isTraceEnabled()) {
					logger.trace("Failed to convert bean '" + name + "' to required type '" +
							ClassUtils.getQualifiedName(requiredType) + "'", ex);
				}
				throw new BeanNotOfRequiredTypeException(name, requiredType, bean.getClass());
			}
		}
		return (T) bean;
	}
```



#### DefaultSingletonBeanRegistry.class





```java
// 尝试从缓存中获取单例对象，如果缓存中有该单例对象，并且该对象正在被创建，则从缓存中获取。
@Nullable
	protected Object getSingleton(String beanName, boolean allowEarlyReference) {
		//快速检查现有实例，而无需完全单例锁定
		Object singletonObject = this.singletonObjects.get(beanName);
		// 从缓存中获取单例对象、判断对象是否正在被创建
        if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
			singletonObject = this.earlySingletonObjects.get(beanName);
			if (singletonObject == null && allowEarlyReference) {
				synchronized (this.singletonObjects) {
					// Consistent creation of early reference within full singleton lock
					singletonObject = this.singletonObjects.get(beanName);
					if (singletonObject == null) {
						singletonObject = this.earlySingletonObjects.get(beanName);
						if (singletonObject == null) {
							ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
							if (singletonFactory != null) {
								singletonObject = singletonFactory.getObject();
								this.earlySingletonObjects.put(beanName, singletonObject);
								this.singletonFactories.remove(beanName);
							}
						}
					}
				}
			}
		}
		return singletonObject;
	}


public Object getSingleton(String beanName, ObjectFactory<?> singletonFactory) {
	Assert.notNull(beanName, "Bean name must not be null");
	// 可以看到，对于单例bean的获取，是要获得“单例缓存”的锁的，否则无法操作bean的获取。
	synchronized (this.singletonObjects) {
		Object singletonObject = this.singletonObjects.get(beanName);
		if (singletonObject == null) {
			
			...
			
			// bean创建之前的回调方法
			beforeSingletonCreation(beanName);
			boolean newSingleton = false;
			
			...
			
			try {
			    // 尝试从单例工厂获取单例bean
			    // 注意，这里的singletonFactory是由方法参数传进来的一个lambda表达式，这个singletonFactory是一个AbstractAutowireCapableBeanFactory实例
				singletonObject = singletonFactory.getObject();
				newSingleton = true;
			}
			catch (IllegalStateException ex) {
			    ...
			}
			catch (BeanCreationException ex) {
			    ...
			}
			finally {
		        
		        ...
		        
				// 单例bean创建过后的回调方法
				afterSingletonCreation(beanName);
			}
			if (newSingleton) {
				addSingleton(beanName, singletonObject);
			}
		}
		return singletonObject;
	}
}
```



#### AbstractAutowireCapableBeanFactory.class



```java
//此类的中央方法：创建Bean实例，填充Bean实例，应用后处理器等
@Override
	protected Object createBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args)
			throws BeanCreationException {

		if (logger.isTraceEnabled()) {
			logger.trace("Creating instance of bean '" + beanName + "'");
		}
		RootBeanDefinition mbdToUse = mbd;

	// 调用实例化前的后置处理器，在这个后置处理器中可以对bean进行处理，可以返回由后置处理器处理的bean而不是被实例化的bean。		Class<?> resolvedClass = resolveBeanClass(mbd, beanName);
		// 这里拦截bean的实例化
        Class<?> resolvedClass = resolveBeanClass(mbd, beanName);
        if (resolvedClass != null && !mbd.hasBeanClass() && mbd.getBeanClassName() != null) {
			mbdToUse = new RootBeanDefinition(mbd);
			mbdToUse.setBeanClass(resolvedClass);
		}

		// Prepare method overrides.
		try {
			mbdToUse.prepareMethodOverrides();
		}
		catch (BeanDefinitionValidationException ex) {
			throw new BeanDefinitionStoreException(mbdToUse.getResourceDescription(),
					beanName, "Validation of method overrides failed", ex);
		}
		try {
			Object bean = resolveBeforeInstantiation(beanName, mbdToUse);
			if (bean != null) {
				return bean;
			}
		}
		catch (Throwable ex) {
			throw new BeanCreationException(mbdToUse.getResourceDescription(), beanName,
					"BeanPostProcessor before instantiation of bean failed", ex);
		}

		try {
			Object beanInstance = doCreateBean(beanName, mbdToUse, args);
			if (logger.isTraceEnabled()) {
				logger.trace("Finished creating instance of bean '" + beanName + "'");
			}
			return beanInstance;
		}
		catch (BeanCreationException | ImplicitlyAppearedSingletonException ex) {
			throw ex;
		}
		catch (Throwable ex) {
			throw new BeanCreationException(
					mbdToUse.getResourceDescription(), beanName, "Unexpected exception during bean creation", ex);
		}
	}




/**
实际创建指定的bean。此时，预创建处理已经发生，例如检查{@code postProcessBeforeInstantiation}回调。
<p>在默认bean实例化，使用工厂方法和自动装配构造函数之间进行区分

正在的创建一个bean，并且按照配置文件的配置来实例化该bean。如果没有初始化前的后置处理器的调用，则调用该方法。
*/
// 在这里看到bean是怎么生成的，
protected Object doCreateBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args)
			throws BeanCreationException {

		// 实例化bean。 创建一个包装类，用于包装真正要创建的bean。
		BeanWrapper instanceWrapper = null;
    // 如果是单例 先把缓存中的同名bean清除
		if (mbd.isSingleton()) {
			instanceWrapper = this.factoryBeanInstanceCache.remove(beanName);
		}
		if (instanceWrapper == null) {
             // 使用适当的实例化策略例如：工厂方法、构造函数自动装配或者简单实例化 来为指定bean创建新的实例。这里仅仅是简单的实例化，为bean设置默认初始值
	    // 也就是name为null，age为0。此时instanceWrapper任然还只是一个包装bean，并不是一个真正意义上的person类bean。
			instanceWrapper = createBeanInstance(beanName, mbd, args);
		}
       // 只需要在BeanWrapper里取出WrapperInstance即可。
    // 接下来就是要拿这个创建好的bean和BeanDefinition进行实例化了。
		Object bean = instanceWrapper.getWrappedInstance();
   // 获取bean的类型
    Class<?> beanType = instanceWrapper.getWrappedClass();
		if (beanType != NullBean.class) {
			mbd.resolvedTargetType = beanType;
		}

		// 允许后处理器修改合并的bean定义。
		synchronized (mbd.postProcessingLock) {
			if (!mbd.postProcessed) {
				try {
					applyMergedBeanDefinitionPostProcessors(mbd, beanType, beanName);
				}
				catch (Throwable ex) {
					throw new BeanCreationException(mbd.getResourceDescription(), beanName,
							"Post-processing of merged bean definition failed", ex);
				}
				mbd.postProcessed = true;
			}
		}
//		急于缓存单例，以便即使在诸如BeanFactoryAware之类的生命周期接口触发时也能够解析循环引用。		boolean earlySingletonExposure = (mbd.isSingleton() && this.allowCircularReferences &&
	boolean earlySingletonExposure = (mbd.isSingleton() && this.allowCircularReferences &&	
    isSingletonCurrentlyInCreation(beanName));
		if (earlySingletonExposure) {
			if (logger.isTraceEnabled()) {
				logger.trace("Eagerly caching bean '" + beanName +
						"' to allow for resolving potential circular references");
			}
            // // 先快速的给bean实例化，然后将bean添加到单例工厂中。这里的单例工厂实际上就是一个Map
			addSingletonFactory(beanName, () -> getEarlyBeanReference(beanName, mbd, bean));
		}

		// 实例化一个真正使用的bean。
		Object exposedObject = bean;
		try {
            // 进入这个方法
			populateBean(beanName, mbd, instanceWrapper);
			exposedObject = initializeBean(beanName, exposedObject, mbd);
		}
		catch (Throwable ex) {
			if (ex instanceof BeanCreationException && beanName.equals(((BeanCreationException) ex).getBeanName())) {
				throw (BeanCreationException) ex;
			}
			else {
				throw new BeanCreationException(
						mbd.getResourceDescription(), beanName, "Initialization of bean failed", ex);
			}
		}

		if (earlySingletonExposure) {
			Object earlySingletonReference = getSingleton(beanName, false);
			if (earlySingletonReference != null) {
				if (exposedObject == bean) {
					exposedObject = earlySingletonReference;
				}
				else if (!this.allowRawInjectionDespiteWrapping && hasDependentBean(beanName)) {
					String[] dependentBeans = getDependentBeans(beanName);
					Set<String> actualDependentBeans = new LinkedHashSet<>(dependentBeans.length);
					for (String dependentBean : dependentBeans) {
						if (!removeSingletonIfCreatedForTypeCheckOnly(dependentBean)) {
							actualDependentBeans.add(dependentBean);
						}
					}
					if (!actualDependentBeans.isEmpty()) {
						throw new BeanCurrentlyInCreationException(beanName,
								"Bean with name '" + beanName + "' has been injected into other beans [" +
								StringUtils.collectionToCommaDelimitedString(actualDependentBeans) +
								"] in its raw version as part of a circular reference, but has eventually been " +
								"wrapped. This means that said other beans do not use the final version of the " +
								"bean. This is often the result of over-eager type matching - consider using " +
								"'getBeanNamesForType' with the 'allowEagerInit' flag turned off, for example.");
					}
				}
			}
		}

		// Register bean as disposable.
		try {
			registerDisposableBeanIfNecessary(beanName, bean, mbd);
		}
		catch (BeanDefinitionValidationException ex) {
			throw new BeanCreationException(
					mbd.getResourceDescription(), beanName, "Invalid destruction signature", ex);
		}

		return exposedObject;
	}




// 使用Bean定义中的属性值填充给定BeanWrapper中的Bean实例。
// 就是给创建出的Bean进行赋值填充。
protected void populateBean(String beanName, RootBeanDefinition mbd, @Nullable BeanWrapper bw) {
    ...        
    // 取出BeanDefinition里的属性值
	PropertyValues pvs = (mbd.hasPropertyValues() ? mbd.getPropertyValues() : null);
    
    // 如果设置的是自动装配模式，则由自动装配来进行赋值
	if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_NAME ||
			mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_TYPE) {
		MutablePropertyValues newPvs = new MutablePropertyValues(pvs);

        // 通过bean名自动装配
		if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_NAME) {
			autowireByName(beanName, mbd, bw, newPvs);
		}

        // 通过bean类型自动装配
		if (mbd.getResolvedAutowireMode() == RootBeanDefinition.AUTOWIRE_BY_TYPE) {
			autowireByType(beanName, mbd, bw, newPvs);
		}

		pvs = newPvs;
	}

	boolean hasInstAwareBpps = hasInstantiationAwareBeanPostProcessors();
	boolean needsDepCheck = (mbd.getDependencyCheck() != RootBeanDefinition.DEPENDENCY_CHECK_NONE);

	if (hasInstAwareBpps || needsDepCheck) {
		if (pvs == null) {
			pvs = mbd.getPropertyValues();
		}
		PropertyDescriptor[] filteredPds = filterPropertyDescriptorsForDependencyCheck(bw, mbd.allowCaching);
		if (hasInstAwareBpps) {
			for (BeanPostProcessor bp : getBeanPostProcessors()) {
				if (bp instanceof InstantiationAwareBeanPostProcessor) {
					InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;
					pvs = ibp.postProcessPropertyValues(pvs, filteredPds, bw.getWrappedInstance(), beanName);
					if (pvs == null) {
						return;
					}
				}
			}
		}
		if (needsDepCheck) {
			checkDependencies(beanName, mbd, filteredPds, pvs);
		}
	}

    // 这里的PropertyValues已经包含了bean字段属性的设置值了
	if (pvs != null) {
	    // 对bean进行赋值
		applyPropertyValues(beanName, mbd, bw, pvs);
	}
}


//应用给定的属性值，从而解决对该bean工厂中其他bean的任何运行时引用。
//必须使用深拷贝，因此我们不会永久修改此属性
protected void applyPropertyValues(String beanName, BeanDefinition mbd, BeanWrapper bw, PropertyValues pvs) {
		if (pvs.isEmpty()) {
			return;
		}

		if (System.getSecurityManager() != null && bw instanceof BeanWrapperImpl) {
			((BeanWrapperImpl) bw).setSecurityContext(getAccessControlContext());
		}

		MutablePropertyValues mpvs = null;
		List<PropertyValue> original;

		if (pvs instanceof MutablePropertyValues) {
			mpvs = (MutablePropertyValues) pvs;
			if (mpvs.isConverted()) {
				// Shortcut: use the pre-converted values as-is.
				try {
					bw.setPropertyValues(mpvs);
					return;
				}
				catch (BeansException ex) {
					throw new BeanCreationException(
							mbd.getResourceDescription(), beanName, "Error setting property values", ex);
				}
			}
			original = mpvs.getPropertyValueList();
		}
		else {
			original = Arrays.asList(pvs.getPropertyValues());
		}

		TypeConverter converter = getCustomTypeConverter();
		if (converter == null) {
			converter = bw;
		}
		BeanDefinitionValueResolver valueResolver = new BeanDefinitionValueResolver(this, beanName, mbd, converter);

		// 拷贝值；创建一个深拷贝副本，应用于任何bean引用此bean的情况。		List<PropertyValue> deepCopy = new ArrayList<>(original.size());
		boolean resolveNecessary = false;
		for (PropertyValue pv : original) {
			if (pv.isConverted()) {
				deepCopy.add(pv);
			}
			else {
				String propertyName = pv.getName();
				Object originalValue = pv.getValue();
				if (originalValue == AutowiredPropertyMarker.INSTANCE) {
					Method writeMethod = bw.getPropertyDescriptor(propertyName).getWriteMethod();
					if (writeMethod == null) {
						throw new IllegalArgumentException("Autowire marker for property without write method: " + pv);
					}
					originalValue = new DependencyDescriptor(new MethodParameter(writeMethod, 0), true);
				}
				Object resolvedValue = valueResolver.resolveValueIfNecessary(pv, originalValue);
				Object convertedValue = resolvedValue;
				boolean convertible = bw.isWritableProperty(propertyName) &&
						!PropertyAccessorUtils.isNestedOrIndexedProperty(propertyName);
				if (convertible) {
					convertedValue = convertForProperty(resolvedValue, propertyName, bw, converter);
				}
				// 可能在合并的bean定义中存储转换后的值，以避免为每个创建的bean实例重新转换。				if (resolvedValue == originalValue) {
					if (convertible) {
						pv.setConvertedValue(convertedValue);
					}
					deepCopy.add(pv);
				}
				else if (convertible && originalValue instanceof TypedStringValue &&
						!((TypedStringValue) originalValue).isDynamic() &&
						!(convertedValue instanceof Collection || ObjectUtils.isArray(convertedValue))) {
					pv.setConvertedValue(convertedValue);
					deepCopy.add(pv);
				}
				else {
					resolveNecessary = true;
					deepCopy.add(new PropertyValue(pv, convertedValue));
				}
			}
		}
		if (mpvs != null && !resolveNecessary) {
			mpvs.setConverted();
		}

		// // 将深拷贝属性数组填充到beanWrapper中。这里就真正的将属性值填充到了bean上，实现了
		try {
			bw.setPropertyValues(new MutablePropertyValues(deepCopy));
		}
		catch (BeansException ex) {
			throw new BeanCreationException(
					mbd.getResourceDescription(), beanName, "Error setting property values", ex);
		}
	}

```

> 其实真正的给bean赋值是在AbstractAutowireCapableBeanFactory中的
>
> applyPropertyValues方法并且是通过对原属性值进行了一次深拷贝，然后将深拷贝后的属性值填充到bean里的。
>
> 和DI关系密切的两个方法分别是 createBeanInstance和populateBean,
>
> 在createBeanInstance中获取Bean的java的对象，可以通过工厂方法生成，也可以通过注解 @Autowire,这些都是通过BeanDefinition指定的





## 总结

主要是分析如何给实例化获取bean，并给bean属性赋值的，

toGetBean是实际得到bean的地方，也是DI的地方



