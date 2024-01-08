# spring boot的一整个处理过程

## 第一步
    创建好整个maven工程，记得配置jdk以及maven仓库相关配置

## 第二部
    在maven工程的pom.xml文件导入场景开发相关的依赖
## 第三步
    在application.properties文件里配置相关配置（数据库连接，server，mapper的映射地址）

# springboot的处理流程：

## 1.首先找到doDispatch()方法

## 2.找到正确处理请求的处理器方法
    // Determine handler for the current request.
	mappedHandler = getHandler(processedRequest);
    
    点击进入：
    HandlerExecutionChain handler = mapping.getHandler(request);
    获取到能处理当前请求的处理器。

## 3.获取HandlerAdapter
    // Determine handler adapter for the current request.
    HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());

    点击进入：
		if (adapter.supports(handler)) {
					return adapter;
		}
    找到适配当前处理器的处理器适配器

## 4.真正执行处理器的方法
    // Actually invoke the handler.
	mv = ha.handle(processedRequest, response, mappedHandler.getHandler());

    点击进入：
    return handleInternal(request, response, (HandlerMethod) handler);

    点击进入：（执行处理器的核心代码）
    // No synchronization on session demanded at all...
	mav = invokeHandlerMethod(request, response, handlerMethod);


    if (this.argumentResolvers != null) {
				invocableMethod.setHandlerMethodArgumentResolvers(this.argumentResolvers);
			}
			if (this.returnValueHandlers != null) {
				invocableMethod.setHandlerMethodReturnValueHandlers(this.returnValueHandlers);
			}
    （给当前执行的方法添加参数解析器和返回值处理器）

    点击进入：
    invocableMethod.invokeAndHandle(webRequest, mavContainer);

    点击进入：（最终获得处理器方法的返回值）
    Object returnValue = invokeForRequest(webRequest, mavContainer, providedArgs)

    点击进入：（如何确定目标方法中参数的每一个值）
    Object[] args = getMethodArgumentValues(request, mavContainer, providedArgs);

    点击：（确定参数解析器，并解析绑定）
    if (!this.resolvers.supportsParameter(parameter)) {
				throw new IllegalStateException(formatArgumentError(parameter, "No suitable resolver"));
			}
			try {
				args[i] = this.resolvers.resolveArgument(parameter, mavContainer, request, this.dataBinderFactory);
			}




    获取到处理器返回值结果之后，进行返回值处理
    try {
			this.returnValueHandlers.handleReturnValue(
					returnValue, getReturnValueType(returnValue), mavContainer, webRequest);
		}
    
    点击进入：（寻找哪个返回值处理器能处理当前返回值）
    HandlerMethodReturnValueHandler handler = selectHandler(returnValue, returnType);
        如何判断是否能处理当前返回值
        if (handler.supportsReturnType(returnType)) {
				return handler;
			}
    找到能处理的返回值处理器handleReturnValue 进行处理
    RequestResponseBodyMethodProcessor 可以处理返回值标了@ResponseBody 注解的。

    点击进入：（具体处理返回值）
    handler.handleReturnValue(returnValue, returnType, mavContainer, webRequest);

    （利用消息转换器进行写出操作）
    // Try even with null return value. ResponseBodyAdvice could get involved.
	writeWithMessageConverters(returnValue, returnType, inputMessage, outputMessage);


    消息转换器代码：（核心）
    else {
			HttpServletRequest request = inputMessage.getServletRequest();
			List<MediaType> acceptableTypes;
			try {
				acceptableTypes = getAcceptableMediaTypes(request); //获取客户端可以接收的类型
			}
			catch (HttpMediaTypeNotAcceptableException ex) {
				int series = outputMessage.getServletResponse().getStatus() / 100;
				if (body == null || series == 4 || series == 5) {
					if (logger.isDebugEnabled()) {
						logger.debug("Ignoring error response content (if any). " + ex);
					}
					return;
				}
				throw ex;
			}
			List<MediaType> producibleTypes = getProducibleMediaTypes(request, valueType, targetType); //服务器可以产出的媒体类型

			if (body != null && producibleTypes.isEmpty()) {
				throw new HttpMessageNotWritableException(
						"No converter found for return value of type: " + valueType);
			}
			List<MediaType> mediaTypesToUse = new ArrayList<>();
			for (MediaType requestedType : acceptableTypes) {
				for (MediaType producibleType : producibleTypes) {
					if (requestedType.isCompatibleWith(producibleType)) {
						mediaTypesToUse.add(getMostSpecificMediaType(requestedType, producibleType));
					}
				}
			}
			if (mediaTypesToUse.isEmpty()) {
				if (body != null) {
					throw new HttpMediaTypeNotAcceptableException(producibleTypes);
				}
				if (logger.isDebugEnabled()) {
					logger.debug("No match for " + acceptableTypes + ", supported: " + producibleTypes);
				}
				return;
			}

			MediaType.sortBySpecificityAndQuality(mediaTypesToUse);

			for (MediaType mediaType : mediaTypesToUse) {
				if (mediaType.isConcrete()) {
					selectedMediaType = mediaType;
					break;
				}
				else if (mediaType.isPresentIn(ALL_APPLICATION_MEDIA_TYPES)) {
					selectedMediaType = MediaType.APPLICATION_OCTET_STREAM;
					break;
				}
			}

			if (logger.isDebugEnabled()) {
				logger.debug("Using '" + selectedMediaType + "', given " +
						acceptableTypes + " and supported " + producibleTypes);
			}
		}



## 内容协商管理器
1.acceptableTypes = getAcceptableMediaTypes(request);
2.return this.contentNegotiationManager.resolveMediaTypes(new ServletWebRequest(request));
通过accept请求头内容

## 服务器可以产出的媒体类型
List<MediaType> producibleTypes = getProducibleMediaTypes(request, valueType, targetType);

protected List<MediaType> getProducibleMediaTypes(
			HttpServletRequest request, Class<?> valueClass, @Nullable Type targetType) {

		Set<MediaType> mediaTypes =
				(Set<MediaType>) request.getAttribute(HandlerMapping.PRODUCIBLE_MEDIA_TYPES_ATTRIBUTE);
		if (!CollectionUtils.isEmpty(mediaTypes)) {
			return new ArrayList<>(mediaTypes);
		}
		List<MediaType> result = new ArrayList<>();
		for (HttpMessageConverter<?> converter : this.messageConverters) {
			if (converter instanceof GenericHttpMessageConverter && targetType != null) {
				if (((GenericHttpMessageConverter<?>) converter).canWrite(targetType, valueClass, null)) {
					result.addAll(converter.getSupportedMediaTypes(valueClass));
				}
			}
			else if (converter.canWrite(valueClass, null)) {
				result.addAll(converter.getSupportedMediaTypes(valueClass));
			}
		}
		return (result.isEmpty() ? Collections.singletonList(MediaType.ALL) : result);
	}

